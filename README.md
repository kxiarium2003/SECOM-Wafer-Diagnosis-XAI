# SECOM Semiconductor Process Anomaly Detection

반도체 제조 공정 데이터(SECOM)를 활용하여 공정 이상을 탐지하고, 이를 기반으로 수율 리스크(Yield Risk) 및 잠재적 경제 손실을 추정하는 데이터 분석 프로젝트입니다.

## 프로젝트 개요

본 프로젝트는 UCI Machine Learning Repository의 SECOM(Semiconductor Manufacturing) 데이터를 활용하여 반도체 제조 공정에서 발생하는 이상 징후를 탐지하고, 이를 비즈니스 관점의 리스크 지표로 연결하는 것을 목표로 수행되고 있습니다.

SECOM 데이터는 실제 반도체 제조 공정에서 수집된 센서 데이터로 구성되어 있으며, 정상(Pass)과 불량(Fail) 여부에 대한 정보만 제공됩니다. 따라서 단순 분류 문제로 접근하기보다 공정 이상 탐지(Anomaly Detection) 관점에서 문제를 재정의하고 분석을 진행하고 있습니다.

### 프로젝트 정보

* 기간 : 2026.05 ~ 진행 중
* 형태 : 개인 프로젝트
* 주제 : 반도체 공정 이상 탐지 및 수율 리스크 추정

---

## 프로젝트 배경

반도체 제조 공정은 수백 개의 센서와 수많은 공정 단계를 거치는 복잡한 시스템으로 구성됩니다.

실제 산업 현장에서는 단순히 불량 여부를 판별하는 것뿐만 아니라, 공정 이상을 조기에 탐지하여 향후 발생 가능한 수율 저하와 경제적 손실을 예측하는 것이 중요합니다.

본 프로젝트에서는 다음과 같은 질문에 답하고자 하였습니다.

* 공정 센서 데이터만으로 이상 징후를 탐지할 수 있는가?
* 정상 제품과 불량 제품을 구분하는 주요 패턴이 존재하는가?
* 이상 탐지 결과를 수율 리스크 및 경제적 손실 추정으로 연결할 수 있는가?

---

## 데이터

### SECOM Dataset

* 출처 : [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/179/secom)

### 데이터 구성

#### secom.data

* 샘플 수 : 1,567
* 센서 변수 : 590개

반도체 제조 공정 전반에 걸쳐 수집된 센서 신호 및 공정 측정값으로 구성되어 있습니다.

#### secom_labels.data

* Timestamp
* Result

결과 변수는 다음과 같이 구성됩니다.

* Pass : -1
* Fail : 1

---

## 프로젝트 파이프라인

```text
SECOM Dataset
    ↓
Data Preprocessing
    ├── Missing Value Analysis
    ├── Variance Threshold
    ├── Median Imputation
    └── Standard Scaling
    ↓
Exploratory Data Analysis
    ↓
Baseline Modeling
    └── MLP Classifier
    ↓
Anomaly Detection
    ├── Isolation Forest
    └── Autoencoder
    ↓
Yield Risk Scoring
    ↓
Financial Impact Estimation
```

---

## Exploratory Data Analysis (EDA)

### 데이터 구조 확인

SECOM 데이터는 총 1,567개의 샘플과 590개의 센서 변수로 구성되어 있습니다.

라벨 데이터와 결합 후 최종 데이터셋은 다음과 같습니다.

* Rows : 1,567
* Columns : 591 (590 Sensors + Result)

---

### 결측치 분석

590개 센서 변수 중 일부 센서는 높은 비율의 결측치를 포함하고 있었습니다.

분석 결과:

* 결측치 비율이 50%를 초과하는 센서 : 28개

공정 데이터의 특성과 변수 의미의 익명화를 고려하여 도메인 기반 보간 대신 통계적 결측치 처리 방식을 적용하기로 하였습니다.

---

### 전처리

#### Variance Threshold

분산이 거의 존재하지 않는 센서를 제거하여 정보량이 없는 변수를 제외하였습니다.

#### Missing Value Imputation

Median Imputation을 사용하여 결측치를 대체하였습니다.

#### Feature Scaling

StandardScaler를 적용하여 센서별 단위 차이를 정규화하였습니다.

---

## Baseline Modeling

### MLP Classifier

이상 탐지 모델 적용 전, 지도학습 기반 분류 성능을 확인하기 위해 MLP(Multi-Layer Perceptron)를 Baseline 모델로 사용하였습니다.

### 학습 설정

* Train : 70%
* Validation : 15%
* Test : 15%

추가적으로 클래스 불균형 문제를 완화하기 위해 class weight를 적용하였습니다.

---

### 결과

| Metric      | Score |
| ----------- | ----: |
| Accuracy    |  0.95 |
| ROC-AUC     |  0.53 |
| Fail Recall |  0.00 |

Classification Report

| Class | Precision | Recall | F1-score |
| ----- | --------: | -----: | -------: |
| Pass  |      0.96 |   0.99 |     0.97 |
| Fail  |      0.00 |   0.00 |     0.00 |

---

### 분석

SECOM 데이터는 정상(Pass) 샘플이 대부분을 차지하는 심한 클래스 불균형 구조를 가지고 있습니다.

MLP 모델은 높은 Accuracy를 보였으나 Fail 샘플을 거의 탐지하지 못하였으며, ROC-AUC 역시 랜덤 수준에 가까운 성능을 기록하였습니다.

이를 통해 본 데이터셋은 단순 지도학습 기반 분류보다 이상 탐지 기반 접근이 더욱 적합할 가능성을 확인하였습니다.

---

## 현재까지의 주요 인사이트

* SECOM 데이터는 높은 차원(590개 센서)의 제조 공정 데이터이다.
* 일부 센서는 높은 비율의 결측치를 포함한다.
* 클래스 불균형이 매우 심하다.
* MLP 기반 분류 모델은 Fail 패턴을 효과적으로 학습하지 못하였다.
* 본 문제는 Classification보다 Anomaly Detection 관점에서 접근하는 것이 적절할 가능성이 높다.

---

## 향후 계획

### Anomaly Detection

* Isolation Forest
* Autoencoder 기반 이상 탐지

### Yield Risk Estimation

이상 탐지 점수(Anomaly Score)를 기반으로 공정 수율 리스크를 정의할 예정이다.

### Financial Impact Estimation

다음 외부 데이터를 활용하여 잠재 손실 규모를 추정할 계획이다.

* USD/KRW 환율
* 반도체 시장 가격 지표
* 생산량 가정(Scenario-based)

### Dashboard

* Streamlit 기반 대시보드 구축
* 공정 상태 모니터링
* 리스크 시각화

---

## Repository Structure

```text
.
├── data/
├── EDA/
├── experiments/
└── README.md
```

---

## Tech Stack

### Data Analysis

* Python
* Pandas
* NumPy

### Machine Learning

* Scikit-learn
* TensorFlow / Keras

### Visualization

* Matplotlib
* Seaborn

---

## Takeaways

* 반도체 제조 공정 데이터 분석
* 결측치 및 고차원 센서 데이터 전처리
* 클래스 불균형 문제 분석
* Baseline 모델 구축 및 평가
* 이상 탐지 기반 문제 재정의
* 제조 AI 및 스마트팩토리 데이터 분석 경험
