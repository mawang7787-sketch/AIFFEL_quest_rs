<img width="1196" height="240" alt="image" src="https://github.com/user-attachments/assets/a1363054-f1e0-4579-8257-2b901a407cb0" /><img width="1196" height="240" alt="image" src="https://github.com/user-attachments/assets/4a3aaef6-f79d-4906-a9b1-70619d204c21" /># AIFFEL Campus Online Code Peer Review Templete
- 코더 : 유비
- 리뷰어 : 장주휘


# PRT(Peer Review Template)
- [ ]  **1. 주어진 문제를 해결하는 완성된 코드가 제출되었나요?**
    - 문제에서 요구하는 최종 결과물이 첨부되었는지 확인
        - 중요! 해당 조건을 만족하는 부분을 캡쳐해 근거로 첨부
          <img width="1212" height="240" alt="image" src="https://github.com/user-attachments/assets/882c1705-9b4d-465f-bf64-20e238c84c3f" />
<img width="1214" height="240" alt="image" src="https://github.com/user-attachments/assets/c1cdfad2-2f11-4217-85c4-f9963ddd8670" />
<img width="1214" height="260" alt="image" src="https://github.com/user-attachments/assets/4870db5c-576e-489e-908d-3b633d45dc49" />
<img width="1250" height="256" alt="image" src="https://github.com/user-attachments/assets/2928cfac-dc95-44a3-9549-c93b38cfeb18" />

    
- [ ]  **2. 전체 코드에서 가장 핵심적이거나 가장 복잡하고 이해하기 어려운 부분에 작성된 
주석 또는 doc string을 보고 해당 코드가 잘 이해되었나요?**
    - 외부에서 미리 학습된 사전인 Word2Vec을 모델에 활용하는 전이학습의 핵심이라 생각함
    - 해당 코드 블럭에 doc string/annotation이 달려 있음
    - 자체 학습 임베딩의 한계를 깨기 위해 외부 행렬 데이터를 가져와 내 임베딩 레이어 가중치에 덮어씌우는 존재 이유와 작동 원리를 잘 기술했음
    - 복잡한 텐서 복사 문법을 '주입'이라는 쉬운 단어로 풀어내어 코드가 명확히 읽혔음
        - 중요! 잘 작성되었다고 생각되는 부분을 캡쳐해 근거로 첨부
        <img width="1242" height="882" alt="image" src="https://github.com/user-attachments/assets/ecc930cc-382c-4292-8023-67106315751b" />

- [ ]  **3. 에러가 난 부분을 디버깅하여 문제를 해결한 기록을 남겼거나
새로운 시도 또는 추가 실험을 수행해봤나요?**
    - 단어 사전 인덱스 범위를 벗어나는 에러가 발생
    - 최대 인덱스를 9996으로 제한하여 충돌 해결
      <img width="1244" height="1012" alt="image" src="https://github.com/user-attachments/assets/4490a723-a3ce-4075-9e14-c8011e987901" />

    - 기본 모델 외에 LSTM2Layer 2층 모델을 추가 실험함
    -  모델의 깊이보다 Word2Vec이 성능에 더 결정적이라는 차이를 실험으로 증명힘
<img width="1230" height="1038" alt="image" src="https://github.com/user-attachments/assets/9e354744-73eb-49c0-9455-7685b78def36" />
        
- [ ]  **4. 회고를 잘 작성했나요?**
    - 여러 자체 모델의 한계와 외부 Word2Vec를 통한 전이학습의 효과를 각 모델별 결과 하단에  기록함
    - 손실과 정확도 추이, 학습 결과 그래프를 한 눈에 볼 수 있도록 첨부함
    - <img width="1240" height="952" alt="image" src="https://github.com/user-attachments/assets/e94b9c90-c9b8-49b1-bbf5-643c0f2834d9" />
<img width="1202" height="712" alt="image" src="https://github.com/user-attachments/assets/b36640ee-f2ea-4d05-8968-e8134912666d" />

       <img width="1218" height="212" alt="image" src="https://github.com/user-attachments/assets/e27a03e2-8396-4f80-ad76-81974836b96a" />

        
- [ ]  **5. 코드가 간결하고 효율적인가요?**
    - 파이썬 스타일 가이드 (PEP8) 를 준수함
    - 코드 중복을 최소화하고 범용적으로 사용할 수 있도록 함수화/모듈화했는지 확인
    - 각기 다른 6개 모델의 반복되는 학습 및 검증 루프를 train_model 함수로 묶어내어 중복을 없애고범용성을 극대화함
        -![Uploading image.png…]()



# 회고(참고 링크 및 코드 개선)
```
# 리뷰어의 회고를 작성합니다.
실험 정신: 짧은 시간 안에 다른 6가지 모델을 직접 비교 분석하여 성능 차이를 알아냈다는 점이 본받을만한 실험정신이다..
깔쌈한 그래프: 사전학습된 Word2Vec을 단순 유사도 확인용으로 두지 않고, 모델을 직접 주입해 학습을 진행 그래프로 한 눈에 확인 가능하도록 시각화했다. 나도 다음에 그렇게 해야지.

# 코드 리뷰 시 참고한 링크가 있다면 링크와 간략한 설명을 첨부합니다.

# 코드 리뷰를 통해 개선한 코드가 있다면 코드와 간략한 설명을 첨부합니다.
(불필요한 컴퓨터 자원과 시간도 아끼고, 실전 테스트 데이터에서 훨씬 더 안정적이고 높은 성능을 뽑을 수 있도록)
검증 손실이 연속 2회 이상 개선되지 않으면 자동으로 학습을 종료하게 하는 얼리스타핑 기능을 사용해보면 좋을 것 같습니다.
```
