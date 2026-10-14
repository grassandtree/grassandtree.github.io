---
title: "AI 데이터 플랫폼 상황별 판단 Q&A: 근거와 1차 출처까지"
layout: single
toc: true
published: true
categories: 데이터플랫폼
tags: [AI, 데이터플랫폼, 레이크하우스, RAG, 거버넌스, 규제]
---

> 정의보다 "이 상황에서 무엇을 고르고 왜 그런가"에 초점을 맞춘 질의응답입니다. 질문 42개를 영역별로 정리한 글은 [AI 데이터 플랫폼 설계 Q&A]({% post_url 2026-10-14-ai-data-platform-design-qa %}), 개념 배경은 [개요판]({% post_url 2026-10-13-enterprise-ai-data-platform-overview %})과 [상세판]({% post_url 2026-10-13-ai-enterprise-data-platform-migration-design %})을 참고하세요.
>
> 링크 접속 확인일: 2026-10-14 (Gartner·ISO·EUR-Lex·ACM은 자동 접속을 차단하므로 검색 결과로 존재를 확인했습니다)

## 이 글의 구성

사실은 검색하면 바로 찾을 수 있다. 실무에서 차이를 만드는 것은 **찾은 사실로 무엇을 판단하느냐**다. 정의를 옮겨 적은 답은 쓸모가 적고, 최신 수치·조문이 틀리면 오히려 신뢰를 잃는다. 그래서 이 글은 다음 원칙으로 정리했다.

- **판단·시나리오·트러블슈팅 중심**: "이 상황에서 무엇을 고르나", "고객 조건이 주어지면 어떻게 설계하나", "원인이 무엇인가"를 묻는 질문 위주다.
- **조건을 답에 반영**: "한국 금융·공공 고객", "망분리", "레거시 MPP" 같은 조건이 붙으면 답도 그 조건에 맞춰 달라진다.
- **최신 규제·표준은 정확히 인용**: AI 기본법, 망분리 완화, Iceberg v3, MCP처럼 자주 바뀌는 내용은 원문 확인이 전제다.

**답변 형식**

- 각 질문은 **포인트**(질문이 겨냥하는 원리) → **핵심**(결론 한 줄) → 근거 불릿·비교표 → **확인할 출처** 순서다.
- 1차 출처(법령, 정부 보도자료, 표준 사양, 공식 문서, 논문)를 우선 인용하고, 블로그·벤더 홍보는 "벤더 주장"으로 표기한다.
- 인용은 짧게 쓴다: "(금융위 보도자료, 2024.8)", "(Iceberg spec v3)".
- 수치에는 성격을 붙인다: **사실 / 예측 / 벤더 주장**.

## 1차 출처 모음

아래 출처를 즐겨찾기 폴더 하나에 모아 두면, 수치·조문·날짜를 확인할 때 시간을 크게 줄일 수 있다.

