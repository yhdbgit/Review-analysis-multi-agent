# 상품 리뷰 분석 에이전트 시스템 (Review Analysis Agent System)
> **"정성적인 고객 리뷰를 정량적 비즈니스 인사이트로 전환하기 위해, 멀티 에이전트 워크플로우와 Rule-based 및 LLM-judge 하이브리드 품질 검증 파이프라인을 구축한 프로젝트입니다."**

**[유혁재의 에이전트 시스템의 품질 보장 레포트](./MY_ROLE.md)**

## 1. 프로젝트 개요
* **목표**: 비정형 상품 리뷰 데이터를 속성(Aspect) 및 감성(Sentiment) 단위로 일괄 분석하여 고객의 Needs를 정량적 지표로 도출
* **핵심 과제**: LLM 에이전트 분석 결과의 신뢰성 검증 및 대시보드 시각화
* **주요 성과**: 
    - **하이브리드 품질 검증(Hybrid QA)**: Rule-based(정밀 검증) + LLM-judge(맥락 평가) 결합을 통한 분석 품질 확보
    - **워크플로우 제어**: LangGraph 기반의 피드백 루프 및 재시도 정책(`reason_code`) 설계
    - **운영 자동화(LLMOps)**: LangSmith 기반의 실행 Trace 추적 및 분석 결과 데이터베이스 일괄 처리(Batch Process)

---

## 2. 시스템 아키텍처 (워크플로우)

입력값과 처리 로직, 최종 출력값을 사전 정의하여 시스템의 안정성을 확보하였습니다.

| 단계 | 입력(Input) | 핵심 처리 로직(Processing) | 출력(Output) |
| :--- | :--- | :--- | :--- |
| **Start** | 리뷰 원문 | 초기 데이터 로드 | - |
| **Supervisor** | 분석/평가 결과 | 프로세스 전체 흐름 제어 및 재시도 정책 결정 | 다음 Agent 지시 |
| **Analyzer** | 리뷰 원문 | Aspect-Based Sentiment Analysis | JSON 분석 결과 |
| **Critic** | 분석 결과 | Rule-based 검증 및 LLM-judge 맥락 평가 | 검증 판정(Verdict) |

---

## 3. 핵심 기술 설계 (Engineering Insight)

### ① 검증 파이프라인 고도화 (Hybrid Approach)
* **Rule-based Logic (본인 담당)**: 속성 범위(Aspect Scope), 출력 JSON 스키마 형식, 데이터 정합성 등 **'정량적 규칙 품질'**을 강제하여 시스템의 하위 안정성을 확보했습니다.
* **LLM-judge Logic (팀원 담당)**: 리뷰의 의미적 모호함과 감성 분석의 적절성 등 **'정성적 맥락 품질'**을 LLM이 직접 평가하여 판단하도록 설계했습니다.
* **설계 철학**: 규칙 기반의 '엄격한 제약'과 LLM의 '유연한 맥락 판단'을 결합하여, 비즈니스 규칙을 지키면서도 분석 결과의 자연스러움을 모두 확보했습니다.

### ② LangGraph 기반 Supervisor 제어 루프
* **상태 제어**: Critic 에이전트가 부적합(Non-Conformity)으로 판정할 경우, `reason_code`를 기반으로 Analyzer에게 구체적인 수정 지침(`repair_directive`)을 전달하는 **자기 주도적 수정(Self-Correction) 파이프라인**을 설계했습니다.
* **운영 효율화**: 재시도 횟수 관리와 에러 코드 시스템을 도입하여 무한 루프를 방지하고, LLMOps 관점에서 운영 리소스를 최적화했습니다.

---

## 🛠️ 주요 기술 스택
* **LLM**: ChatOpenAI
* **Framework**: LangGraph, LangChain
* **Ops/Eval**: LangSmith, LLMOps
* **Frontend/Storage**: Streamlit, SQLite
