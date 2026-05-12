# SECOM Semiconductor Wafer Anomaly Detection & Diagnosis
> **LSTM Autoencoder** 기반 이상 탐지 및 **SHAP**를 활용한 불량 원인 분석 파이프라인

본 프로젝트는 반도체 공정 센서 데이터(SECOM)를 활용하여 공정의 이상 유무를 감지하고, 불량 발생 시 핵심 원인 센서를 추적하는 고도화된 분석 시스템을 구축하는 과정입니다. 

*기존 동아리 프로젝트의 기초 로직을 바탕으로 하였으며, 본인은 **기술적 결함 해결(Debugging), 모델 파이프라인 최적화, 그리고 원인 분석 모듈(XAI) 고도화**를 전담하였습니다.*

---

## 🛠 Tech Stack
- **Language**: Python 3.x
- **Environment**: Google Colab (T4 GPU)
- **Deep Learning**: TensorFlow, Keras (LSTM Autoencoder)
- **XAI & ML**: SHAP, XGBoost
- **Data Analysis**: Pandas, NumPy, Scikit-learn

---

## 🚀 Key Features
1. **Temporal Data Split**: 시계열 특성을 고려하여 과거 데이터로 학습하고 미래 데이터를 예측하는 7:15:15 분할 적용.
2. **Advanced Preprocessing**: 분산이 0이거나 결측치가 과도한 센서(노이즈)를 필터링하여 591개에서 **468개의 핵심 센서** 추출.
3. **Anomaly Detection**: LSTM Autoencoder를 통해 정상 공정의 리듬을 학습하고, 복원 오차(MSE)를 기반으로 이상 징후 포착.
4. **Root Cause Analysis (XAI)**: SHAP(KernelExplainer)를 사용하여 불량으로 판정된 특정 웨이퍼의 주범 센서를 시각화.
5. **Case-Based Reasoning**: Cosine Similarity를 활용하여 현재 불량과 가장 유사한 과거 조치 사례 매칭 시스템 구현.

---

## 🐞 Troubleshooting (Debugging Record)

로컬 환경의 기술적 한계를 극복하고 코랩(Colab) 환경에서 안정적인 파이프라인을 구축하기 위해 해결한 주요 이슈들입니다.

### 1. NumPy 2.0 호환성 및 커널 크래시
- **Issue**: 최신 NumPy 2.0 설치 시 TensorFlow 및 SHAP 라이브러리와의 충돌로 인해 모델 학습 중 커널이 무한 재시작되는 현상 발생.
- **Solution**: `!pip install "numpy<2"` 명령어를 통해 버전을 1.2x대로 강제 고정하여 환경 정합성 확보.

### 2. 메모리 참조 오류 (NameError: 'autoencoder' is not defined)
- **Issue**: 대규모 연산이 필요한 SHAP 분석 단계에서 세션 타임아웃 혹은 실행 순서 꼬임으로 인해 모델 객체가 유실됨.
- **Solution**: 모델 빌드-학습-평가를 유기적으로 연결하는 체크포인트 로직을 도입하고, `Model(inputs=autoencoder.input, outputs=...` 방식을 통해 학습된 모델로부터 인코더를 동적으로 추출하도록 개선.

### 3. 데이터 인덱싱 불일치
- **Issue**: 시퀀스 데이터 생성 시 라벨 데이터와의 길이 불일치로 인한 Boolean Indexing 에러 발생.
- **Solution**: `quick_make_sequences` 함수 내에서 `X_seq`와 `y_seq`를 동시에 반환하도록 로직을 수정하여 데이터 정렬 보장.

---

## 📊 Analysis Result
- **Detection Rate**: 테스트 샘플 222개 중 5개의 이상 샘플 감지 (약 2.25%).
- **Diagnosis**: #111번 웨이퍼 불량의 주범으로 **'Sensor_128_t0'** 식별. 
- **Action Plan**: 과거 사례 #817과 79.7%의 유사성을 확인하여 '로봇 암 영점 조절' 조치 권고.

---

## 📝 Credits & Acknowledgement
- **Original Idea**: 친구의 동아리 경진대회 주제 기반
- **Major Contributions**: 
  - 로컬 환경의 기술적 결함(Dependency, Memory) 해결
  - LSTM-AE 기반 원인 분석 파이프라인(XAI) 고도화
  - Colab GPU 자원 활용 최적화