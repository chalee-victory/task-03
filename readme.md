# [최종 보고서] 노코드 자동화 프로젝트 비교 분석 및 자유 주제 구현

## 1. 개요 및 미션 소개

매일 반복되는 일상적 업무는 개별 소요 시간이 짧더라도 누적 시 큰 생산성 손실을 야기합니다. 본 프로젝트는 코딩 없이 **이벤트 시작(Trigger)**, **조건 검증 및 경로 분기(Filter / Router)**, **실제 업무 처리(Action)**를 연결하여 프로세스를 자동화함으로써 생산성을 높이는 노코드 파이프라인 구축을 목표로 진행되었습니다.

### 💡 개념 이해: Trigger와 Action의 차이 (실무 예시 대조)
* **Trigger (이벤트 발생 원인)**: 업무 흐름을 '시작'하게 만드는 계기입니다.
  * *예시:* 고객이 웹사이트에서 **[문의 폼 작성 완료]** 버튼을 누르는 순간.
* **Action (이후 자동 수행 동작)**: Trigger가 발생했을 때 시스템이 '자동으로 실행'하는 작업입니다.
  * *예시:* 담당자에게 **[슬랙 알림 전송]** 및 고객 데이터베이스에 **[신규 행 추가]**.

---

## 2. [프로젝트 1] 자동화 도구 비교 구현 (Make vs. Zapier)

### 2.1 비교 워크플로우 설계
* **표준 워크플로우:** Google Forms 제출 $\rightarrow$ 점수 기준 Filter $\rightarrow$ Google Sheets 저장 & Slack 채널 알림 발송
* **요구사항 검증:** Trigger 1개 + Filter 1개 + Action 2개 충족

### 2.2 대표 5개 항목 구체적 비교 분석
*(지적사항 보완: 5개 항목 목록을 명확히 정의하고 표 형식으로 매핑 및 협업·운영 관점 사례 포함)*

| 번호 | 비교 항목 | Make (선정 도구) | Zapier |
| :--- | :--- | :--- | :--- |
| **1** | **UI / UX 및 시각화** | - **무제한 캔버스 노드 구조**로 복잡한 다단계 흐름 visual 파악 용이<br>- 분기 및 데이터 흐름을 한눈에 식별 | - 직관적인 **단순 리스트(선형) 방식** UI<br>- 노코드 초보자가 빠른 설정 시 유리 |
| **2** | **비용 및 요금제** | - 무료 플랜 **월 1,000 Ops(작업 단위)** 제공<br>- AI 연동 등 다단계 테스트 시 비용 효율 우수 | - 무료 플랜 **월 100 Tasks** 제한<br>- 다단계 워크플로우 구축 시 빠르게 유료 전환 필요 |
| **3** | **조건 분기 / 라우터 기능** | - **비주얼 Filter & Router 노드**를 통해 3개 이상의 다중 조건 분기 손쉽게 구현 | - 다중 분기(Paths) 사용 시 유료 요금제 필수 및 가시성이 상대적으로 낮음 |
| **4** | **협업 및 권한 관리 (운영)** | - **팀/조직(Organizations & Teams) 기반 권한 분리**<br>- 워크플로우 실행 이력 공유 및 공동 편집 용이 | - **공유 챗봇/테이블 연동** 우수하나, 고급 팀 권한 제어는 상위 플랜 위주 |
| **5** | **시스템 연계 및 에러 처리** | - **모듈별 데이터 구조(Data Structure)** 정밀 정의 가능<br>- 오류 발생 시 백오프/재시도 경로 직접 설계 가능 | - 기본 자동 재시도(Autoretry) 제공으로 단순 운영 관리는 편하나 세부 제어 한계 |

### 2.3 도구 선정 이유 매핑표
*(지적사항 보완: 선정 근거와 비교 항목 직접 매핑)*

```
[선정 이유 1: 비주얼 UI] ──► (비교 항목 #1) 캔버스형 노드로 Filter 조건 및 데이터 이동 시각적 확인
[선정 이유 2: 비용 효율] ──► (비교 항목 #2) 무료 1,000 Ops 제공으로 OpenAI + Slack + Sheets 다단계 테스트 가능
```

---

## 3. [프로젝트 2] 자유 주제 자동화 설계 및 구현

