# 한국어 학습 모델별 한국어 쓰기 답안지 점수 구간 예측 성능 비교

이 소스코드는 "**한국어 학습 모델별 한국어 쓰기 답안지 점수 구간 예측 성능 비교**" (**정보처리학회논문지 소프트웨어 및 데이터 공학 11권 3호 pp. 133-140, 2022**) 논문에 사용된 실험 코드입니다.

세 종류의 한국어 딥러닝 언어모델([KcBERT](https://github.com/Beomi/KcBERT), [KoBERT](https://github.com/SKTBrain/KoBERT), [KR-BERT](https://github.com/snunlp/KR-BERT))을 미세조정하여 유학생이 쓴 한국어 글쓰기 답안지를 A, B, C, D 중 하나의 평어(grade)로 분류하는 텍스트 자동분류 모델을 구축하여 성능을 평가했습니다.

또 전통적인 확률적 기계학습 기법인 나이브베이즈(NB)와 로지스틱 회귀(LR) 분류기의 성능도 비교했습니다.

실험에는 `HuggingFace`의 `transformer` 패키지와 `scikit-learn` 패키지를 사용했습니다.

## 데이터셋

실험 데이터는 총 304개의 유학생 글쓰기 답안지에 평어가 부여된 지도학습 데이터입니다

글쓰기 주제는 하나가 아니라 총 4개로 아래와 같습니다. (하나의 주제에 대하여 많은 양의 글쓰기 답안지를 확보하지 못했기 때문에 되도록 비슷한 주제를 선정하여 답안지를 모았습니다.)

1. 직업의 조건(직업): 100개
2. 행복의 조건(행복): 95개
3. 경제와 행복(경제): 61개
4. 성공의 기준(성공): 44개

실험 데이터는 아래의 URL에서 다운로드하실 수 있습니다. 

다운로드 후 압축을 푸시고, 폴더명을 `data`로 변경하신 뒤 실험을 진행하시면 됩니다.

http://aihumanities.org/ko/archive/data/?vid=1

실험 데이터셋에서 레이블은 3, 2, 1, 0으로 부여돼 있는데, `A = 3, B = 2, C = 1, D = 0`에 해당합니다.

## 실험 구성

다양한 주제의 글쓰기 문서를 혼합하여 훈련 데이터를 구성한 뒤, 직업 테스트 데이터를 예측하는 실험(표 첫 번째 칼럼: 실험 1\~5번)과 행복 테스트 데이터를 예측하는 실험(표 두 번째 칼럼: 실험 6\~10번)을 진행했습니다.

또 304건의 데이터를 모두 섞어서 훈련 데이터와 테스트 데이터로 나눈 뒤(표 세 번째 칼럼: 실험 11번) 실험을 진행하기도 했습니다.

| 훈련 데이터 ⟶ 테스트 데이터  | 훈련 데이터 ⟶ 테스트 데이터     | 훈련 데이터 ⟶ 테스트 데이터 |
|------------------------------|--------------------------------|----------------------------|
| 1. 직업 ⟶ 직업               | 6. 행복 ⟶ 행복                |                             |
| 2. 직업+경제 ⟶ 직업          | 7. 행복+경제 ⟶ 행복            |                            |
| 3. 직업+성공 ⟶ 직업          | 8. 행복+성공 ⟶ 행복            | 11. 통합 ⟶ 통합           |
| 4. 직업+행복 ⟶ 직업          | 9. 행복+직업 ⟶ 행복            |                            |
| 5. 직업+경제+성공+행복 ⟶ 직업 | 10. 행복+경제+성공+직업 ⟶ 행복 |                            |

## 성능 비교

KcBERT, KoBERT, KR-BERT, NB (나이브베이즈), LR (로지스틱 회귀) 분류기의 성능을 비교했습니다.

평가 척도는 정확도(accuracy)입니다. 

실험 결과, 딥러닝 기반 한국어 언어모델이 전통적인 기계학습 분류기보다 높은 성능을 보였으며, 특히 KR-BERT는 전반적인 평균 예측 정확도가 55.83%로 다른 딥러닝 언어모델 분류기에 비해 약간 높은 성능을 보였습니다. 그 다음은 KcBERT(55.77%)였고, KoBERT(54.91%)가 뒤를 이었습니다

### <표1> 직업 테스트 데이터 분류 정확도(1번~5번 실험)

|실험 | 훈련 데이터        | KcBERT | KoBERT | KR-BERT |  NB  |  LR  |
|-----|-------------------|--------|--------|--------|-------|------|
| 1. | 직업               | 46.67% | 46.67% | 44.49% | 45.65% | 36.60% |
| 2. | 직업+경제          | 45.71% | **49.66%** | 44.49% | 46.80% | **46.67%** |
| 3. | 직업+성공          | 50.75% | 46.60% | 41.70% | 45.51% | 41.63% |
| 4. | 직업+행복          | 43.47% | 46.60% | 48.50% | 44.69% | 40.48% |
| 5. | 직업+경제+성공+행복 | **51.63%** | 46.67% | **50.54%** | **47.62%** | 42.65% |
|    | 평균 정확도        | 47.65% | 47.24% | 45.95% | 46.05% | 41.61% |

### <표2> 행복 테스트 데이터 분류 정확도(6번~10번 실험)

|실험 | 훈련 데이터        | KcBERT | KoBERT | KR-BERT |  NB  |  LR  |
|-----|-------------------|--------|--------|--------|-------|------|
| 1. | 행복               | 56.12% | 55.34% | 60.28% | **59.26%** | 54.24% |
| 2. | 행복+경제          | **63.34%** | 58.40% | **64.60%** | 57.22% | 50.00% |
| 3. | 행복+성공          | 57.14% | 60.36% | 55.10% | 55.26% | 57.30% |
| 4. | 행복+행복          | 60.20% | 57.38% | 58.24% | 57.22% | 56.36% |
| 5. | 행복+경제+성공+행복 | 60.13% | **61.46%** | 63.27% | 56.20% | **59.42%** |
|    | 평균 정확도        | 59.39% | 58.59% | 60.30% | 57.03% | 55.46% |

### <표3> 통합 테스트 데이터 분류 정확도(11번 실험)와 전체 평균 정확도

|실험 | 테스트 데이터      | KcBERT | KoBERT | KR-BERT |  NB  |  LR  |
|-----|-------------------|--------|--------|--------|-------|------|
| 1~5 | 직업 테스트 데이터  | **51.63%** | 49.66% | 50.54% | 47.62% | 46.67% |
| 6~10 | 행복 테스트 데이터 | 63.34% | 61.46% | **64.60%** | 59.26% | 59.42% |
| 11  | 통합 테스트 데이터  | 52.32% | **53.60%** | 52.35% | 50.66% | 44.75% |
|     | 전체 평균 정확도   | 55.77% | 54.91% | **55.83%** | 52.52% | 50.28% |

## 논문 인용

이 코드 또는 코드 관련 실험을 인용하시려면 아래의 논문을 인용해 주십시오.
```
조희련, 임현열, 이유미, 차준우, 
"한국어 학습 모델별 한국어 쓰기 답안지 점수 구간 예측 성능 비교," 
정보처리학회논문지 소프트웨어 및 데이터 공학 XX(XX), pp. XX-XX, 2021.
```
