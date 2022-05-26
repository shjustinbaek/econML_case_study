#Customer Segmentation at TripAdvisor with Recommendation AB Tests
![image](https://user-images.githubusercontent.com/46898478/170302276-9a4ea9b9-1067-4203-beb8-50a25ac2ca45.png)
![image](https://user-images.githubusercontent.com/46898478/170302308-f9810bf1-7cda-4eac-bbee-b1df5b5e6423.png)
![image](https://user-images.githubusercontent.com/46898478/170302347-6d45edda-3a3b-4c9a-b10f-f12a12cf42a6.png)
- Trip Advisor의 궁금증:
    - Trip Advisor의 맴버쉽 가입이 더 긴 이용시간으로 이어지나? (인게이지먼트 상승으로 이어지는지?)
    - 어떤 고객에게 맴버쉽이 가장 큰 효과가 있는지? 입니다

![image](https://user-images.githubusercontent.com/46898478/170302369-0d9c3463-0a49-4e55-9a76-ee47ced95c73.png)
- 이 문제에 대한 답을 얻기 위해 할 수 있는것이 무엇이 있는지 생각해보았을 때:<br>
- 기존 membership 회원과 비회원의 데이터를 이용해서 분석할 수 있지 않을까?라고 생각해볼 수 있습니다.
- 즉 단순히 맴버쉽에 가입된 유저와 가입되지 않은 유저의 인게이지먼트를 비교해보면 안되나?라는 생각이 들 수 있습니다.
- 결론부터 말하자면 안됩니다.
- 현재 TripAdvisor 맴버쉽에 가입한 사람들은 그렇지 않은 사람보다 TripAdvisor에 대해 친밀감을 더 많이 느끼는 경우가 있습니다.
    - TripAdvisor에 대한 친민감이 많은 유저들은 그렇지 않은 유저에 비해서 더 높은 인게이지먼트를 보입니다.
    - 즉 TripAdvisor에 대한 친밀감(User Affinity)은 treatment (멤버쉽 가입)와 outcome (인게이지먼트)에 모두 영향을 주는 confounder입니다.
- 그리고 우리는 user affinity를 완벽하게 관찰할 수 없습니다.
- 그래서 단순히 맴버쉽 회원과 비회원의 인게이먼트를 비교하면 biased한 결론에 도달할 수 밖에 없습니다.

![image](https://user-images.githubusercontent.com/46898478/170302388-9d9543d7-241f-4f35-b7ac-12da3feb6fdc.png)
- 단순히 회원과 비회원의 인게이지먼트를 비교할 수 없다면 A/B 테스트를 진행하는 대안도 생각해볼 수 있습니다.
- 하지만, 랜덤한 유저에게 맴버쉽을 강요할 수 없기 때문에 A/B 태스트도 진행할 수 없습니다.


![image](https://user-images.githubusercontent.com/46898478/170302412-38e5b263-47a0-4a35-af94-e058b8794d6b.png)
- 이렇게 A/B 테스트를 사용할 수 없는 상황일 때 적용할 수 있는 방법론으로 recommendation A/B 테스트가 있습니다.
- 직접 맴버쉽 가입을 강요할 수는 없지만, 맴버쉽 가입 유도는 랜덤한 사용자들에게 줄 수 있습니다. 

![image](https://user-images.githubusercontent.com/46898478/170302427-5c2046ad-6f4e-437a-9b61-8883a03a9683.png)
![image](https://user-images.githubusercontent.com/46898478/170302450-21bb2f75-a4bf-4639-92b9-8af33f7ed2ba.png)
- 앞에서 설명한 실험을 진행한다면, 맴버쉽 가입 유도는 IV가 됩니다.
- IV 방법론으로 인과추론을 진행할 수 있습니다.




![image](https://user-images.githubusercontent.com/46898478/170302462-6b139dea-6c44-46b2-9201-4272cc535772.png)
- 하지만 보편적인 IV 방법론들도 한계점이 존재합니다.
- 보편적인 ML 알고리즘과 다르게 IV 방법론은 정말 큰 규모의 데이터가 필요한 경우가 많습니다.
- 또한 Compliance heterogneity에 대한 고려를 할 수 없어 biased한 인과 효과를 얻을 수 있습니다.
- 그래서 DRIV라는 방법론을 사용하게됩니다.



![image](https://user-images.githubusercontent.com/46898478/170302477-6757733d-ab08-4dd0-a82c-d0eb78baa536.png)
- Trip Advisor 실험 구성 소개
    - 400만명의 유저를 랜덤하게 반으로 분할하고
    - 실험군에게 간편 맴버쉽 가입 화면을 표시해줍니다.

- 유저 단위로 수집하는 데이터는 다음과 같습니다:
    - Treatment: 유저가 맴버쉽에 가입을 했는지 여부
    - outcome: 실험 시작 이후 14일간 페이지 방문 횟수 (인게지먼트)
    - features: 유저의 특성을 나타내는 feature
    - IV: A/B테스트에서의 assignment (간편 맴버쉽 가입 화면 표시 여부)

- 그리고 맴버쉽과 인게이지먼트의 인과관계를 다음과 같이 정의합니다.
    - 지난 14일간 방문 횟수 = CATE * 맴버 여부 + 맴버 여부와 무관한 기본 방문 횟수 + 오차
    - 즉, 유저 인게이지먼트는 유저의 특징과 맴버쉽 여부에 의해 결정되느 값이라고 보는 것입니다.
    - 맴버 여부도 유저 특징과 IV(맴버쉽 유도)에 의해 결정되는 함수를 따른다고 봅니다.

![image](https://user-images.githubusercontent.com/46898478/170302494-1070c910-6235-4a2f-b8cb-8bd79b6fa886.png)
- 문제 간략화를 위해서, Treatment, IV를 binary로 정의합니다.
- 또한 유저에게 간편 맴버쉽 강비 화면이 표시될 확률을 1/2로 정의합니다.

![image](https://user-images.githubusercontent.com/46898478/170302507-1d2f1716-7360-49ed-b16b-61ccad691c21.png)
- 이 과정을 EconML로 편리하게 진행할 수 있습니다.


![image](https://user-images.githubusercontent.com/46898478/170302527-91a78f8c-3bb4-4a51-96b5-985f6a3bfcdd.png)
![image](https://user-images.githubusercontent.com/46898478/170302552-92a9e7dd-1462-48f7-8803-da63d8f11051.png)
- TripAdvisor에서 수집한 데이터를 살펴보면

![image](https://user-images.githubusercontent.com/46898478/170302567-d68ae9cd-f71b-4f92-a00c-3686cbd4128b.png)
![image](https://user-images.githubusercontent.com/46898478/170302578-fdd3aa2e-16d4-461c-bd1e-f3b2e98fe8e5.png)
![image](https://user-images.githubusercontent.com/46898478/170302595-d6ff5d35-09d3-421a-80b1-066df104543e.png)
![image](https://user-images.githubusercontent.com/46898478/170302616-2e7f3b38-6168-436b-bbbd-b593e50d5485.png)
![image](https://user-images.githubusercontent.com/46898478/170302634-3c5188b4-cf9f-4df3-b8f4-e3977322f4cb.png)
![image](https://user-images.githubusercontent.com/46898478/170302647-4a38f0b8-d7ff-4f1d-ac92-0d0d9ee62411.png)






