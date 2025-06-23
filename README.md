# 프로젝트명

📢 2025년 1학기 [AIKU](https://github.com/AIKU-Official) 활동으로 진행한 프로젝트입니다

## 소개
본 프로젝트는 유네스코가 '소멸 위기 언어'로 지정한 제주 방언을 보존하고 기술적으로 활용하기 위해 시작되었습니다.  기존 음성 인식 기술은 대부분 표준어를 기반으로 개발되어 제주 방언과 같은 다양한 언어 변이체에 대해 낮은 성능을 보입니다.  이 문제를 해결하고자, 본 연구에서는 제주 방언 음성을 표준 한국어 텍스트로 변환하는 End-to-End 음성 번역 모델을 개발했습니다.
프로젝트의 핵심은 사전 훈련된 음성 인코더(Whisper)와 텍스트 디코더(T5)를 '커넥터(Connector)' 모듈로 연결하는 아키텍처를 실험하는 것입니다.  그러나 실험 결과, MLP, Q-Former, STE 등 모든 커넥터 기반 모델은 단순 미세조정(Fine-tuning)된 Whisper 모델보다 성능이 현저히 낮았습니다.  본 프로젝트는 이러한 실패의 원인을 분석하고, 모듈식 음성 번역 시스템의 불안정성과 그 원인을 탐구한 내용을 담고 있습니다. 분석 결과, Whisper의 음향-음운론적 표현과 T5의 구문-의미론적 표현 간의 근본적인 불일치가 주요 실패 원인으로 밝혀졌습니다. 

## 방법론

본 프로젝트는 제주 방언 음성을 표준 한국어 텍스트로 변환하기 위해 Sedláček 등의 연구에서 제안된 Encoder-Connector-Decoder 구조를 채택했습니다. 

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
