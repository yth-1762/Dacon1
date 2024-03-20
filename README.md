# 데이콘 AI 경진대회1

# 일시
- 2023-11-22 ~ 2023-12-11

# 주제
- 학습 플랫폼 구독자 예측 AI 해커톤
- 
# 배경 및 목적
- 학습 플랫폼 입장에서는 다음달에도 이용자들도 구독을 할지 안할지 미리 파악하는 것이 중요
- 학습 플랫폼 이용자들이 다음달 구독 여부를 판별
 

# 데이터
- https://dacon.io/competitions/official/236179/data(데이터 출처) 
- 데이터 개수 : 10000개
| Column | Description |
|--------|-------------|
| user_id | 사용자의 고유 식별자 |
| subscription_duration | 사용자가 서비스에 가입한 기간 (월) |
| recent_login_time | 사용자가 마지막으로 로그인한 시간 (일) |
| average_login_time | 사용자의 일반적인 로그인 시간 |
| average_time_per_learning_session | 각 학습 세션에 소요된 평균 시간 (분) |
| monthly_active_learning_days | 월간 활동적인 학습 일수 |
| total_completed_courses | 완료한 총 코스 수 |
| recent_learning_achievement | 최근 학습 성취도 |
| abandoned_learning_sessions | 중단된 학습 세션 수 |
| community_engagement_level | 커뮤니티 참여도 |
| preferred_difficulty_level | 선호하는 난이도 |
| subscription_type | 구독 유형 |
| customer_inquiry_history | 고객 문의 이력 |
| payment_pattern | 사용자의 지난 3개월 간의 결제 패턴을 10진수로 표현한 값. |
| target | 사용자가 다음 달에도 구독을 계속할지 (1) 또는 취소할지 (0)를 나타냅니다. |

  

# 사용언어/ 최종 선정 모델
- python/ catboost

# 모델 성능 지표
- macrof1

# EDA
- target 변수 비율확인(1: 61.99% / 0: 38.01%)
- 모든 범주형 변수 범주별 비율 확인(preferred_difficulty_level, subscription_type, payment_pattern)
- 수치형 변수(subscription_duration, recent_login_time, average_login_time, average_time_per_learning_session, monthly_active_learning_days, total_completed_courses, recent_learning_achievement, abandoned_learning_sessions, community_engagement_level, customer_inquiry_history)  target 범주별 분포 히스토그램으로 확인
- 수치형 변수별 preferred_difficulty_level count를 target별로 히스토그램, boxplot으로 확인(결과: target이 0인 데이터는 preferred_difficulty_level이 low, high, medium순으로 많고 target이 1인 데이터는 medium, low, high 순으로 많다.)
- 수치형 변수별 subscription_type를 target별로 히스토그램, boxplot으로 확인(결과: target0, target1 모두 subscription_type이 basic이 premium보다 많다.-> 아무래도 어떤 조건이든 basic인 이용자들이 압도적으로 많아서라고 판단)
- 수치형 변수별 payment_pattern를 target별로 히스토그램, boxplot으로 확인(결과: target 0,1 모두 0,1,2,3,4,5,6,7 순으로 count가 많음)
- 수치형 변수의 상관관계를 heatmap으로 파악(가장 높은 것이 약 0.08로 모든 수치형변수 높지 않은 상관관계를 보임)
- 범주형 변수('preferred_difficulty_level', 'payment_pattern)별 subscription_type을 target별로 히스토그램으로 확인(결과: target 0,1 모두 basic이 premium보다 높게 나옴.)

  

# 데이터 전처리
- 12000개의 데이터만 무작위로 추출
- tweets 변수에서 텍스트만 반영하기 위해 https:// 부분, 특수문자 제거 이후 사전을 만든 후 정수형으로 인코딩
- 종속변수는 good tweet는 1, neutral tweet는 0, bat tweet는 –1로 처리한 후 원핫인코딩 실시
- train data, test data 8:2 비율로 설정

# 프로젝트 내용
- RNN 모형 FITTING(epochs=10, batch size= 32, optimizer=adam, loss=categorical entropy, activation=softmax, 유닛수=64, bidirectional(return_sequences=True) 1번,bidirectional 1번 dropout(0.2)옵션 1번 추가)
- LSTM 모형 FITTING(epochs=10, batch size 32, optimizer=adam, loss=categorical entropy, activation=softmax, 유닛수=128,bidirectional(return_sequences=True) 1번, dropout(0.2)옵션 1번 추가)
- GRU 모형 FITTING(epochs=10, batch size 32, optimizer=adam, loss=categorical entropy, activation=softmax, 유닛수=128,bidirectional(return_sequences=True, recurrent_dropout=0.2) 1번, bidirectional(recurrent_dropout=0.2) 1번 dropout(0.2)옵션 1번 추가)
- test data accuracy 기준(RNN: 64.46%, LSTM: 69.29, GRU: 72.29%) GRU 모형 최종 선택


# 기대효과
- 향후 chatgpt에 대한 의견을 나타내는 tweets이 앞으로도 계속 생겨날 것이고 이 모델을 통해 chatgpt에 대한 전반적인 여론이 어떠한지 파악
