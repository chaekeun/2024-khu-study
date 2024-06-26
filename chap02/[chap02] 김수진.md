# 2️장 개략적인 규모 추정

> 시스템 설계 면접을 볼 때, **시스템 용량**, **성능 요구사항**을 개략적으로 추정해보라는 요구를 받게 될 수 있다.
> <br><br>
> 개략적 규모 추정을 효과적으로 해내려면 **_규모 확장성_**을 잘 표현해야 하는데, 이를 위해선 <span style= color:orange>**_2의 제곱수_**, **_응답지연(latency)값_**, **_가용성_**</span> 에 관련된 수치들을 잘 알아야 한다.

### 1️⃣ 2의 제곱수

분산 시스템에서 다루는 데이터 양은 엄청나게 커질 수 있지만 계산법은 기본에서 크게 벗어나지 않는다.<br>
데이터 볼륨의 단위는 2의 제곱수로 표현하고, <span style="background-color: #fff5b1; color: black;">**최소 단위는 1바이트(8비트)이다.**</span> (ASCII 문자 하나가 1바이트)<br>

| 2의 X 제곱 | 근사치 | 이름            |
| ---------- | ------ | --------------- |
| 10         | 1천    | 1KB             |
| 20         | 1백만  | 1MB             |
| 30         | 10억   | 1GB             |
| 40         | 1조    | 테1TB           |
| 50         | 1000조 | 1PB(페타바이트) |

### 2️⃣ 모든 프로그래머가 알아야 하는 응답지연 값

<img src="https://i.ibb.co/KjNtGP0/Kakao-Talk-20240523-025634241.jpg" alt="Kakao-Talk-20240523-025634241" width=50%><div>

### ✅결론

- <span style= "color: pink;">메모리는 빠르지만 디스크는 아직 느리다.</span>
- 디스크 탐색은 가능하면 피해라.
- 단순한 압축 알고리즘은 빠르다.
- 데이터를 인터넷으로 전송하기 전에 가능하면 <span style= "color: pink;">압축</span>해라.
- 데이터 센터는 보통 여러 지역에 분산되어 있고, 센터들 간에 데이터를 주고받는 데는 시간이 걸린다.

### 3️⃣ 가용성에 관계된 수치들

> **고가용성** : 시스템이 오랜 시간 동안 지속적으로 중단 없이 운영될 수 있는 능력 (100% : 시스템이 단 한 번도 중단된 적이 없음. 보통 99%~100%)

**SLA(Service Level Agreement)**

- 서비스 사업자와 고객 사이에 맺어진 합의로, 서비스 사업자가 제공하는 서비스의 가용시간(uptime)이 공식적으로 기술되어 있다.
- Amazon, Google, Microsoft는 99& 이상의 SLA를 제공한다.

### 💥예제: 트위터 QPS와 저장소 요구량 추정💥

**_가정_**

- 월간 능동 사용자는 3억 명이다.
- 50%의 사용자가 트위터를 매일 사용한다.
- 평균적으로 각 사용자는 매일 2건의 트윗을 올린다.
- 미디어를 포함하는 트윗은 10% 정도다.
- 데이터는 5년간 보관된다.

**_추정_**

> QPS(Query Per Second)

데이터베이스나 웹 서버 API 등에서 특정 작업이나 요청이 초당으로 얼마나 처리되는지를 나타내는 지표다. 이는 시스템의 성능과 처리 능력을 평가하는 중요한 측정 지표 중 하나이다.

🔗 https://www.bigabid.com/glossary/qps-queries-per-second/

- 일간 능동 사용자, DAU(Daily, Active User) = 3억 X 50% = 1.5억
- QPS = 1.5억 X 2트윗/24시간/3600초 = 약 3500(1초에 3500건 처리됨)
- 최대 QPS(Peek QPS) = 2 X QPS = 약 7000 (한 번에 2건 올릴 수도 있음)

**_미디어 저장을 위한 저장소 요구량_**

- 평균 트윗 크기
  - tweet_id : 64 byte
  - 텍스트 : 140 byte
  - 미디어 : 1MB
- 미디어 저장소 요구량 : 1.5억 X 2 x 10% X 1MB = 30TB/일
- 5년간 미디어를 보관하기 위한 저장소 요구량 : 30TB X 365 X 5 = 약 55PB

### ✍️팁

개략적인 규모 추정과 관계된 면접에서 가장 중요한 것은 **문제를 풀어 나가는 절차다.**
**_올바른 절차를 밟느냐가 결과를 내는 것보다 중요하다._**

1. 근사치를 활용한 계산(rounding and approximation)
2. 가정은 나중에 살펴볼 수 있도록 적어둬라.
3. 단위(unit)를 붙이는 습관을 들여라.
4. 많이 출제되는 개략적 규모 추정 문제
   - QPS, 최대 QPS
   - 저장소 요구량
   - 캐시 요구량
   - 서버 수