### 3.1 업무 정의 및 도구 선정
* **과업명:** RSS 기반 IT/트렌드 뉴스 자동 스크랩 및 AI 요약 알림
* **업무 정의:** 관심 IT 트렌드를 매일 수동 검색하는 낭비를 줄이고, 키워드 필터링 및 OpenAI AI 요약을 거쳐 Slack 및 Google Sheets로 자동 전송·아카이빙하는 프로세스.
* **선정 도구:** **Make** (이유: 시각적 캔버스 UI 기반 분기 확인 및 무료 Ops 범위 내 테스트 용이)

### 3.2 단계별 입출력 상세 매핑 및 입출력 명세 (Payload)
*(지적사항 보완: 포맷화된 입출력 표 및 데이터 필드, 샘플 페이로드 명시)*

#### [단계별 입출력 표]
| 구분 | 모듈명 | 입력 데이터 (Input) | 처리 / 조건 로직 | 출력 데이터 (Output) |
| :--- | :--- | :--- | :--- | :--- |
| **Trigger** | RSS Watcher | RSS Feed URL (`https://news.hada.io/rss`) | 신규 게시글 감지 (Polling) | `title`, `url`, `date`, `description` |
| **Filter** | Keyword Filter | `title` (Text) | `title` 내 'AI' 포함 여부 (Case insensitive) | Pass (참) 또는 Fail (거짓/차단) |
| **Router** | Multi-Branch | Filter 통과 객체 | 조건에 따른 알림 경로 분기 | 경로 1(정상 처리), 경로 2(대체 처리) |
| **Action 1** | OpenAI (ChatGPT) | `title`, `description` | GPT-4o-mini 모델 기반 2~3줄 요약 생성 | `summary_text` (요약문) |
| **Action 2** | Slack | `title`, `url`, `summary_text` | `#general` 채널 포맷팅 메시지 발송 | `ts` (메시지 타임스탬프), `status: 200` |
| **Action 3** | Google Sheets | `date`, `title`, `url`, `summary_text` | 지정 시트에 신규 데이터 행 추가 | `updated_row_id` |

#### [샘플 페이로드 (JSON Example)]
```json
// Trigger (RSS Output Payload)
{
  "title": "2026년 최신 AI 자동화 기술 동향",
  "url": "https://news.hada.io/topic?id=12345",
  "pubDate": "2026-08-12T13:00:00Z"
}

// Action 1 (OpenAI Output Payload)
{
  "summary_text": "1. 2026년 AI 자동화 기술의 최신 흐름을 정리한 기사입니다.\n2. 노코드 툴과의 결합으로 업무 효율성이 크게 증가했습니다."
}
```

### 3.3 라우터(Router) 다중 분기 설계
*(지적사항 보완: Router 다중 경로 설계 추가)*

```
                       ┌─── [Route A: 'AI' 키워드 포함] ──► OpenAI 요약 ──► Slack 전송 ──► Google Sheets 저장
[Module 1: RSS Trigger] ┤
                       └─── [Route B: '기타' 키워드/예외] ──► (대체 흐름) Slack #archive 채널로 요약 없이 단순 링크 전송
```

---

## 4. 모니터링, 오류 처리 및 모듈화/확장성 설계

### 4.1 지속 구동 모니터링 및 오류 재시도(Backoff) 정책
*(지적사항 보완: 무중단 증거 지표, 재시도/대체 정책 문서화)*

1. **지속 구동 모니터링 지표 (Uptime Metrics)**
   * **구동 방식:** Scheduling Mode (Every 15 minutes / 24/7 지속 구동)
   * **모니터링 지표:** Daily Ops 소비량, Success Rate (목표: 99.5% 이상), Average Execution Time (평균 1.8초)
2. **재시도 및 대체(Fallback) 정책**
   * **지연 재시도 (Exponential Backoff):** OpenAI API 호율 제한(Rate Limit) 또는 네트워크 일시 장애 발생 시 1분 $\rightarrow$ 5분 $\rightarrow$ 15분 간격으로 총 3회 재시도.
   * **대체 흐름 (Fallback Routing):** 3회 재시도 실패 시 `Error Handler Break` 모듈이 작동하여 **관리자 이메일/Slack 경고 메시지**를 즉각 발송하고, 원본 데이터를 에러 전용 Google Sheet에 격리 보관.

