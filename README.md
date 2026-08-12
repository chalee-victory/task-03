[최종 보고서] 노코드 자동화 프로젝트 비교 분석 및 자유 주제 구현

1. 개요 및 미션 소개

매일 반복되는 일상적 업무(이메일 확인, 데이터 수집, 스프레드시트 이관, 팀 알림 공유 등)는 개별 작업당 소요 시간이 짧더라도 누적 시 상당한 생산성 손실을 야기합니다. 본 프로젝트는 코딩 없이 Trigger(시작 이벤트)와 Action(처리 동작) 및 Filter/Router(조건 분기)를 연결하여 프로세스를 자동화함으로써, 업무 효율성을 극대화하는 노코드 파이프라인 구축을 목표로 진행되었습니다. 

본 보고서는 아래 두 가지 주요 프로젝트의 수립, 사전 테스트, 비교 평가 및 완공 결과를 총괄 종합한 최종 제출 문서입니다.

[프로젝트 1] 자동화 도구 비교 구현: 동일한 워크플로우를 Make와 Zapier 2개 플랫폼에서 구축 후 비교 평가. 

[프로젝트 2] 자유 주제 자동화 설계 및 구현: 'RSS 기반 IT/트렌드 뉴스 자동 스크랩 및 AI 요약 알림' 구축 (보너스 과제 연동). 

2. [프로젝트 1] 자동화 도구 비교 구현 (Make vs. Zapier)

2.1 비교 워크플로우 설계

표준 워크플로우: Google Forms 제출 $\rightarrow$ 점수 기준 Filter $\rightarrow$ Google Sheets 저장 & Slack 채널 알림 발송 

요구사항 검증: Trigger 1개 + Filter 1개 + Action 2개 충족 



2.2 대표 5개 항목 비교 분석

과제 명세서 요구사항에 따라 2개 도구를 직접 비교한 결과는 다음과 같습니다. 

2.3 종합 평가 및 추천 상황

Make: 시각적 노드 구조로 직관적인 흐름 파악이 가능하며, 무료 Ops 제공량이 넉넉하여 AI 연동 등 다단계 복잡 프로세스 구축에 최적입니다.

Zapier: 빠르고 단순한 1:1 연동 및 노코드 초보자가 신속히 워크플로우를 생성할 때 적합합니다. 

3. [프로젝트 2] 자유 주제 자동화 설계 및 구현

3.1 업무 정의 및 도구 선정 이유

과업명: RSS 기반 IT/트렌드 뉴스 자동 스크랩 및 AI 요약 알림 

업무 정의: 관심 IT 트렌드/뉴스를 매일 수동 검색하는 낭비를 줄이고, 키워드 조건 필터링 및 OpenAI AI 요약을 거쳐 Slack 및 Google Sheets로 자동 전송·아카이빙하는 프로세스.

선정 도구: Make 

선정 이유: 시각적 캔버스 UI를 통한 조건 분기(Filter) 확인이 용이하며, 무료 플랜(1,000 Ops) 내에서 AI Action까지 완벽하게 테스트 가능함.

3.2 워크플로우 구조 설계 및 조건 검증

[Module 1: RSS Trigger] 

       │

 [Condition Filter] ── (키워드 미포함 시 차단 ❌)

       │ (키워드 'AI' 포함 ⭕)

       ▼

[Module 2: OpenAI (Action 1 - 보너스)] ➔ 기사 2~3줄 요약

       │

       ▼

[Module 3: Slack (Action 2)] ➔ 채널 알림 메세지 발송

       │

       ▼

[Module 4: Google Sheets (Action 3)] ➔ 데이터 행 추가 (아카이빙)

Trigger: RSS 모듈 (Watch RSS feed items / GeekNews https://news.hada.io/rss 등록) 

Filter (조건 분기): Title(기사 제목) 내 특정 키워드(예: AI) 포함 조건 (Text operators: Contains (case insensitive)) 

Action 1 (보너스 과제): OpenAI ChatGPT (Create a Chat Completion / gpt-4o-mini 모델로 한국어 2~3줄 요약 생성) 

Action 2 (메신저 알림): Slack (Send a Slack Message / #general 채널로 기사 제목, AI 요약, URL 발송) 

Action 3 (데이터 저장): Google Sheets (Add a Row / 날짜, 제목, 링크, 요약본 기록) 

4. 실행 및 감점 방지 검증 포인트

4.1 필수 요구사항 검증

Trigger 1개 이상: RSS Trigger 모듈 정상 구동 확인.

Action 2개 이상: OpenAI, Slack, Google Sheets 총 3개 Action 연결로 기준을 여유 있게 초과 달성.

조건 분기 1개 이상: RSS와 OpenAI 연결선상에 키워드 Filter 배치 완료.

4.2 조건 분기 실행 테스트 (Run Once) 결과

조건 통과 케이스 (⭕): RSS 기사 제목에 지정 키워드('AI')가 포함되어 Filter를 통과, 이후 OpenAI 요약 $\rightarrow$ Slack 알림 $\rightarrow$ Google Sheets 행 추가까지 전 모듈 초록색 체크(✔) 및 실행 성공 로그 확보.

조건 차단 케이스 (❌): Filter 키워드를 미포함 단어로 설정 후 실행 시, RSS 수집 후 Filter 단계에서 차단(❌)되어 이후 Action 모듈들이 실행되지 않고 안전하게 중단되는 제어 로직 검증 완료.

4.3 보안 및 민감정보 보호 처리

제출 스크린샷 및 문서 내 OpenAI API Key(sk-proj-...), Slack Webhook URL, 계정 이메일 등 개인 민감정보는 모두 마스킹(*******) 및 모자이크 처리 적용 완료. 

5. 결론 및 학습 소회

본 프로젝트를 통해 단순 연동을 넘어 Trigger – Filter – Action으로 이어지는 노코드 자동화의 핵심 동작 메커니즘을 체득하였습니다.

Make와 Zapier의 플랫폼 특징을 비교·분석하여 상황에 맞는 도구 선택 기준을 수립할 수 있었으며 , 생성형 AI(OpenAI) 연동을 통해 데이터 수집부터 요약, 전송, 저장까지 이르는 완결성 높은 자동화 파이프라인 구축 역량을 확보하였습니다.

