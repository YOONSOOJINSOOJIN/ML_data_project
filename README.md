# ML_data_project
## 머신러닝을 이용한 세종시 아파트 전세가 예측 시스템
---------------------------------------------
### 프로젝트 주제 선정 배경

- 아파트 전세가 선정 이유

최근 임대차 3법으로 인해 부동산 시장에 큰 변동이 일어나며 전세 대란이 일었습니다. 전세난에 도움이 되는 시스템을 만들어 보고자 아파트 전세가를 예측할 수 있는 시스템을 만들어 보았습니다.

임대차 3법 시행을 앞두고 집주인들이 전세를 거둬들여 매물이 줄고 전세 가격이 오르는 현상에서 임차인들을 보호하기 위해 만들어진 시스템입니다. 
기대 효과로는 2가지를 생각해봤습니다. 

1. 임차인 보호
: 전세 가격을 분석하여 가격 추이 예측을 통해 임차인 보호를 수행합니다. 
2. 깡통 전세 방지
: 주택 가격이 전세 가격 이하로 떨어지면 전세 계약이 만료시 보증금(전세금) 회수가 어려워집니다. 거주 중인 집의 전세가를 미리 예측한다면 깡통 전세를 방지할 수 있을 것이라 생각합니다.

- 세종시 선정 이유

<img src = "https://user-images.githubusercontent.com/18055781/98625421-eae37e00-2352-11eb-82b9-661eb2001672.jpg" width = "50%"></img>

<img src = "https://user-images.githubusercontent.com/18055781/98625737-8e349300-2353-11eb-8f85-5bdbe772abc7.jpg" width = "50%></img>

전국에서 세종시의 전세 수급 지수가 제일 높아 공급량 부족이 심각한 문제가 있고

이에 따라 매매 및 전세 가격이 상승하는 지역이었기 때문에 세종시를 선정하게 되었습니다. 

--------------------------------------------------
### 분석 목표
1. 회귀 분석을 통해 특정 변수에 따른 세종시 전세 가격 파악

: 머신 러닝 모델 중 다중 선형 회귀 분석 모델과 의사 결정 트리 모델, xgboost 모델을 사용하여 전세가 예측을 진행했습니다. 

2. 분석 결과에 따른 세종시 지역별 아파트 가격 예측 후 서비스 제안

----------------------------------------
### 변수 선정 기준

- 4가지 요인으로 나누어 변수를 모아 보았습니다. 

1. 심리적 요인

: SNS 긍정률, 부동산 심리지수, 소비자 물가 지수 

2. 거리적 요인

: 정부 청사와의 거리

3. 가격적 요인

: 도시형 생활주택, 주택 가격 매매지수, 아파트 전세 가격 지수

4. 경제적 요인

: 금리, COFIX, 국고채

<img src = "https://user-images.githubusercontent.com/18055781/98626324-d607ea00-2354-11eb-9a10-127b11093a08.png" width = "90%"></img>

-----------------------------
### 특이 변수
* 정부 청사와의 거리
[github][]

: 세종시 정부 청사가 있는 행정 구역 특성상 정부 청사 주변 전세값이 특히 높았던 것을 고려해 변수로 정부 청사와의 거리도 포함시키게 되었습니다. 

정부 청사와의 거리는 adrresslocation.ipynb 코드에 자세히 나와 있는데, 

정부 청사가 모여 있는 곳의 좌표와 각 아파트들의 좌표를 havasine 거리 계산법을 이용해 거리 계산 알고리즘을 구현했습니다. 

이를 통해, 각 아파트들의 정부 청사 거리를 구할 수 있었습니다. 



* SNS 긍정률

: 심리적 요인에 sns가 가격 변동에 영향이 있을지를 알아 보기 위해 긍부정어 사전을 만들어 키워드를 세종으로 두고 긍정률을 조사했습니다. 

Korea Positive Opinion Lexion을 참고하여 긍정률을 산출하였고 불용어 처리 후 긍정 점수를 산출하였습니다. 

비정형 데이터를 활용하여 예측 모델의 신뢰성을 높였습니다. 



* 도시형 생활주택

: 도시형 생활주택은 서민과 1~2인 가구의 주거 안정을 위해 정부가 도입한 주거 형태입니다. 

주변 시세보다 현저히 저렴하기 때문에 분리하여 범주형 변수로 지정하여 사용했습니다. 

------------------------------------------
### 데이터 모델 및 전처리

## 데이터 전처리 - 이상치 및 특이치 제거

* 이사분범위를 바탕으로 Q3 + 1.5 * IQR 이상, Q1 - 1.5 * IQR 이하의 값을 outlier로 정의하였습니다. 
* 이상치 데이터가 머신러닝/딥러닝 모델이 학습하는 과정에 입력되면 WEIGHT에 큰 영향을 미치기 때문에 제거하여 진행하였습니다.

<img src = "https://user-images.githubusercontent.com/18055781/98628306-a9a29c80-2359-11eb-9849-33cde59b7d3e.png" width="60%"></img>

'데이터 전처리 결과: 10859개 (기존 11620개 -> 이상치 761개 제거)'

## 데이터 전처리 - 다중공선성 점검

* 독립 변수들 중 서로 상관관계가 0.9 이상인 변수들은 하나를 제거해 예측의 방해 주는 요인을 제거했습니다. 

상관 분석 결과,

[계약년월 & COFIX], [계약년월 & 금리], [건축년도 & 정부청사거리], [부동산 심리지수 & COFIX], [부동산 심리지수 & 금리], [부동산 심리지수 & 국고채], [국고채 & 계약년월]

사이에 높은 상관관계가 있는 것을 확인할 수 있었습니다. 

## 데이터 전처리 - 중요 변수 확인

* 중요 변수 확인과 과적합을 방지하기 위해 Lasso 회귀모형을 통해 변수를 추출했습니다. 

회귀 계수를 축소함으로써 잡음(noise) 제거 후 정확도를 개선하였고

다중 공선성의 문제를 완화시켜 모형의 해석 능력을 향상시켰습니다. 