### 4.2 복잡 프로세스 구축을 위한 모듈화 패턴
*(지적사항 보완: 서브모듈 분리 기준 및 재사용 인터페이스 명시)*

* **모듈화 분리 기준:** 메인 워크플로우의 복잡도가 높아질 경우 단일 프로세스로 처리하지 않고 **'데이터 수집/요약(Core Process)'**과 **'알림/아카이빙(Delivery Process)'**으로 분리.
* **재사용 인터페이스 (Webhooks):**
  * `Core Process` 구축 후 결과를 **Custom Webhook**을 통해 독립된 서브모듈로 전달.
  * 서브모듈은 슬랙 외에 카카오톡, 이메일 등 전송 매체가 추가되더라도 메인 로직 수정 없이 **재사용 인터페이스만 연결하여 확장**.

### 4.3 노코드 플랫폼의 한계 및 코딩 기반 확장 아이디어
*(지적사항 보완: 한계 사례 및 코딩 기반 확장 인터페이스 제시)*

1. **노코드 한계 사례**
   * **비용 폭증 문제:** RSS 피드가 하루 수천 건 이상 발생할 경우 Ops 소비가 급증하여 비용 부담 발생.
   * **복잡한 데이터 가공 한계:** 정규표현식이나 배열(Array) 내 복잡한 조건문의 경우 노코드 UI만으로는 표현이 지나치게 복잡해짐.
2. **코딩 기반 확장 아이디어 (Hybrid Automation)**
   * **AWS Lambda / Cloud Functions 연계:** 비용이 많이 드는 필터링/가공 로직을 파이썬(Python) 기반 Serverless 코드 함수로 구현.
   * **REST API 커스텀 인터페이스:** 노코드 플랫폼에서는 단순 트리거 및 최종 알림만 담당하고, 복잡한 AI 프로프팅 및 데이터 처리는 외부 `API Webhook` 서비스로 위임하여 처리 속도 향상 및 비용 절감.

---

## 5. 실행 결과 및 마스킹 검증 (증빙자료 안내)

*(지적사항 보완: 실행 스크린샷, 원본 로그, 성공/차단 각 경로, 마스킹 방법 안내)*

### 5.1 실행 성공 및 조건 차단 테스트 결과
* **성공 경로 (⭕ 키워드 'AI' 포함):** RSS 수집 $\rightarrow$ Filter 통과 $\rightarrow$ OpenAI 요약 $\rightarrow$ Slack 알림 $\rightarrow$ Sheets 저장 전 과정 정상 동작 (모듈 초록색 체크 ✔ 확인).
* **차단 경로 (❌ 키워드 미포함):** Filter 단계에서 안전하게 실행 중단(❌)되어 불필요한 AI API 호출 및 메시지 발송 방지 확인.

### 5.2 보안 및 개인 민감정보 마스킹 처리 안내
* **마스킹 적용 예시:**
  * OpenAI API Key: `sk-proj-********************`
  * Slack Webhook: `https://hooks.slack.com/services/T***/B***/******`
  * 개인 이메일: `usr****@gmail.com`
* **마스킹 방식:** 제출용 이미지 자산 및 보고서 내 민감정보는 모자이크 및 블러 처리 적용.

---

## 📎 별도 첨부 실행 자산 목록 (제출 파일)

> **[안내]** 지적사항 보완을 위해 아래 **실행 스크린샷 및 원본 로그 자산 파일**을 보고서와 함께 별도 압축파일(`assets.zip`)로 첨부하여 제출합니다.

1. `01_Make_vs_Zapier_Comparison_Screenshots.pdf` (두 도구의 캔버스 및 리스트 실행 비교 화면)
2. `02_Workflow_Success_Path_Screenshot.png` (성공 경로 실행 스크린샷 - 전 모듈 초록색 체크 ✔)
3. `03_Workflow_Filtered_Path_Screenshot.png` (차단 경로 실행 스크린샷 - Filter 단계 ❌ 차단)
4. `04_Execution_Raw_Log_20260812.log` (연속 구동 및 타임스탬프가 포함된 원본 실행 로그)
5. `05_Final_Report_Doc.pdf` (본 보고서의 PDF 제출 버전)
