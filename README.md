# ML_data_project
## 머신러닝을 이용한 세종시 아파트 전세가 예측 시스템
---------------------------------------------
## 프로젝트 주제 선정 배경

- 아파트 전세가 선정 이유

최근 임대차 3법으로 인해 부동산 시장에 큰 변동이 일어나며 전세 대란이 일었습니다. 전세난에 도움이 되는 시스템을 만들어 보고자 아파트 전세가를 예측할 수 있는 시스템을 만들어 보았습니다.

임대차 3법 시행을 앞두고 집주인들이 전세를 거둬들여 매물이 줄고 전세 가격이 오르는 현상에서 임차인들을 보호하기 위해 만들어진 시스템입니다. 
기대 효과로는 2가지를 생각해봤습니다. 

1. 임차인 보호
: 전세 가격을 분석하여 가격 추이 예측을 통해 임차인 보호를 수행합니다. 
2. 깡통 전세 방지
: 주택 가격이 전세 가격 이하로 떨어지면 전세 계약이 만료시 보증금(전세금) 회수가 어려워집니다. 거주 중인 집의 전세가를 미리 예측한다면 깡통 전세를 방지할 수 있을 것이라 생각합니다.

- 세종시 선정 이유

<img src = "" width = "50%"></img>
<img src = "https://user-images.githubusercontent.com/18055781/98625737-8e349300-2353-11eb-8f85-5bdbe772abc7.jpg" width = "50%"></img>

전국에서 세종시의 전세 수급 지수가 제일 높아 공급량 부족이 심각한 문제가 있고

이에 따라 매매 및 전세 가격이 상승하는 지역이었기 때문에 세종시를 선정하게 되었습니다. 

--------------------------------------------------
## 분석 목표
1. 회귀 분석을 통해 특정 변수에 따른 세종시 전세 가격 파악

: 머신 러닝 모델 중 다중 선형 회귀 분석 모델과 의사 결정 트리 모델, xgboost 모델을 사용하여 전세가 예측을 진행했습니다. 

2. 분석 결과에 따른 세종시 지역별 아파트 가격 예측 후 서비스 제안

----------------------------------------
## 변수 선정 기준

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
## 특이 변수
* 정부 청사와의 거리
[github][addresslocation.ipynb]

: 세종시 정부 청사가 있는 행정 구역 특성상 정부 청사 주변 전세값이 특히 높았던 것을 고려해 변수로 정부 청사와의 거리도 포함시키게 되었습니다. 

정부 청사와의 거리는 adrresslocation.ipynb 코드에 자세히 나와 있는데, 

정부 청사가 모여 있는 곳의 좌표와 각 아파트들의 좌표를 havasine 거리 계산법을 이용해 거리 계산 알고리즘을 구현했습니다. 

이를 통해, 각 아파트들의 정부 청사 거리를 구할 수 있었습니다. 



* SNS 긍정률
[github][positive_rate.ipynb]

: 심리적 요인에 sns가 가격 변동에 영향이 있을지를 알아 보기 위해 긍부정어 사전을 만들어 키워드를 세종으로 두고 긍정률을 조사했습니다. 

Korea Positive Opinion Lexion을 참고하여 긍정률을 산출하였고 불용어 처리 후 긍정 점수를 산출하였습니다. 

비정형 데이터를 활용하여 예측 모델의 신뢰성을 높였습니다. 



* 도시형 생활주택

: 도시형 생활주택은 서민과 1~2인 가구의 주거 안정을 위해 정부가 도입한 주거 형태입니다. 

주변 시세보다 현저히 저렴하기 때문에 분리하여 범주형 변수로 지정하여 사용했습니다. 

------------------------------------------
## 데이터 모델 및 전처리

### 데이터 전처리 - 이상치 및 특이치 제거

* 이사분범위를 바탕으로 Q3 + 1.5 * IQR 이상, Q1 - 1.5 * IQR 이하의 값을 outlier로 정의하였습니다. 
* 이상치 데이터가 머신러닝/딥러닝 모델이 학습하는 과정에 입력되면 WEIGHT에 큰 영향을 미치기 때문에 제거하여 진행하였습니다.

<img src = "https://user-images.githubusercontent.com/18055781/98628306-a9a29c80-2359-11eb-9849-33cde59b7d3e.png" width="60%"></img>

'데이터 전처리 결과: 10859개 (기존 11620개 -> 이상치 761개 제거)'

### 데이터 전처리 - 다중공선성 점검

