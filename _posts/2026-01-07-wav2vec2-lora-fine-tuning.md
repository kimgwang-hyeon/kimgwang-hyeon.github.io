---
layout: post
title: "Wav2Vec2 + LoRA로 어린이 음성 인식 모델 개발하기"
date: 2026-01-07
tags: [AI, PyTorch, LoRA, Wav2Vec2, 음성인식]
---

## 들어가며

Reading Buddy 프로젝트에서 어린이 음성 인식 모델을 개발하면서 Wav2Vec2와 LoRA를 활용한 경험을 공유합니다.

## 문제 상황

기존 한국어 음성 인식 모델들은 주로 성인 음성으로 학습되어 어린이 음성에 대한 인식률이 낮았습니다.
- 초기 테스트 결과 PER(음소 오류율) **36.67%**
- 3억 개 파라미터 전체 파인튜닝은 GPU 메모리 부담

## 해결 방법

### 1. Wav2Vec2 선택

Wav2Vec2는 Self-Supervised Learning으로 43.6만 시간의 오디오로 사전학습된 모델입니다.

```python
from transformers import Wav2Vec2ForCTC, Wav2Vec2Processor

model = Wav2Vec2ForCTC.from_pretrained("kresnik/wav2vec2-large-xlsr-korean")
processor = Wav2Vec2Processor.from_pretrained("kresnik/wav2vec2-large-xlsr-korean")
```

### 2. LoRA 적용

전체 파라미터의 **1.5%만 학습**하여 효율성 확보:

```python
from peft import LoraConfig, get_peft_model

lora_config = LoraConfig(
    r=8,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj"],
    lora_dropout=0.1,
)

model = get_peft_model(model, lora_config)
```

### 3. Curriculum Learning

쉬운 데이터부터 점진적으로 난이도를 높여 학습:
1. 명확한 발음 (30시간)
2. 중간 난이도 (90시간)
3. 난독증 아동 음성 (148시간)

## 결과

- PER **60.3% 개선** (36.67% → 14.55%)
- GPU 메모리 **70% 절감**
- 학습 시간 **40% 단축**

## 배운 점

LoRA 같은 Parameter-Efficient Fine-Tuning 기법을 활용하면 제한된 자원으로도 고품질 모델을 개발할 수 있다는 것을 체감했습니다.
