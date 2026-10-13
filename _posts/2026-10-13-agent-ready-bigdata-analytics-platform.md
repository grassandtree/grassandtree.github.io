---
title: "Agent-Ready 빅데이터분석플랫폼 구성요소 설명"
layout: single
toc: true
published: true
categories: 데이터플랫폼
tags: [AI, 에이전트, 온톨로지, 데이터플랫폼, text-to-SQL]
---

![Agent Ready 빅데이터플랫폼 그림](/files/2026-10-13/agent_ready_bigdataplatform.png)

> 일반론과 기술 선택 기준은 [엔터프라이즈 AI를 위한 차세대 데이터 구조…]({% post_url 2026-10-13-enterprise-ai-data-platform-overview %})와 [AI 기반 엔터프라이즈 데이터 구조·검색·플랫폼 전환 설계]({% post_url 2026-10-13-ai-enterprise-data-platform-migration-design %})를 참고하세요. 이 글은 그 원칙을 한 현장의 구조에 적용한 설명입니다.
>
> 링크 접속 확인일: 2026-10-13

## 1. 개요

> **참고 링크**: [Model Context Protocol](https://modelcontextprotocol.io/) · [CQRS(Fowler)](https://martinfowler.com/bliki/CQRS.html) · [Wikipedia: Ontology](https://en.wikipedia.org/wiki/Ontology_%28information_science%29) · [Wikipedia: Intelligent agent](https://en.wikipedia.org/wiki/Intelligent_agent)

본 구조는 AI 에이전트가 기업의 데이터와 지식을 안전하고 정확하게 활용할 수 있도록, 현행 빅데이터분석플랫폼 위에 에이전트 전용 계층을 추가하는 것을 목표로 합니다. 그림은 위에서 아래로 Agent Layer, Agent Access·Tool Layer, AX 마트와 Ontology Layer, 빅데이터분석플랫폼, 원천 시스템 순으로 구성되며, 오른쪽에는 전 레이어를 관통하는 수직 축이 있습니다.

설계의 핵심 원칙은 다음 네 가지입니다.

| 원칙 | 내용 |
|---|---|
| 부하 격리 | 에이전트의 질의가 빅데이터분석플랫폼에 부하를 주지 않도록 별도의 경량 마트(AX 마트)를 둡니다. |
| 정의 단일화 | 지표와 엔티티의 정의는 Ontology Layer 한 곳에만 두고, 마트 가공과 질의가 이를 참조합니다. |
| 단일 접근 경로 | 에이전트는 Agent Access·Tool Layer를 통해서만 데이터와 지식에 접근합니다. |
| 사람의 승인 | 지식은 자동으로 확정되지 않으며, 검증과 승인을 거쳐 반영됩니다. |

### 범례

| 표기 | 의미 |
|---|---|
| 회색 박스 | 현행 자산 |
| 파란색 박스 | 신규 구축 (이번 과제 범위) |
| 초록색 박스 | Ontology Layer (이번 과제 범위) |
| 노란색 기둥 | 전 레이어를 관통하는 수직 축 |
| 회색 점선 박스 | 이번 과제 범위 외 (제조운영 Agent 개발 과제 또는 후속 과제) |
| 진한 실선 화살표 | 주 흐름 |
| 점선 화살표 | 정의 참조, 피드백 |
| 연회색 실선 화살표 | 범위 외 흐름 |

### 이번 과제 범위 요약

| 레이어 | 이번 과제 범위 | 범위 외 |
|---|---|---|
| Agent Layer | 없음 | 오케스트레이터, 에이전트들 |
| Agent Access·Tool Layer | 시맨틱 질의(text-to-SQL), 지식 검색 | 도구 게이트웨이(MCP), 정형 질의 도구, 가드레일 |
| AX 마트 | 통합 디멘전, 검사 결과(요약), 설비 파라미터(요약) | 지표, LOT·셀 추적 등 |
| Ontology Layer | 전체 | 없음 |
| 빅데이터분석플랫폼 | AX 마트 가공 | 없음 (그 외는 현행 자산) |

## 2. 레이어별 설명

### 2.1 Agent Layer (범위 외)

> **참고 링크**: [Wikipedia: Intelligent agent](https://en.wikipedia.org/wiki/Intelligent_agent) · [Wikipedia: Orchestration](https://en.wikipedia.org/wiki/Orchestration_%28computing%29) · [Model Context Protocol](https://modelcontextprotocol.io/)

**역할**: 사용자의 질문이나 업무 요청을 받아 도구를 호출하고, 결과를 종합하여 답변하거나 작업을 수행합니다.

| 박스 | 설명 |
|---|---|
| 오케스트레이터 | 요청을 해석하여 적절한 에이전트에 배분하고, 여러 에이전트의 결과를 조율합니다. |
| 에이전트들 | 도메인별 업무 에이전트입니다. 구체적인 구성은 제조운영 Agent 개발 과제에서 정해집니다. |

**설계 이유**: 어떤 에이전트가 도입될지 확정되지 않은 상황이므로, 특정 에이전트를 명시하지 않고 하위 계층이 어떤 에이전트든 동일한 방식으로 지원하는 구조를 보여주는 데 집중합니다. 본 과제의 목적은 에이전트 자체가 아니라, 에이전트가 사용할 데이터와 지식 기반을 준비하는 데 있습니다.

### 2.2 Agent Access · Tool Layer

> **참고 링크**: [MCP 공식 사이트](https://modelcontextprotocol.io/) · [Spider 2.0 논문](https://arxiv.org/abs/2411.07763) · [Spider 2.0 리더보드](https://spider2-sql.github.io/) · [OWASP: SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) · [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) · [Wikipedia: Prepared statement](https://en.wikipedia.org/wiki/Prepared_statement) · [Wikipedia: SQL injection](https://en.wikipedia.org/wiki/SQL_injection)

**역할**: 에이전트가 데이터와 지식에 접근하는 유일한 창구입니다. 도구를 등록하고 호출을 중개하며, 권한과 질의 제한을 적용합니다.

| 박스 | 범위 | 설명 |
|---|---|---|
| 도구 게이트웨이 ([MCP](https://modelcontextprotocol.io/)) | 범위 외 | 도구의 이름, 설명, 입출력 스키마, 권한 정책을 등록하고 호출을 라우팅합니다. |
| 정형 질의 도구 (파라미터화 SQL) | 범위 외 | 사전에 검증된 SQL에 기간, LOT, 설비 등의 파라미터만 받아 실행합니다. |
| 시맨틱 질의 · text-to-SQL | 신규 | Data Ontology의 정의를 바탕으로 자연어 질문을 AX 마트에 대한 질의로 변환합니다. |
| 지식 검색 | 신규 | Ontology Layer의 엔티티, 규칙, 예외, 분석 방법론 등을 검색합니다. |
| 가드레일 | 범위 외 | 사용자와 에이전트의 권한을 확인하고, 행 수 제한, 비용 상한, 타임아웃 등 질의 제한을 적용합니다. |

**설계 이유**: 에이전트는 사람 분석가와 달리 질의 패턴을 예측하기 어렵습니다. 모든 접근이 이 계층을 거치도록 하면 권한 통제와 부하 제어를 한 지점에서 일관되게 적용할 수 있습니다.

**정형 질의 도구와 text-to-SQL의 관계**: 두 도구는 서로를 보완합니다. 자주 묻는 질문은 결과가 항상 같고 성능과 감사 추적이 보장되는 정형 질의 도구로, 예측하기 어려운 나머지 질문은 text-to-SQL로 처리하는 구성이 일반적입니다. 정형 질의 도구의 SQL은 지표 계산식을 직접 포함하지 않고 Data Ontology의 정의를 따라야 정의 이중화를 막을 수 있으며, 파라미터는 바인딩 변수로 받아야 [SQL 인젝션](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)을 방지할 수 있습니다.

**유의 사항**

- 그림에서는 도구를 이 계층에 표시하지만, 게이트웨이가 보관하는 것은 등록 정보와 라우팅뿐입니다. 실제 도구(MCP 서버)는 각자가 다루는 데이터나 지식 저장소 가까이에서 실행됩니다.
- 도구 게이트웨이와 가드레일은 범위 외이지만 시맨틱 질의와 지식 검색은 이번 과제 범위입니다. 따라서 이번 과제에서 구축하는 도구가 어떤 인터페이스로 노출되는지, 그리고 이번 과제 범위에서 권한 통제를 어떤 방식으로 적용하는지를 별도로 정의해야 합니다. 임시 방안으로는 두 도구가 AX 마트의 허용 테이블에 대한 읽기 전용 계정으로만 질의를 실행하고, 도구 내부에서 행 수와 타임아웃 상한을 거는 구성을 검토할 수 있습니다.

### 2.3 AX 마트 (신규 · 경량)

> **참고 링크**: [CQRS(Fowler)](https://martinfowler.com/bliki/CQRS.html) · [Spider 2.0 논문](https://arxiv.org/abs/2411.07763) · [Wikipedia: Data mart](https://en.wikipedia.org/wiki/Data_mart) · [Wikipedia: Domain model](https://en.wikipedia.org/wiki/Domain_model)

**역할**: 에이전트의 질의를 처리하는 전용 읽기 저장소입니다. 빅데이터분석플랫폼의 AX 마트 가공에서 만든 데이터를 증분 적재하여 에이전트가 사용하기 좋은 형태로 제공합니다.

| 박스 | 범위 | 설명 |
|---|---|---|
| 통합 디멘전 | 신규 | 제품, 공정, 설비, 자재 등 여러 데이터가 공통으로 참조하는 기준 정보입니다. |
| 검사 결과 (요약) | 신규 | 검사 공정의 결과와 판정 정보를 에이전트가 사용하기 좋은 단위로 요약한 데이터입니다. |
| 설비 파라미터 (요약) | 신규 | 설비 파라미터를 원본 그대로 두지 않고, 분석 단위로 요약한 데이터입니다. |
| 지표, LOT · 셀 추적 등 | 범위 외 | 미리 계산한 지표와, 원자재 LOT에서 셀까지 이어지는 제품 추적 정보입니다. |

**엔티티 중심 스키마**: 마트의 테이블을 빅데이터분석플랫폼의 테이블 구조가 아니라 Data Ontology의 엔티티 모델 단위로 설계합니다. 그 이유는 세 가지입니다. 첫째, 향후 적재 경로가 바뀌어도 마트의 규격을 유지할 수 있습니다. 둘째, 업무 개념과 테이블이 대응하여 조인이 줄어들어 text-to-SQL의 정확도가 높아집니다. 기업 규모의 복잡한 스키마에서 text-to-SQL 정확도가 크게 떨어진다는 점은 [Spider 2.0](https://arxiv.org/abs/2411.07763) 등의 벤치마크에서도 확인됩니다(발표 당시 최고 성공률 21.3%, 단순한 스키마의 Spider 1.0에서는 91.2%). 셋째, 빅데이터분석플랫폼의 테이블이 변경되어도 영향이 가공 로직에서 끝나고 에이전트까지 전파되지 않습니다.

**적재 시점 정보**: AX 마트는 빅데이터분석플랫폼의 배치를 거쳐 한 번 더 적재되므로 최신성이 빅데이터분석플랫폼의 주기보다 좋아질 수 없습니다. 에이전트는 사람과 달리 데이터가 오래되었을 가능성을 스스로 의심하지 않으므로, 테이블별 기준 시각을 함께 두어 도구가 질의 결과와 함께 기준 시각을 반환하도록 합니다. 이를 통해 에이전트는 "09:30 기준입니다"와 같이 답하거나, 기준을 초과한 경우 경고할 수 있습니다.

**요약 데이터를 두는 이유**: FDC 등 설비·공정 데이터의 원본은 양이 매우 많습니다. 원본을 마트에 그대로 두면 경량 마트의 취지에 맞지 않고 에이전트의 질의 비용도 커지므로, 분석 단위로 요약한 특징량만 두고 원본은 빅데이터분석플랫폼에 남깁니다.

**지표에 대한 유의 사항**: 지표의 물리화는 범위 외이므로, 이번 과제 범위에서는 text-to-SQL이 Data Ontology의 지표 모델을 질의 시점에 참조하여 지표를 계산합니다. 후속 과제에서 지표가 AX 마트에 물리화되면, 동일한 정의를 따르는 계산 결과를 조회하는 방식으로 전환할 수 있습니다.

**설계 이유**: 소프트웨어 아키텍처의 [CQRS 패턴](https://martinfowler.com/bliki/CQRS.html)과 같이 정본 처리(빅데이터분석플랫폼)와 읽기 모델(AX 마트)을 분리하여, 에이전트의 질의 부하가 빅데이터분석플랫폼의 운영에 영향을 주지 않도록 합니다.

### 2.4 Ontology Layer

> **참고 링크**: [W3C OWL 2](https://www.w3.org/TR/owl2-overview/) · [W3C SHACL(제약 검증)](https://www.w3.org/TR/shacl/) · [dbt MetricFlow(지표 모델 예)](https://docs.getdbt.com/docs/build/about-metricflow) · [Wikipedia: Ontology](https://en.wikipedia.org/wiki/Ontology_%28information_science%29) · [Wikipedia: Semantic layer](https://en.wikipedia.org/wiki/Semantic_layer) · [Wikipedia: Entity–relationship model](https://en.wikipedia.org/wiki/Entity%E2%80%93relationship_model) · 상세: [시맨틱 레이어 vs 온톨로지]({% post_url 2026-10-13-ai-enterprise-data-platform-migration-design %})

**역할**: 에이전트가 데이터를 올바르게 계산하고, 개념을 정확히 이해하며, 결과를 적절히 해석하는 데 필요한 지식을 관리합니다.

#### Ontology Studio (지식 등록 포털 : 단일 창구)

지식을 입력하는 단일 창구입니다. 창구는 하나이지만, 입력 경로는 지식의 성격에 따라 나뉩니다.

| 경로 | 대상 | 방식 | 검증 |
|---|---|---|---|
| LLM 구조화 → 관리자 승인 | Domain Ontology | 현업이 자유롭게 서술한 내용을 LLM이 구조화하고, 관리자가 승인합니다. | 사람의 판단 |
| 정형 입력 → 자동 검증 → 승인 | Data Ontology | 정해진 형식의 화면이나 정의 코드로 입력합니다. | 기계적 검증 후 승인 |

**설계 이유**: Data Ontology는 맞고 틀림을 기계가 판정할 수 있는 구조이므로, 입력 단계부터 형식을 강제해야 오류가 누적되지 않습니다. 지표 모델은 SQL 컴파일과 기존 수치와의 대사로, 엔티티 모델은 제약 위반, 중복 엔티티, 매핑 누락 검사로 검증합니다. 반면 Domain Ontology는 서술형이므로 형식을 먼저 강제하면 현업의 입력 자체가 어려워집니다. 따라서 자유롭게 입력받고 LLM이 구조화하는 방식이 적합합니다. LLM은 Data Ontology에서도 기존 분석 질의나 원천 스키마로부터 초안을 생성하는 데 활용할 수 있으며, 확정은 정형 경로를 따릅니다.

#### Domain Ontology (지식 · 컨텍스트)

| 항목 | 설명 |
|---|---|
| 규칙 · 예외 | 업무 규칙과 그 예외입니다. 예: "화성 설비 교정 직후 3일간의 용량 측정값은 추세 판단에서 제외합니다." |
| 신뢰 판단 규칙 | 메타데이터를 근거로 데이터의 신뢰 여부를 판단하는 규칙입니다. 예: "AX 마트의 기준 시각이 24시간을 초과하면 답변에 기준 시각을 명시하고 경고합니다." |
| 분석 방법론 | 특정 분석을 수행하는 표준 절차와 그 전제입니다. |
| 인과관계 | 공정 파라미터와 품질 특성 사이의 알려진 인과관계입니다. 불량 원인 분석의 근거가 됩니다. |

#### Data Ontology

| 칸 | 설명 |
|---|---|
| 지표 모델 | 엔티티 모델 위에 지표 정의, 조인 경로, 접근 정책을 정의합니다. |
| 엔티티 모델 | LOT, 셀, 설비, 공정, 검사 결과 등 엔티티와 그 관계, 제약을 정의합니다. |

**설계 이유**: 지표를 정의하는 도구도 자체적으로 엔티티를 정의하는 경우가 많아, 두 모델을 따로 운영하면 엔티티가 두 곳에서 정의되는 이중화 위험이 생깁니다. 엔티티는 엔티티 모델에서만 정의하고 지표 모델은 그 위에 지표를 얹는 구조로 두어, 한 박스 안에서 기반과 상부의 관계가 드러나도록 표현했습니다. 두 모델을 한 박스 안에서 구분한 것은 검증 방식(실행 검증과 논리 검증)과 관리 주체(데이터팀과 도메인 아키텍트)가 다르기 때문입니다.

#### Domain Ontology와 Data Ontology를 구분하는 이유

| 구분 | 답하는 질문 | 변화 속도 | 관리 주체 |
|---|---|---|---|
| 지표 모델 | 어떻게 계산하는가 | 중간 | 데이터팀 |
| 엔티티 모델 | 무엇이 무엇과 어떤 관계인가 | 가장 느림 | 도메인 아키텍트 |
| Domain Ontology | 결과를 어떻게 해석하고 믿어야 하는가 | 가장 빠름 | 현업 전문가 |

영역을 구분하면 한 영역의 변경이 다른 영역의 재검토로 번지지 않으며, 에이전트가 잘못된 답을 냈을 때 그 원인이 계산 오류, 개념 오해, 해석 오류 중 어디에 있는지 추적할 수 있습니다.

### 2.5 빅데이터분석플랫폼

> **참고 링크**: [Wikipedia: Big data](https://en.wikipedia.org/wiki/Big_data) · [Wikipedia: ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) · [Wikipedia: Data warehouse](https://en.wikipedia.org/wiki/Data_warehouse)

**역할**: 원천 데이터를 수집, 정제, 통합하는 현행 플랫폼입니다. 신규 구조와 한동안 공존하며, 정제 로직의 원천이자 AX 마트를 가공하는 곳입니다.

| 박스 | 범위 | 설명 |
|---|---|---|
| 수집 | 현행 | 원천 시스템의 데이터를 수집합니다. |
| 정제 · 통합 | 현행 | 데이터를 정제하고 통합합니다. |
| AX 마트 가공 | 신규 | 정제·통합된 데이터를 Data Ontology의 정의에 따라 AX 마트 형태로 가공합니다. |
| 데이터서비스, 분석도구, 분석워크플로우 | 현행 | 기존 사용자가 이용하는 데이터서비스와 분석 환경입니다. |

**AX 마트 가공을 빅데이터분석플랫폼에서 수행하는 이유**: 정제 로직이 한 곳에만 존재하므로 에이전트의 답과 기존 분석 결과가 달라지는 위험이 가장 낮습니다. 이 방식은 부하 격리 원칙과도 충돌하지 않습니다. 격리하려는 대상은 예측하기 어려운 에이전트의 질의 부하이며, 마트 가공은 일정이 정해진 배치 부하로서 빅데이터분석플랫폼이 원래 처리하던 종류의 작업이기 때문입니다.

**유의 사항**: 이번 과제 범위에서 데이터서비스, 분석도구, 분석워크플로우는 Data Ontology의 정의를 참조하지 않습니다. 따라서 같은 지표라도 에이전트의 답과 기존 분석 결과의 수치가 다를 수 있으며, 후속 과제에서 기존 분석 환경도 동일한 정의를 참조하도록 확장하는 것을 검토할 수 있습니다.

### 2.6 원천 시스템

> **참고 링크**: [Wikipedia: MES](https://en.wikipedia.org/wiki/Manufacturing_execution_system) · [Wikipedia: SPC](https://en.wikipedia.org/wiki/Statistical_process_control) · [Wikipedia: Fault detection and isolation](https://en.wikipedia.org/wiki/Fault_detection_and_isolation)

**역할**: 제조 현장과 업무 시스템에서 데이터가 발생하는 곳입니다.

| 박스 | 설명 |
|---|---|
| [MES](https://en.wikipedia.org/wiki/Manufacturing_execution_system) | 생산 실행 시스템(Manufacturing Execution System)으로, 작업 실적과 LOT 이력을 관리합니다. |
| [SPC](https://en.wikipedia.org/wiki/Statistical_process_control) | 통계적 공정 관리(Statistical Process Control) 시스템으로, 공정 품질 특성의 관리도와 이상 여부를 관리합니다. |
| [FDC](https://en.wikipedia.org/wiki/Fault_detection_and_isolation) | 설비 이상 감지·분류(Fault Detection and Classification) 시스템으로, 설비 센서 데이터를 수집하고 이상을 감지합니다. |
| RMS | 레시피 관리 시스템(Recipe Management System)으로, 설비별 공정 조건(레시피)을 관리합니다. |
| GDW | (설명 보완 필요) |

에이전트는 원천 시스템에 직접 접근하지 않습니다.

## 3. 수직 관통 축

> **참고 링크**: [OpenLineage](https://openlineage.io/) · [DAMA-DMBOK](https://www.dama.org/cpages/body-of-knowledge) · [Wikipedia: Metadata](https://en.wikipedia.org/wiki/Metadata) · [Wikipedia: Data lineage](https://en.wikipedia.org/wiki/Data_lineage) · [Wikipedia: Data governance](https://en.wikipedia.org/wiki/Data_governance) · [Wikipedia: Observability](https://en.wikipedia.org/wiki/Observability_%28software%29) · [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)

| 축 | 설명 |
|---|---|
| 메타데이터 · 데이터리니지 | 모든 레이어에서 자동으로 수집되는 사실 기록입니다. 기술 메타데이터(스키마, 데이터리니지)와 운영 메타데이터(적재 시각, 건수, 실행 결과)를 포함합니다. |
| 거버넌스 · 보안 | 데이터와 지식의 소유, 접근 권한, 에이전트의 신원과 권한을 관리합니다. |
| 관측 · 평가 | 적재 지연과 실패, 에이전트의 도구 호출과 답변 품질을 추적하고 평가합니다. |

### 메타데이터와 Ontology Layer의 관계

두 영역은 **기록과 해석의 관계**입니다. 수직 축의 메타데이터는 시스템이 관찰한 사실을 자동으로 수집한 기록이고, Domain Ontology의 신뢰 판단 규칙은 그 기록을 에이전트가 판단에 사용할 수 있도록 해석한 지식입니다. 그림의 "메타데이터 참조" 점선이 이 관계를 나타냅니다.

| 구분 | 메타데이터 · 데이터리니지 | Ontology Layer |
|---|---|---|
| 생성 방식 | 파이프라인 실행, SQL 파싱 등에서 자동 수집 | 검증과 승인을 거쳐 등록 |
| 변경 빈도 | 배치가 실행될 때마다 갱신 | 필요할 때 드물게 갱신 |
| 범위 | 전 레이어 | 에이전트 판단에 필요한 범위 |
| 근거 | 시스템이 관찰한 사실 | 도메인 전문가의 합의 |

두 영역을 분리하는 가장 큰 이유는 생명주기가 다르기 때문입니다. 자동 수집되는 데이터리니지를 승인 대상에 포함하면 배치가 실행될 때마다 승인 대기가 쌓이고, 반대로 사람의 판단이 들어간 해석을 자동 수집 영역에 두면 사실과 해석의 신뢰 수준이 구분되지 않습니다. [DMBOK](https://www.dama.org/cpages/body-of-knowledge)의 분류로 보면 기술·운영 메타데이터는 수직 축에, 비즈니스 메타데이터 중 해석과 규칙에 해당하는 부분은 Ontology Layer에 속합니다.

## 4. 흐름 설명

> **참고 링크**: [Wikipedia: ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) · [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

### 4.1 주 흐름 (진한 실선)

| 흐름 | 설명 |
|---|---|
| 원천 시스템 → 수집 | 원천 데이터를 빅데이터분석플랫폼으로 수집합니다. |
| 수집 → 정제·통합 | 수집한 데이터를 정제하고 통합합니다. |
| 정제·통합 → 데이터서비스, 분석도구, 분석워크플로우 | 기존 분석 환경에 데이터를 제공합니다. |
| 정제·통합 → AX 마트 가공 | 정제·통합된 데이터를 AX 마트 형태로 가공합니다. |
| AX 마트 가공 → AX 마트 (증분 적재) | 가공이 완료된 데이터를 AX 마트에 증분 적재합니다. |
| AX 마트 → Agent Access·Tool Layer (데이터 조회) | 도구가 AX 마트의 데이터를 조회합니다. |
| Ontology Layer → Agent Access·Tool Layer (정의·지식 제공) | 도구가 Data Ontology의 정의와 Domain Ontology의 지식을 조회합니다. |
| Agent Access·Tool Layer ↔ Agent Layer (도구 호출 / 결과) | 에이전트가 도구를 호출하고 결과를 받습니다. |

### 4.2 정의 참조 · 피드백 (점선)

| 흐름 | 설명 |
|---|---|
| Ontology Layer → AX 마트 가공 (정의 참조) | 엔티티 모델이 AX 마트 스키마의 기준이 되고, 지표 모델의 정의가 가공 로직에 반영됩니다. |
| Agent Layer → Ontology Studio (지식 후보 제안) | 에이전트가 작업 중 발견한 지식을 후보로 제안하며, Ontology Studio의 검증과 승인을 거쳐 반영됩니다. |
| 메타데이터 · 데이터리니지 → Ontology Layer (메타데이터 참조) | 신뢰 판단 규칙이 메타데이터를 근거로 데이터의 신뢰 여부를 판단합니다. |

### 4.3 범위 외 흐름 (연회색 실선)

| 흐름 | 설명 |
|---|---|
| 빅데이터분석플랫폼 → 도구 게이트웨이 (RAW 데이터 조회) | AX 마트에 없는 원본 데이터를 에이전트가 제한적으로 조회하는 경로입니다. |

**유의 사항**: 이 경로는 에이전트의 질의가 빅데이터분석플랫폼에 직접 도달하므로 부하 격리 원칙의 예외에 해당합니다. 도입 시에는 가드레일을 통한 조회 건수·시간대 제한, 승인된 도구에 한정한 허용 등 적용 조건을 함께 정의해야 합니다. 반복적으로 필요한 원본 데이터는 AX 마트에 요약 형태로 추가하는 것을 우선 검토합니다.

## 5. 용어 구분

> **참고 링크**: 아래 용어 중 일반 용어는 Wikipedia 링크를 달았습니다. Domain/Data Ontology 등은 이 문서의 한정된 의미입니다.

| 용어 | 의미 | 위치 |
|---|---|---|
| Domain Ontology ([Ontology](https://en.wikipedia.org/wiki/Ontology_%28information_science%29)) | 본 문서에서는 규칙, 예외, 신뢰 판단 규칙, 분석 방법론, 인과관계 등 도메인의 해석 지식을 의미합니다. | Ontology Layer |
| Data Ontology | 본 문서에서는 지표 모델과 엔티티 모델을 합한 데이터의 의미 정의를 의미합니다. | Ontology Layer |
| LOT · 셀 추적 | 원자재 LOT에서 셀까지 이어지는 제품의 물리적 추적 정보입니다. 업무 데이터입니다. | AX 마트 |
| [데이터리니지](https://en.wikipedia.org/wiki/Data_lineage) | 데이터가 어느 시스템에서 발생하여 어떤 변환을 거쳤는지에 대한 기록입니다. 메타데이터입니다. | 수직 관통 축 |
| [메타데이터](https://en.wikipedia.org/wiki/Metadata) | 시스템이 자동으로 수집한 데이터에 대한 사실 기록입니다. | 수직 관통 축 |
| 적재 시점 정보 | AX 마트 테이블별 데이터의 기준 시각입니다. 도구가 질의 결과와 함께 반환합니다. | AX 마트 |
