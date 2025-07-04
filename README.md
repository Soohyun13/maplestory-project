![프로젝트 배너](https://file.nexon.com/NxFile/download/FileDownloader.aspx?oidFile=5485424096059594172)


# 🎮 메이플스토리 뉴비 유저 육성 가이드 프로젝트

> **멋쟁이사자처럼 데이터 분석 4기 데이터톤 프로젝트**  
> 고레벨 유저의 장비 데이터를 기반으로 전투력을 예측하고, 유사 유저를 추천하는 시스템을 통해 뉴비 유저의 육성 방향을 제시하는 딥러닝 기반 분석 프로젝트
>
> 진행 기간: 2025.04.11 - 2025.05.13 (33일)

---

## 📌 프로젝트 개요

메이플스토리의 공식 성장 가이드인 '하이퍼 버닝 이벤트'를 통해 빠르게 성장한 뉴비 유저들이 가이드에서 의도한 성장 레벨인 260레벨 이후 육성 방향성을 잃고 이탈하는 문제에 주목
<br />
이탈의 주요 원인 중 하나인 **장비 세팅 정보 부족**을 데이터 분석으로 해결하고자 함
<br />
고레벨 유저의 장비 패턴을 분석하여 효율적인 장비 세팅 방식을 추론한 후, 뉴비 유저에게 제공함으로써 정착률을 높이고 이탈을 방지하고자 함

---

## 🎯 목표

- **딥러닝 모델 구축**
  - 고레벨 유저 장비 데이터를 기반으로 전투력 예측
  - 사용 예정인 장비 정보를 입력하면, 해당 장비 정보로 예측된 전투력과 유사한 수치의 전투력을 가진 고레벨 유저 추천
  
- 뉴비 유저가 직접 사용할 수 있는 **Streamlit UI 구현**

---

## 📂 데이터 개요

- **출처**: [Nexon Open API](https://open.api.nexon.com)
- **특징**
  - 46개 직업별 레벨 상위 1,000명의 장비 데이터로, 100만 행 이상의 방대한 데이터셋
#### 📑 주요 변수 목록
  - 캐릭터 정보: `nickname`, `world`, `subclass`, `level`
  - 장비 부위: `equipment_slot`, `item_name`, `item_upgrade`
  - 강화 정보: `starforce`, `potential_option_grade`, `additional_potential_option_grade`
  - 전투력 관련 스탯: `main_stat_type`, `str_total`, `attack_power_total`

---

## 🛠 사용 기술

- **언어/환경**: Python, Jupyter Notebook
- **데이터 수집**: requests, json
- **데이터 처리**: pandas, numpy 
- **시각화**: seaborn, matplotlib
- **통계 분석**: scipy (카이제곱 검정, Cramér’s V) 
- **모델링**: DeepSets 기반 딥러닝  
- **UI 구현**: Streamlit
- **문서화 및 협업**: GitHub, Notion, PowerPoint

---

## 🔍 분석 및 모델링 과정

### 1. EDA 및 전처리
(1) 데이터 구조 및 정제
- 한 캐릭터당 최대 24개의 장비 데이터를 보유 → 1인당 다수의 행
- 분석 편의를 1캐릭터당 1행 구조 전환 시도했으나, 정보 손실 문제 발생
- 최종적으로는 원본 구조 유지 → DeepSets 구조에서 장비 단위 임베딩 활용

(2) 컬럼 정리
- 캐릭터 주스탯과 무관한 능력치 컬럼 제거 및 통합
- 고유값이 과도한 범주형 변수는 ‘기타’ 카테고리로 축소 정리

(3) 결측치 처리 → 결측의 의미를 분리하여 레이블화
- 잠재옵션/에디셔널 잠재옵션에서 결측치 다수 확인
- 잠재옵션이 존재 가능한 경우: "Not Granted"
- 존재 불가능한 장비일 경우: "None"

(4) 범주형 및 수치형 처리
- Label Encoding: 10개 범주형 변수 처리
- StandardScaler: 수치형 변수 표준화 (평균 0, 표준편차 1)

### 2. 통계 검정 및 인사이트 도출
- 메이플스토리의 고레벨 유저(공식 가이드의 최종 성장 단계인 Lv.260보다 높은 유저)들의 장비 세팅 경향성을 확인
  - 방어구의 등급과 아이템 강화(스타포스) 수치: 장비 등급이 낮을 수록 고강화 아이템을 더 많이 사용 (p-value < 0.05)
  - 아이템의 가격과 아이템 강화 옵션 등급(잠재능력/에디셔널 잠재능력): 고가 아이템일 수록 최상위 등급의 잠재 옵션을 사용하는 경향이 있으나, 그 차이가 뚜렷하지는 않음 (p-value < 0.05, Cramér's V = 0.0850)
  - 아이템의 레벨과 아이템 강화 옵션 등급(추가옵션): '최소 수준'의 추가옵션을 충족하지 못하는 경우가 많음 (p-value < 0.05, Cramér's V < 0.2)

### 3. 시각화

**(1) 장비 등급이 낮을 수록 고강화 아이템을 더 많이 사용**
![image](https://github.com/user-attachments/assets/661bb6af-63d4-4d12-ac26-a35a8bbe6c22)
- 카이제곱 검정 결과 p-value < 0.05
- 전 직업군(전사, 마법사, 궁수, 도적, 해적)에서 같은 경향성을 보임

<br /> **(2) 고가 아이템일 수록 최상위 등급의 잠재 옵션을 사용하는 경향이 있으나, 그 차이가 뚜렷하지는 않음**
![image](https://github.com/user-attachments/assets/dd0f73db-bff5-4839-a58c-e15cf2ae796c)
- 카이제곱 검정 결과 p-value < 0.05
- 효과 크기(Cramér's V = 0.0850)는 매우 작음

<br /> **(3) '최소 수준'의 추가옵션을 충족하지 못하는 경우가 많음**
![image](https://github.com/user-attachments/assets/2245675d-dd9c-420a-82de-37c7608e89fc)
![image](https://github.com/user-attachments/assets/93af2c6d-536f-43d6-a331-17501700b98d)

- 카이제곱 검정 결과 p-value < 0.05
- 효과 크기(Cramér's V < 0.2)는 매우 작음
- 직업군을 불문하고 방어구, 장신구 아이템 모두에서 같은 경향성이 나타남

### 4. 딥러닝 모델링
- 딥러닝 모델: DeepSets 기반 딥러닝 (장비 단위 임베딩 → 합산 → 전투력 예측)
  - **학습 방식**
    - Loss 함수: MSE(평균제곱오차)
    - K-Fold 교차 검증 사용
    - 예측 오차가 큰 유저 대상으로 Fine-Tuning
    - 코사인 유사도 기반 유사 유저 추천 시스템 연동
  - **성능 지표** (최종 모델 기준)
    - RMSE (평균): **0.70**
    - 결정계수 R²: **0.7083**

### 5. Streamlit 기반 사용자 인터페이스 구현

- 캐릭터 정보 입력
- 메이플스토리 장비 아이템의 종류가 매우 많은 점을 고려하였을 때, 뉴비 유저들의 쉬운 사용을 위해서 전투력 예측을 위해 중요도가 높은 **무기 / 모자 / 장갑 3개의 장비만 입력**해도 추천 가능하도록 함
- 해당 정보 입력을 통해 예측된 전투력과 유사한 전투력을 갖춘 동일 직업의 고레벨 유저 추천

---

## 📈 성과

- **신뢰도 높은 전투력 예측 가능** → 전투력 예측 딥러닝 모델의 평균 RMSE 0.70, R² = 0.7083 달성(**기존 머신러닝 모델 대비 40% 이상의 성능 상승**)
  - 특수한 데이터 구조(한 캐릭터당 여러 개의 행을 차지하고 있는 구조)에 맞는 적합한 모델 선택
- 데이터 분석 결과를 **실서비스 수준으로 구현**
- 유저 정착 유도는 게임사의 장기 수익 창출과 직결 → **비즈니스적 기여도 확보**

---

## 🙌 팀원

- **김수현**: 조장 / 시각화, 인사이트 도출 / 딥러닝 기반 모델 구축 / 최종 팀 공통 코드 정리 및 취합 / PPT 작성 / 발표
- **박재홍**: 시각화, 인사이트 도출 / Streamlit 기반 UI 구축 / PPT 작성
- **최지영**: 시각화, 인사이트 도출 / 머신러닝 기반 모델 구축 / Streamlit 기반 UI 구축

---

## 📌 브랜치 & 파일 구조

| 브랜치       | 파일명                                | 설명                    |
| --------- | ---------------------------------- | --------------------- |
| `main`    | [README.md](https://github.com/Soohyun13/maplestory-project/blob/main/README.md)                        | 메인 프로젝트 설명 문서         |
| `main`    | [데이터톤_메이플스토리_통합코드_뉴비_이탈_방지팀.ipynb](https://github.com/Soohyun13/maplestory-project/blob/main/%EB%8D%B0%EC%9D%B4%ED%84%B0%ED%86%A4_%EB%A9%94%EC%9D%B4%ED%94%8C%EC%8A%A4%ED%86%A0%EB%A6%AC_%ED%86%B5%ED%95%A9%EC%BD%94%EB%93%9C_%EB%89%B4%EB%B9%84_%EC%9D%B4%ED%83%88_%EB%B0%A9%EC%A7%80%ED%8C%80.ipynb) | 전체 분석 통합 코드           |
| `soohyun` | [README.md](https://github.com/Soohyun13/maplestory-project/blob/Soohyun/README.md)                | 동일한 프로젝트 설명 문서          |
| `soohyun` | [item_데이터_병합.ipynb](https://github.com/Soohyun13/maplestory-project/blob/Soohyun/item_%EB%8D%B0%EC%9D%B4%ED%84%B0_%EB%B3%91%ED%95%A9.ipynb)                | 장비 데이터 병합 작업          |
| `soohyun` | [item_데이터_전처리.ipynb](https://github.com/Soohyun13/maplestory-project/blob/Soohyun/item_%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%A0%84%EC%B2%98%EB%A6%AC.ipynb)               | 결측치 처리, 정규화 등 전처리 과정(개인 코드)  |
| `soohyun` | [가설_시각화_및_추론통계.ipynb](https://github.com/Soohyun13/maplestory-project/blob/Soohyun/%EA%B0%80%EC%84%A4_%EC%8B%9C%EA%B0%81%ED%99%94_%EB%B0%8F_%EC%B6%94%EB%A1%A0%ED%86%B5%EA%B3%84.ipynb)              | 가설 기반 시각화 및 통계 검정(개인 코드)     |
| `soohyun` | [딥러닝_모델링_및_성능_높이기.ipynb](https://github.com/Soohyun13/maplestory-project/blob/Soohyun/%EB%94%A5%EB%9F%AC%EB%8B%9D_%EB%AA%A8%EB%8D%B8%EB%A7%81_%EB%B0%8F_%EC%84%B1%EB%8A%A5_%EB%86%92%EC%9D%B4%EA%B8%B0.ipynb)           | 딥러닝 모델 설계 및 성능 개선 실험(개인 코드)  |
| `soohyun` | [직업군별_데이터_수집.ipynb](https://github.com/Soohyun13/maplestory-project/blob/Soohyun/%EC%A7%81%EC%97%85%EA%B5%B0%EB%B3%84_%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%88%98%EC%A7%91.ipynb)                | 직업군별 고레벨 유저 데이터 수집 과정(개인 코드) |