| 분야 | 출처 | 확인할 내용 |
| --- | --- | --- |
| 법령 | [AI 기본법(국가법령정보센터)](https://www.law.go.kr/%EB%B2%95%EB%A0%B9/%EC%9D%B8%EA%B3%B5%EC%A7%80%EB%8A%A5%EB%B0%9C%EC%A0%84%EA%B3%BC%EC%8B%A0%EB%A2%B0%EA%B8%B0%EB%B0%98%EC%A1%B0%EC%84%B1%EB%93%B1%EC%97%90%EA%B4%80%ED%95%9C%EA%B8%B0%EB%B3%B8%EB%B2%95) | AI 기본법 제2조(고영향 정의), 제31조(투명성), 제34조(사업자 책무), 시행일 |
| 법령 | [EU AI Act 원문(EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng) | 제10조(데이터 거버넌스), 제50조(투명성) |
| 금융 | [금융위원회 보도자료](https://www.fsc.go.kr/no010101): [망분리 로드맵](https://www.fsc.go.kr/no010101/82885) · [AI 활용 지원](https://www.fsc.go.kr/no010101/83594) · [SaaS 개선](https://www.fsc.go.kr/no010101/86080) | 망분리 개선 로드맵(2024.8), 생성형 AI 활용 지원(2024.12), SaaS 예외 |
| 개인정보 | [개인정보위: 생성형 AI 개인정보 처리 안내서](https://www.pipc.go.kr/np/cop/bbs/selectBoardArticle.do?bbsId=BS074&mCode=C020010000&nttId=11410) | 4단계 수명주기, 정당한 이익 요건 |
| 공공 | 디지털플랫폼정부위원회 — "초거대 AI 가이드라인 2.0" / [CSAP 고시(국가법령정보센터)](https://www.law.go.kr/%ED%96%89%EC%A0%95%EA%B7%9C%EC%B9%99/%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C%EC%BB%B4%ED%93%A8%ED%8C%85%EC%84%9C%EB%B9%84%EC%8A%A4%EB%B3%B4%EC%95%88%EC%9D%B8%EC%A6%9D%EC%97%90%EA%B4%80%ED%95%9C%EA%B3%A0%EC%8B%9C) | 공공 AI 인프라 선택지, CSAP 상·중·하 기준 |
| AI 위험 | [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) / [ISO/IEC 42001](https://www.iso.org/standard/42001) | 4기능(Govern·Map·Measure·Manage), AIMS 정의 |
| 보안 | [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/) | 위협 항목 이름 |
| 테이블 포맷 | [Apache Iceberg spec](https://iceberg.apache.org/spec/) | v3 기능(deletion vector, row lineage, variant) |
| 카탈로그 | [Apache Polaris](https://polaris.apache.org) | REST 카탈로그, TLP 졸업(2026.2) |
| MPP | [Apache Cloudberry](https://cloudberry.apache.org) | Greenplum 폐쇄와 후계 경과 |
| 메타데이터 | [OpenLineage](https://openlineage.io) / [Bitol ODCS](https://bitol-io.github.io/open-data-contract-standard/latest/) | Job·Run·Dataset 모델, ODCS 구성 요소 |
| 에이전트 | [Model Context Protocol](https://modelcontextprotocol.io) / [A2A Protocol](https://a2a-protocol.org/) | 구성 요소, LF 이관 날짜 |
| 검색 | [Anthropic Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) / [RAGAS 문서](https://docs.ragas.io/) | 실패율 감소 수치, 평가 지표 이름 |
| Text-to-SQL | [Spider 2.0 논문](https://arxiv.org/html/2411.07763v2) | 엔터프라이즈 과제 성공률 |
| 전환 | [AWS 마이그레이션 전략(7R)](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html) / [Strangler Fig(Fowler)](https://martinfowler.com/bliki/StranglerFigApplication.html) | 7R 이름과 정의, 스트랭글러 원문 |
| 시장 예측 | [Gartner 보도자료(AI-ready 데이터)](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk) / [Gartner 보도자료(에이전틱 AI)](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027) | 60%·40% 예측 문구 |

**검색 팁**: 한국어 공식 자료는 "기관명 + 보도자료 + 키워드", 영문 표준은 "공식 프로젝트명 + spec" 조합이 빠르다. 예: "금융위원회 보도자료 망분리 로드맵", "iceberg spec deletion vectors".

## A. 데이터 구조·레이크하우스

### A1. 품질 점검을 통과한 데이터인데도 AI 모델 성능이 낮다면, AI-ready 데이터 관점에서 원인과 대응은?

> **참고 링크**: [Gartner: AI-ready 데이터(2025.2)](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk) · [EU AI Act 원문(EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng)

**포인트**: 품질은 용도에 따라 달라진다(fitness for use).

**핵심**: 품질 점검이 '결산·보고 용도' 기준이었기 때문이며, AI 유스케이스별 적합성 기준을 따로 정의·측정해야 한다.

- 같은 데이터도 용도에 따라 합격·불합격이 갈린다. 결산용으로 이상치를 제거한 데이터는 이상탐지 학습에는 부적합하다.
- AI 기준은 대표성·편향, 최신성, 라벨 품질, 비정형 파싱 성공률처럼 기존 DQ 항목에 없는 것이 많다.
- 대응: 유스케이스-데이터 적합성 매트릭스 작성 → 기준·임계값 합의 → 자동 측정 → 카탈로그에 용도별 인증 기록.
- 근거: Gartner는 AI-ready 데이터 없는 AI 프로젝트의 60%가 2026년까지 포기될 것으로 예측했다(예측).

**확인할 출처**: Gartner 보도자료(2025.2.26)의 60%·63% 수치 / EU AI Act 제10조 "의도된 목적에 비추어" 문구.

### A2. DW와 데이터 레이크를 운영 중인 고객에게 레이크하우스를 제안한다면, 도입 근거와 도입하지 말아야 할 조건은?

> **참고 링크**: [Lakehouse 논문(CIDR 2021)](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf) · [Wikipedia: Data warehouse](https://en.wikipedia.org/wiki/Data_warehouse) · [Wikipedia: Data lake](https://en.wikipedia.org/wiki/Data_lake)

**포인트**: 장점만이 아니라 적용 한계까지 따진다.

**핵심**: 정형·비정형을 한 벌의 데이터로 여러 엔진이 공유해야 할 때 도입하고, 정형 BI만 있다면 기존 DW 유지가 합리적이다.

- 근거: 2계층(레이크→DW) 구조의 ETL 복제·지연·ML 접근 제약 해소, 스토리지·컴퓨트 분리로 비용 탄력성 확보, AI·비정형 데이터 통합 거버넌스.
- 기술 핵심: 객체 스토리지 위 오픈 테이블 포맷(ACID, 스키마 진화, 타임트래블) + 공유 카탈로그.
- 비도입 조건: 수백 명 동시 접속 저지연 BI가 핵심이고 데이터 증가가 완만함, 운영 인력이 없음, AI·ML 계획이 없음.
- 절충: DW는 Gold 서빙으로 유지하고 이력·비정형만 레이크하우스로.

**확인할 출처**: Lakehouse 논문(Armbrust 외, CIDR 2021) 제목과 연도.

### A3. 망분리 금융사의 온프레미스 레이크하우스 표준 테이블 포맷으로 Iceberg·Delta·Hudi 중 무엇을 골라야 하나?

> **참고 링크**: [Iceberg 테이블 사양](https://iceberg.apache.org/spec/) · [Iceberg REST 카탈로그 사양](https://iceberg.apache.org/rest-catalog-spec/) · [Delta Lake](https://delta.io/) · [Apache Hudi](https://hudi.apache.org/) · [Polaris TLP 졸업 공지(2026.2)](https://polaris.apache.org/blog/2026/02/19/apache-polaris-graduates-to-top-level-project/)

**포인트**: 선택 기준을 고객 조건에 연결한다.

**핵심**: 엔진 중립성과 감사 추적이 우선인 온프레미스 금융 환경에서는 Iceberg를 표준으로 한다.

| 기준 | Iceberg | Delta | Hudi |
| --- | --- | --- | --- |
| 엔진 중립 | 높음(REST 카탈로그 사양) | Databricks 중심 | 중간 |
| 행 단위 삭제·감사 | v3 deletion vector, row lineage | deletion vector | 레코드 인덱스, upsert 강점 |
| 카탈로그 | Polaris(ASF 최상위 프로젝트) | Unity Catalog | Hive 계열 |

- 근거: 다중 엔진(Spark·Trino·MPP) 공유, 개인정보 삭제 요구 대응, 행 계보로 감사 추적.
- 리스크: v3는 다운그레이드 불가, 엔진별 지원 편차 → PoC로 엔진 버전 매트릭스 검증.

**확인할 출처**: Iceberg spec v3 항목 / Polaris 최상위 프로젝트 졸업일.

### A4. 운영계 RDBMS, MPP(Greenplum), 오픈 테이블 포맷의 역할은 어떻게 나눠야 하나?

> **참고 링크**: [Wikipedia: Greenplum](https://en.wikipedia.org/wiki/Greenplum) · [Cloudberry 프로젝트 연혁](https://cloudberry.apache.org/docs/introduction/cloudberry-history) · [Iceberg 테이블 사양](https://iceberg.apache.org/spec/)

**포인트**: "OTF로 다 바꾼다"는 함정을 피한다.

**핵심**: 트랜잭션은 RDBMS, 고동시성 정형 분석은 MPP, 대용량·다중 엔진·AI 데이터는 OTF에 두고 진실의 원천은 OTF로 한다.

- RDBMS: 행 저장·잠금/MVCC로 작은 트랜잭션에 최적. OTF는 작은 쓰기가 잦으면 small file·커밋 충돌이 생긴다.
- MPP: 대용량 SQL 분석 성능은 충분하다. 전환 근거는 성능이 아니라 스토리지–엔진 결합, 동반 확장 비용, AI·비정형 수용 한계, 벤더 지속성이다.
- OTF: 원천 이력, 비정형, 학습 데이터, 다중 엔진 공유.
- 연결: 운영계 → CDC → OTF(Bronze/Silver) → Gold(OTF + MPP 마트) → BI·RAG·ML.

**확인할 출처**: Greenplum 폐쇄 전환 시점(2024.5)과 Cloudberry 인큐베이팅 상태.

### A5. Greenplum 오픈소스 버전을 쓰는 고객에게는 어떤 전략 옵션이 있고, 무엇을 권할까?

> **참고 링크**: [Apache Cloudberry](https://cloudberry.apache.org/) · [Cloudberry 프로젝트 연혁](https://cloudberry.apache.org/docs/introduction/cloudberry-history)

**포인트**: OSS 지속성 리스크를 의사결정에 반영한다.

**핵심**: 단기 상용 지원으로 안정화, 중기 Cloudberry 호환 이전 검토, 장기 분석 데이터는 OTF로 옮기는 단계적 탈출을 권한다.

| 옵션 | 장점 | 단점 |
| --- | --- | --- |
| 상용 Greenplum 유지 | 위험 최소 | 라이선스 비용, 단일 벤더 종속 |
| Apache Cloudberry 이전 | Greenplum 호환, 재단 거버넌스, 최신 PostgreSQL 커널 | 인큐베이팅 단계, 국내 지원 체계 확인 필요 |
| OTF + 쿼리 엔진 재설계 | 개방성, 장기 TCO | SQL 방언·UDF·동시성 재검증 비용 |

- 금융 마감 배치 SLA 때문에 빅뱅 전환은 피한다.

**확인할 출처**: Cloudberry 최신 릴리스 버전 / Greenplum 상용 지원 정책.

### A6. Iceberg 테이블 조회가 점점 느려진다면 원인과 대응은?

> **참고 링크**: [Iceberg 유지보수 가이드](https://iceberg.apache.org/docs/latest/maintenance/) · [Iceberg Spark 프로시저](https://iceberg.apache.org/docs/latest/spark-procedures/)

**포인트**: 운영 경험 기반 트러블슈팅.

**핵심**: 대부분 작은 파일·삭제 파일·메타데이터 누적 문제이므로, 테이블 유지보수를 운영 SLA로 정의한다.

- 작은 파일(스트리밍·CDC 적재) → 컴팩션(rewrite\_data\_files).
- 삭제 파일 누적(merge-on-read 비용) → 컴팩션, v3 deletion vector 검토.
- 스냅샷·매니페스트 비대 → expire\_snapshots, rewrite\_manifests.
- 레이아웃 불일치 → 파티션 진화, 정렬 재설계. 파일 수·평균 크기·삭제 비율을 모니터링 지표로 둔다.

**확인할 출처**: Iceberg 유지보수 프로시저 이름(Spark procedures 문서).

### A7. 온프레미스 S3 호환 객체 스토리지(Ceph, MinIO, 상용 어플라이언스, VAST Data 등)는 어떻게 골라야 하나?

> **참고 링크**: [Ceph Object Gateway 문서](https://docs.ceph.com/en/latest/radosgw/) · [MinIO GitHub 저장소](https://github.com/minio/minio) · [AWS S3 Object Lock(WORM)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html) · [VAST Data](https://www.vastdata.com/)

**포인트**: 성능보다 지속성·호환·보안 기준을 먼저 본다.

**핵심**: 1차 기준은 유지보수 주체의 지속성, S3 API 호환 범위, 보안 기능이며, 성능은 고객 데이터 PoC로 검증한다.

- Ceph: 성숙한 오픈소스, 운영 난도 높음 → 전문 인력이나 상용 지원 필요.
- MinIO 커뮤니티판: 2025년 말 유지보수 모드 후 저장소 아카이브 → 신규 구축 배제, 기존 고객은 이관 계획.
- 상용 어플라이언스: 기술지원·보안인증 확보 용이. S3 호환만 지키면 교체 가능.
- VAST 등 통합 AI 플랫폼: 고처리량·RAG 통합 장점, 성능 수치는 벤더 주장이고 고유 기능 사용 범위만큼 락인.
- 티어링: GPU 근접 고성능 계층(소량) + 대용량 저가 객체 계층.

**확인할 출처**: MinIO 커뮤니티판 저장소 상태 / 후보 제품의 Object Lock(WORM) 지원 여부.

## B. 메타데이터·거버넌스

### B1. 기술 카탈로그와 비즈니스 메타데이터 플랫폼을 하나로 통합하자는 요구, 받아들여야 할까?

> **참고 링크**: [Iceberg REST 카탈로그 사양](https://iceberg.apache.org/rest-catalog-spec/) · [Apache Polaris](https://polaris.apache.org/) · [Unity Catalog](https://www.unitycatalog.io/)

**포인트**: 분리해야 하는 이유를 메커니즘으로 설명한다.

**핵심**: 화면(조회 경험)은 통합하되, 시스템은 분리한다. 일관성 요구·장애 반경·소유 주체가 다르기 때문이다.

- 기술 카탈로그: 모든 읽기·쓰기가 거치는 쿼리 경로, 스냅샷 포인터의 원자적 교체가 필요한 강한 일관성, 장애 시 쿼리 중단.
- 비즈니스 메타데이터: 용어·오너·품질·분류, 최종적 일관성으로 충분, 장애 시 탐색만 불편.
- 원칙: 분류·정책은 비즈니스 계층에서 작성하고, 기술 계층·쿼리 엔진에서 집행. 속성별 진실의 원천 지정.
- 대안: 플랫폼 통합형(Unity Catalog 등)은 플랫폼 밖 자산이 사각지대.

**확인할 출처**: Iceberg REST Catalog 사양 / Polaris 기능 범위.

### B2. 메타데이터 관리 시스템을 구축했는데 현업이 쓰지 않는다면 원인과 개선 방안은?

> **참고 링크**: [OpenLineage](https://openlineage.io/) · [OpenLineage 객체 모델](https://openlineage.io/docs/spec/object-model)

**포인트**: 능동형 메타데이터 개념의 실무 적용.

**핵심**: 수동 입력·조회 전용인 '수동형' 메타데이터라 금방 낡고 쓸 이유가 없기 때문이며, 이벤트 기반 자동 수집과 행동 연결로 전환한다.

- 원인: 수동 등록으로 최신성 상실, 별도 포털이라 업무 흐름 밖, 결과가 아무 행동도 일으키지 않음, 오너 부재.
- 개선: OpenLineage 등으로 계보 자동 수집, 품질·사용 통계 연동, BI·SQL 편집기·사내 챗봇 안에 노출.
- 행동 연결: 스키마 변경 시 영향 알림·CI 차단, 품질 이상 시 하위 대시보드 경고, PII 태그 전파로 자동 마스킹.
- 조직: 데이터 오너 KPI에 메타데이터 충족률 포함.

**확인할 출처**: Gartner의 능동형 메타데이터 Market Guide(2021) 정의 문구 / OpenLineage 객체 모델.

### B3. 데이터 계약(Data Contract)은 무엇으로 구성되고, 어디에 적용하나?

> **참고 링크**: [Open Data Contract Standard(ODCS)](https://bitol-io.github.io/open-data-contract-standard/latest/)

**포인트**: 데이터 계약은 문서가 아니라 집행 장치다.

**핵심**: 데이터 계약은 생산자–소비자 간 스키마·품질·SLA·책임의 기계 판독형 합의이며, CI/CD 게이트에서 집행해야 효과가 난다.

- 구성: 스키마, 품질 규칙, SLA(신선도·가용성), 오너·역할, 사용 조건(ODCS 기준 YAML).
- 적용 위치: 스키마 변경 PR에서 호환성 검사 → 위반 시 머지 차단, 파이프라인 실행 시 품질 규칙 검사 → 위반 시 격리·알림.
- 확장: 계약 + 품질 SLO + 계보 + 오너 = 데이터 제품. RAG 인덱스도 데이터 제품으로 관리.
- SI 관점: 차세대 인터페이스 정의서를 계약 YAML로 바꾸면 문서가 테스트가 된다.

**확인할 출처**: Bitol ODCS 최신 버전과 주요 섹션 이름.

### B4. 그룹사 데이터 조직에는 데이터 메시와 데이터 패브릭 중 어느 접근이 맞나?

> **참고 링크**: [Data Mesh 원칙(Fowler)](https://martinfowler.com/articles/data-mesh-principles.html)

**포인트**: 조직 현실을 반영한 판단.

**핵심**: 둘은 배타적이지 않으며, 한국 대기업에는 '중앙 플랫폼(패브릭) + 핵심 도메인 데이터 제품(메시)' 하이브리드가 현실적이다.

| 구분 | 데이터 메시 | 데이터 패브릭 |
| --- | --- | --- |
| 본질 | 조직·소유권 운영모델 | 메타데이터 기반 통합 기술 |
| 전제 | 도메인 팀의 데이터 역량 | 메타데이터 성숙도 |
| 위험 | 역량 편차, 중복 비용 | 중앙 병목 |

- 한국 맥락: 중앙 데이터 조직·IT 자회사가 강하고 현업 역량 편차가 커서 순수 메시는 실패 위험이 크다.
- 실행: 중앙은 플랫폼·표준·보안, 역량 있는 도메인 2~3개부터 데이터 제품 오너 지정.

**확인할 출처**: Zhamak Dehghani의 데이터 메시 4원칙 명칭.

### B5. 시맨틱 레이어, 온톨로지, 지식그래프는 어떻게 다르고, 언제 무엇을 쓰나?

> **참고 링크**: [Wikipedia: Semantic layer](https://en.wikipedia.org/wiki/Semantic_layer) · [Wikipedia: Ontology](https://en.wikipedia.org/wiki/Ontology_%28information_science%29) · [Wikipedia: Knowledge graph](https://en.wikipedia.org/wiki/Knowledge_graph) · [dbt Labs: Open Semantic Interchange](https://www.getdbt.com/blog/the-osi-spec-updates) · [ISO/IEC 39075 GQL](https://www.iso.org/standard/76120.html)

**포인트**: 용어를 혼동하지 않고 용도별로 배치한다.

**핵심**: 정량 지표 질의는 시맨틱 레이어, 개념·관계 추론은 온톨로지와 지식그래프로 배치한다.

- 시맨틱 레이어: 지표·차원·조인 경로의 단일 정의. BI·API·LLM이 같은 숫자를 쓰게 한다.
- 온톨로지: 클래스·관계·제약의 형식 모델(RDF/OWL).
- 지식그래프: 온톨로지에 인스턴스를 채운 데이터. 다중 홉 탐색, GraphRAG의 기반.
- 선택: "지난달 매출"은 시맨틱 레이어, "이 고객과 연결된 위험 거래 경로"는 지식그래프.

**확인할 출처**: Open Semantic Interchange(Apache Ossie) 현황 / GQL 표준 번호(ISO/IEC 39075).

### B6. 데이터 거버넌스와 AI 거버넌스를 통합하는 조직·프로세스는 어떻게 설계하나?

> **참고 링크**: [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) · [ISO/IEC 42001](https://www.iso.org/standard/42001)

**포인트**: 규제·표준을 운영 체계로 연결한다.

**핵심**: 단일 데이터·AI 위원회와 공통 메타데이터(카탈로그에 AI 자산까지 등록)로 중복과 공백을 없앤다.

- 조직: CDO·CAIO·CPO·CISO 참여 위원회, 데이터 오너·AI 유스케이스 오너.
- 프로세스: 접수 → 위험등급 분류(고영향·개인정보) → 데이터 적합성 검증 → 모델 승인 → 운영 모니터링 → 폐기.
- 공통 기반: 모델·프롬프트·인덱스·에이전트를 카탈로그에 등록하고 데이터에서 응답까지 계보로 추적.
- 표준 매핑: ISO/IEC 42001(AI 경영시스템), NIST AI RMF 4기능.

**확인할 출처**: NIST AI RMF 4기능 명칭 / ISO/IEC 42001 발행 연도.

## C. 검색·RAG·에이전트

### C1. 사내 RAG의 답변 정확도가 낮다는 불만이 많다면 어떻게 진단하고 개선하나?

> **참고 링크**: [Anthropic: Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) · [Lost in the Middle 논문](https://arxiv.org/abs/2307.03172) · [RAGAS 문서](https://docs.ragas.io/)

**포인트**: 검색 실패와 생성 실패를 분리한다.

**핵심**: 정답 근거가 검색 결과에 들어왔는지부터 계측해 검색 문제와 생성 문제를 분리하고, 골든셋으로 한 번에 한 변수씩 개선한다.

- 검색 실패: 청킹 경계, 코드·약어 미매칭(키워드 검색 부재), 한국어 도메인 임베딩 부적합, 구버전 문서 혼재, 표 파싱 실패.
- 생성 실패: 컨텍스트 과다·순서 문제, 근거 인용 미강제, 지시 불명확.
- 개선: 하이브리드 검색(BM25+벡터)+리랭킹, 구조 기반 청킹, 문서 버전 메타데이터 필터, 근거 인용 강제.
- 루프: 실패 로그 라벨링 → 원인별 처방 → 회귀 평가 → 배포.

**확인할 출처**: Anthropic Contextual Retrieval의 실패율 감소 수치(49%, 67%) / RAGAS 지표 이름.

### C2. 하이브리드 검색과 리랭킹을 포함한 검색 파이프라인은 어떻게 설계하나?

> **참고 링크**: [Wikipedia: Okapi BM25](https://en.wikipedia.org/wiki/Okapi_BM25) · [RRF 원 논문(SIGIR 2009)](https://doi.org/10.1145/1571941.1572114) · [Sentence Transformers: Cross-Encoder](https://www.sbert.net/examples/applications/cross-encoder/README.html) · [Anthropic: Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)

**포인트**: 각 단계가 왜 필요한지 이해한다.

**핵심**: 키워드(BM25)와 의미(벡터) 검색으로 재현율을 확보하고, 리랭커로 정밀도를 끌어올리는 2단 구조로 설계한다.

- BM25: 단어 빈도·희소성·문서 길이 보정으로 점수화. 상품코드·조문번호·약어처럼 정확 일치가 필요한 질의에 강하다. 한국어는 형태소 분석 필수.
- 벡터: 표현이 달라도 의미가 같은 문서를 찾는다.
- 결합: RRF 등으로 후보 50~150개 → 크로스 인코더 리랭커로 상위 5~20개.
- 망분리 환경: 임베딩·리랭커 모두 내부망 설치형 모델, 사내 골든셋으로 선정.

**확인할 출처**: Contextual Retrieval 실험 조건 / Reciprocal Rank Fusion 원 논문.

### C3. 문서 유형별(규정집, 상품설명서, 회의록, 표 중심 보고서) 청킹은 어떻게 해야 하나?

> **참고 링크**: [Anthropic: Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) · [RAG 서베이(Gao 외, 2023)](https://arxiv.org/abs/2312.10997)

**포인트**: 고정 길이 청킹의 한계.

**핵심**: 문서 구조를 보존하는 단위로 자르고, 고정 길이는 마지막 수단으로 쓴다.

- 규정집·약관: 조·항·호 단위, 상위 제목 경로를 메타데이터로 부착.
- 상품설명서: 섹션 단위, 상품명·버전·시행일 메타데이터로 최신 버전 필터.
- 회의록: 일자·발화자 단위, 요약 청크를 별도 인덱스로.
- 표 중심 보고서: 표는 구조화 추출(행 단위 텍스트화 또는 테이블 적재) 후 캡션과 결합.
- 공통: 청크 앞 문서 맥락 부착, 오버랩 10~20%, 평가로 크기 조정.

### C4. 경영진용 자연어 질의 서비스에는 Text-to-SQL과 시맨틱 레이어 경유 중 무엇이 맞나?

> **참고 링크**: [Spider 2.0 논문(ICLR 2025)](https://arxiv.org/abs/2411.07763) · [dbt MetricFlow](https://docs.getdbt.com/docs/build/about-metricflow)

**포인트**: 정확성·통제 관점의 판단.

**핵심**: 경영 지표는 시맨틱 레이어 경유를 기본으로 하고, 탐색적 분석에만 가드레일을 건 Text-to-SQL을 허용한다.

- 정확성: 실제 엔터프라이즈 스키마에서 자유형 SQL 생성은 조인·지표 정의 오류가 잦다.
- 일관성: 같은 "매출"이 보고서와 챗봇에서 다르면 신뢰가 무너진다.
- 통제: 시맨틱 레이어에서 행·열 권한과 비용을 일원화.
- Text-to-SQL 조건: 읽기 전용, 스키마 화이트리스트, 비용 상한, 생성 SQL 표시, 골든셋 회귀 평가.

**확인할 출처**: Spider 2.0 논문의 엔터프라이즈 과제 성공률(21.3%) / 최신 리더보드 수치.

### C5. 권한 인지형(Permission-aware) RAG는 어떻게 설계하나?

> **참고 링크**: [Wikipedia: RBAC](https://en.wikipedia.org/wiki/Role-based_access_control) · [OWASP Top 10 for LLM](https://genai.owasp.org/llm-top-10/)

**포인트**: 권한을 어느 단계에서 강제하는가.

**핵심**: 권한은 생성 단계가 아니라 검색 단계에서 사전 필터로 강제하고, 원천 시스템 ACL을 인덱스 메타데이터로 동기화한다.

- Why: "권한 없는 내용은 답하지 마" 같은 프롬프트 지시는 우회될 수 있고, 검색 결과에 들어가는 순간 유출 위험이 생긴다.
- 설계: 청크별 ACL 메타데이터(부서·직급·보안등급) 저장 → 사용자 SSO 클레임으로 사전 필터 → 생성 직전 재검증.
- 동기화: 원천(그룹웨어·ECM) 권한 변경을 이벤트로 반영하고 지연 SLA 설정. 고민감 문서는 별도 인덱스.
- 검증: 권한별 테스트 계정 레드팀, 응답 로그에 근거 문서 ID 기록.

**확인할 출처**: 국가망보안체계(N2SF) 데이터 등급 분류(공공 사업일 경우).

### C6. RAG·에이전트 평가 체계와 운영 KPI는 어떻게 설계하나?

> **참고 링크**: [RAGAS 문서](https://docs.ragas.io/)

**포인트**: 확률적 시스템의 품질을 계약 가능한 지표로 바꾼다.

**핵심**: 오프라인(골든셋), 온라인(로그·피드백), 위험(레드팀) 3층 평가를 배포 게이트에 연결한다.

- 검색: Context Precision·Recall, Recall@k, nDCG. 생성: Faithfulness, Answer Relevancy.
- 에이전트: 과업 성공률, 도구 호출 정확도, 건당 비용.
- 골든셋: 현업 SME가 질문·정답·근거 200~500건 구축, 분기마다 갱신. LLM 평가자는 사람 평가와 일치도 확인 후 사용.
- 운영 KPI: 만족도, 무응답률, P95 지연, 토큰 비용, 보안 이벤트.

**확인할 출처**: RAGAS 공식 문서의 지표 정의.

### C7. MCP와 A2A는 어떻게 다르고, 엔터프라이즈에 도입할 때 보안은 어떻게 고려하나?

> **참고 링크**: [Model Context Protocol](https://modelcontextprotocol.io/) · [Anthropic: MCP의 Agentic AI Foundation 기증](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) · [A2A Protocol](https://a2a-protocol.org/)

**포인트**: 표준의 위치와 보안 경계.

**핵심**: MCP는 에이전트와 도구·데이터, A2A는 에이전트와 에이전트를 잇는 표준이며, 둘 다 보안 경계가 아니므로 게이트웨이 통제가 필수다.

| 구분 | MCP | A2A |
| --- | --- | --- |
| 연결 대상 | 에이전트 ↔ 도구·데이터 | 에이전트 ↔ 에이전트 |
| 구조 | 호스트·클라이언트·서버 | 에이전트 카드, 과업 위임 |
| 거버넌스 | Linux Foundation 산하 재단 | Linux Foundation 프로젝트 |

- 보안: 도구 권한 최소화, MCP 서버 화이트리스트, 문서 경유 프롬프트 인젝션 방어, 사용자 위임 권한 전파, 도구 호출 감사 로그.

**확인할 출처**: MCP의 LF 이관일(2025.12.9) / A2A 발표·이관 시점.

### C8. 고객이 "전사 AI 에이전트 100개 구축"을 요구한다면 범위와 거버넌스는 어떻게 잡아야 하나?

> **참고 링크**: [Gartner: 에이전틱 AI 40% 취소 예측(2025.6)](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027)

**포인트**: 숫자 목표보다 가치·위험 기반 판단.

**핵심**: 개수가 아니라 가치·실현성·위험 3축으로 우선순위를 매기고, 에이전트 레지스트리와 수명주기 관리를 먼저 세운다.

- 근거: Gartner는 비용·가치 불명확·위험통제 미흡으로 2027년 말까지 에이전틱 AI 프로젝트 40% 이상이 취소될 것으로 예측(예측).
- 범위: 규칙 기반 자동화로 충분한 업무는 제외, 다단계 판단·비정형 데이터 업무부터.
- 거버넌스: 레지스트리(오너·권한·도구·모델·위험등급), 비용 모니터링, 저활용 에이전트 폐기 기준, 사람 승인 지점.

**확인할 출처**: Gartner 보도자료(2025.6.25) 문구.

## D. 플랫폼 전환

### D1. 레거시 DW(Teradata·Exadata)를 전환할 때 7R 관점에서 어떤 전략 조합이 좋을까?

> **참고 링크**: [AWS: 마이그레이션 전략(7R)](https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html)

**포인트**: 전략은 워크로드 단위로 고른다.

**핵심**: 7R은 시스템 전체가 아니라 워크로드 단위로 고르며, DW는 'Retire(미사용) + Replatform(대다수) + Refactor(핵심 마트)' 조합이 일반적이다.

- Retire: 사용 로그로 미사용 테이블·리포트를 폐기해 범위 축소.
- Retain: 규제·마감 SLA 때문에 당분간 유지.
- Replatform: SQL 호환 엔진이나 OTF로 최소 수정 이전.
- Refactor: AI 활용 데이터와 핵심 KPI는 데이터 제품·시맨틱 레이어로 재설계. 가장 비싸므로 선택적으로.

**확인할 출처**: AWS 7R 정의(특히 Refactor가 대규모 이관에 권장되지 않는다는 문구).

### D2. 스트랭글러 패턴을 데이터 플랫폼 전환에 어떻게 적용하나?

> **참고 링크**: [Strangler Fig(Fowler)](https://martinfowler.com/bliki/StranglerFigApplication.html)

**포인트**: 웹 애플리케이션 패턴을 데이터 영역으로 옮겨 설계한다.

**핵심**: 소비 계층 앞에 라우팅 계층(시맨틱 레이어·페더레이션·뷰)을 두고 도메인 단위로 물리 원천을 교체해, 사용자 영향 없이 레거시를 퇴역시킨다.

- 1단계: BI·리포트·인터페이스를 라우팅 계층 경유로 전환.
- 2단계: 도메인별 OTF 이관, CDC 동기화, 병행 대사 후 라우팅 전환.
- 3단계: 사용량 0 확인 후 해당 레거시 폐기.
- 약점 대응: 공존 비용 → 단계별 폐기 일정 / 좀비 레거시 → 최종 종료일 경영 확정 / 동기화 복잡도 → 데이터 흐름 단방향.

**확인할 출처**: Martin Fowler "StranglerFigApplication" 원문(2004).

### D3. CDC 기반 이관과 병행 운영·컷오버는 어떻게 계획하나?

> **참고 링크**: [Debezium 문서](https://debezium.io/documentation/reference/stable/index.html) · [Wikipedia: Change data capture](https://en.wikipedia.org/wiki/Change_data_capture)

**포인트**: 차세대 사업 경험의 구조화.

**핵심**: 초기 적재, CDC 증분, 정합성 자동 검증, 리허설 2회 이상, 롤백 기준이 컷오버 계획의 다섯 기둥이다.

- 초기 적재와 CDC: 스냅샷 시점(SCN·LSN) 기록, 로그 기반 CDC로 순서·삭제 보존.
- 정합성: 건수·합계·해시를 파티션 단위로 자동 비교, 주요 리포트 수치 대사.
- 병행 운영: 월마감 1회 이상 포함, 허용 차이 사전 합의.
- 컷오버: 동결 시점, Go/No-Go 체크리스트, 롤백 트리거(정합성 오류율·배치 SLA 초과)와 소요 시간.

### D4. Hadoop(Cloudera) 기반 빅데이터 플랫폼은 어떤 전략으로 전환하나?

> **참고 링크**: [Iceberg Hive 마이그레이션](https://iceberg.apache.org/docs/latest/hive-migration/) · [Iceberg Spark 프로시저](https://iceberg.apache.org/docs/latest/spark-procedures/)

**포인트**: 저장 계층과 연산 계층을 분리해 단계화한다.

**핵심**: HDFS + Hive 메타스토어를 객체 스토리지 + Iceberg + REST 카탈로그로 바꾸는 저장 계층 전환을 먼저 하고, 연산 엔진은 Spark·Trino로 이어 쓴다.

- Why: 컴퓨트·스토리지 결합으로 확장 비용이 높고, 작은 파일·NameNode 한계와 운영 인력 부족이 누적된다.
- 단계: Hive 테이블 → Iceberg 변환(in-place 또는 CTAS) → 객체 스토리지 이동 → 카탈로그 전환 → 워크로드 재배치.
- 리스크: Hive UDF·SerDe 호환, Ranger 정책 이관, 스케줄러 이관.

**확인할 출처**: Iceberg의 Hive 테이블 마이그레이션 프로시저(migrate, snapshot).

### D5. 온프레미스 레이크하우스, 퍼블릭 클라우드, 기존 MPP 유지의 5년 TCO는 어떻게 비교하나?

> **참고 링크**: [FinOps Foundation](https://www.finops.org/)

**포인트**: 숨은 비용과 결론을 바꾸는 변수.

**핵심**: 인프라·라이선스·인력·전환·리스크 비용을 모두 넣어야 하며, 데이터 이그레스·GPU 사용률·병행 운영 기간이 결론을 뒤집는 핵심 변수다.

| 비용 항목 | 포함 내용 |
| --- | --- |
| 직접비 | HW·클라우드 사용료, 라이선스, 유지보수, 상면·전력 |
| 간접비 | 운영 인력, 교육, 병행 운영 이중 비용, 전환 프로젝트 |
| 리스크 비용 | 벤더 종속(가격 인상), 지원 종료 SW, 규제 미준수 |

- 민감도 분석: 데이터 증가율, 질의량, GPU 사용률 시나리오.
- 전제: 망분리·CSAP 때문에 클라우드가 불가한 시스템을 먼저 분류.

### D6. DataOps, MLOps, LLMOps는 어떻게 다르고, 통합 운영 체계는 어떻게 만드나?

> **참고 링크**: [Wikipedia: MLOps](https://en.wikipedia.org/wiki/MLOps)

**포인트**: 관리 대상의 확장.

**핵심**: 관리 대상이 데이터 → 모델 → LLM 애플리케이션으로 넓어지며, 공통 기반(CI/CD·관측·카탈로그)을 공유하는 통합 체계가 효율적이다.

- DataOps: 파이프라인 CI/CD, 계약 테스트, 데이터 SLA 관측.
- MLOps: 피처 스토어, 실험 추적, 모델 레지스트리, 드리프트 감시.
- LLMOps: 프롬프트 버전, RAG 인덱스 수명주기, 골든셋 평가, 가드레일, 토큰 비용, 모델 라우팅.

## E. 규제·보안

규제는 조문 번호·날짜·수치가 자주 바뀌어 **가장 틀리기 쉬운 영역**이다. 답의 골격은 아래를 참고하되, 조문 번호·날짜·수치는 반드시 원문으로 확인한다.

### E1. AI 기본법의 주요 의무는 SI 사업 산출물에 어떻게 반영하나?

> **참고 링크**: [AI 기본법(국가법령정보센터)](https://www.law.go.kr/%EB%B2%95%EB%A0%B9/%EC%9D%B8%EA%B3%B5%EC%A7%80%EB%8A%A5%EB%B0%9C%EC%A0%84%EA%B3%BC%EC%8B%A0%EB%A2%B0%EA%B8%B0%EB%B0%98%EC%A1%B0%EC%84%B1%EB%93%B1%EC%97%90%EA%B4%80%ED%95%9C%EA%B8%B0%EB%B3%B8%EB%B2%95)

**포인트**: 법 조문을 설계·검수 산출물로 바꾼다.

**핵심**: 고영향 AI 해당 여부 확인 → 투명성(고지·표시) → 안전성·신뢰성 조치 문서화를 설계 산출물과 검수 기준에 내재화한다.

- 시행: 2026.1.22.
- 고영향 AI: 보건의료, 채용·대출 심사 등 권리·의무 판단, 공공 의사결정 등 법정 영역.
- 투명성: 생성형·고영향 AI 사용 사실 사전 고지, 생성물 표시.
- 사업자 책무: 위험관리, 설명 방안(학습용 데이터 개요 포함), 이용자 보호, 사람의 관리·감독, 문서 보관.
- 산출물: 고영향 해당성 검토서, AI 위험관리 계획서, 설명가능성 설계서, 사람 개입 지점 정의서, 고지·표시 화면 설계, 로그 보관 정책.

**확인할 출처(필수)**: 국가법령정보센터에서 제2조·제31조·제34조 원문, 과태료 상한과 계도기간 방침, 최근 개정 여부.

### E2. 금융사가 망분리 환경에서 생성형 AI 상담 지원 시스템을 원한다면 아키텍처와 규제 대응은?

> **참고 링크**: [금융위: 망분리 개선 로드맵(2024.8)](https://www.fsc.go.kr/no010101/82885) · [금융위: 금융권 AI 활용 지원(2024.12)](https://www.fsc.go.kr/no010101/83594) · [금융위: SaaS 망분리 규제 개선(2026.1)](https://www.fsc.go.kr/no010101/86080) · [금융위: AI 관련 망분리 완화(2026.5)](https://www.fsc.go.kr/no010101/86972) · [금융위: 제2차 망분리 긴급 완화(2026.9)](https://www.fsc.go.kr/no010101/87646)

**포인트**: 최신 규제 완화 흐름을 아키텍처에 반영한다.

**핵심**: 개인신용정보를 다루는 업무는 내부망 설치형 모델, 일반 지식 업무는 규제 예외 범위 안의 외부 AI로 나누는 이원 구조를 제안한다.

- 규제 흐름: 망분리 개선 로드맵(2024.8) → 생성형 AI 활용 지원 방안(2024.12) → 이후 SaaS 예외·AI 관련 완화 조치.
- 아키텍처: 내부망 GPU(LLM·임베딩·리랭커) + 권한 인지형 검색 + 입출력 필터(PII 마스킹) + 감사 로그. 외부 연계는 가명처리 게이트웨이 경유.
- AI 기본법: 결과가 대출 심사 등 권리·의무 판단에 쓰이면 고영향 해당 여부 검토.

**확인할 출처(필수)**: 금융위 보도자료에서 각 조치의 날짜와 허용 범위, 2026년 이후 최신 완화 조치.

### E3. 생성형 AI를 구축할 때 개인정보보호법상 쟁점과 대응은?

> **참고 링크**: [개인정보위: 생성형 AI 개인정보 처리 안내서(2025.8)](https://www.pipc.go.kr/np/cop/bbs/selectBoardArticle.do?bbsId=BS074&mCode=C020010000&nttId=11410) · [Wikipedia: Pseudonymization](https://en.wikipedia.org/wiki/Pseudonymization)

**포인트**: 개인정보위 안내서의 구조를 따른다.

**핵심**: 개인정보위 안내서의 수명주기 단계에 맞춰 적법 근거와 안전조치를 체크리스트로 만든다.

- 목적 설정: 의도된 용도와 예견 가능한 오용 정의.
- 학습 데이터: 공개된 개인정보 활용 시 정당한 이익 요건 검토, 가명·익명처리.
- 적용·관리: 입출력 필터, 정보주체 권리 대응, 레드티밍, 개인정보 영향평가(대규모·민감정보).
- 가명정보: 통계·과학적 연구·공익적 기록보존 목적이면 동의 없이 처리 가능하나 재식별 금지.

**확인할 출처(필수)**: 개인정보위 「생성형 AI 개발·활용을 위한 개인정보 처리 안내서」 발행일과 단계 이름.

### E4. 공공기관 RAG 사업에서 CSAP 등급과 데이터 보안 등급을 고려하면 배치를 어떻게 설계하나?

> **참고 링크**: [클라우드컴퓨팅서비스 보안인증에 관한 고시](https://www.law.go.kr/%ED%96%89%EC%A0%95%EA%B7%9C%EC%B9%99/%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C%EC%BB%B4%ED%93%A8%ED%8C%85%EC%84%9C%EB%B9%84%EC%8A%A4%EB%B3%B4%EC%95%88%EC%9D%B8%EC%A6%9D%EC%97%90%EA%B4%80%ED%95%9C%EA%B3%A0%EC%8B%9C)

**포인트**: 데이터 등급별 인프라 배치 판단.

**핵심**: 데이터 중요도에 따라 공개 데이터는 민간 클라우드, 비공개 업무는 중 등급, 민감·행정 내부는 상 등급 또는 자체 구축으로 배치한다.

- 절차: 데이터 분류 → 등급별 인덱스·모델 배치 → 등급 간 이동 통제 → 로그 통합.
- 등급이 섞인 질의는 상위 등급 환경에서 처리하거나 질의 분리.
- 공공 AI 가이드라인의 인프라 선택지(범정부 공통 기반·자체·민간 클라우드)를 근거로 인용.

**확인할 출처(필수)**: CSAP 상·중·하 등급 기준 / 공공부문 초거대 AI 가이드라인 최신판.

### E5. LLM·RAG 시스템의 보안 위협과 대응은?

> **참고 링크**: [OWASP Top 10 for LLM](https://genai.owasp.org/llm-top-10/)

**포인트**: 위협을 지점별 방어로 구조화한다.

**핵심**: 핵심 위협은 간접 프롬프트 인젝션·민감정보 유출·과도한 에이전트 권한이며, 입력·검색·출력·도구 4지점에서 다층 방어한다.

| 지점 | 위협 | 대응 |
| --- | --- | --- |
| 입력 | 직접·간접 프롬프트 인젝션 | 지시와 데이터 분리, 콘텐츠 정화 |
| 검색 | 권한 밖 문서 노출 | ACL 사전 필터, 등급별 인덱스 |
| 출력 | 민감정보 유출, 환각 | PII 마스킹, 근거 인용 강제 |
| 도구 | 과도한 권한, 공급망 | 최소 권한, 승인 기반 실행, 서버 화이트리스트 |

**확인할 출처**: OWASP LLM Top 10 최신판 항목 이름(인용 시 정확한 명칭 사용).

## F. SI 사업 수행·시나리오

이 영역은 검색으로 답이 나오지 않는다. 경험과 판단이 그대로 드러나므로 **실력 차이가 가장 크게 나는 영역**이다.

### F1. AI·데이터 플랫폼 사업의 요구사항 정의는 기존 SI와 어떻게 달라야 하나?

> **참고 링크**: [RAGAS 문서](https://docs.ragas.io/) · [AI 기본법(국가법령정보센터)](https://www.law.go.kr/%EB%B2%95%EB%A0%B9/%EC%9D%B8%EA%B3%B5%EC%A7%80%EB%8A%A5%EB%B0%9C%EC%A0%84%EA%B3%BC%EC%8B%A0%EB%A2%B0%EA%B8%B0%EB%B0%98%EC%A1%B0%EC%84%B1%EB%93%B1%EC%97%90%EA%B4%80%ED%95%9C%EA%B8%B0%EB%B3%B8%EB%B2%95)

**포인트**: 확률적 시스템의 검수 기준을 계약 언어로 바꾼다.

**핵심**: 기능 명세가 아니라 품질 목표(정확도·지연·비용)와 측정 방법으로 요구사항을 정의해야 검수 분쟁을 막는다.

- 확률적 출력: "어떤 골든셋으로 정확도 몇 %"를 계약 단계에서 합의.
- 데이터 의존성: 고객 데이터 품질·제공 범위를 전제조건과 R&R로 명시.
- 지속 개선형: 구축 후 튜닝 기간과 운영 이관 기준 정의.
- 산출물: 유스케이스 정의서, 골든셋 구축 계획, 평가 계획서, 데이터 제공 확약, 위험관리 계획.

### F2. PoC는 성공했는데 본사업에서 성능이 떨어졌다면 원인과 PoC 설계 원칙은?

> **참고 링크**: [Gartner: 생성형 AI PoC 후 포기 예측(2024.7)](https://www.gartner.com/en/newsroom/press-releases/2024-07-29-gartner-predicts-30-percent-of-generative-ai-projects-will-be-abandoned-after-proof-of-concept-by-end-of-2025)

**포인트**: PoC와 운영 조건의 차이.

**핵심**: PoC가 선별된 데이터·소수 사용자·권한 미적용으로 수행됐기 때문이며, PoC는 처음부터 운영 조건의 축소판으로 설계한다.

- 원인: 데이터 규모·다양성 차이, 권한 필터 적용 후 재현율 하락, 동시 사용자 부하, 문서 최신성 관리 부재, 실사용 질문의 모호성.
- 원칙: 실제 데이터 샘플(파싱 어려운 문서 포함), 권한 적용, 현업 질문 골든셋, 성공 기준 사전 합의, 비용·지연 측정, 확장 리스크 목록 산출.

**확인할 출처**: Gartner의 생성형 AI PoC 후 포기 예측(2024.7) 수치.

### F3. Iceberg 표준 채택 같은 아키텍처 결정은 고객에게 어떻게 문서화하고 설득하나?

> **참고 링크**: [ADR(adr.github.io)](https://adr.github.io/) · [Nygard: Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)

**포인트**: 의사결정 근거를 남기는 방식.

**핵심**: ADR(Architecture Decision Record)로 맥락·선택지·평가기준·결정·트레이드오프를 남기고, PoC 수치와 리스크 비용으로 설득한다.

- ADR 구조: 배경, 선택지 3개 이상, 평가기준과 가중치, 결정, 트레이드오프, 재검토 조건.
- 설득 논리: 고객 전략(개방성·종속 회피)과의 정렬, PoC 결과, 5년 TCO, 레퍼런스.
- OSS 판단: 단일 벤더 주도 프로젝트보다 재단(ASF·LF) 거버넌스 프로젝트 우선. 근거 사례는 Greenplum 폐쇄, MinIO 커뮤니티판 중단.

### F4. AI 플랫폼 사업의 비용 산정과 인력 계획은 어떻게 세우나?

> **참고 링크**: [FinOps Foundation](https://www.finops.org/)

**포인트**: 기존 FP 방식으로 잡히지 않는 공수.

**핵심**: 구축, 데이터 준비, GPU 인프라, 운영 튜닝을 분리 산정하고, 데이터·ML/LLM·도메인 전문가·보안 인력을 균형 배치한다.

- 데이터 정제·파싱·골든셋 구축은 공수 비중이 크지만 기능 점수로는 잡히지 않는다.
- GPU: 동시 사용자 × 평균 토큰 × 모델 크기로 추론 용량 산정, 작은 모델 우선 라우팅으로 절감.
- 인력: 현업 SME 투입 확약, LLMOps·평가·보안 핵심 역할은 사내 전문가 풀로 확보.

### F5. AI 플랫폼 구축 사업의 품질·리스크 관리 체계는 어떻게 만드나?

> **참고 링크**: [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) · [ISO/IEC 42001](https://www.iso.org/standard/42001)

**포인트**: 품질 게이트와 리스크 범주의 구조화.

**핵심**: 품질은 데이터 → 검색 → 응답 → 운영 4단계 게이트로, 리스크는 기술·데이터·규제·조직 4범주 레지스터로 관리한다.

| 품질 게이트 | 기준 예시 |
| --- | --- |
| 데이터 | 계약 테스트 통과, 파싱 성공률 |
| 검색 | Recall@k |
| 응답 | 근거성, 정답률 |
| 운영 | P95 지연, 가용성, 비용 |

- 리스크: 기술(OSS 지원 종료, 엔진 호환), 데이터(품질·제공 지연), 규제(고영향 판정·망분리 규정 변경), 조직(현업 참여 부족).

### F6. [시나리오] 제조사 고객이 "공장 설비 매뉴얼·정비 이력으로 정비 지원 에이전트를 만들어 달라"고 한다면 데이터 구조와 검색은 어떻게 설계하나?

> **참고 링크**: [GraphRAG 논문](https://arxiv.org/abs/2404.16130) · [Wikipedia: Knowledge graph](https://en.wikipedia.org/wiki/Knowledge_graph) · [dbt MetricFlow](https://docs.getdbt.com/docs/build/about-metricflow)

**포인트**: 정형·비정형 결합 설계를 실제 업무에 적용한다.

**핵심**: 매뉴얼은 RAG, 정비 이력은 정형 질의, 설비 간 관계는 지식그래프로 나누고 에이전트가 질의 유형별로 라우팅한다.

- 매뉴얼: 장·절·부품 단위 청킹, 모델명·개정판 메타데이터, 도면·표는 구조화 추출.
- 정비 이력: 레이크하우스 테이블 + 시맨틱 레이어(고장률, MTBF 같은 지표 단일 정의).
- 설비 관계: 설비-부품-고장모드 지식그래프로 "같은 부품을 쓰는 설비" 같은 다중 홉 질의 처리.
- 운영: 현장 단말 오프라인 대응, 정비사 피드백을 골든셋으로 축적.

### F7. [시나리오] "AI 도입 전에 데이터부터 정비하라"는 컨설팅 결과를 받은 고객사가 무엇부터 할지 묻는다면, 첫 6개월 실행 계획은?

> **참고 링크**: [Gartner: AI-ready 데이터(2025.2)](https://www.gartner.com/en/newsroom/press-releases/2025-02-26-lack-of-ai-ready-data-puts-ai-projects-at-risk)

**포인트**: 범위를 좁혀 가치를 먼저 낸다.

**핵심**: 전사 데이터 정비가 아니라 가치가 큰 유스케이스 2~3개를 골라, 그 데이터만 AI-ready로 만들고 결과로 확산 근거를 확보한다.

1. 1~2개월: 유스케이스 선정(가치 × 실현성 × 위험), 적합성 매트릭스, 고영향 해당 여부 검토.
2. 2~4개월: 대상 데이터의 계보·품질 측정, 데이터 계약, 시맨틱 정의, 카탈로그 등록.
3. 4~6개월: 파일럿 RAG·분석 서비스, 골든셋 평가, 성과 측정 후 확산 로드맵.

- Why: 전사 정비부터 하면 가치 실현이 늦어져 예산·지지를 잃는다.

## 답변 정리 템플릿과 도식

설계 판단을 슬라이드나 문서 한 장으로 정리할 때 아래 구성을 쓰면 결론과 근거가 한눈에 보인다.

**템플릿 구성**

1. 제목: 질문 + 한 줄 요약
2. 핵심(굵게): "A를 선택한다, 왜냐하면 B" 형태의 결론
3. 왼쪽 60%: 근거 불릿 3~5개 (메커니즘 → 대안 → 리스크 순)
4. 오른쪽 40%: 비교표 또는 박스 도식 1개
5. 하단 1줄: 전제 / 리스크 / KPI, 인용 출처 (예: "근거: 금융위 보도자료 2024.8")

**자주 쓰는 도식 4종**

- **레이어 스택**: 원천 → 수집(CDC) → 저장(OTF) → 카탈로그·거버넌스 → 시맨틱·컨텍스트 → 검색·에이전트 → 소비. 옆에 세로 막대로 보안·운영.
- **RAG 흐름**: 질의 → 라우터 → 하이브리드 검색(ACL 필터) → 리랭크 → 생성 → 출력 필터 → 로그·평가.
- **2×2 매트릭스**: 가치 × 실현성(유스케이스 우선순위), 위험 × 효과(규제 대응).
- **단계 로드맵**: Phase 0 진단 → 1 기반 → 2 파일럿 → 3 확산 → 4 폐기, 단계 사이에 게이트 다이아몬드와 통과 기준.

**답변 점검 3문**

- 핵심만 읽어도 결론이 보이는가?
- 질문의 조건(망분리, 금융, 레거시 등)이 답변에 반영됐는가?
- 수치에 출처와 성격(사실·예측·벤더 주장)이 붙어 있는가?
