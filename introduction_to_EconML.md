# Introduction to EconML
![image](https://user-images.githubusercontent.com/46898478/169684043-97fc8cfd-76d0-4970-adc8-8018c8c2cb0f.png)
## Intro
![image](https://user-images.githubusercontent.com/46898478/169684056-10023c27-395f-41be-9782-3169bef4c1c8.png)
- 마이크소프트 리써치 ALICE (Automated Learning and Intelligence for Causation and Economics) 프로젝트 팀은 계량경제학과 ML 관련 연구를 진행하고 있습니다.
- 산업과 사회 문제를 해결하기 위한 연구를 진행하고 있다고 합니다.
- ALICE 프로젝트 팀의 연구 결과를 프로덕션 퀄리티의 open-source로 공개한 결과물이 EconML입니다.
- https://www.microsoft.com/en-us/research/group/alice/case-study/

![image](https://user-images.githubusercontent.com/46898478/169684059-17707ae0-b15e-4116-9599-35ca0de2949c.png)
- EconML은 관찰된 데이터와 머신러닝을 이용해 개인화된 인과 효과(indivualized causal reponse)를 추정할 수 있도록 두와주는 파이썬 라이브러리입니다.

![image](https://user-images.githubusercontent.com/46898478/169684062-e136f14c-01f3-4069-a21f-410735a0e769.png)
- EconML를 사용할 때 주로 관심을 가지고 추정을 하고싶은 target은 CATE입니다.
- CATE:
    - feature X가 주어진 sample의 treatment T0를 T1으로 변경했을 때 outcome Y가 어떻게 변할 것인가입니다.

![image](https://user-images.githubusercontent.com/46898478/169684065-d9e35a58-ebc0-4e81-a930-d571838f47be.png)
- EconML을 사용할 때 거치되는 작업 flow는 다음과 같습니다:
    - outcome(Y), Treatments(T), Feature/Effect modifiers(X), Control(W), instruments(Z) 데이터를 확보합니다.
    - 확보한 데이터를 EconML estimator에 입력해줍니다.
    - Test Feature 과정을 수행하고
    - CATE $\theta(X)$를 추정합니다.

![image](https://user-images.githubusercontent.com/46898478/169684066-8f04397a-7f2e-49b9-b731-a1ad0d60686b.png)
- EconML 패키지는 통일된 스타일의 API를 사용하여 편리합니다.
- CATE estimator의 .fit, .effect 메소드를 사용해서 분석을 진행하게됩니다.
![image](https://user-images.githubusercontent.com/46898478/169684069-dfbfaedf-fdd8-41d5-bb74-d60c821b8771.png)
- EconML은 편리할 뿐 아니라 강력한 인과추론을 방법론을 지원합니다.
- ML based 방법론:
    - conditional exogeneity 또는 instrumental variable이 있다면 DoubleML, Causal Forest, Rlearner, Meta-Learner 등 다양한 방법론을 사용할 수 있습니다.
- Confidence intervals 지원:
    - ML 방법론을 사용해도 confidence interval을 사용할 수 있는 최신 방법론이 지원됩니다.
- Causal Scoring 및 Causal Cross-validation 지원:
    - CATE 모델의 성능 평가를 scoring 및 Cross-validation을 통해 진행할 수 있습니다.
- Causal Interpretability:
    - CATE를 tree와 SHAP을 통해 해석하는 기능이 지원됩니다.
- Causal Validation
    - EconML은 DoWhy 라이브러리와 함께 사용할 수 있도록 통합되어 있습니다.
    - DoWhy의 validation 기능을 이용하여 sensitivity analysis를 할 수 있습니다.
- Policy 추천
    - 인과 추론 결과를 이용해서 개인화된 treatment 정책을 추천해주는 기능도 있습니다.

# CATE Estimation
![image](https://user-images.githubusercontent.com/46898478/169684073-82f4d6e6-fad6-4e2c-a4e8-18e4aaecf1db.png)

![image](https://user-images.githubusercontent.com/46898478/169684076-797dbb7d-b57d-47b9-8475-6be614242294.png)
- 앞에서 설명 드렸듯이 ML 방법론을 사용하여 인과추론을 진행하기 위해서는 conditional exogneity 또는 instrumental variable이 있어야합니다.
- 첫번 째 conditional exogeneity가 보장되었을 때 인과추론을 진행하는 예시를 소개해드리겠습니다.
- Conditional Exogneity란?
    - feature X가 outcome Y에게 영향을 받지 않는다는 가정입니다. 
    - Conditional Exogneity가 보장되기 위해서는 관찰되지 않은 effect(Y)와 treament(T)에 모두 영향을 주는 confounder가 없어야합니다.

![image](https://user-images.githubusercontent.com/46898478/169684084-cc221b17-720d-4866-acc6-83e3a6ebff23.png)
- 예를 들어서 Double Machine Learning (Rlearner)를 활용해서 인과추론을 진행하고 싶다면 다음과 같은 과정을 거쳐서 진행할 수 있습니다.
1. Cross-fitting: 보유하고 있는 데이터를 2개의 같은 크기 subset으로 분할합니다.
2. 분할한 첫번째 subset에 feature X, Control W를 입력으로 받고 outcome을 예측하는 모델을 훈련시킵니다. 그리고 이 모델로 2번째 subset의 outcome을 예측하고 실제 outcome과 예측값의 residual을 예측하는 모델을 훈련시킵니다. <br> 같은 과정을 사용 데이터 순서를 변경해서도 진행합니다.
3. 분할한 첫번째 subset에 feature X, Control W를 입력으로 받고 treatment를 예측하는 모델을 훈련시킵니다. 그리고 이 모델로 2번째 subset의 treatment를 예측하고 실제 treatment와 예측값의 residual을 예측하는 모델을 훈련시킵니다. 마찬가지로, 같은 과정을 사용하는 데이터 subset 순서를 변경해서 진행합니다.
4. 이후 예측된 residual treament로 residual outcome을 예측하는 모델을 훈련시킵니다. 이때 최소화하는 loss는 residual outcome - CATE * residual treatment가 되도록 설정해줍니다.

![image](https://user-images.githubusercontent.com/46898478/169684090-e2eefabe-4ff0-4be2-bea2-42b20ae18d0d.png)
- 이 과정을 EconML에서는 CATE estimator를 선언하고, .fit, .effect 메소드를 사용하는 것으로 편리하게 진행할 수 있습니다.


![image](https://user-images.githubusercontent.com/46898478/169684094-3690153a-d878-4f2b-886e-6ed42e6f2df7.png)
- Azure AutoML을 사용해서도 진행할 수 있습니다 (추가 확인 필요!)

![image](https://user-images.githubusercontent.com/46898478/169684096-a6f2dd3c-fe8a-463f-852a-6612fb43423a.png)
- instrumental variable이 존재할 때도 est.fit()에 Z=Z argument를 넣어주면 자동으로 CATE를 추론할 수 있습니다.

![image](https://user-images.githubusercontent.com/46898478/169684100-73b0b9dd-a446-4d9f-b9a0-ab74a6f18376.png)
![image](https://user-images.githubusercontent.com/46898478/169684105-e9df6aad-a197-40cc-bd2a-804cb41e0c62.png)
- 지금까지 소개해준 방식으로 CATE 추정을 진행한다면, .est 메소드는 CATE 추정치만 반환해줍니다.

![image](https://user-images.githubusercontent.com/46898478/169684111-e2a41b40-872f-46d0-816a-f2f424ce52a1.png)
- .fit 메소드에 inference="bootstrap" argument를 추가하고 .effect_inference 메소드를 사용하면 Generic bootstrap inference 방식으로 추정된 CATE의 confidence interval도 구해볼 수 있습니다.
- Generic bootstrap 방식이란?
    - 확인 필요!

![image](https://user-images.githubusercontent.com/46898478/169684113-4ba72cf0-52dc-4491-b8b3-66145892751c.png)
- 

![image](https://user-images.githubusercontent.com/46898478/169684117-9db19882-97f0-4877-99ac-d5f70c140f4c.png)
![image](https://user-images.githubusercontent.com/46898478/169684120-d70d5fbd-c81d-499d-84e9-bb5c3c77ef42.png)
![image](https://user-images.githubusercontent.com/46898478/169684124-47718780-84fd-4df7-b095-1ced088536e2.png)
![image](https://user-images.githubusercontent.com/46898478/169684128-c2d5064a-84d6-4bd5-b00f-43cf49bd4dd7.png)
![image](https://user-images.githubusercontent.com/46898478/169684133-259a88cd-7326-4530-ada0-8cf13123120e.png)
![image](https://user-images.githubusercontent.com/46898478/169684134-f15de271-9338-429f-baa7-c39da79be828.png)
![image](https://user-images.githubusercontent.com/46898478/169684136-f6ff01fe-3ea1-4738-b0df-a8484e6faa03.png)
![image](https://user-images.githubusercontent.com/46898478/169684137-fd200e6b-9544-42d2-844d-30232776b54f.png)
![image](https://user-images.githubusercontent.com/46898478/169684139-1a7cab80-205b-438e-a59d-792d07dfae0a.png)
![image](https://user-images.githubusercontent.com/46898478/169684144-fa10f3c4-f54a-43a2-95a0-1ec89b115462.png)
![image](https://user-images.githubusercontent.com/46898478/169684147-c3fbbf68-ee78-4e5c-8f44-52a0ea14b241.png)
![image](https://user-images.githubusercontent.com/46898478/169684153-51ff9e9b-b9ce-4f59-be51-adf461008da2.png)
![image](https://user-images.githubusercontent.com/46898478/169684157-cc8320bf-dff9-4cd0-855b-82cfadbed4a0.png)
![image](https://user-images.githubusercontent.com/46898478/169684162-38a13738-c42f-45ff-85cf-1334d1a606be.png)
![image](https://user-images.githubusercontent.com/46898478/169684170-880aa646-5505-4b81-95ab-c9520b2a9c28.png)
![image](https://user-images.githubusercontent.com/46898478/169684172-cfff1cc5-8d65-4c9a-80f6-a4113d52de5e.png)
![image](https://user-images.githubusercontent.com/46898478/169684173-c4a0c302-a09d-4166-8ae0-1347184843b9.png)






Automated Learning