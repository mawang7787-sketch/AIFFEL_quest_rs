# AIFFEL_quest_rs
18기 유비 레포지토리
# AIFFEL Campus Online Code Peer Review Template

- 코더 : 유비
- 리뷰어 : 송반디

---

# PRT(Peer Review Template)

- [x] 1. 주어진 문제를 해결하는 완성된 코드가 제출되었나요?

    - 네, 주어진 문제를 해결하기 위한 전체 코드가 제출되었습니다.
    - Stanford Dogs 데이터셋을 다운로드하고, ImageFolder를 이용해 이미지 데이터를 불러온 뒤 train/test 데이터셋으로 분리하였습니다.
    - 기본 전처리, 기본 Augmentation, CutMix, MixUp을 각각 구현하고, ResNet50 pretrained 모델을 활용해 성능 비교까지 진행했습니다.
    - 최종적으로 Baseline, Basic Augmentation, CutMix, MixUp의 validation accuracy를 비교하는 그래프와 결과 해석이 포함되어 있어 프로젝트의 최종 결과물이 확인됩니다.

    근거로 볼 수 있는 부분
    - 데이터셋 다운로드 및 압축 해제 코드
    - ImageFolder를 이용한 데이터 로딩 코드
    - normalize_and_resize_img(), augment() 함수 정의
    - cutmix(), mixup() 함수 구현
    - ResNet50 모델 학습 및 validation accuracy 출력
    - 최종 비교 그래프와 회고 markdown 작성 부분

---

- [x] 2. 전체 코드에서 가장 핵심적이거나 가장 복잡하고 이해하기 어려운 부분에 작성된 주석 또는 doc string을 보고 해당 코드가 잘 이해되었나요?

    - 네, 핵심 코드에 주석이 비교적 잘 작성되어 있어 코드의 흐름을 이해할 수 있었습니다.
    - 특히 CutMix 구현 부분에서 랜덤 박스 좌표를 생성하는 get_clip_box() 함수, 이미지의 일부 영역을 다른 이미지로 대체하는 mix_2_images() 함수, 라벨을 면적 비율에 따라 soft label로 섞는 mix_2_labels() 함수에 주석이 달려 있어 작동 원리를 파악하기 좋았습니다.
    - MixUp 구현 부분에서도 이미지 전체를 일정 비율로 섞고, 라벨도 같은 비율로 섞는다는 설명이 있어 CutMix와 MixUp의 차이를 이해하기 쉬웠습니다.
    - 학습 함수 train_with_mix()에서는 soft label을 사용하기 때문에 일반적인 CrossEntropyLoss 대신 log_softmax를 이용해 직접 loss를 계산한 부분이 핵심인데, 해당 부분에도 “soft label용 loss”라는 주석이 있어 코드의 목적을 이해하는 데 도움이 되었습니다.

    핵심 코드라고 생각한 부분
    python     def mix_2_labels(label_a, label_b, x_min, y_min, x_max, y_max,                      image_size_x, image_size_y, num_classes=120):         # 박스 면적이 전체에서 차지하는 비율         mixed_area = (x_max - x_min) * (y_max - y_min)         total_area = image_size_x * image_size_y         ratio = mixed_area / total_area          # 라벨을 원-핫 벡터로 변환         label_a = F.one_hot(torch.tensor(label_a), num_classes=num_classes).float()         label_b = F.one_hot(torch.tensor(label_b), num_classes=num_classes).float()          # 면적 비율대로 섞기         mixed_label = (1 - ratio) * label_a + ratio * label_b         return mixed_label     

    이 부분은 CutMix에서 단순히 이미지만 섞는 것이 아니라, 섞인 이미지의 비율에 맞춰 라벨도 함께 섞어야 한다는 핵심 개념을 구현한 부분입니다. 주석을 통해 왜 원-핫 인코딩을 하고, 왜 면적 비율을 계산하는지 이해할 수 있었습니다.

---

