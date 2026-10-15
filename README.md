# Field-Driven Shift of the AFM–FM Transition in FeRh
A spin-1 Ising Monte Carlo study (PHY4008 중간 프로젝트)

## 물리 질문
자기장이 변하면 FeRh의 반강자성(AFM) → 강자성(FM) 전이온도 T_t는 어떻게, 얼마나 변하는가?
Gruner et al. (2003)의 spin-1 Ising 모델에 자기장 항을 더해 dT_t/dB를 계산하고, 실험값(Maat et al. 2005, 약 -8 K/T)과 비교한다.
모델 매개변수는 논문이 B = 0의 실험 온도들(Néel 온도, T_C, T_m)에 맞춰 정한 값이며, 자기장 의존성과 전이 엔트로피는 맞추지 않았다.

## 환경
- Python 3.8 이상
- numpy, matplotlib, numba (`pip install -r requirements.txt`)
- Google Colab에서도 그대로 실행 가능

## 실행 방법
`midterm_final.ipynb`를 열고 위에서부터 모든 셀을 실행(Run All)한다.
기본 설정으로 약 2–5분 걸린다. 시간이 오래 걸리는 세 계산은 기본값이 저장된 결과 불러오기이고, 파일이 없으면 새로 계산한다.
- Step 9 본 계산: `ferh_field_scan.npz` (다시 계산하려면 `RUN_MAIN_SCAN = True`, 약 15–25분)
- Backup B8 격자 크기 검증: `ferh_size_check.npz` (다시 계산하려면 `RUN_SIZE_CHECK = True`, 약 15–40분)
- Backup B9 평형화 길이 검증: `ferh_equil_check.npz` (다시 계산하려면 `RUN_EQUIL_CHECK = True`, 약 5–10분)

seed가 고정돼 있으므로 다시 계산해도 같은 숫자가 나온다. 노트북 PC는 충전기를 꽂고 전원 모드를 '최고 성능'으로 두면 빨라진다.
(`Computational_Physics2_midterm.ipynb`는 발표 순서로 재구성하기 전의 이전 버전이다.)

## 입력과 매개변수
- 교환상수: J_FF = -1.00, J_FR = 2.13, D_Rh = -11.10 mRy (Gruner et al. 2003, Table II; B = 0의 실험 온도들에 맞춘 값)
- 자기모멘트: mu_Fe = 3.2 mu_B, mu_Rh = 0.9 mu_B (Lewis et al. 2016)
- 격자 L = 6 (Fe 216개 + Rh 216개, 주기 경계)
- 온도 150–350 K (5 K 간격, AFM에서 시작해 가열)
- 자기장 0–3 T (0.25 T 간격, 13개)
- 온도마다 평형화 300 sweep, 그 뒤 2 sweep마다 측정해 표본 1000개
- 자기장마다 독립 실행 10번. seed 100, 300, ..., 1900
  (실제 seed = seed × 10000 + 4B × 100 + 온도 번호)

## 노트북 구성 (발표 순서와 같음)
메인 Step 1–14가 먼저 나오고, 질문 대비 자료는 모두 맨 뒤 Backup B1–B12에 모았다.

| Step | 내용 |
|---|---|
| 1–3 | 모델: 매개변수, 격자와 시작 배열, 해밀토니안(자기장 항 추가) |
| 4–6 | 방법: Metropolis + 집단 이동, 가열 스캔과 측정량, T_t 판정 |
| 7 | 검증 ①: 에너지 계산 (빠른 ΔE = 전체 에너지 차이, 자기장 0–3 T) |
| 8 | 검증 ②: L = 2 엄밀해(1,679,616개 배열)와 Monte Carlo 비교 (B = 0, 2 T) |
| 9 | 본 계산: 온도 × 자기장 스캔 (9-2: 실제 스핀 배열 그림) |
| 10 | 결과 ①: B = 0에서의 AFM → FM 전이 |
| 11 | 결과 ②: 자기장이 T_t를 낮춘다 (실험 기울기와 함께) |
| 12 | 해석 ①: G = U − TS − BM, B = 0에서 측정한 ΔU, ΔM, ΔS로 dT_t/dB = −ΔM/ΔS 계산 |
| 13 | 해석 ②: 왜 직선이 아닌가 (세 구간 표와 점선 그림 → 1 T 폭 구간 기울기 → 맞춤 없는 유한 격자 식) |
| 14 | 한계, 결론, 다음 단계, 참고문헌 |

| Backup | 내용 |
|---|---|
| B1 | 매개변수 출처 |
| B2 | 자기장 13개 각각의 질서도 곡선 |
| B3 | 엔트로피 vs 실험 (실험 기울기에 필요한 ΔS) |
| B4 | 엄밀해 비교 숫자 B = 0, 1, 2, 3 T (+ 3 T, 400 K 재확인) |
| B5 | 집단 이동의 8가지 제안과 에너지 변화 |
| B6, B7 | 계산 조건과 선택 이유, 약어 |
| B8 | 격자 크기(L = 4, 6, 8)와 가열/냉각 |
| B9 | 평형화 길이 |
| B10 | dT_t/dB = −ΔM/ΔS 유도 |
| B11 | 왜 Ising 모델인가 |
| B12 | 처음 버전에서 보완한 점 |

