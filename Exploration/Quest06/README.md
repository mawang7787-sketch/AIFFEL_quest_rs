# AIFFEL_quest_rs
18기 유비 레포지토리
AIFFEL Campus Online Code Peer Review Template

코더 : 유비
리뷰어 : 송반디


PRT(Peer Review Template)
1. 주어진 문제를 해결하는 완성된 코드가 제출되었나요?
→ 예, 완성된 코드가 제출되었습니다.
다음 핵심 과제가 모두 구현되어 있습니다:

세 가지 토큰화 방식 비교: 공백 기반(vocab 238,305) → MeCab 형태소(vocab 53,642) → SentencePiece(vocab 8,000)를 순서대로 구현하고 결과를 비교함
SentencePiece 모델 학습 및 적용: spm.SentencePieceTrainer.Train()으로 모델을 학습하고, NSMC 감성 분류 과제에 적용함
LSTM 분류 모델 학습 및 평가: SPClassifier 구현 후 train/test loss 및 accuracy 4가지 지표를 모두 추적함
추가 실험: vocab_size(4000/8000/16000)와 model_type(unigram/bpe) 조합 4가지를 run_experiment() 함수로 비교 실험 완료


2. 핵심 코드에 주석 또는 doc string이 잘 작성되었나요?
→ 예, 핵심 부분에 주석이 매우 충실하게 작성되어 있습니다.
특히 Tokenizer 클래스는 각 메서드마다 역할이 명확하게 설명되어 있습니다:

import torch
from torch.nn.utils.rnn import pad_sequence

# 공백 기반 토크나이저
class Tokenizer:
    def __init__(self, filters=''):
        self.word_index = {}   # {단어: 번호} 사전
        self.index_word = {}   # {번호: 단어} 역사전 (디코딩용)
        self.filters = filters

    # 코퍼스 전체를 훑어 단어사전을 구축
    def fit_on_texts(self, corpus):
        for sentence in corpus:
            # 문자열이면 공백으로 자름, 이미 리스트면 그대로
            tokens = sentence.split() if isinstance(sentence, str) else sentence
            for token in tokens:
                # 처음 보는 단어만 사전에 등록 (번호는 1부터 차례로)
                if token not in self.word_index:
                    self.word_index[token] = len(self.word_index) + 1
        # 단어→번호 사전을 뒤집어 번호→단어 역사전 생성
        self.index_word = {idx: word for word, idx in self.word_index.items()}

    # 문장(글자) → 숫자 시퀀스로 변환 (인코딩)
    def texts_to_sequences(self, corpus):
        sequences = []
        for sentence in corpus:
            tokens = sentence.split() if isinstance(sentence, str) else sentence
            # 각 단어를 번호로. 사전에 없는 단어는 0으로 처리
            seq = [self.word_index.get(token, 0) for token in tokens]
            sequences.append(torch.tensor(seq, dtype=torch.long))
        return sequences

    # 숫자 시퀀스 → 문장으로 복원 (디코딩)
    def sequences_to_texts(self, sequences):
        texts = []
        for seq in sequences:
            # 텐서면 리스트로 변환
            if isinstance(seq, torch.Tensor):
                seq = seq.tolist()
            # 번호를 단어로 되돌림. 0(패딩)은 건너뜀
            tokens = [self.index_word.get(idx, "") for idx in seq if idx != 0]
            texts.append(tokens)
        return texts

print("Tokenizer 클래스 정의 완료")



train_model() 함수에서도 # train 정확도용, # test loss, # 검증 (loss + acc 둘 다) 처럼 의도를 짧고 명확하게 표현하고 있습니다.

3. 디버깅 기록 또는 추가 실험이 있나요?
→ 예, 디버깅과 추가 실험이 모두 기록되어 있습니다.
디버깅 기록으로는, 문장 길이 1짜리 노이즈를 시각화로 발견한 뒤 check_sentence_with_length()로 원인(쉼표)을 확인하고 필터링으로 해결한 과정이 기록되어 있습니다. NSMC 데이터 경로를 찾지 못한 상황에서 !find 명령을 시도하다가 wget으로 직접 다운로드하는 방식으로 전환한 과정도 솔직하게 남겨져 있습니다.
추가 실험으로는 vocab_size와 model_type 조합 4가지를 run_experiment() 함수로 체계적으로 비교하고, 과적합이 발생한 epoch(7~8 epoch)를 axvline으로 시각화에 표시한 부분이 돋보입니다.

4. 회고가 잘 작성되었나요?
→ 별도의 회고 섹션은 없으나, 셀 실행 후 마크다운으로 관찰과 분석이 단계마다 잘 정리되어 있습니다.

SentencePiece 테스트 결과: "띄어쓰기없이 쓴 문장도 거의 형태소 수준으로 나뉨, 인코딩과 디코딩 완벽 일치"
학습 결과 분석: "7~8 epoch 이후 test loss가 반등함", 최고 정확도 시점(8ep, 84.21%) 명시
최종 비교 결론: "vocab size 변경해도 큰 차이는 없고 unigram이 bpe보다 약간 우세"

전체를 아우르는 마지막 회고 섹션(배운 점, 아쉬운 점, 느낀 점)이 별도로 있으면 더 완결성이 높아질 것 같습니다.

5. 코드가 간결하고 효율적인가요?
→ 예, 전반적으로 간결하고 재사용성이 높게 작성되었습니다.
tokenize() 함수로 단어사전 구축, 시퀀스 변환, 패딩을 한 번에 처리해 공백/형태소 두 방식 모두에 재사용하고 있고, run_experiment() 함수 하나로 SentencePiece 학습부터 결과 반환까지 전 파이프라인을 파라미터만 바꿔 실행하는 구조도 좋습니다. 데이터 전처리도 set으로 중복 제거 후 list comprehension으로 필터링하는 파이썬다운 방식을 사용했습니다.
한 가지 제안으로, sp_encode() 함수와 run_experiment() 내부의 encode() 함수가 거의 동일한 로직을 담고 있어 하나로 통합하면 중복을 줄일 수 있습니다.

리뷰어 회고
전체적으로 완성도가 높은 프로젝트입니다. 세 가지 토큰화 방식의 vocab 크기 차이를 직접 눈으로 확인하고 추가 실험까지 체계적으로 설계한 점이 인상적입니다. 특히 Tokenizer 클래스를 직접 구현하면서 내부 동작 원리를 학습한 것과, 과적합 발생 지점을 시각적으로 분석한 부분은 단순히 라이브러리를 가져다 쓰는 것을 넘어 깊이 있는 이해를 보여줍니다.
아쉬운 점이 있다면, Early Stopping을 실제 코드에 적용해보거나 Dropout 추가 같은 과적합 완화 시도까지 이어졌다면 실험이 한층 더 완결성을 가졌을 것 같습니다.
# 코드 리뷰 시 참고한 링크가 있다면 링크와 간략한 설명을 첨부합니다.
# 코드 리뷰를 통해 개선한 코드가 있다면 코드와 간략한 설명을 첨부합니다.
```