- [x] 3. 에러가 난 부분을 디버깅하여 문제를 해결한 기록을 남겼거나 새로운 시도 또는 추가 실험을 수행해봤나요?

    - 명시적인 에러 디버깅 기록은 많지 않았지만, 프로젝트 평가 기준을 넘어서는 추가 실험이 수행되었습니다.
    - 기본 Augmentation만 적용한 모델과 적용하지 않은 모델을 비교한 것에서 끝나지 않고, CutMix와 MixUp까지 직접 구현하여 총 4가지 조건을 비교했습니다.
    - 특히 CutMix와 MixUp은 단순히 라이브러리 함수를 사용한 것이 아니라, 이미지와 라벨을 섞는 과정을 직접 함수로 구현했다는 점에서 추가 실험의 의미가 있습니다.
    - 실험 결과도 각 epoch별 validation accuracy로 출력되었고, 마지막에 그래프로 비교하여 결과를 시각적으로 확인할 수 있도록 구성했습니다.

    수행한 실험
    - Baseline, 즉 augmentation 없는 ResNet50 학습
    - 기본 augmentation 적용 학습
    - CutMix 적용 학습
    - MixUp 적용 학습
    - 네 가지 실험 결과의 validation accuracy 비교

    실험 결과
    - Baseline: 최종 validation accuracy 약 80.07%
    - Basic Augmentation: 최종 validation accuracy 약 79.85%
    - CutMix: 최종 validation accuracy 약 79.50%
    - MixUp: 최종 validation accuracy 약 77.70%

    결과를 통해 3 epoch 기준으로는 Baseline이 가장 높은 성능을 보였지만, 회고에서 증강 기법은 초반 학습을 어렵게 만들 수 있으며 더 긴 학습이 필요하다는 점을 분석한 부분이 좋았습니다.

---

- [x] 4. 회고를 잘 작성했나요?

    - 네, 회고가 잘 작성되어 있습니다.
    - 프로젝트에서 무엇을 수행했는지, 어떤 방법을 사용했는지, 실험 결과가 어떻게 나왔는지, 왜 그런 결과가 나왔을 수 있는지에 대한 해석이 포함되어 있습니다.
    - 특히 “증강 기법일수록 초반 정확도는 낮지만 상승 폭이 가팔랐다”, “3 epoch는 증강 효과가 드러나기엔 짧았다”, “pretrained 모델을 사용했기 때문에 baseline부터 높아 증강 효과가 크게 드러나지 않았을 수 있다”는 분석이 인상적이었습니다.
    - 단순히 결과 수치만 나열하지 않고, 실험 조건과 한계까지 함께 작성한 점이 좋았습니다.

    회고에서 좋았던 부분
    - CutMix와 MixUp의 차이를 직접 구현을 통해 이해했다고 정리한 점
    - soft label의 개념을 언급한 점
    - 짧은 epoch 수가 실험 결과에 영향을 줄 수 있음을 인정한 점
    - pretrained 모델 사용으로 인해 augmentation 효과가 상대적으로 덜 보였을 가능성을 분석한 점

    보완하면 좋을 부분
    - 전체 코드 실행 플로우를 간단한 순서도 형태로 추가하면 더 이해하기 쉬울 것 같습니다.
    - 예를 들어 아래와 같은 흐름을 markdown으로 넣으면 좋습니다.

    text     데이터 다운로드     → ImageFolder로 데이터 로딩     → train/test split     → 기본 전처리 및 augmentation 함수 정의     → CutMix / MixUp 함수 구현     → ResNet50 모델 준비     → Baseline / Basic Aug / CutMix / MixUp 학습     → validation accuracy 비교     → 결과 분석 및 회고     

---