## 그림과 데이터
노트북을 실행하면 다음 파일이 생성된다.
- `figures/fig1_validation_exact.png`: L = 2 엄밀해 곡선과 Monte Carlo, (MC − 엄밀해)/SE (Step 8)
- `figures/fig_snapshots.png`: 세 온도에서의 실제 스핀 배열, 한 층 (Step 9-2)
- `figures/fig2_order_vs_T.png`: B = 0 T에서 온도에 따른 FM/AFM 질서도 (Step 10)
- `figures/fig3_Tt_vs_B_mc_exp.png`: T_t(B) 시뮬레이션과 실험 기울기 (Step 11)
- `figures/fig3a_Tt_vs_B_data.png`: T_t(B)와 1 T 폭 구간 기울기 (Step 13)
- `figures/fig3a_Tt_vs_B_regions.png`, `..._nolabel.png`: 세 구간을 점선으로 나눈 T_t(B) (Step 13)
- `figures/fig3_Tt_vs_B.png`: 시뮬레이션, 유한 격자 식, 무한 결정 직선, 실험 기울기 비교 (Step 13)
- `figures/fig_order_all_fields.png`: 자기장 13개 각각의 질서도 곡선 (B2)
- `figures/table_exact_B1_B3.png`, `exact_enumeration_table_B0-3T.csv`: 엄밀해 비교표 (B4)
- `figures/fig4_size_check.png`: 격자 크기(L = 4, 6, 8)와 가열/냉각 비교 (B8)
- `ferh_field_scan.npz`, `ferh_size_check.npz`, `ferh_equil_check.npz`, `ferh_clausius_clapeyron.npz`: 원자료

## 주요 결과
- B = 0: T_t = 288.4 ± 1.2 K (실험 약 320–370 K, 조성에 따라 다름)
- 자기장을 걸수록 T_t가 내려간다: 0 T → 3 T에서 288 K → 201 K
- T_t(B)는 직선이 아니다. 1 T 폭 구간 기울기가 -17 K/T(0–1 T)에서 -46 K/T(2–3 T)까지 변한다.
- 낮은 자기장의 완만함은 작은 격자 효과(FM의 위/아래 두 방향)로, 맞춤 매개변수 없는 식이 0–2 T의 시뮬레이션을 재현한다.
- 실험과 비교할 무한 결정의 기울기: -23.7 K/T (Clausius–Clapeyron). 실험(-8 K/T)보다 약 3배 가파르며, 원인은 모델의 엔트로피 부족이다.
- 모델의 전이 엔트로피 0.115 k_B/f.u.(약 6 J/kg·K)는 Gruner et al.이 보고한 "이 모델의 엔트로피 = 실험의 30–45%"와 일치한다.

## 검증과 한계
- 검증 ① (Step 7): 계산에 쓰는 자기장(0–3 T)마다 L = 6 무작위 배열 200개에서 빠른 에너지 계산 = 전체 에너지 차이 (최대 차이 1e-12 mRy)
- 검증 ② (Step 8, B4): L = 2 엄밀해를 Monte Carlo가 재현. B = 0, 2 T에서 30개 중 90%가 ±2 SE 안, B = 0–3 T로 넓히면 60개 중 87%. 가장 큰 차이(3 T, 400 K, 3.7 SE)는 다른 seed 32번 재계산에서 0.1 SE로 줄어 통계적 흔들림으로 확인
- 안정성 (B8, B9): L = 6에서 가열과 냉각의 T_t가 약 5 K 안에서 일치, 평형화 100 / 300 / 1000 sweep에서 T_t(B=0)가 오차 범위 안에서 같음
- 한계: 실험보다 기울기 약 3배(스핀만 있는 모델 → 전자·격자 엔트로피 없음), L = 8 가열에서 일부 실행이 AFM에 갇힘, L >= 10 미확인, 2 T 이상의 휘어짐은 추정 단계(ΔS 감소)
- 범위 밖: 실제 샘플의 절대 T_t (조성·결함·두께에 따라 다름). 이번에는 자기장에 따른 변화에 초점을 맞췄다.
- 다음 단계(기말): 빠진 엔트로피 추가, 더 좋은 샘플링으로 L >= 10, 박막 효과(변형·두께)와 내 FeRh 박막 데이터 비교

## 발표 자료
- `Computational_Physics2_midterm.pptx`: 중간 발표 (한국어 본문, 영어 제목). 그림은 `figures/`와 `figures/slides/`에 있다.
- `Backup_dTt_dB_derivation.pptx`: dT_t/dB 유도 백업 슬라이드 한 장
- 슬라이드용 그림 다시 만들기: 노트북 실행 → `python make_slide_figures.py` (figures/slides/)

## 참고문헌
- M. E. Gruner, E. Hoffmann, P. Entel, Phys. Rev. B 67, 064415 (2003)
- S. Maat, J.-U. Thiele, E. E. Fullerton, Phys. Rev. B 72, 214432 (2005)
- J. S. Kouvel, C. C. Hartelius, J. Appl. Phys. 33, 1343 (1962)
- N. Metropolis et al., J. Chem. Phys. 21, 1087 (1953)
- W. K. Hastings, Biometrika 57, 97 (1970)
- C. J. Lewis, C. H. Marrows, S. Langridge, J. Phys. D 49, 323002 (2016)
- J. A. Cooke et al., Phys. Rev. Lett. 109, 255901 (2012)
- J. Barker, R. W. Chantrell, Phys. Rev. B 92, 094402 (2015)

## AI 사용
코드 구현, 주석, 그림 작성, 노트북을 발표 순서로 재구성하는 데 AI(Claude)를 사용했다. 연구 주제(FeRh)와 프로젝트 구성(물리적 동기 → 모델 → 검증 → 해석)은 내가 정했고,
자기장 효과와 비교 논문은 AI와 논의해 정했다. 검증 보완(자기장별 에너지 검사, 엄밀해 곡선 비교, 중간 자기장 추가와 직선 근사 재검토)은
내가 첫 결과를 검토하면서 제기한 문제를 바탕으로 했다. 모든 셀은 직접 실행해 결과를 확인했다.
