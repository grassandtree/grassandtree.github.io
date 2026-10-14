---
title: "AI 기반 엔터프라이즈 데이터 구조·검색·플랫폼 전환 설계"
layout: single
toc: true
published: true
categories: 데이터플랫폼
tags: [AI, 데이터플랫폼, 레이크하우스, RAG, 거버넌스]
---

> 개요 글은 [엔터프라이즈 AI를 위한 차세대 데이터 구조, 검색 파이프라인 및 플랫폼 전환 설계 전략]({% post_url 2026-10-13-enterprise-ai-data-platform-overview %})을 참고하세요. 이 글은 선택 기준·전환 로드맵·규제 매핑까지 다루는 상세판입니다.
>
> 링크 접속 확인일: 2026-10-13 (Gartner·ISO·EUR-Lex는 자동 접속을 차단하므로 검색 결과로 존재를 확인했습니다)

## 핵심 결론

AI 시대 데이터 플랫폼 전환의 핵심은 모델 선택이 아니라 **"AI가 신뢰하고 소비할 수 있는 데이터 기반"**을 만드는 일이다. 오픈 테이블 포맷 기반 레이크하우스, 메타데이터·시맨틱 레이어, 권한 인지형 하이브리드 검색, 규제 정합 거버넌스를 스트랭글러 방식으로 단계적으로 구축한다.

- **데이터 구조**: 정형·비정형을 하나의 거버넌스로 묶는 오픈 레이크하우스(Iceberg/Delta + REST 카탈로그)가 바탕이다. 그 위에 시맨틱 레이어(OSI/Apache Ossie, dbt MetricFlow)와 데이터 계약(ODCS)을 얹어 컨텍스트 레이어를 만든다. 운영계 RDBMS·MPP는 교체 대상이 아니라 역할을 나누는 대상이다.
- **검색**: 문서 질의는 하이브리드 검색(BM25+벡터)과 리랭킹으로, 수치 질의는 시맨틱 레이어 경유로 처리한다. 에이전트 접근은 MCP와 사용자 위임 권한으로 통제한다.
- **전환**: 7R 분류 → 기반 구축 → 유스케이스 단위 스트랭글러 이관 → 병행 운영 검증 → 레거시 폐기 순서로 간다. 규제는 AI 기본법, EU AI Act, 개인정보보호법, NIST AI RMF, ISO/IEC 42001을 하나의 통제 프레임으로 매핑한다.
- **메타데이터**: 기술 카탈로그(쿼리 경로의 운영 시스템)와 비즈니스 메타데이터(의미 체계)를 분리하고, 능동형 메타데이터 루프로 정책·영향 차단·품질 경보를 자동화한다.

