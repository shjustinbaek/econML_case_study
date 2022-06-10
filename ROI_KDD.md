![image](https://user-images.githubusercontent.com/46898478/172955449-10dd0348-e947-467f-8b18-ee21397741d1.png)
![image](https://user-images.githubusercontent.com/46898478/172955486-7b7bd566-a87e-4c5c-826c-0e41aad0c4df.png)
- 이번 케이스 스터디의 목표:
    - 비즈니스 관점에서 그리고 기술적 관점에서 holistic(큰 그림을 보는) ROI에 대한 인과추론을 진행할 때 극복해야할 점을 이해
    - 관측 데이터로 다수 투자의 장기적 인과 효과를 추정하기 위한 통일된 파이프라인 소개
    - EconML 패키지 사용법 소개

![image](https://user-images.githubusercontent.com/46898478/172955814-623f796b-defc-49d5-ad1b-b84bd2b62f9c.png)
![image](https://user-images.githubusercontent.com/46898478/172955846-db376228-8e2f-4a07-9170-3d9b29fad6ac.png)
- 해결하고자는 비즈니스 문제 소개
    - 마이크로소프트는 자사 제품의 판매를 촉진하기 위해서 여러 기업 고객에게 금전적 지원을 제공합니다. (투자)
    - 여러 회사에 대한 투자 중 어떤 투자가 더 효과적인지 알고 싶습니다.
- 위 질문에 대한 답을 쉽게 할 수 없는 이유
    - 투자 프로그램마다 각자 다른 모델로 ROI를 계산하고 있습니다.
    - 장기적 ROI (예: 2년 ROI)에 대해 분석하고 싶으나, 그렇게 긴 시간동안 기다릴 수 없습니다.
- 하고싶은 것:
    - 단기적 데이터로 장기적 ROI를 홀리스틱하게 추정하는 것

![image](https://user-images.githubusercontent.com/46898478/172956308-c9644d04-30b8-4991-b0eb-0fce435f842c.png)
- holistic(통합된 모든 사항을 고려한) 관점에서 ROI를 계산하고 싶은 이유:
    - 여러 투자를 다양한 시간대에서 비교하고 싶기 때문
        - 투자가 없어도 존재하는, 즉 투자의 인과 효과가 아닌 organic growth 등 요소도 고려하여 투자의 ROI를 구하고 싶습니다. 
        - 각 투자별로 얼마나 효과가 있었는지 측정하기 위함도 있습니다.

![image](https://user-images.githubusercontent.com/46898478/172956880-3ca79170-47c3-4204-a1ef-005ed6fe6d8a.png)
- 이 경우에도 Confounder가 존재하기 때문에 unbiased한 투자 인과 효과를 쉽게 구할 수 없습니다.
    - 예: 규모가 큰 기업 고객은 더 큰 투자를 받을 가능성이 높습니다. 큰 금액의 투자는 큰 금액의 매출로 이어질 가능성도 높습니다. 즉 기업의 규모는 treatment와 outcome에 모두 영향을 주는 confounder입니다.

![image](https://user-images.githubusercontent.com/46898478/172957232-e8a77cf2-a03b-43d9-ab72-b95bda8f8a86.png)
- DML Double Machine Learning을 사용하면 confounder가 존재하는 상황에서도 CATE를 추정할 수 있습니다.
- DML은 two stage 모델을 사용해서 confounder의 효과를 상쇄시켜, 순수하게 투자가 매출에 주는 영향을 고립시킬 수 있습니다.
    - 또한 DML은 고차원 데이터를 활용할 수 있고
    - confidence interval도 반환해줍니다.


![image](https://user-images.githubusercontent.com/46898478/172957576-424443ba-0b91-4fa6-83a6-5797a79c2ec5.png)
- DML 방법론은 다음과 같습니다.
1. confounder로 outcome을 예측하는 모델을 생성합니다.<br>
이 케이스 스터디의 경우 고객 특성과 과거 투자로 매출액을 예측하는 모델을 훈련합니다.

2. confounder로 treatment를 예측하는 모델을 생성합니다.
이 케이스 스터디의 경우 고객 특성과 과거 투자로 현재 투자액을 예측하는 모델을 훈련합니다.

3. 예측된 residual treament로 residual outcome을 예측하는 모델을 훈련시킵니다

4. 3번 모델로 CATE를 주정할 수 있습니다.
ATE를 구한다면 즉 constant한 $\theta$값이 알고싶다면 3번 모델의 coefficient를 확인하면 됩니다.

![image](https://user-images.githubusercontent.com/46898478/172958484-c87cdfe9-9b6b-449d-81d2-4b2e6a0b7fb9.png)

![image](https://user-images.githubusercontent.com/46898478/172958605-41028fbf-c5e2-48c8-9ee3-25a79a7c4f62.png)
- confounder 문제를 DML로 해결해도 우리의 목표인 장기 매출을 예측할 수는 없습니다.
    - 현재 진행중인 투자의 경우 장기 매출이 아직 관측되지 않은 경우도 있기 때문에, Long-term Revenue는 fully observed되지 않았습니다.
- 대신 Long-term Revenue에 대한 단서가 될 short-term observed surrogate를 찾을 수 있다면 새로운 시도를 해볼 수 있습니다.

![image](https://user-images.githubusercontent.com/46898478/172958977-35e42584-a681-4cf9-b339-65abce820e6a.png)

![image](https://user-images.githubusercontent.com/46898478/172959026-f6365e83-d365-4380-b88e-09a3b7953189.png)
- surrogate가 있을 떄 Long-Term effect를 추정하는 방법
- 가정: treatment T가 outcome Y에 미치는 영향은 모두 surrogate를 통해서 진행됩니다.

- 과거 데이터 O가 존재하고, 이 데이터에는 long-term revenue Y와 surrogate S가 모두 존재합니다.
- 현재 데이터 E에는 surrogate S만 존재하는 상황입니다.

- surrogate S로 long-term revenue Y를 예측하는 regression 모델을 훈련합니다.

- 우리는 현재 데이터의 long-term revenue를 예측할 수 있습니다. ($\hat{Y}$)

- $\hat{Y}$를 사실이라고 가정하고 DML모델로 CATE를 추정합니다.

![image](https://user-images.githubusercontent.com/46898478/172961401-17b3f983-0025-46c9-af4e-8de8635f9671.png)

![image](https://user-images.githubusercontent.com/46898478/172961432-47dc39bd-7602-4462-8f63-164af51f4f4b.png)

![image](https://user-images.githubusercontent.com/46898478/172961484-21c85c11-bfe6-4756-8bf6-c8da305559ee.png)

![image](https://user-images.githubusercontent.com/46898478/172961517-ca649c7a-516d-4bf9-8f95-3b372ee2c315.png)
- 이렇게 진행을 해도 unbiased한 인과 효과를 추정할 수는 없습니다.
- 그 이유는 과거 데이터 O의 long-term revenue에서는 첫 투자 이후 추가 투자의 영향이 섞여있기 때문입니다. upward biased effect 존재

![image](https://user-images.githubusercontent.com/46898478/172961631-ed8e7332-6514-43a5-a032-a3c2f424e061.png)

- 위에 기술한 문제를 해결하기 위해 Dynamic treatment effect approach를 사용할 수 있습니다.
- 추가 투자의 long-term revenue에 대한 영향을 단절시킬 수 있습니다.

![image](https://user-images.githubusercontent.com/46898478/172961831-df9d0386-938d-4fd2-b7f6-ad37226f7b86.png)

- 2번의 시점 t, t+1이 존재한다고 가정했을 때
    - DML로 t+1 시점에서 treatment effect를 추정합니다.
- 추정된 t+1 시점의 추가 투자 treatment effect를 제거해 adjusted outcome을 생성합니다.
- adjusted outcome으로 surrogate model을 훈련시킵니다.

![image](https://user-images.githubusercontent.com/46898478/172962043-fd84e224-14d5-48bd-9450-c7ae9adf4091.png)
![image](https://user-images.githubusercontent.com/46898478/172962294-393ed081-cd29-4991-99ba-a7784365bbf0.png)
![image](https://user-images.githubusercontent.com/46898478/172962305-ad70e3d4-c6e2-4d55-b224-a75a77fad4e3.png)

- 이번 케이스 스터디에서는 2년 단위의 지속 투자를 6개월 단위로 binning
    - 최초 투자를 제외한 모든 사후 투자의 treatment effect를 제거했습니다.
    - 4개 반기의 매출액을 합하여 adjusted outcome으로 활용
    - adjusted outcome으로 surrogate 모델 훈련

![image](https://user-images.githubusercontent.com/46898478/172962332-39f72a34-0e09-4dcc-8eed-e5d57a860ff7.png)

![image](https://user-images.githubusercontent.com/46898478/172962465-c5a3e5b2-d93d-4a62-8413-ff4c579d24f7.png)
- 업데이트된 causal graph는 다음과 같습니다.

![image](https://user-images.githubusercontent.com/46898478/172962524-12359692-cc7b-4aac-98dc-e11fa14fd1c4.png)
- 최종적인 인과추론 pipeline은 다음과 같습니다.

![image](https://user-images.githubusercontent.com/46898478/172962584-32b49c2a-1ee4-4746-9881-0b410cb015bd.png)
![image](https://user-images.githubusercontent.com/46898478/172962614-ce699438-0a1c-4962-a4b7-5742d398551c.png)
![image](https://user-images.githubusercontent.com/46898478/172962646-bdb2095d-4903-4ff6-ae92-be67d60d8c13.png)
![image](https://user-images.githubusercontent.com/46898478/172962677-e192ed9c-cb4e-4385-8411-4502101d017e.png)
![image](https://user-images.githubusercontent.com/46898478/172962701-027318e7-64cb-4572-ab97-5dd05bd56ab0.png)

![image](https://user-images.githubusercontent.com/46898478/172962730-28054911-1796-4fca-bfdd-2da4af2cec03.png)
![image](https://user-images.githubusercontent.com/46898478/172962746-e546012b-3ec3-4f25-94ea-564658b7f5e6.png)
![image](https://user-images.githubusercontent.com/46898478/172962760-51bb9e65-592e-44c2-ac0b-3b8b0d04caad.png)
![image](https://user-images.githubusercontent.com/46898478/172962772-7deb43fb-6de9-4479-98cf-d69960b7ce22.png)