* 독립 변수들 중 서로 상관관계가 0.9 이상인 변수들은 하나를 제거해 예측의 방해 주는 요인을 제거했습니다. 

상관 분석 결과,

[계약년월 & COFIX], [계약년월 & 금리], [건축년도 & 정부청사거리], [부동산 심리지수 & COFIX], [부동산 심리지수 & 금리], [부동산 심리지수 & 국고채], [국고채 & 계약년월]

사이에 높은 상관관계가 있는 것을 확인할 수 있었습니다. 

### 데이터 전처리 - 중요 변수 확인 (Lasso 회귀 모형)

* 중요 변수 확인과 과적합을 방지하기 위해 Lasso 회귀모형을 통해 변수를 추출했습니다. 

회귀 계수를 축소함으로써 잡음(noise) 제거 후 정확도를 개선하였고

다중 공선성의 문제를 완화시켜 모형의 해석 능력을 향상시켰습니다. 

<img src = "https://user-images.githubusercontent.com/18055781/98630238-0607bb00-235e-11eb-8e90-d9a33ff3734a.png" width = "40%"></img>

Lasso 회귀 모형 중요도 분석 결과,

다중 공선성 제거를 위해 *계약년월, 건축년도, 주택매매가격지수, 금리, 부동산 심리지수, 국고채 변수*를 제외하는 판단을 하였습니다. 

### 최종 독립변수: 층, 정부 청사와의 거리, 전입인구, COFIX, SNS 긍정률, 전용 면적, 도시형 생활주택

### 종속 변수: 보증금(만원)

--------------------------

## 데이터 분석 및 결과

: 다중 공선성을 제거한 데이터 모델을 train set과 test set으로 분리하여 구현한 세 가지 머신러닝 모델에 적용시켜 예측 값을 도출했습니다. 

세 가지 모델의 각 변수들의 회귀 계수(coef)와 예측 정확도(adj.R-squared) 값을 비교했습니다. 

#### 다중 선형 회귀 분석 모델

<img src = "https://user-images.githubusercontent.com/18055781/98631193-3f412a80-2360-11eb-9e43-1a2377660ac6.png" width = "80%"></img>

#### 의사 결정 트리 모델

<img src = "https://user-images.githubusercontent.com/18055781/98631226-541dbe00-2360-11eb-921f-d2c0e3d1df4c.png" width = "80%"></img>

#### XGboost 모델

<img src = "https://user-images.githubusercontent.com/18055781/98631263-626bda00-2360-11eb-9fa4-c09470a42828.png" width = "80%"></img>

## 프로젝트 결과
* folium을 활용해 세종시 지도와 아파트들의 좌표로 예측 값을 보기 쉽게 구현했습니다. 

: 각 지역에 관한 가상의 dataset(180개)를 바탕으로 각 머신러닝 모델에 적용해 예측값을 지도에 나타난 결과입니다. 

세 가지 모델의 예측 값을 도출해 결과의 신뢰성을 높이고자 각 모델에서 전세 값이 높을 것이라 예측하는 지역을 어두운 색으로 표시했습니다. 

그 결과, 세 가지 모델을 통해 나온 예측 집값을 하나의 지도에 겹친 결과 **증촌동, 새롬동, 한솔동, 대평동**이 

세종시 전세 값이 가장 높게 오를 것이라 예측되는 결과를 얻을 수 있었습니다. 

#### 다중 선형 회귀 분석 모델의 예측 값
<img src = "https://user-images.githubusercontent.com/18055781/98636781-d9a66b80-236a-11eb-8d67-3efda9a9e2ce.png" width="80%"></img>


#### 의사 결정 나무 모델의 예측 값
<img src = "https://user-images.githubusercontent.com/18055781/98636829-eb880e80-236a-11eb-84f9-20f39cfeece4.png" width="80%"></img>


#### XGboost 모델의 예측 값
<img src = "https://user-images.githubusercontent.com/18055781/98636879-ffcc0b80-236a-11eb-9000-994338919f0e.png" width="80%"></img>


--------------------------------------------
## 분석 모델을 통한 서비스 방안

 **기존의 부동산 어플리케이션 서비스는 과거와 현재의 시세만 제공합니다.**

**이 정보만으로 집을 구하는 무주택자의 미래 전세 가격과 깡통 전세 같은 걱정을 해결할 수 없습니다.**

**프로젝트에서 구현한 전세가 예측 서비스를 통해 무주택자의 고민을 해결하는 방안을 제시하고 싶습니다.** 




