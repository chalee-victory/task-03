# RSS/OpenAI를 이용한 뉴스배포 사무자동화 기법
**Make · RSS · OpenAI(ChatGPT) · Slack · Google Sheets 연동 가이드북**

---

## 1. 단계별 개요 및 워크플로우

| 단계 | 구분 | 사용 모듈/도구 | 주요 역할 및 목적 |
| :--- | :--- | :--- | :--- |
| **1단계** | Trigger | RSS (Watch RSS Feed) | 최신 뉴스/블로그 기사 자동 감지 및 수집 |
| **2단계** | Action 1 | OpenAI (Create Completion) | 기사 본문 분석, 3줄 요약 및 키워드 추출 |
| **3단계** | Action 2 | Slack (Create a Message) | 팀 채널에 요약 뉴스 및 원본 링크 자동 공유 |
| **4단계** | Action 3 | Google Sheets (Add a Row) | 수집/요약 데이터를 시트에 기록하여 DB화 |
| **5단계** | Test & ON | Make Scheduling | Run once 검증 후 자동화 주기(상시 가동) 설정 |

---

## 2. 세부 구축 가이드

### 1단계: 트리거(Trigger) - RSS 모듈 설정
* Make 시나리오에서 첫 번째 모듈로 **[RSS]**를 추가하고 `'Watch RSS feed items'`를 선택합니다.
* **URL**: 수집하고자 하는 뉴스, 미디어, 테크 블로그의 RSS 피드 주소를 입력합니다.
* **Limit**: 1회 실행 시 가져올 최대 기사 개수를 지정합니다 (권장: 3~5개).

> **💡 핵심 팁 & 구축 포인트**  
> RSS 주소를 찾기 어려울 경우, 구글 검색창에 `'사이트명 + RSS'`를 검색하거나 네이버/다음 뉴스 등의 카테고리별 RSS 피드 주소를 활용할 수 있습니다.

---

### 2단계: 액션(Action 1) - OpenAI(ChatGPT) 모듈 설정
* RSS 모듈 오른쪽에 **[OpenAI (ChatGPT)]** 모듈을 추가하고 `'Create a Completion (Prompt/Chat)'`을 선택합니다.
* OpenAI API Key를 연동하고 아래 세부 항목을 설정합니다:
  * **Model**: `gpt-4o-mini` 또는 `gpt-4o` (속도 및 가성비 우수)
  * **System Message**: `"너는 뉴스 요약 전문 에디터야. 전달받은 기사를 읽고 핵심 내용을 3줄 요약하고, 관련 키워드 3개를 추출해 줘."`
  * **User Message**: RSS 모듈에서 넘겨받은 `[Title]` 및 `[Description / Content]` 변수 매핑 입력

---

### 3단계: 액션(Action 2) - Slack 모듈 설정
* OpenAI 모듈 오른쪽에 **[Slack]** 모듈을 추가하고 `'Create a Message'`를 선택합니다.
* 슬랙 계정 및 워크스페이스를 연동하고 메시지를 발송할 **[Channel]**을 선택합니다.
* **Text (메시지 템플릿 예시)**:
  ```text
  📢 [신규 뉴스 요약]
  제목: {{RSS: Title}}
  📝 AI 요약: {{OpenAI: choices[].message.content}}
  🔗 원본 링크: {{RSS: URL}}
  ```

---

### 4단계: 액션(Action 3) - Google Sheets 모듈 설정
1. 구글 드라이브에서 미리 뉴스 저장용 시트를 생성하고 열(Header)을 작성합니다.
   * **시트 구성**: A열(수집일시), B열(기사제목), C열(AI 요약), D열(원본URL)
2. Slack 모듈 오른쪽에 **[Google Sheets]** 모듈을 추가하고 `'Add a Row'`를 선택합니다.
3. 구글 시트 파일 및 워크시트를 지정한 후 아래와 같이 변수를 매핑합니다:
   * **A열 (날짜)**: `{{now}}` 또는 RSS의 `[Date]`
   * **B열 (제목)**: RSS의 `[Title]`
   * **C열 (요약)**: OpenAI의 `[Content]`
   * **D열 (URL)**: RSS의 `[URL]`

---

### 5단계: 테스트 및 자동 실행(Scheduling)
* **테스트 실행**: 좌측 하단의 **[Run once]** 버튼을 클릭합니다.
* **동작 검증**: RSS 기사 수집 ➔ OpenAI 요약 생성 ➔ Slack 메시지 수신 ➔ Google Sheets 행 추가 여부를 순차적으로 확인합니다.
* **자동화 활성화**: 테스트가 완벽히 완료되면 좌측 하단의 **[SCHEDULING]** 스위치를 **'ON'**으로 변경합니다.
* **주기 설정**: 매 1시간마다 실행하거나, 매일 아침 특정 시간(예: 09:00)에 기사를 수집하도록 실행 간격을 지정합니다.