| 구분 | 핵심 사실 | 출처 |
| --- | --- | --- |
| 문제의 크기 | 데이터관리 리더 63%가 AI용 데이터관리 관행이 없거나 불확실. "2026년까지 AI-ready 데이터 없는 AI 프로젝트 60% 폐기" 예측 | [Gartner 보도자료(2025.2.26)](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk) |
| 에이전트 리스크 | 2027년 말까지 에이전틱 AI 프로젝트 40% 이상 취소 예측 | [Gartner 보도자료(2025.6.25)](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027) |
| 시맨틱 레이어 | 2028년까지 MCP에만 의존하는 에이전틱 분석 프로젝트 60% 실패 예측 | Gartner Market Guide(2026.2, 2차 자료로 확인. 원문은 유료) |
| 레이크하우스 근거 | Armbrust 외, Lakehouse 논문 | [CIDR 2021 논문](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf) |
| 오픈 카탈로그 | Apache Polaris가 2026.2.18 ASF 최상위 프로젝트로 졸업 | [ASF Polaris 블로그](https://polaris.apache.org/blog/2026/02/19/apache-polaris-graduates-to-top-level-project/) |
| 에이전트 프로토콜 | MCP가 2025.12.9 Linux Foundation 산하 AAIF에 기증 | [Anthropic 공지](https://anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) |
| Text-to-SQL 한계 | Spider 2.0 발표 당시 최고 성공률 21.3% | [arXiv 2411.07763](https://arxiv.org/abs/2411.07763) (ICLR 2025) |
| 하이브리드 검색 효과 | 맥락 임베딩+BM25+리랭킹으로 검색 실패율 67% 감소 | [Anthropic 엔지니어링 문서](https://www.anthropic.com/engineering/contextual-retrieval) |
| 규제 일정 | AI 기본법 2026.1.22 시행. EU AI Act 고위험 의무 2027.12.2로 연기 | [AI 기본법](https://www.law.go.kr/lsInfoP.do?lsiSeq=268543), [EU AI Act](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng) |

## 1. 배경 및 현황 문제점

> **참고 링크**: [Gartner: AI-ready 데이터 부재](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk) · [Spider 2.0](https://arxiv.org/abs/2411.07763) · [NIST AI 600-1](https://doi.org/10.6028/NIST.AI.600-1) · [Anthropic: Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)

LLM의 출력 품질 상한은 **조회되는 데이터의 품질, 의미 정의, 권한 정합성**이 정한다. 모델은 기업 고유의 사실·수치·권한 맥락을 학습하지 않았기 때문이다.

이 상한은 세 가지 경로로 드러난다.

1. **의미 불일치 → 수치 오답**: "매출" 정의가 부서마다 다르면, SQL 문법이 맞아도 비즈니스적으로 틀린 답이 나온다. Spider 2.0에서 정답 SQL 함수 문서를 줘도 성능이 거의 오르지 않았다(5.68%→5.85%). 병목은 문법이 아니라 업무 의미 이해다.
2. **메타데이터 부재 → 검색 실패와 환각**: 청크가 맥락을 잃으면 검색이 실패하고, 모델은 빈 곳을 그럴듯하게 채운다(NIST AI 600-1의 Confabulation 위험).
3. **권한 모델 단절 → 정보 유출**: DW의 행·열 권한이 벡터 인덱스나 에이전트 도구로 전파되지 않으면 RAG가 우회 경로가 된다.

Gartner가 권고한 AI-ready 데이터 확보 5단계는 그대로 현황 진단 프레임으로 쓸 수 있다. ① 데이터를 유스케이스에 정렬 ② AI용 거버넌스 요건 정의 ③ 메타데이터를 수동에서 능동으로 진화 ④ 학습·실시간 피드용 파이프라인 준비 ⑤ 지속적 품질 보증.

| 영역 | 레거시 증상 | AI 관점의 결과 |
| --- | --- | --- |
| 저장 구조 | DW(정형)와 Hadoop·파일서버(비정형) 이원화, 다단계 ETL 복제 | 데이터 신선도 저하, 같은 사실의 다중 버전 |
| 의미 | BI 도구마다 메트릭 정의가 따로 있음 | LLM·BI·에이전트 간 수치 불일치 |
| 메타데이터 | 수동 문서, 계보 단절 | 근거 추적·설명 불가, 규제 대응 불가 |
| 비정형 | 문서가 권한 체계 밖, 품질 기준 없음 | RAG 품질 편차, 개인정보 혼입 |
| 운영 | 배치 중심, 사후 장애 발견 | 에이전트의 실시간 행동에 부적합 |
| 거버넌스 | 데이터 거버넌스와 AI 거버넌스 분리 | 고영향 AI 문서화·설명 의무 대응 불가 |
| 비용 | 온프레미스 고정비, 라이선스 종속 | PoC 비용 과다, 확장 경직 |
| 벤더 지속성 | 단일 벤더 주도 오픈소스 의존(Greenplum 폐쇄 소스 전환, MinIO 커뮤니티판 배포 중단) | 보안 패치·업그레이드 경로 단절 |

## 2. 목표 아키텍처

> **참고 링크**: [Lakehouse 논문(CIDR 2021)](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf) · [Apache Iceberg 사양](https://iceberg.apache.org/spec/) · [Data Mesh 원칙(Fowler)](https://martinfowler.com/articles/data-mesh-principles.html)

목표 구조는 수집부터 소비까지 7개 계층과 횡단 계층으로 나뉜다. 핵심 설계 원칙은 **질의 유형별 정답 원천의 분리**다. 수치는 시맨틱 레이어, 개념·관계·규정은 지식그래프, 서술형 근거는 벡터 인덱스가 담당하고, 이를 하나의 컨텍스트 레이어로 노출한다.

**목표 아키텍처: 7계층 + 횡단 계층**

| 계층 | 구성 | 설명 |
| --- | --- | --- |
| L1 원천 | 운영계 RDBMS·MPP, 문서·파일, 외부 API, 이벤트 스트림 | 교체하지 않고 원천으로 유지 |
| L2 수집·변환 | CDC, ELT, 문서 파싱·청킹 | 원천을 Bronze로 가져오고 정제 |
| L3 저장 | 오픈 레이크하우스(Iceberg/Delta + 카탈로그), 메달리온 | 정형·비정형을 하나의 거버넌스로 저장 |
| **L4 컨텍스트** | 시맨틱 레이어, 지식그래프, 벡터 인덱스, 용어집 | AI 답변 품질을 좌우하는 핵심 계층 |
| L5 검색·접근 | 쿼리 라우터, 하이브리드 RAG, MCP 게이트웨이 | 질의 유형별로 정답 원천에 연결 |
| L6 AI 서비스 | RAG 앱, 에이전트, BI, ML 모델 | 컨텍스트 레이어를 소비 |
| L7 소비 | 임직원·고객·업무 시스템 | 최종 사용자 권한으로 접근 |
| 횡단 | 메타데이터·거버넌스, IAM·보안, 관측·평가, FinOps | 모든 계층에 공통 적용 |

운영계 RDBMS·MPP는 교체하지 않고 원천으로 유지하며, 강조한 L4 컨텍스트 계층이 AI 답변 품질을 좌우하는 핵심이다.

### 2.1 저장 계층: 오픈 레이크하우스

> **참고 링크**: [Lakehouse 논문](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf) · [Iceberg v3 해설(Databricks)](https://www.databricks.com/blog/apache-icebergtm-v3-moving-ecosystem-towards-unification) · [Iceberg 사양](https://iceberg.apache.org/spec/) · [Delta Lake](https://delta.io/) · [메달리온 아키텍처](https://www.databricks.com/glossary/medallion-architecture)

Armbrust 외(CIDR 2021)의 논거는 이렇다. "데이터 레이크 + 다운스트림 DW"의 2계층 구조는 ETL 복잡성, 데이터 지연, ML용 직접 I/O 부재를 낳는다. 오픈 파일 위에 트랜잭션 메타데이터 계층을 두면 DW급 관리 기능(ACID, 스키마 진화, 타임 트래블)과 ML 엔진의 직접 접근을 함께 얻는다. AI 관점의 가치는 **한 벌의 데이터를 SQL·Spark·ML·벡터화 파이프라인이 복제 없이 공유**하는 데 있다.

Iceberg v3 핵심 기능은 다음과 같다.

- **삭제 벡터**: 위치 삭제 파일을 데이터 파일당 비트맵으로 대체해, CDC·개인정보 삭제 요구 같은 잦은 행 삭제 비용을 줄인다.
- **행 계보**: 행마다 고유 ID와 최종 변경 시퀀스를 부여해 증분 처리, 감사, 학습 데이터셋 재현성의 근거가 된다.
- **VARIANT 타입**: JSON 같은 반정형 데이터를 1급 타입으로 다룬다. LLM 입출력 로그 저장에 유리하다.
- **포맷 수렴**: 삭제 벡터 인코딩과 행 추적이 Delta Lake와 호환된다. 실제 락인 지점은 포맷이 아니라 **카탈로그**로 옮겨갔다.
- **주의**: v3→v2 다운그레이드가 불가하고 엔진별 지원이 고르지 않아, 업그레이드 전 엔진 호환성 검증이 필수다.

메달리온 설계는 Bronze(원천, 비정형 원본) → Silver(정제, 문서 파싱·청크) → Gold(업무 집계, 메트릭 원천, 임베딩 서빙)로 나눈다. 비정형도 **문서 테이블 + 청크 테이블 + 임베딩 테이블**로 모델링해 레이크하우스의 계보·권한을 상속시킨다.

### 2.2 벡터 데이터 구조

> **참고 링크**: [Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) · [HNSW 논문](https://arxiv.org/abs/1603.09320) · [RAG 원논문](https://arxiv.org/abs/2005.11401) · [pgvector](https://github.com/pgvector/pgvector)

| 설계 요소 | 대안 | 선택 기준·트레이드오프 |
| --- | --- | --- |
| 청킹 | 고정 길이+오버랩 / 구조 기반(조항·표) / 시맨틱 / 계층형 | 규정·계약은 조항 단위가 인용에 유리. 작은 청크는 정밀도↑ 맥락↓ |
| 맥락 보강 | 청크에 문서 요약·제목 접두(Contextual Retrieval) | 검색 실패율 35~49% 감소. 인덱싱 시 LLM 비용 발생 |
| 인덱스 | HNSW / IVF-PQ / DiskANN류 | HNSW는 재현율·지연 우수, 메모리 비용 큼. PQ는 메모리 절감, 재현율 손실 |
| 메타데이터 | 출처 ID, 버전, ACL, 유효기간, 민감도 라벨 | 권한 필터링과 근거 인용의 전제조건 |
| 임베딩 버전관리 | 모델 버전 컬럼, 재임베딩 파이프라인 | 모델 교체 시 전량 재색인 → 블루/그린 인덱스 |

### 2.3 메타데이터·거버넌스 계층

> **참고 링크**: [Apache Polaris](https://polaris.apache.org/) · [Iceberg REST 카탈로그 사양](https://iceberg.apache.org/rest-catalog-spec/) · [ODCS 데이터 계약](https://bitol-io.github.io/open-data-contract-standard/) · [OpenLineage](https://openlineage.io/)

- **카탈로그의 역할 변화**: 검색용 목록에서 **정책 집행 지점**으로 바뀌었다. Apache Polaris는 Iceberg REST 카탈로그의 벤더 중립 구현체로, 여러 엔진이 같은 진실 원천을 보게 한다.
- **능동형 메타데이터**: 사용 통계·품질 신호·계보를 자동 수집해 정책·추천·자동화를 구동한다(2.9 참조).
- **데이터 계약**: 생산자–소비자 간 스키마·품질·SLA·소유권을 기계 검증 가능한 YAML로 명세한다(ODCS, Linux Foundation 산하 Bitol 프로젝트). 데이터 메시 환경에서 "조용한 스키마 변경"을 CI/CD 게이트에서 차단한다.
- **데이터 제품**: 소유자·계약·SLA·문서를 갖춘 재사용 단위로, AI 에이전트가 "도구"로 소비하는 최소 단위다.

### 2.4 컨텍스트 레이어: 시맨틱 레이어 vs 온톨로지

> **참고 링크**: [dbt: OSI 사양 업데이트](https://www.getdbt.com/blog/the-osi-spec-updates) · [dbt MetricFlow](https://docs.getdbt.com/docs/build/about-metricflow) · [W3C OWL 2](https://www.w3.org/TR/owl2-overview/) · [W3C SHACL](https://www.w3.org/TR/shacl/) · [W3C SPARQL](https://www.w3.org/TR/sparql11-overview/) · [Neo4j](https://www.neo4j.com/)

컨텍스트 레이어는 LLM에 "무엇이 무엇을 의미하는가"를 명시해 **추론의 자유도를 줄이는 장치**다. 자유도가 줄면 오답 공간도 줄어든다.

- **시맨틱 레이어(메트릭 중심)**: 메트릭·차원·관계를 선언적으로 정의하고 SQL을 결정론적으로 생성한다. OSI는 2025년 9월 Snowflake 등이 출범한 벤더 중립 교환 사양으로, Apache Incubator에서 'Apache Ossie'로 개명했다.
- **RDF/OWL**: W3C 표준. 형식 의미론·추론·SPARQL·SHACL 검증이 강점, 모델링 난도가 단점이다.
- **LPG(Neo4j 등)**: 노드·엣지 속성 모델, GQL(ISO/IEC 39075:2024). 경로 탐색에 강하고 형식 추론은 약하다.

### 2.5 검색·에이전트 접근 계층

> **참고 링크**: [Model Context Protocol](https://modelcontextprotocol.io/) · [Agent2Agent(A2A)](https://a2a-protocol.org/) · [Anthropic: MCP 기증과 AAIF](https://anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) · [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

- 쿼리 라우터가 질의를 분류한다: 수치·집계 → 시맨틱 레이어, 다중 홉 관계 → 그래프, 서술형 → 하이브리드 RAG, 복합 → 에이전트 계획.
- **MCP**: 호스트–클라이언트–서버 구조로 도구·리소스를 표준화한다. 에이전트 간 협업 표준은 A2A다.
- **게이트웨이 원칙**: 에이전트는 서비스 계정이 아니라 **최종 사용자 위임 권한**으로 접근한다. 모든 도구 호출은 감사 로그에 남기고, 비가역 행동에는 사람 승인을 둔다.

### 2.6 데이터 메시 vs 데이터 패브릭

> **참고 링크**: [Data Mesh 원칙(Fowler)](https://martinfowler.com/articles/data-mesh-principles.html) · [Wikipedia: Data mesh](https://en.wikipedia.org/wiki/Data_mesh) · [Wikipedia: Data fabric](https://en.wikipedia.org/wiki/Data_fabric)

| 구분 | 데이터 메시 | 데이터 패브릭 |
| --- | --- | --- |
| 본질 | 사회기술적 운영모델(도메인 소유, 데이터 제품, 셀프서브, 연합 거버넌스) | 기술 아키텍처(능동형 메타데이터·지식그래프로 통합 자동화) |
| 강점 | 확장성, 도메인 책임 명확 | 중앙 통합 자동화, 이기종 연결 |
| 약점 | 도메인 역량 편차, 중복 비용 | 메타데이터 성숙도 의존, 중앙 병목 |
| AI 관점 | 데이터 제품 = 에이전트 도구 단위 | 지식그래프 = 컨텍스트 레이어 |

둘은 배타적이지 않다. **메시(조직·소유권) + 패브릭(메타데이터 자동화)**의 하이브리드가 현실적이다.

### 2.7 RDBMS·MPP와 오픈 테이블 포맷: 교체가 아닌 역할 분리

> **참고 링크**: [Iceberg 사양](https://iceberg.apache.org/spec/) · [Apache Cloudberry 이력](https://cloudberry.apache.org/docs/introduction/cloudberry-history) · [Cloudberry 인큐베이션 보고서(2025.11)](https://cloudberry.apache.org/blog/apache-cloudberry-incubation-report-202511) · [Wikipedia: Greenplum](https://en.wikipedia.org/wiki/Greenplum) · [pgvector](https://github.com/pgvector/pgvector) · [Trino](https://trino.io/) · [Apache Spark](https://spark.apache.org/)

오픈 테이블 포맷(OTF) 도입은 RDBMS의 대체가 아니라 **분석·AI 워크로드의 분리**다. 두 구조는 최적화된 지점이 정반대다.

- **RDBMS(OLTP)**: 행 저장, B-tree 인덱스, 잠금/MVCC로 작고 잦은 트랜잭션을 밀리초 단위로 처리한다. PK/FK 제약과 즉시 일관성이 엔진에 내장돼 있다.
- **OTF**: 객체 스토리지 위 Parquet 파일 + 스냅샷 메타데이터 구조다. 커밋마다 새 스냅샷을 만들고 카탈로그 포인터를 원자적으로 교체하는 낙관적 동시성 방식이다. 대량 스캔, 스토리지·컴퓨트 분리, 여러 엔진의 복제 없는 동시 접근에 강하다.
- **OTF의 약점**: 작은 쓰기가 잦으면 small file 문제와 커밋 충돌이 생기고 컴팩션 부담이 커진다. FK 같은 제약조건이 없다.

**MPP RDBMS(Greenplum 등)도 대용량 정형 분석 성능은 충분하다.** 따라서 전환 근거는 성능이 아니라 다음 네 가지다.

| 관점 | MPP RDBMS | OTF 레이크하우스 |
| --- | --- | --- |
| 데이터 소유 | 엔진 세그먼트 안에 있어 그 엔진만 효율적으로 읽음 | 개방 포맷이라 Spark·Trino·ML이 직접 읽음 |
| 확장 | 노드 추가 시 데이터 재분산, 컴퓨트·스토리지 동반 확장 | 스토리지와 컴퓨트를 따로 확장 |
| ML·AI 접근 | export(gpfdist, PXF)나 DB 내 PL/Python 필요 | 학습 파이프라인이 같은 파일을 바로 읽음 |
| 비정형 데이터 | 대상 밖 | 문서·청크·임베딩을 같은 카탈로그·권한으로 관리 |
| 장기 보관 비용 | 콜드 데이터도 고가 세그먼트 디스크 점유 | 객체 스토리지 단가 |
| 벤더 지속성 | Greenplum은 2024년 5월 폐쇄 소스 전환, 저장소 아카이브 | 재단 거버넌스(ASF) 기반 오픈 사양 |

Greenplum의 오픈소스 후계는 Apache Cloudberry(Incubating)다. PostgreSQL 14 커널 기반으로 시작해 메인 브랜치는 16.9로 올라갔고, Iceberg·Hudi 연동을 로드맵에 두고 있다.

**판단 기준과 대안**

1. **역할 분리형(기본안)**: 운영계 RDBMS는 유지하고 CDC로 레이크하우스 Bronze에 연계한다. 운영계 부하를 분석 쿼리로부터 격리하는 효과도 있다.
2. **DW만 OTF로 Replatform**: 운영 RDBMS는 Retain, 분석용 DW·마트만 전환한다.
3. **MPP 유지 + OTF 병행**: MPP는 Gold 서빙 계층, 레이크는 공유 저장 계층으로 쓴다.
4. **Hot/Cold 티어링**: 최근 데이터는 MPP, 이력은 Iceberg로 내리고 외부 테이블로 조회한다. TCO 효과가 가장 빠르다.
5. **RDBMS + AI 확장(소규모)**: PostgreSQL+pgvector처럼 기존 DB에 벡터 검색을 붙여 권한·트랜잭션 정합을 재사용한다.
6. **페더레이션**: 이관 없이 쿼리 엔진으로 원격 조회한다. 스트랭글러 초기 단계에 유용하나 대량 조인 성능과 원천 부하가 문제다.

SQL 분석이 대부분이고 AI가 소규모라면 MPP 유지가 합리적이다. ML·에이전트·비정형 비중이 커지고 여러 엔진이 같은 데이터를 써야 하면 OTF 공유 계층을 도입한다.

### 2.8 온프레미스 목표 구조와 스토리지 대안

> **참고 링크**: [Ceph](https://ceph.io/en/) · [MinIO 저장소](https://github.com/minio/minio) · [MinIO 경과(매체)](https://itsfoss.com/news/minio-moves-away-from-open-source/) · [VAST Data](https://www.vastdata.com/) · [VAST 벤치마크(벤더 자료)](https://vastdata.com/blog/the-next-generation-database-for-the-ai-era) · [Apache Polaris](https://polaris.apache.org/) · [Trino](https://trino.io/)

온프레미스에서도 기본 구조는 클라우드 레이크하우스와 같다. **S3 호환 객체 스토리지 + 오픈 테이블 포맷 + 카탈로그 + 분리된 컴퓨트(Kubernetes)**를 사내에 재현한다. 스토리지·컴퓨트 분리는 클라우드 전용 기능이 아니라, S3 API라는 표준 인터페이스로 경계를 나누는 설계 원칙이기 때문이다.

온프레미스 고유의 제약은 네 가지다. 용량·GPU를 선구매해야 하는 탄력성 부재, 분산 스토리지 운영 인력, 망분리·보안인증, 그리고 스토리지 처리량이 GPU 활용률을 좌우하는 문제다.

| 대안 | 구성 | 장점 | 주의점 |
| --- | --- | --- | --- |
| A. 오픈소스 직접 구축 | Ceph RGW + Iceberg + Polaris + Trino/Spark | 라이선스 비용 최소, 락인 최소 | 운영 난도 높음. MinIO 커뮤니티판은 바이너리 배포를 중단하고 소스만 제공하며 GitHub 저장소는 2026.4.25 아카이브(상태 변동 가능). 신규 도입에서는 제외 권고 |
| B. 엔터프라이즈 객체 스토리지 + 오픈 스택 | Dell, Pure Storage, NetApp, Hitachi, Scality 등 | 기술지원, 국내 레퍼런스·보안인증 확보 용이 | S3 호환만 지키면 스토리지 교체 가능 → 락인이 스토리지 계층에 갇힘 |
| C. 통합 AI 데이터 플랫폼 | VAST Data(스토리지·DataBase·벡터·스트리밍·InsightEngine 통합) | 올플래시 고처리량, GPU 근접, RAG 파이프라인 단순화 | 성능 근거가 벤더 자체 벤치마크. 독자 DataBase 사용 범위만큼 테이블 계층까지 락인. 높은 단가 |
| D. 패키지형 레이크하우스 | Cloudera Private Cloud, Dell Data Lakehouse 등 | 구축 기간 짧음, Hadoop 이행 조직에 익숙 | 플랫폼 수준 락인, 라이선스 비용 |
| E. 하이브리드 티어링 | GPU 옆 소량 고성능 플래시 + 저가 HDD 객체 스토리지 + 기존 MPP 서빙 | 성능과 용량 단가를 분리 최적화 | 계층 간 데이터 배치 설계 필요 |

VAST는 Iceberg 대비 TPC-DS 25% 빠르고 업데이트·삭제는 60배 빠르다고 주장한다. 다만 자체 비교 결과이므로 본문에서는 "벤더 주장"으로 표기하고 PoC 검증 조건을 붙인다. InsightEngine은 데이터 유입 시 자동 벡터화해 내장 벡터 DB에 저장하며, 벤더 설명상 벡터 행 권한을 원본 권한과 연동한다.

**VAST 도입 여부를 가르는 질문**

1. 병목이 정말 스토리지 처리량인가? GPU가 노는 원인이 데이터 준비·품질이라면 고가 스토리지로 해결되지 않는다.
2. VAST 고유 기능(DataBase, InsightEngine)을 어디까지 쓸 것인가? 쓰는 범위가 곧 락인 범위다. 원천·Gold는 Iceberg로 유지하고 AI 서빙·벡터 영역에 한정하는 경계를 둔다.
3. 같은 예산의 B+E 조합과 동일 워크로드(Iceberg 쿼리, 임베딩 처리량, 동시성)로 TCO를 비교했는가?

민감 원천은 온프렘에 두고 비식별화된 학습 데이터나 대량 임베딩만 클라우드 GPU로 보내는 **"온프렘 저장 + 클라우드 버스트"**도 대안이다. 단, 개인정보·망분리 규정 검토가 선행돼야 한다.

### 2.9 능동형 메타데이터

> **참고 링크**: [Alation: 능동형 메타데이터(벤더 자료)](https://www.alation.com/blog/active-metadata) · [OpenLineage](https://openlineage.io/) · [OpenMetadata](https://open-metadata.org/) · [DataHub](https://datahub.com/) · [Open Policy Agent](https://www.openpolicyagent.org/) · [ODCS](https://bitol-io.github.io/open-data-contract-standard/) · [Collibra](https://www.collibra.com/) · [Atlan](https://atlan.com/) · [Informatica](https://www.informatica.com/)

능동형 메타데이터는 **기록만 하던 메타데이터를 계속 분석해, 사람이나 시스템의 행동을 자동으로 일으키는 관리 방식**이다. Gartner는 2021년 8월 메타데이터 관리 Magic Quadrant를 없애고 능동형 메타데이터 Market Guide로 대체하며, 기존 관행이 "불충분하다"고 진단했다.

기존 방식이 실패한 이유는 세 가지다. 수동 입력에 의존해 금방 낡고, 조회만 가능해 문제가 생겨도 행동하지 않으며, 설계된 데이터와 실제 사용 데이터가 따로 논다.

| 구분 | 수동형 | 능동형 |
| --- | --- | --- |
| 수집 | 정기 크롤링, 수동 입력 | 이벤트 기반 실시간 수집 |
| 내용 | 기술 메타데이터 위주 | 기술 + 운영 + 사용 + 비즈니스 + 사회적 |
| 저장 | 목록형 저장소 | 자산·사람·파이프라인·정책을 잇는 그래프 |
| 분석 | 없음 | 규칙·통계·ML로 패턴 탐지, 추천 |
| 출력 | 사람이 검색 | 알림, 추천, 자동 실행, 다른 도구로 역주입 |
| 방향 | 단방향 | 양방향 |

**작동 루프는 수집 → 연결 → 분석 → 행동이며, 행동 결과가 다시 메타데이터로 돌아온다.**

1. **수집**: 각 시스템이 이벤트를 내보낸다. 계보 표준은 OpenLineage다. 쿼리 로그, dbt 실행, 품질 검사, 접근 로그도 같은 방식으로 들어온다.
2. **연결**: "테이블 → 대시보드 → 보고서 → 소유자" 관계를 그래프로 엮는다. 영향 분석의 전제다.
3. **분석**: 개인정보 패턴 탐지, 장기 미사용 테이블 식별, 적재량 이상 징후, 인증 테이블 추천 등을 수행한다.
4. **행동**: 분석 결과로 실제 동작을 일으킨다. 이것이 "능동"의 본질이다.

**대표 시나리오**

- **개인정보 태그 전파 → 자동 마스킹**: 전화번호 패턴 탐지 → PII 태그 부여 → 계보 따라 Silver·Gold·벡터 청크까지 전파 → 태그 기반 ABAC로 마스킹 → RAG 색인 전 가명처리.
- **스키마 변경 영향 차단**: PR 시 하위 영향(대시보드, 메트릭, 데이터 계약) 조회 → 계약 위반이면 CI 차단 → 소비자 자동 알림.
- **품질 사고 전파 차단**: 적재 건수 급감 탐지 → 하위 대시보드에 경고 배지 → 에이전트가 해당 테이블 근거 답변 시 경고 또는 보류.
- **비용 최적화**: 장기 미사용 테이블을 콜드 스토리지로 이동하거나 7R의 Retire 후보로 지정.

**AI 시대에 더 중요해진 이유**: 에이전트는 메타데이터(설명, 인증, 신선도, 품질 점수)만 보고 데이터를 고른다. 컨텍스트 레이어의 연료도 메타데이터다. AI 기본법 제34조의 "학습용 데이터 개요" 설명도 계보가 자동으로 쌓여야 대응할 수 있다. Gartner는 메타데이터 분석을 적극 도입하면 새 데이터 자산 전달 시간을 최대 70% 줄일 수 있다고 예측했다.

**구현 대안**

- **오픈소스**: OpenMetadata, DataHub + OpenLineage + 품질 도구. 락인 없음, 통합·운영 부담.
- **상용**: Collibra, Alation, Informatica, Atlan 등. 용어집·워크플로 강점, 비용.
- **플랫폼 내장**: Unity Catalog, Snowflake Horizon. 자동 집행이 매끄럽지만 플랫폼 밖 자산이 사각지대.
- **조합형(권고)**: 기술 카탈로그 + 이벤트 계보 + 비즈니스 메타데이터 플랫폼 + 정책 엔진(OPA 등)을 이벤트 버스로 연결.
- **발전 방향**: 카탈로그를 MCP 서버로 노출해 에이전트가 쿼리 전에 신뢰도를 조회하고, 에이전트 사용 기록을 다시 메타데이터로 되먹이는 루프.

| 리스크 | 대응 |
| --- | --- |
| 알림 피로 | 하위 영향도 기반 우선순위, 단계적 임계치 |
| 자동 분류 오탐·미탐 | 고위험 태그는 사람 승인, 탐지 정확도 지표 관리 |
| 자동화 폭주 | 삭제·폐기 같은 비가역 행동은 승인 후 실행, 드라이런 |
| 메타데이터 자체의 민감성 | 쿼리 로그에도 접근통제·보존기간 적용 |
| 커버리지 공백 | 커넥터 우선순위화, 커버리지율을 KPI로 관리 |
| 양방향 동기화 충돌 | 속성별 진실의 원천(SoT) 지정 |

**성숙도**: L1 수동(엑셀·위키) → L2 자동 수집 → L3 연결·관측(이벤트 계보, 품질 연동) → L4 자동 행동(정책 집행, CI 차단) → L5 자율·AI 연계(에이전트 사용 피드백이 메타데이터를 개선).

### 2.10 기술 카탈로그 vs 비즈니스 메타데이터 플랫폼

> **참고 링크**: [Iceberg REST 카탈로그 사양](https://iceberg.apache.org/rest-catalog-spec/) · [Apache Polaris](https://polaris.apache.org/) · [Unity Catalog](https://www.unitycatalog.io/) · [OpenMetadata](https://open-metadata.org/) · [DataHub](https://datahub.com/) · [PostgreSQL 시스템 카탈로그](https://www.postgresql.org/docs/current/catalogs.html)

기술 카탈로그는 **엔진이 데이터를 읽고 쓰기 위해 반드시 거치는 운영 시스템**이다. 비즈니스 메타데이터 플랫폼은 **사람과 AI가 데이터의 의미를 이해하기 위한 지식 시스템**이다. 이름은 둘 다 카탈로그지만 성격은 정반대다.

| 관점 | 기술 카탈로그 (Polaris, Unity Catalog 등) | 비즈니스 메타데이터 플랫폼 (OpenMetadata, DataHub, Collibra 등) |
| --- | --- | --- |
| 핵심 질문 | 현재 버전 파일은 어디 있고 누가 읽고 쓸 수 있나 | 무슨 의미이고, 믿어도 되며, 누구에게 묻나 |
| 주 사용자 | 쿼리 엔진, 파이프라인 | 분석가, 현업, 데이터 오너, 거버넌스, AI 에이전트 |
| 담는 내용 | 네임스페이스, 스키마, 스냅샷 포인터, 파티션, 스토리지 위치, 권한, 자격증명 발급 | 용어집, 설명, 소유자·스튜어드, 분류, 품질, 인증, 사용 통계, 계보 시각화 |
| 쿼리 경로 | 모든 읽기·쓰기의 필수 경유지 | 쿼리 경로 밖 |
| 일관성 | 강한 일관성(포인터 원자적 교체) | 최종적 일관성으로 충분 |
| 장애 영향 | 쿼리·적재 중단 | 탐색·검색만 불편 |
| 변경 빈도 | 초~분 단위 커밋 | 월 단위 수준 |
| 데이터 모델 | 카탈로그 > 네임스페이스 > 테이블 계층 | 자산·용어·사람·정책 그래프 |
| 표준 | Iceberg REST Catalog 사양 | 부분 표준 조합(OpenLineage, ODCS 등) |

**분리하는 이유**

1. **일관성 요구가 다르다**: 커밋 포인터 교체는 DB급 운영이 필요하지만 설명 수정은 몇 분 늦어도 된다. 한 시스템에 넣으면 덜 중요한 기능까지 최고 운영 기준을 따라가야 한다.
2. **장애 격리**: 비즈니스 플랫폼의 크롤링·검색 부하가 운영 쿼리에 영향을 주면 안 된다.
3. **소유자가 다르다**: 기술 카탈로그는 플랫폼팀, 비즈니스 메타데이터는 도메인 오너·거버넌스 조직이 소유한다.
4. **교체 가능성**: 둘을 분리해야 각자 독립적으로 교체할 수 있다.

**겹치는 속성의 배치 원칙은 "작성은 의미를 아는 쪽, 집행은 쿼리 경로에 있는 쪽"이다.**

| 속성 | 작성·관리 | 집행·저장 |
| --- | --- | --- |
| 스키마 | 기술 카탈로그 | 기술 카탈로그 |
| 컬럼 설명 | 비즈니스 플랫폼 | 기술 카탈로그 comment로 역동기화 |
| 분류 태그(PII) | 비즈니스 플랫폼(자동 탐지 + 승인) | 기술 카탈로그·정책 엔진 |
| 접근 정책 | 정책 엔진에 코드로 정의 | 기술 카탈로그·쿼리 엔진 |
| 계보 | OpenLineage 이벤트 | 비즈니스 플랫폼 그래프 |
| 소유자·용어 | 비즈니스 플랫폼 | 비즈니스 플랫폼 |
| 품질·신선도 | 품질 도구 산출 | 비즈니스 플랫폼 표시 + 에이전트 노출 |

**"월매출" 질의 흐름**: 에이전트가 비즈니스 플랫폼에서 인증된 용어 정의를 확인한다. 시맨틱 레이어 메트릭으로 질의를 만들고, 기술 카탈로그가 현재 스냅샷과 권한을 확인한 뒤 엔진이 실행한다. 답변에는 "재무팀 인증 정의, 어제 적재분" 같은 근거를 붙인다.

**구성 대안**: A. 2계층 분리형(기본안) / B. 플랫폼 통합형(Unity Catalog 등, 플랫폼 밖 자산이 사각지대) / C. 비즈니스 플랫폼 중심형(여러 메타스토어 수집, 전환 초기에 현실적) / D. 단계적 수렴형(C로 가시화 후 A로 이행).

**흔한 실패**: 설명 이중 관리로 인한 불일치, 비즈니스 플랫폼을 권한 시스템으로 착각(집행은 반드시 쿼리 경로에서), 기술 카탈로그에 업무 정보 과적재, 용어집이 실제 테이블과 연결되지 않음.

**PostgreSQL의 경우**: 기술 메타데이터 원본은 `pg_catalog`(`pg_class`, `pg_attribute` 등)이고, `information_schema`는 이를 SQL 표준으로 보여주는 뷰다. `pg_stats`는 운영 메타데이터, `COMMENT ON`은 초보적 비즈니스 메타데이터다. 결정적 차이는 PostgreSQL 카탈로그가 **엔진 내부에 갇혀 자신만 쓴다**는 점이다. 레이크하우스는 DB 엔진 안의 카탈로그를 밖으로 꺼내 여러 엔진의 공용으로 만든 구조다.

## 3. 핵심 기술 비교와 선택 기준

> **참고 링크**: [Apache Iceberg](https://iceberg.apache.org/) · [Delta Lake](https://delta.io/) · [Apache Hudi](https://hudi.apache.org/)

포맷 간 기능이 수렴하고 있어서, **포맷 선택보다 카탈로그 개방성과 거버넌스 일원화**가 더 중요한 의사결정 변수다.

### 3.1 오픈 테이블 포맷

> **참고 링크**: [Iceberg 사양](https://iceberg.apache.org/spec/) · [Delta Lake](https://delta.io/) · [Apache Hudi](https://hudi.apache.org/)

| 항목 | Apache Iceberg | Delta Lake | Apache Hudi |
| --- | --- | --- | --- |
| 출발점 | 대규모 분석 테이블 정확성(Netflix) | Spark 중심 ACID(Databricks) | 스트리밍 upsert·증분(Uber) |
| 강점 | 엔진 중립성, 숨은 파티셔닝, REST 카탈로그 생태계 | Spark 통합, 성숙한 운영 기능 | CDC·레코드 인덱스, MoR/CoW 선택 |
| 약점 | 엔진별 v3 지원 편차 | 타 엔진 쓰기 생태계가 좁음 | 운영 복잡도 |
| 선택 기준 | 멀티엔진·락인 회피 | Databricks 중심 | 고빈도 upsert 스트리밍 |

### 3.2 카탈로그

> **참고 링크**: [Unity Catalog](https://www.unitycatalog.io/) · [Apache Polaris](https://polaris.apache.org/) · [OpenMetadata](https://open-metadata.org/) · [DataHub](https://datahub.com/)

| 항목 | Unity Catalog(OSS) | Apache Polaris | OpenMetadata / DataHub |
| --- | --- | --- | --- |
| 성격 | 데이터+AI 자산 통합 거버넌스 | Iceberg REST 기술 카탈로그 | 비즈니스 메타데이터 플랫폼 |
| 강점 | 테이블·파일·모델·함수 일원 관리 | 벤더 중립, 자격증명 발급, 페더레이션 | 용어집, 계보, 협업, 커넥터 폭 |
| 한계 | 생태계가 Databricks 중심 | 비즈니스 메타데이터는 별도 필요 | 쿼리 경로의 접근통제 집행은 제한적 |

### 3.3 벡터 저장소

> **참고 링크**: [pgvector](https://github.com/pgvector/pgvector) · [HNSW 논문](https://arxiv.org/abs/1603.09320) · [Wikipedia: Vector database](https://en.wikipedia.org/wiki/Vector_database)

| 기준 | 전용 벡터 DB | RDBMS·검색엔진 확장 | 레이크하우스 내장 |
| --- | --- | --- | --- |
| 성능·규모 | 대규모·저지연 최적 | 중규모까지 충분 | 배치·분석형 |
| 하이브리드 검색 | 제품별 상이 | 검색엔진 계열은 BM25 내장 | 제한적 |
| 권한 | 별도 구현(이중 관리) | 기존 RLS·ACL 재사용 | 카탈로그 권한 상속 |
| 정합성 | 원천과 비동기 | 원천과 같은 트랜잭션 가능 | 테이블 버전과 연동 |
| 적합 | 수억 벡터, 초저지연 SLA | 대부분 엔터프라이즈 초기 단계 | 분석·추천 배치 |

초기에는 기존 엔진 확장으로 시작하고, 규모·지연 요건이 입증되면 분리하는 점진 전략이 리스크가 낮다.

### 3.4 RAG 아키텍처 진화

> **참고 링크**: [RAG 원논문](https://arxiv.org/abs/2005.11401) · [GraphRAG 논문](https://arxiv.org/abs/2404.16130) · [GraphRAG 문서](https://microsoft.github.io/graphrag/) · [AgenticRAG(arXiv 2605.05538)](https://arxiv.org/abs/2605.05538) · [Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) · [HyDE 논문](https://arxiv.org/abs/2212.10496)

| 단계 | 구조 | 해결하는 문제 | 한계 |
| --- | --- | --- | --- |
| Naive RAG | 청킹→임베딩→top-k→생성 | 파라메트릭 지식 한계 | 맥락 손실, 키워드 약함 |
| Advanced RAG | 쿼리 재작성, 하이브리드, 리랭킹, 맥락 보강 | 정밀도·재현율 | 튜닝 비용 |
| Modular RAG | 검색·라우팅·검증 모듈 조합 | 질의 유형별 최적 경로 | 설계 복잡도 |
| Agentic RAG | 계획·반성·도구 사용으로 반복 검색 | 다중 홉·모호 질의 | 지연·토큰 비용, 연쇄 환각 |
| GraphRAG | 엔티티 그래프 + 커뮤니티 요약 | 전역 요약 질의 | 인덱싱·갱신 비용 |

근거 수치: 맥락 임베딩+BM25+리랭킹으로 top-20 검색 실패율 5.7%→1.9%(-67%, Anthropic). 엔터프라이즈 지식베이스 실험에서는 단발 검색에서 에이전트형 도구 사용으로 전환한 것이 가장 큰 개선 요인이었고(약 5.9배), BRIGHT recall@1이 49.6%에 도달했다([arXiv 2605.05538](https://arxiv.org/abs/2605.05538)). 질의 분포를 먼저 계측해 라우팅하고, GraphRAG는 전역 요약 질의가 반복되는 고정 코퍼스에서만 비용 대비 효과가 난다.

### 3.5 수치 질의: Text-to-SQL vs 시맨틱 레이어

> **참고 링크**: [Spider 2.0 논문](https://arxiv.org/abs/2411.07763) · [Spider 2.0 리더보드](https://spider2-sql.github.io/) · [DevRev NL2SQL(arXiv 2609.04641)](https://arxiv.org/abs/2609.04641) · [dbt MetricFlow](https://docs.getdbt.com/docs/build/about-metricflow)

| 기준 | 자유형 Text-to-SQL | 시맨틱 레이어 경유 |
| --- | --- | --- |
| 유연성 | 높음 | 정의된 메트릭·차원 범위 |
| 정확성 | 스키마 복잡도에 취약 | 결정론적 정의로 일관성 높음 |
| 거버넌스 | 생성 SQL별 통제 어려움 | 권한·행 필터 일원화 |
| 설명가능성 | SQL 검토 필요 | 사용한 메트릭 정의 제시 가능 |

Spider 2.0은 발표 당시 최고 성공률이 21.3%였고, 이후 점수는 [리더보드](https://spider2-sql.github.io/)에서 확인할 수 있다. 중첩 스키마가 많은 엔터프라이즈 벤치마크(DevRev NL2SQL)에서는 기존 최고 베이스라인이 약 37%에 그쳤다(제안 시스템은 91.7%, [arXiv 2609.04641](https://arxiv.org/abs/2609.04641)). 핵심 KPI는 시맨틱 레이어 경유를 강제하고, 탐색 질의만 Text-to-SQL을 허용하되 실행 검증과 비용 상한을 둔다.

### 3.6 권한 인지형 검색

> **참고 링크**: [Open Policy Agent](https://www.openpolicyagent.org/) · [pgvector(RLS 재사용)](https://github.com/pgvector/pgvector) · [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) · [Wikipedia: ABAC](https://en.wikipedia.org/wiki/Attribute-based_access_control) · [Wikipedia: ReBAC](https://en.wikipedia.org/wiki/Relationship-based_access_control)

| 방식 | 장점 | 단점 |
| --- | --- | --- |
| 사전 필터(ACL을 검색 조건에 포함) | 유출 원천 차단 | 필터 선택도 높으면 ANN 재현율 저하 |
| 사후 필터(top-k 후 제거) | 구현 단순 | 결과 부족, 정보 노출 가능 |
| 인덱스 분할(권한 그룹별) | 격리 우수 | 그룹 폭증 시 관리 불가 |
| 원천 위임 조회 | 최신 권한 반영 | 지연 증가 |

권고는 메타데이터 ACL 사전 필터 + 생성 직전 재검증의 이중 통제, 권한 변경의 인덱스 반영 SLA, 에이전트의 사용자 위임 토큰이다.

### 3.7 평가 지표

> **참고 링크**: [RAGAS 논문](https://arxiv.org/html/2309.15217v1) · [RAGAS 저장소](https://github.com/explodinggradients/ragas)

| 계층 | 지표 |
| --- | --- |
| 검색 | Context Precision·Recall, Recall@k, nDCG |
| 생성 | Faithfulness(근거성), Answer Relevance |
| 수치 질의 | 실행 정확도(EX), 메트릭 일치율 |
| 위험 | 환각률, 권한 위반 건수, 프롬프트 인젝션 차단률 |
| 운영 | p95 지연, 질의당 비용, 에이전트 작업 성공률 |

RAGAS의 인간 판단 일치도는 faithfulness 0.95, answer relevance 0.78, context relevance 0.70이다. 근거성 자동 평가는 비교적 믿을 만하지만, 맥락 관련성은 사람의 표본 검수로 보완해야 한다.

## 4. 단계별 전환 로드맵

> **참고 링크**: [AWS: 마이그레이션 전략(7R)](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html) · [Strangler Fig(Fowler)](https://martinfowler.com/bliki/StranglerFigApplication.html)

권고 조합은 **Replatform 다수 + 선택적 Re-architect**이고, 실행은 빅뱅이 아닌 스트랭글러 방식이다. 사용 로그로 미사용 자산을 Retire하면 이관 범위 자체가 줄어든다.

### 4.1 7R 전략

> **참고 링크**: [AWS: 마이그레이션 전략(7R)](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html)

AWS Prescriptive Guidance는 Gartner 5R을 7R로 확장했다. Refactor는 가장 복잡하고 비용이 커서 대규모 이관에는 권장하지 않는다.

| 전략 | DW·Hadoop 적용 예 | 적합 대상 |
| --- | --- | --- |
| Rehost | Hadoop 클러스터를 VM으로 이전 | 폐기 예정 워크로드 |
| Replatform | Hive 테이블을 Iceberg로 변환, 관리형 엔진 사용 | 대다수 배치 ETL, 분석용 DW·마트 |
| Refactor | 데이터 제품·시맨틱 레이어 중심 재설계 | 핵심 KPI, AI 우선 유스케이스 |
| Repurchase | 레거시 BI·ETL을 SaaS로 교체 | 범용 기능 |
| Relocate | 하이퍼바이저 수준에서 인프라를 코드 변경 없이 이전 | 온프렘 가상화 환경을 그대로 옮길 때. 레이크하우스 전환과는 직접 관련이 적음 |
| Retire | 미사용 마트·리포트 폐기 | 사용 로그상 미사용 자산 |
| Retain | 운영계 RDBMS, 규제상 온프렘 유지 | OLTP, 규제 대상 워크로드 |

### 4.2 스트랭글러 + 병행 운영

> **참고 링크**: [Strangler Fig 패턴(Fowler)](https://martinfowler.com/bliki/StranglerFigApplication.html) · [AWS: 마이그레이션 전략](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html)

신규 플랫폼을 레거시 옆에 세우고, 라우팅 계층(가상화·시맨틱 레이어·API)으로 유스케이스 단위 트래픽을 점진 이전한 뒤 레거시를 폐기한다. 빅뱅의 위험을 작은 단위로 나누고 단계마다 롤백 가능성을 유지하기 때문이다.

- **병행 검증 항목**: 행 수·합계·해시 대사, 핵심 KPI 일치율(허용 오차 사전 정의), 적재 완료 시각, 쿼리 성능 회귀, 권한 동등성.
- **컷오버 기준**: "N회 연속 배치에서 KPI 차이 0, 성능 회귀 없음, 사용자 수용 테스트 통과" 같은 정량 기준을 사전 합의한다.

### 4.3 단계별 로드맵(18~24개월 예시)

> **참고 링크**: [AWS: 마이그레이션 전략(7R)](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html) · [FinOps Foundation](https://www.finops.org/)

| 단계 | 기간 | 주요 활동 | 게이트 |
| --- | --- | --- | --- |
| 0. 진단·전략 | 0~3개월 | 자산 인벤토리, 사용 로그 분석, 7R 분류, 유스케이스 우선순위, 고영향 AI 해당 여부 | 경영진 승인 |
| 1. 기반 구축 | 3~9개월 | 레이크하우스·카탈로그·IAM/ABAC, DataOps, 계보 수집, 계약 표준, 1~2개 도메인 Replatform | 보안·성능 기준 통과 |
| 2. 컨텍스트·AI 파일럿 | 6~12개월 | 핵심 KPI 시맨틱 레이어화, 하이브리드 검색, 평가 하네스, 읽기 전용 MCP 게이트웨이 | 근거성·정확도 목표 달성 |
| 3. 확산·이관 | 12~20개월 | 도메인별 이관과 병행 검증, 데이터 제품 카탈로그, 승인 기반 쓰기 에이전트, LLMOps | 컷오버 기준 충족 |
| 4. 폐기·최적화 | 18~24개월 | 레거시 폐기, FinOps, 성숙도 재평가 | KPI 목표 달성 |

### 4.4 성숙도 모델

> **참고 링크**: [ISO/IEC 42001](https://www.iso.org/standard/42001) · [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)

| 레벨 | 데이터 | 검색·AI | 거버넌스 |
| --- | --- | --- | --- |
| L1 초기 | 사일로, 수동 ETL | 키워드 검색 | 문서 기반 정책 |
| L2 관리 | 중앙 DW·레이크 | 단순 RAG PoC | 카탈로그 도입 |
| L3 정의 | 오픈 레이크하우스, 메달리온 | 하이브리드 검색 + 평가 | 계약·계보 자동화 |
| L4 정량 관리 | 데이터 제품, 시맨틱 레이어 | 라우팅, 시맨틱 질의, KPI 계측 | ABAC, AI 위험 평가 정례화 |
| L5 최적화 | 능동형 메타데이터로 자동화 | 권한 인지형 에이전트 운영 | ISO/IEC 42001 인증 수준 |

### 4.5 운영모델

> **참고 링크**: [OpenLineage](https://openlineage.io/) · [Great Expectations](https://greatexpectations.io/) · [RAGAS](https://github.com/explodinggradients/ragas) · [Wikipedia: MLOps](https://en.wikipedia.org/wiki/MLOps) · [FinOps Foundation](https://www.finops.org/)

- **DataOps**: 파이프라인 코드화, 계약·품질 테스트의 CI 편입, 신선도·볼륨·스키마 드리프트 관측.
- **MLOps**: 피처·모델 레지스트리, 테이블 스냅샷 기반 재현성, 드리프트 모니터링.
- **LLMOps**: 프롬프트 버전관리, 골든셋 회귀 테스트, 임베딩 재색인, 가드레일(PII 마스킹), 토큰 비용 관측, 레드팀.
- **조직**: 플랫폼 팀 + 도메인 데이터 제품 팀 + 연합 거버넌스 위원회(데이터·AI·법무·보안) + AI 엔지니어링 CoE.

## 5. 리스크와 거버넌스

> **참고 링크**: [AI 기본법(국가법령정보센터)](https://www.law.go.kr/lsInfoP.do?lsiSeq=268543) · [EU AI Act(EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng) · [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) · [ISO/IEC 42001](https://www.iso.org/standard/42001)

규제마다 별도 체계를 만들지 않고 **"통제 목표 1개 → 다수 규제 매핑"**의 공통 통제 프레임을 카탈로그 메타데이터로 구현한다(Compliance by Design). 예를 들어 데이터 출처 기록 하나가 AI 기본법 제34조 설명 방안, EU AI Act 데이터 거버넌스, NIST MAP, ISO 42001 데이터 관리를 함께 충족한다.

### 5.1 규제 매핑(2026년 10월 기준)

> **참고 링크**: [AI 기본법](https://www.law.go.kr/lsInfoP.do?lsiSeq=268543) · [PIPC 생성형 AI 안내서](https://pipc.go.kr/np/cop/bbs/selectBoardArticle.do?bbsId=BS074&mCode=C020010000&nttId=11410) · [개인정보보호위원회](https://www.pipc.go.kr) · [EU AI Act 개요(EC)](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai) · [EU AI Act 원문(EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng) · [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) · [NIST AI 600-1](https://doi.org/10.6028/NIST.AI.600-1) · [ISO/IEC 42001](https://www.iso.org/standard/42001)

| 규제·표준 | 핵심 내용 | 플랫폼 설계 반영 |
| --- | --- | --- |
| 한국 AI 기본법 | 2026.1.22 시행. 고영향 AI 영역(보건의료, 채용·대출 심사, 공공 의사결정 등) | 유스케이스별 고영향 해당 여부 사전 검토 기록(제33조) |
| ― 투명성(제31조) | 생성형·고영향 AI 사실 고지, 생성물 표시 | 출력 메타데이터에 생성 표시, 고지 UI |
| ― 사업자 책무(제34조) | 위험관리, 설명 방안(학습용 데이터 개요 포함), 사람의 감독, 문서 보관 | 계보·데이터 카드·평가 리포트가 법적 증빙 |
| ― 제재(제43조) | 과태료 최대 3천만원. 계도기간 "최소 1년 이상" | 계도기간은 집행 방침이지 법적 유예가 아님 |
| 개인정보보호법·데이터 3법 | 가명정보(통계·연구·공익 기록보존), 자동화된 결정에 대한 거부·설명 요구권 | 가명처리 파이프라인, 결합키 관리, 재식별 위험 평가, 설명 로그 |
| PIPC 생성형 AI 안내서(2025.8) | 생애주기 4단계별 안전조치, 유형별 기준 | 외부 LLM 전송 데이터 관리, 학습 데이터 가명·익명화 |
| EU AI Act | Annex III 고위험 의무 2027.12.2, Annex I 2028.8.2로 연기. 제50조 투명성은 2026.8.2 적용 대상(유예·개정 여부는 EUR-Lex 통합본으로 확인) | EU 사업 노출 시 데이터 거버넌스·로그·인간 감독 선반영 |
| NIST AI RMF / AI 600-1 | GOVERN·MAP·MEASURE·MANAGE, 생성형 AI 위험 12개 | 위험 레지스터 분류 체계, 환각률·근거성을 MEASURE에 매핑 |
| ISO/IEC 42001:2023 | AI 경영시스템(AIMS) 요구사항 | 데이터·AI 거버넌스를 PDCA로 통합, 인증으로 대외 신뢰 확보 |

### 5.2 리스크 레지스터

> **참고 링크**: [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) · [NIST AI 600-1](https://doi.org/10.6028/NIST.AI.600-1) · [MinIO 경과(매체)](https://itsfoss.com/news/minio-moves-away-from-open-source/) · [Apache Cloudberry](https://cloudberry.apache.org/)

| 리스크 | 원인 | 대응 |
| --- | --- | --- |
| 환각·오답 | 검색 실패, 의미 불일치 | 하이브리드+리랭킹, 시맨틱 레이어 강제, 근거 인용 필수, 근거성 미달 시 답변 거부 |
| 권한 우회·유출 | 인덱스에 ACL 미전파, 서비스 계정 남용 | 사전 필터+재검증, 위임 토큰, 감사 로그, DLP |
| 프롬프트 인젝션·도구 오남용 | 외부 문서·도구 출력의 악성 지시 | 최소 권한, 읽기·쓰기 분리, 비가역 행동 승인, MCP 서버 화이트리스트 |
| 에이전트 비용 폭증 | 반복 검색·재시도 루프 | 단계·토큰 상한, 캐싱, FinOps 관측 |
| 이관 정합성 붕괴 | 변환 로직 차이, 시간대·NULL 처리 | 병행 대사, 컷오버 기준, 롤백 계획 |
| 표준 미성숙·락인 | OSI·Iceberg v3·MCP 사양 변동 | 추상화 계층, 오픈 사양 우선, 엔진 호환성 매트릭스 |
| 오픈소스 벤더 지속성 | 단일 벤더 주도 프로젝트의 폐쇄·중단(Greenplum 2024, MinIO 2025~2026) | 재단(ASF·LF) 거버넌스 프로젝트 우선, 포크·대체재 전환 계획 |
| 메타데이터 자동화 폭주 | 잘못된 규칙의 대량 마스킹·폐기 | 비가역 자동화는 승인 후 실행, 드라이런 |
| 조직 저항·역량 부족 | 도메인 팀 역량 편차 | 셀프서브 플랫폼, CoE, 단계별 성과 공유 |
| 규제 변경 | 시행령·고시·EU 일정 변동 | 규제 모니터링, 공통 통제 프레임으로 흡수 |

## 6. 기대효과와 KPI

> **참고 링크**: [FinOps Foundation](https://www.finops.org/) · [Gartner: AI-ready 데이터](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk) · [RAGAS](https://github.com/explodinggradients/ragas)

기대효과는 네 가지다. 단일 메트릭 정의로 BI·AI·에이전트 수치가 일치하고, 데이터 제품·계약으로 신규 유스케이스 준비 기간이 줄며, 계보·문서화가 고영향 AI 책무 증빙으로 자동 생성되고, 고정비가 사용량 기반 변동비로 바뀐다.

| 관점 | KPI | 측정 방법 |
| --- | --- | --- |
| 데이터 기반 | AI-ready 데이터셋 비율(계약·품질·계보·소유자 보유) | 카탈로그 메타데이터 충족률 |
| 데이터 기반 | 신선도 SLA 준수율, 품질 테스트 통과율 | DataOps 관측성 |
| 메타데이터 | 커버리지율, 용어–메트릭–테이블 연결률, 자동 분류 정확도 | 비즈니스 메타데이터 플랫폼 |
| 검색·AI 품질 | 근거성, Recall@k, 메트릭 일치율, 환각률 | 골든셋 정기 평가 |
| 보안 | 권한 위반 0건, 인젝션 차단률 | 감사·레드팀 |
| 전환 진척 | 이관 완료율, 폐기 자산 수, 병행 대사 일치율 | 이관 대시보드 |
| 경제성 | 3~5년 TCO, 질의당 비용, 스토리지 중복률 | FinOps |
| 비즈니스 | 분석 리드타임, 셀프서브 사용자 수, 업무시간 절감 | 사용 로그·업무 측정 |
| 거버넌스 | 고영향 AI 사전검토 완료율, 문서화 충족률 | 컴플라이언스 레지스터 |

TCO에는 이관 인건비, 병행 운영 기간의 이중 비용, 재교육, LLM 토큰·재색인 비용, 레거시 폐기 절감을 모두 넣는다. 병행 기간이 길어지면 이중 비용이 ROI를 잠식하므로, **컷오버 기준의 정량화가 TCO 관리의 핵심 레버**다.

## 7. 핵심 용어 정의

| 용어 (Wikipedia) | 정의 |
| --- | --- |
| AI-ready 데이터 | 특정 AI 유스케이스에 정렬되고 품질·거버넌스·메타데이터 요건이 지속 보증되는 데이터(Gartner) |
| [레이크하우스](https://en.wikipedia.org/wiki/Data_lakehouse) | 오픈 파일 위 트랜잭션 메타데이터 계층으로 DW급 관리와 ML 직접 접근을 함께 제공하는 아키텍처 |
| [오픈 테이블 포맷](https://en.wikipedia.org/wiki/Apache_Iceberg) | 파일 위 스냅샷·매니페스트로 ACID, 스키마 진화, 타임 트래블을 구현하는 사양(Iceberg·Delta·Hudi) |
| [MPP](https://en.wikipedia.org/wiki/Massively_parallel_%28computing%29) | 데이터를 세그먼트에 분산하고 병렬 처리하는 shared-nothing DB 구조(Greenplum, Cloudberry) |
| 삭제 벡터 | 파일별 삭제 행 위치를 비트맵으로 기록해 재작성 없이 삭제를 반영하는 기법 |
| [행 계보](https://en.wikipedia.org/wiki/Data_lineage) | 행 단위 ID와 변경 시퀀스로 증분 처리·감사를 지원하는 메타데이터 |
| [REST 카탈로그](https://en.wikipedia.org/wiki/Representational_state_transfer) | 엔진 중립 HTTP API로 테이블 커밋·조회를 표준화한 Iceberg 카탈로그 사양 |
| 자격증명 발급 | 카탈로그가 테이블 단위 단기 스토리지 자격증명을 엔진에 발급하는 접근통제 방식 |
| [기술 카탈로그](https://en.wikipedia.org/wiki/Data_catalog) | 스키마·스냅샷 포인터·권한을 관리하는 쿼리 경로상의 운영 시스템 |
| [비즈니스 메타데이터](https://en.wikipedia.org/wiki/Metadata) | 용어·소유자·분류·품질 등 데이터의 의미와 신뢰를 다루는 메타데이터 |
| [능동형 메타데이터](https://en.wikipedia.org/wiki/Metadata) | 메타데이터를 지속 분석해 알림·추천·정책 집행 등 행동을 자동으로 일으키는 관리 방식 |
| [OpenLineage](https://en.wikipedia.org/wiki/Data_lineage) | 파이프라인 실행의 입출력 계보를 이벤트로 표현하는 오픈 표준 |
| [데이터 메시](https://en.wikipedia.org/wiki/Data_mesh) | 도메인 소유, 데이터 제품, 셀프서브 플랫폼, 연합 거버넌스의 분산형 운영모델 |
| [데이터 패브릭](https://en.wikipedia.org/wiki/Data_fabric) | 능동형 메타데이터·지식그래프로 이기종 통합·거버넌스를 자동화하는 아키텍처 |
| [데이터 제품](https://en.wikipedia.org/wiki/Data_product) | 소유자·계약·SLA·문서를 갖춘 재사용 가능한 데이터 단위 |
| [데이터 계약](https://en.wikipedia.org/wiki/Data_contract) | 생산자–소비자 간 스키마·품질·SLA의 기계 검증 가능한 합의(ODCS) |
| [시맨틱 레이어](https://en.wikipedia.org/wiki/Semantic_layer) | 메트릭·차원·관계를 중앙에서 선언적으로 정의해 도구에 일관되게 제공하는 계층 |
| OSI / Apache Ossie | 시맨틱 모델을 벤더 중립으로 교환하기 위한 오픈 사양 |
| [온톨로지](https://en.wikipedia.org/wiki/Web_Ontology_Language) | 도메인 개념·관계·제약의 형식적 명세(RDF/OWL) |
| [컨텍스트 레이어](https://en.wikipedia.org/wiki/Knowledge_graph) | 시맨틱 레이어·지식그래프·용어집·계보로 AI에 의미와 맥락을 공급하는 계층 |
| [하이브리드 검색](https://en.wikipedia.org/wiki/Okapi_BM25) | BM25와 벡터 검색을 결합해 키워드 정확성과 의미 유사성을 함께 확보 |
| [리랭킹](https://en.wikipedia.org/wiki/Learning_to_rank) | 1차 후보를 크로스 인코더 등으로 재정렬해 정밀도를 높이는 단계 |
| [Agentic RAG](https://en.wikipedia.org/wiki/Retrieval-augmented_generation) | 에이전트가 계획·반성·도구 사용으로 검색을 반복·조정하는 RAG |
| [GraphRAG](https://en.wikipedia.org/wiki/Knowledge_graph) | 엔티티 그래프의 커뮤니티 요약으로 전역 질의에 답하는 RAG |
| [MCP](https://en.wikipedia.org/wiki/Model_Context_Protocol) | AI 앱이 외부 도구·데이터에 접근하는 개방형 표준 프로토콜(LF AAIF 산하) |
| [권한 인지형 검색](https://en.wikipedia.org/wiki/Information_retrieval) | 사용자 권한을 검색·생성 단계에 강제해 권한 밖 정보가 섞이지 않게 하는 설계 |
| Faithfulness | 답변의 주장이 검색된 근거로 뒷받침되는 비율 |
| [Confabulation](https://en.wikipedia.org/wiki/Hallucination_%28artificial_intelligence%29) | 그럴듯하지만 틀린 내용을 확신 있게 생성하는 위험(NIST AI 600-1) |
| [스트랭글러 패턴](https://en.wikipedia.org/wiki/Strangler_fig_pattern) | 레거시를 기능 단위로 점진 대체한 뒤 폐기하는 이관 패턴 |
| 7R | Retire·Retain·Rehost·Relocate·Repurchase·Replatform·Refactor 이관 전략 분류 |
| 병행 운영 | 신구 시스템을 동시 운영하며 결과를 대사해 정합성을 검증하는 방식 |
| [LLMOps](https://en.wikipedia.org/wiki/MLOps) | 프롬프트·평가·가드레일·비용을 포함한 LLM 애플리케이션 운영 체계 |
| 고영향 인공지능 | 생명·안전·기본권에 중대한 영향 우려가 있고 법정 영역에서 쓰이는 AI(AI 기본법 제2조제4호) |
| [가명정보](https://en.wikipedia.org/wiki/Pseudonymization) | 추가 정보 없이 특정 개인을 알아볼 수 없게 처리한 정보. 통계·연구·공익 목적에 한해 동의 없이 처리 가능 |

## 8. 활용 예: 서술형 설계 문제와 답변 골격

> 42문항 PPT 답안형 정리는 [사내 기술인증 대비: 예상 문항과 모범 답안]({% post_url 2026-10-14-ai-data-platform-certification-exam-qa %})을 참고하세요.

각 기술마다 **Why → 대안 비교 → 선택 기준 → 트레이드오프**의 4단 구조를 반복하면 설계 근거가 일관되게 전달된다.

### 문제 1. 생성형 AI·에이전트 도입을 위한 전사 데이터 플랫폼 전환 방안을 제안하시오.

1. 배경: AI-ready 데이터 부재(Gartner 63%·60%), 이원화·의미 불일치·권한 단절
2. 목표 아키텍처: 7계층과 계층별 설계 근거, 운영계 RDBMS와의 역할 분리
3. 기술 선택: 포맷·카탈로그·벡터 저장소·RAG·수치 질의 비교
4. 로드맵: 7R → 기반 → 파일럿 → 스트랭글러 확산 → 폐기, 단계 게이트
5. 거버넌스: 공통 통제 프레임
6. KPI: 근거성·정확도·이관율·TCO·규제 충족률

### 문제 2. RAG의 한계와 고도화 방안을 비교하고 엔터프라이즈 평가 체계를 제시하시오.

1. 필요성 → 2. Naive RAG 실패 메커니즘 → 3. Advanced·Modular·Agentic·GraphRAG 비교(-67% 수치) → 4. 질의 분포 기반 라우팅 → 5. 4계층 평가 지표, 자동평가와 인간 검수 혼합 → 6. 골든셋 회귀, 임베딩 버전관리

### 문제 3. LLM 기반 자연어 데이터 질의의 수치 정확성 보장 설계를 제시하시오.

1. Text-to-SQL 한계(Spider 2.0 21%, 벤치마크–현업 괴리) → 2. 원인(스키마 복잡도, 의미 부재) → 3. 시맨틱 레이어, 쿼리 라우팅, 실행 검증 → 4. 권한 일원화, 비용 상한 → 5. 메트릭 일치율, EX

### 문제 4. 레거시 DW·MPP·Hadoop을 레이크하우스로 전환하는 전략과 리스크 관리 방안을 기술하시오.

1. 현황 진단(자산·사용 로그·의존성) → 2. MPP도 성능은 충분하다는 반론 인정, 전환 근거 4가지 제시 → 3. 7R 권고 조합 → 4. 스트랭글러·병행 운영·컷오버 기준 → 5. 온프레미스면 스토리지 대안 비교 → 6. 리스크 레지스터, 롤백, TCO

### 문제 5. AI 에이전트의 사내 데이터 접근을 위한 보안·거버넌스 체계를 설계하시오.

1. 위협 모델 → 2. MCP 게이트웨이, 위임 권한, ABAC, 권한 인지형 검색 → 3. 최소 권한, 읽기·쓰기 분리, 사람 승인, 감사 로그 → 4. 규제 매핑 → 5. 권한 위반 0건, 차단률, 레드팀

### 문제 6. 능동형 메타데이터 기반 데이터 거버넌스 체계를 설계하시오.

1. 수동형 메타데이터의 한계 → 2. 기술 카탈로그와 비즈니스 메타데이터의 분리 이유 → 3. 수집–연결–분석–행동 루프와 표준(OpenLineage, ODCS, REST 카탈로그) → 4. 시나리오(PII 전파·마스킹, 스키마 변경 차단) → 5. 자동화 리스크와 사람 승인 → 6. 성숙도와 KPI(커버리지, 연결률)

### 요약 문장 모음

- **RDBMS와 OTF**: 오픈 테이블 포맷 도입은 RDBMS의 대체가 아니라 분석·AI 워크로드의 분리다. 운영계는 RDBMS로 유지하고, CDC로 레이크하우스에 연계해 여러 엔진이 복제 없이 공유하는 분석·AI 기반을 구축한다.
- **MPP**: MPP RDBMS는 대용량 정형 분석 성능이 충분하므로 전환 근거는 성능이 아니다. 근거는 스토리지–엔진 결합에 따른 데이터 공유 제약, 컴퓨트·스토리지 동반 확장 비용, AI·비정형 수용 한계, 벤더 지속성 리스크다.
- **온프레미스**: S3 호환 객체 스토리지를 경계로 컴퓨트와 스토리지를 분리하고, 오픈 테이블 포맷과 REST 카탈로그를 표준으로 채택해 상위 엔진의 교체 가능성을 확보한다. 스토리지는 용량 계층과 성능 계층으로 티어링하고, 통합 AI 플랫폼은 PoC로 검증해 AI 서빙 영역에 한정 적용한다.
- **능동형 메타데이터**: 이벤트 기반으로 수집한 메타데이터를 그래프로 연결·분석해 정책 집행, 영향 차단, 품질 경보를 자동으로 일으킨다. 에이전트가 메타데이터로 데이터를 고르는 환경에서는 메타데이터의 신선도와 정확도가 AI 답변 품질의 상한이 된다.
- **기술 vs 비즈니스 메타데이터**: 일관성 요구, 장애 반경, 소유 주체가 다르므로 두 계층을 분리한다. 분류와 정책은 비즈니스 계층에서 작성하고, 기술 계층과 쿼리 엔진에서 집행한다.
- **결론부**: 모델보다 데이터·컨텍스트를 우선하고, 단계 게이트와 정량 KPI로 점진 전환하며, 설계 산출물(계보·데이터 카드·평가 리포트)이 곧 규제 증빙이 되도록 한다.

## 해석상 주의와 출처

- **예측과 사실 구분**: Gartner의 60%·40%·70% 수치는 예측이다. 시맨틱 레이어 관련 60% 예측과 능동형 메타데이터 Market Guide 정의는 유료 원문 대신 이를 인용한 2차 자료로 확인했으므로 "Gartner에 따르면" 수준으로 요약해 쓴다.
- **벤치마크 시효성**: Spider 2.0의 21%는 발표 시점 기준이며, 2026년 리더보드와 현업 벤치마크 간 괴리가 크다. Anthropic의 -67%, VAST의 Iceberg 대비 성능은 자체 실험 결과다.
- **표준 미성숙**: OSI/Apache Ossie는 인큐베이팅 단계이고, Iceberg v3는 엔진별 지원이 고르지 않으며 다운그레이드가 불가하다.
- **벤더 동향**: MinIO 경과는 커뮤니티 매체와 포크 관리자 글로 확인했다. MinIO GitHub 저장소가 2026.4.25 아카이브된 것은 [저장소 페이지](https://github.com/minio/minio)에서 확인했다. 이후 상태는 바뀔 수 있으므로 공지를 다시 확인한다.
- **법령 세부**: AI 기본법 조문과 과태료 세부는 국가법령정보센터 원문으로 확인한다. 계도기간 종료일은 확정 공표되지 않았다.

**주요 출처** (접속 확인: 2026-10-13)

- [Gartner: Lack of AI-Ready Data Puts AI Projects at Risk (2025.2.26)](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk) (설문 표본 수는 사본마다 표기가 달라 본문에서 생략)
- [Gartner: 40% of Agentic AI Projects Canceled by 2027 (2025.6.25)](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027)
- [Armbrust 외, Lakehouse (CIDR 2021)](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf)
- [Apache Iceberg 사양](https://iceberg.apache.org/spec/) · [REST 카탈로그 사양](https://iceberg.apache.org/rest-catalog-spec/)
- [Databricks: Apache Iceberg v3](https://www.databricks.com/blog/apache-icebergtm-v3-moving-ecosystem-towards-unification)
- [Apache Polaris 최상위 프로젝트 졸업](https://polaris.apache.org/blog/2026/02/19/apache-polaris-graduates-to-top-level-project/)
- [Anthropic: MCP 기증과 AAIF 설립](https://anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) · [MCP 공식 사이트](https://modelcontextprotocol.io/)
- [Spider 2.0 (arXiv 2411.07763)](https://arxiv.org/html/2411.07763v2) · [DevRev NL2SQL (arXiv 2609.04641)](https://arxiv.org/abs/2609.04641) · [AgenticRAG (arXiv 2605.05538)](https://arxiv.org/abs/2605.05538)
- [Anthropic: Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)
- [GraphRAG (arXiv 2404.16130)](https://arxiv.org/abs/2404.16130) · [RAGAS (arXiv 2309.15217)](https://arxiv.org/html/2309.15217v1)
- [AWS: 마이그레이션 전략(7R)](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html) · [Strangler Fig(Fowler)](https://martinfowler.com/bliki/StranglerFigApplication.html)
- [dbt: OSI 사양 업데이트](https://www.getdbt.com/blog/the-osi-spec-updates)
- [인공지능 발전과 신뢰 기반 조성 등에 관한 기본법(국가법령정보센터)](https://www.law.go.kr/lsInfoP.do?lsiSeq=268543)
- [PIPC 생성형 AI 개인정보 처리 안내서](https://pipc.go.kr/np/cop/bbs/selectBoardArticle.do?bbsId=BS074&mCode=C020010000&nttId=11410)
- [EU AI Act (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng) · [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) · [NIST AI 600-1](https://doi.org/10.6028/NIST.AI.600-1) · [ISO/IEC 42001](https://www.iso.org/standard/42001)
- [ODCS 데이터 계약](https://bitol-io.github.io/open-data-contract-standard/) · [OpenLineage](https://openlineage.io/)
- [Apache Cloudberry 프로젝트 이력](https://cloudberry.apache.org/docs/introduction/cloudberry-history) · [인큐베이션 보고서(2025.11)](https://cloudberry.apache.org/blog/apache-cloudberry-incubation-report-202511)
- [VAST: DataBase vs Iceberg 벤치마크(벤더 자료)](https://vastdata.com/blog/the-next-generation-database-for-the-ai-era)
- [MinIO 오픈소스 중단 경과(매체 기사)](https://itsfoss.com/news/minio-moves-away-from-open-source/) · [MinIO 저장소](https://github.com/minio/minio)
- [Alation: Gartner 능동형 메타데이터 Market Guide 요약(벤더 자료)](https://www.alation.com/blog/active-metadata)
