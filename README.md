# 프로젝트명

📢 2025년 1학기 [AIKU](https://github.com/AIKU-Official) 활동으로 진행한 프로젝트입니다

## 소개
본 프로젝트는 유네스코가 '소멸 위기 언어'로 지정한 제주 방언을 보존하고 기술적으로 활용하기 위해 시작되었습니다.  기존 음성 인식 기술은 대부분 표준어를 기반으로 개발되어 제주 방언과 같은 다양한 언어 변이체에 대해 낮은 성능을 보입니다.  이 문제를 해결하고자, 본 연구에서는 제주 방언 음성을 표준 한국어 텍스트로 변환하는 End-to-End 음성 번역 모델을 개발했습니다.
프로젝트의 핵심은 사전 훈련된 음성 인코더(Whisper)와 텍스트 디코더(T5)를 '커넥터(Connector)' 모듈로 연결하는 아키텍처를 실험하는 것입니다.  그러나 실험 결과, MLP, Q-Former, STE 등 모든 커넥터 기반 모델은 단순 미세조정(Fine-tuning)된 Whisper 모델보다 성능이 현저히 낮았습니다.  본 프로젝트는 이러한 실패의 원인을 분석하고, 모듈식 음성 번역 시스템의 불안정성과 그 원인을 탐구한 내용을 담고 있습니다. 분석 결과, Whisper의 음향-음운론적 표현과 T5의 구문-의미론적 표현 간의 근본적인 불일치가 주요 실패 원인으로 밝혀졌습니다. 

## 방법론
<img width="630" alt="image" src="https://github.com/user-attachments/assets/a297046b-3f5e-42b5-a848-6201d89ade94" />

본 프로젝트는 제주 방언 음성을 표준 한국어 텍스트로 변환하기 위해 Sedláček 등의 연구에서 제안된 Encoder-Connector-Decoder 구조를 채택했습니다. 
모델은 세 가지 핵심 요소로 구성됩니다. 

음성 인코더 (Whisper Encoder)

입력된 음성 신호(x)로부터 음향적 특징($H_ASR$)을 추출합니다. 
자연어 처리에 강점을 보이는 Whisper 모델을 사용했으며, 학습 과정에서는 파라미터를 동결(freeze)하여 사전 훈련된 가중치를 그대로 활용했습니다. 

커넥터 (Connector)

동결된 Whisper 인코더와 T5 디코더 사이의 표현 공간 차이를 매끄럽게 연결하는 역할을 합니다. 
Whisper가 추출한 음성 임베딩을 T5 디코더가 이해할 수 있는 텍스트 기반의 임베딩(Z)으로 변환합니다. 

이 모듈의 파라미터만 학습하여 효율성을 높이고자 했습니다.
텍스트 디코더 (T5 Decoder)

커넥터를 통해 변환된 임베딩(Z)을 입력받아 최종적으로 표준 한국어 텍스트(y)를 생성합니다. 
한국어 데이터로 사전 훈련된 `paust/pko-t5-base` 모델을 사용했으며, 인코더와 마찬가지로 파라미터를 동결했습니다. 

2. 커넥터 아키텍처
<img width="518" alt="image" src="https://github.com/user-attachments/assets/e5a516da-7e03-474d-bfd7-379938187d25" />

인코더와 디코더를 잇는 최적의 방법을 찾기 위해 다음과 같은 세 가지 커넥터 모듈을 직접 구현하고 실험했습니다. 

Q-Former: 고정된 개수의 학습 가능한 쿼리(learnable queries)를 사용하여 ASR 임베딩에서 필요한 정보를 추출하는 경량 트랜스포머 기반 모듈입니다.  각 쿼리는 Cross-Attention을 통해 음성 시퀀스로부터 정보를 요약하고 변환합니다. 

구현 코드: `qformer.py`
STE (Subsampler-Transformer Encoder): 1D Convolution 레이어로 음성 임베딩의 시퀀스 길이를 줄인 후(Subsampling), 여러 개의 트랜스포머 인코더 블록을 통과시켜 특징을 추출하는 구조입니다. 

구현 코드: `ste.py`
MLP (Multi-Layer Perceptron): 가장 단순한 형태의 커넥터로, 여러 개의 선형 레이어(Linear Layer)와 활성화 함수로 구성됩니다.

3. 분석 및 평가
<img width="755" alt="image" src="https://github.com/user-attachments/assets/7a490c85-a2be-432a-a379-9c2e8314f1e8" />
<img width="849" alt="image" src="https://github.com/user-attachments/assets/d70a3b62-42ea-46c7-a973-1324b22d83d2" />
성능 평가: 번역 품질은 BLEU, 단어 오류율은 WER, 글자 오류율은 CER 지표를 사용하여 측정했습니다. 
UMAP 시각화: Whisper, 커넥터, T5의 임베딩 공간을 2차원으로 시각화하여 각 모듈 간의 표현이 얼마나 잘 정렬되었는지 분석했습니다.  분석 결과, 세 임베딩 공간이 명확하게 분리되어 커넥터가 제 역할을 하지 못함을 확인했습니다

## 환경 설정
```
  conda env create -f environment.yml
```
## 사용 방법

데이터 전처리 후 `whisper_t5_ddp_connector.py`실행 \
데이터 : [제주어 AIhub 데이터셋](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=121) \
전처리 : [선행 연구](https://github.com/maeseok/Jeju_Translation.github.io) 에서 나온 전처리 방법을 그대로 따라하여 csv 파일을 만들었음. 

## 예시 결과

<img width="557" alt="image" src="https://github.com/user-attachments/assets/dc2dbbf9-3a5c-449f-8f41-de65787c55d6" />


## 팀원

(프로젝트에 참여한 팀원의 이름과 깃헙 프로필 링크, 역할을 작성해주세요)

- [권도영](https://github.com/douyoung89): 프로젝트 총괄, 학습 코드 구현 및 논문 작성
- [이창엽](): Q-Former 및 STE 커넥터 모듈 구현 및 실험
- [홍예진]():  Baseline 모델 및 UMAP 시각화 구현 및 실험 