- [x] 5. 코드가 간결하고 효율적인가요?

    - 전체적으로 코드는 기능별로 잘 나누어져 있어 비교적 간결하게 작성되었습니다.
    - 전처리 함수, augmentation 함수, CutMix 함수, MixUp 함수, 일반 학습 함수, mix 학습 함수가 각각 분리되어 있어 코드의 역할이 명확합니다.
    - 특히 train()과 train_with_mix()를 분리하여 hard label 학습과 soft label 학습을 구분한 점이 좋았습니다.
    - 다만 일부 개선할 수 있는 부분도 있습니다.

    좋았던 점
    - 반복되는 전처리 과정을 함수화함
    - CutMix와 MixUp 과정을 함수 단위로 나누어 구현함
    - 실험 결과를 history에 저장하고 그래프로 비교함
    - pretrained ResNet50의 마지막 fc layer만 클래스 수에 맞게 교체하여 효율적으로 전이학습을 수행함

    개선하면 좋을 점
    - apply_normalize_on_dataset() 함수에서 dataset.dataset.transform을 직접 바꾸는 방식은 여러 DataLoader가 같은 원본 dataset을 공유할 때 transform이 덮어써질 가능성이 있습니다. 실험별 transform을 더 명확히 분리하면 좋습니다.
    - CutMix와 MixUp에서 tensor를 numpy로 변환한 뒤 다시 tensor로 바꾸고 있는데, 가능하면 PyTorch tensor 연산으로 통일하면 GPU 사용과 속도 측면에서 더 효율적일 수 있습니다.
    - num_workers=0으로 설정되어 있는데, 실행 환경이 허용된다면 값을 늘려 데이터 로딩 속도를 개선할 수 있습니다.
    - random seed를 고정하면 실험 결과의 재현성이 좋아질 수 있습니다.
    - 학습 epoch이 3으로 짧기 때문에, augmentation 효과를 더 정확히 비교하려면 10 epoch 이상 학습하거나 early stopping을 적용해보는 것도 좋습니다.

---

# 회고(참고 링크 및 코드 개선)

이번 프로젝트는 Stanford Dogs 데이터셋을 이용해 이미지 분류 문제에서 데이터 증강 기법이 모델 성능에 어떤 영향을 주는지 비교한 실험입니다. 기본 전처리만 적용한 Baseline 모델, 기본 Augmentation 모델, CutMix 모델, MixUp 모델을 각각 ResNet50 pretrained 모델로 학습시켜 validation accuracy를 비교했습니다.

가장 인상적인 부분은 CutMix와 MixUp을 직접 구현했다는 점입니다. CutMix에서는 이미지의 일부 영역을 다른 이미지로 대체하고, 해당 영역의 면적 비율에 따라 라벨도 함께 섞었습니다. MixUp에서는 두 이미지를 전체적으로 비율에 따라 섞고, 라벨 역시 같은 비율로 soft label을 만들었습니다. 이를 통해 데이터 증강이 단순히 이미지를 변형하는 작업이 아니라, 모델이 더 일반화된 특징을 학습하도록 입력 데이터와 정답 라벨을 함께 조정하는 과정이라는 점을 확인할 수 있었습니다.

실험 결과는 3 epoch 기준으로 Baseline이 약 80.07%로 가장 높았고, Basic Augmentation은 약 79.85%, CutMix는 약 79.50%, MixUp은 약 77.70%로 나타났습니다. 결과만 보면 augmentation을 적용하지 않은 모델이 가장 좋아 보이지만, 학습 epoch이 짧고 pretrained 모델을 사용했기 때문에 augmentation의 일반화 효과가 충분히 드러나지 않았을 가능성이 있습니다. 실제로 증강 기법은 초반 학습 난이도를 높일 수 있으므로, 더 긴 epoch에서 비교하면 다른 결과가 나올 수도 있습니다.

코드 구조는 전체적으로 함수화가 잘 되어 있어 이해하기 쉬웠습니다. 데이터 전처리, augmentation, CutMix, MixUp, 학습 함수가 각각 분리되어 있어 실험 흐름을 따라가기 좋았습니다. 다만 dataset.dataset.transform을 직접 변경하는 방식은 여러 실험 조건이 같은 dataset 객체를 공유할 때 transform이 의도치 않게 덮어써질 수 있으므로 주의가 필요합니다. 또한 CutMix와 MixUp 구현에서 numpy 변환을 거치기보다 PyTorch tensor 연산으로 구현하면 성능과 확장성 측면에서 더 좋아질 수 있습니다.

전체적으로 프로젝트의 목표에 맞게 데이터 로딩, 증강 기법 구현, 모델 학습, 결과 비교, 회고까지 잘 구성된 코드였습니다. 특히 단순히 모델을 학습시키는 데서 끝나지 않고, 왜 이런 결과가 나왔는지까지 분석한 점이 좋았습니다. 이후에는 epoch 수를 늘리고 random seed를 고정한 뒤, 여러 번 반복 실험하여 평균 성능을 비교하면 더 신뢰도 높은 실험 결과를 얻을 수 있을 것 같습니다.
