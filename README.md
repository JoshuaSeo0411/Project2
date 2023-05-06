# [중소형 상장 기업 대상 기업 정보의 동태적 분석을 통한 실질적 부실 기업 예측] by FBI

UBION 6기 Project2 최우수상🏆

![1](https://user-images.githubusercontent.com/124761683/236612569-c23585e4-316e-4ebf-a551-68f28b5fdf7c.JPG)

![2](https://user-images.githubusercontent.com/124761683/236612584-e07de9c6-3135-47a9-ab33-36511c977a97.JPG)

![3](https://user-images.githubusercontent.com/124761683/236612588-562d13ec-76f9-4701-9892-4f70ecae1a69.JPG)

![4](https://user-images.githubusercontent.com/124761683/236612593-991a1a6d-ff09-4a46-a6a3-6e5eb75560c4.JPG)


---
# **프로젝트 소개**
 본 프로젝트는 코스피와 코스닥 종목(대형주 제외)을 대상으로 t+1년에 실질적 부실기업(상장폐지 또는 관리종목)이 될지, 아니면 정상기업으로 유지할지를 분류 예측하고자 한다. 이 과정에서 기존 연구들과의 차별을 주기 위해 동태적 분석을 실시하였다. 이를 통해 9개의 최적 피쳐를 선정하였고, 총 7개의 분류, 딥러닝 알고리즘을 활용해 가장 뛰어난 성능을 보인 XGB Classfier를 최종 모델로 선정하였다. 최종 모델을 통해 부실기업을 분류할 때에 있어 부실기업을 정상기업으로 분류하는 2종 오류를 방지하기 위해 재현률(Recall)에 집중하였으며, 이를 통해 부실기업 분류의 위험성을 최소화 하였다. 또한 다른 연구와 차별된 거래량 파생변수들을 통해 투자자들에게 간접적인 기여를 할 수 있음을 확인하였다.

---
# **구조**


## **0. 데이터 수집**
- 대상 : KOSPI & KOSDAQ 상장 기업
- 제외 : 금융 및 외국계 기업, SPAC주, 대형주(시가총액 상위 100위 내 기업)
- 수집기간 : 2010 ~ 2019년 (동태적 분석을 위한 변화율 목적 2010년 부터 수집)
- 분석기간 : 2011 ~ 2019년 
- 최초피쳐 : 총 134개(재무정보 + 시장정보)
- 개수 : 2,478사 / 20,505개
- 출처 : TS2000

## **1. Preprocessing**
#### 결측치
- 연결제무재표데이터 결측치 별도제무재표데이터로 대체
- 다트(DART)확인 후 채울 수 있는 값들 대체
- 전체 값이 결측치인 컬럼 제거
- 이 외의 결측지는 제거

#### 이상치
- 거래소코드가 2개인 기업 중복 제거
- 회사명이 2개인 거래소코드 중복 확인
- 999999 (inf, -inf) 제거
- 양극단치 0.5%씩 Trimming(IQR 0.5% 이하, 99.5% 이상 값들 제거)

## 2. Labeling
- Label1 : 상장폐지(자발적 상장폐지 제외) + 관리종목 지정 기업 t-1기, t-2기
  * 상장폐지가 4월 전에 이루어진 경우 t-1기 데이터가 없기에 t-2기, t-3기에 1부여
  * 관리종목의 경우 최초 지정된 년도를 t기로 판단
- Label0 : 정상기업(1에 해당하지 않는 기업)

## 3. Data split & Scaling
- Train set : 2011 ~ 2017 (7년)
- Test set : 2018 ~ 2019 (2년)
- Scaling : Train set, test set 각각 Standard scaling(Data Leakage 방지)

## 4. Feature Selection
- Train set으로만 Feature Selection
- 검정
  - 정규성검정(Shapiro-Wilks, Anderson, Dist plot, qq plot)
  - 독립성검정(Durbin-Watson)
    - 분류 모형에서는 통계검정이 중요하지 않기에 등분산성 검증 따로 진행 X
- 아래 3개 모델 중 3개 이상 중복 피쳐를 최종 선택
  - T-test(통계분석)
  - Lasso(Wrapper Method)
  - Stepwise(Embbeded Method)

## 5. Modeling

[단일분류]
- Logistic
- Decision Tree
- SVM(SVC 활용)

[Ensamble]
- Random Forest
- **XGB**
- Stacking (Logistic + SVC + XGBoost)

[Deep Learning]
- TabNet

**-> 최종모델 : XGBClassifier**


## 6. Evaluation(Test set)
- ACC : 0.65
- Precision : 0.07
- **Recall : 0.92**
- F1-score : 0.13
- AUC : 0.77

[Recall이 상대적으로 높은 성능을 보이는 모델]
- 부실기업을 정상기업으로 판단하는 2종 오류 해소
- 기업 부실을 판단하는 입장에서 위험 최소화

[정상기업과 부실기업의 차이 및 거래량 확인]

![5](https://user-images.githubusercontent.com/124761683/236614634-29462555-8d7c-40e3-89a3-4f07ed6330db.JPG)

![6](https://user-images.githubusercontent.com/124761683/236614642-3009744e-f775-4ca5-953b-2024d7bf38f5.JPG)

![7](https://user-images.githubusercontent.com/124761683/236614647-f1a0726e-d920-4df3-b0c5-34d5ce3e6484.JPG)

![8](https://user-images.githubusercontent.com/124761683/236614649-2f9b0d4d-1713-4c02-b5c8-284f1c19c8c8.JPG)


