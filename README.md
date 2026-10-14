# Field-Driven Shift of the AFM–FM Transition in FeRh
A spin-1 Ising Monte Carlo study (PHY4008 중간 프로젝트)

## 물리 질문
FeRh의 반강자성(AFM) → 강자성(FM) 1차 전이온도 T_t는 외부 자기장에 따라 얼마나 낮아지는가?
Gruner et al. (2003)의 spin-1 Ising 모델에 자기장 항을 더해 dT_t/dB를 계산하고, 실험값(Maat et al. 2005, 약 -8 K/T)과 비교한다.
모델 매개변수는 논문이 B = 0의 실험 온도들(Néel 온도, T_C, T_m)에 맞춰 정한 값이며, 자기장 의존성과 전이 엔트로피는 맞추지 않았다.

## 환경
- Python 3.8 이상
- numpy, matplotlib, numba (`pip install -r requirements.txt`)
- Google Colab에서도 그대로 실행 가능

## 실행 방법
`FeRh_midterm_261013.ipynb`를 열고 위에서부터 모든 셀을 실행(Run All)한다.
기본 설정으로 약 20–35분 걸린다(대부분 Step 7 본 계산). 시간이 오래 걸리는 두 검증은 저장된 결과를 불러온다.
- Step 10 격자 크기 검증: `ferh_size_check.npz` (다시 계산하려면 `RUN_SIZE_CHECK = True`, 약 15–40분)
- Step 11 평형화 길이 검증: `ferh_equil_check.npz` (다시 계산하려면 `RUN_EQUIL_CHECK = True`, 약 5–10분)

seed가 고정돼 있으므로 다시 계산해도 같은 숫자가 나온다. 노트북 PC는 충전기를 꽂고 전원 모드를 '최고 성능'으로 두면 빨라진다.

## 입력과 매개변수
- 교환상수: J_FF = -1.00, J_FR = 2.13, D_Rh = -11.10 mRy (Gruner et al. 2003, Table II; B = 0의 실험 온도들에 맞춘 값)
- 자기모멘트: mu_Fe = 3.2 mu_B, mu_Rh = 0.9 mu_B (Lewis et al. 2016)
- 격자 L = 6 (Fe 216개 + Rh 216개, 주기 경계)
- 온도 150–350 K (5 K 간격, AFM에서 시작해 가열)
- 자기장 0–3 T (0.25 T 간격, 13개)
- 온도마다 평형화 300 sweep, 그 뒤 2 sweep마다 측정해 표본 1000개
- 자기장마다 독립 실행 10번. seed 100, 300, ..., 1900
  (실제 seed = seed × 10000 + 4B × 100 + 온도 번호)

## 노트북 구성
| Step | 내용 |
|---|---|
| 1–5 | 매개변수, 격자, 해밀토니안(+ 검증 1: 에너지 계산), Metropolis + 집단 이동, 측정량과 T_t 정의 |
| 6 | 검증 2: L = 2 엄밀해(1,679,616개 배열)와 Monte Carlo 비교 (B = 0, 2 T) |
| 7 | 본 계산: 온도 × 자기장 스캔 |
| 8 | 결과 1: B = 0에서의 AFM → FM 전이 (8-2: 자기장 13개 각각의 질서도 곡선) |
| 9 | 결과 2: T_t(B)와 1 T 폭 구간 기울기 (직선이 아님을 확인) |
| 10–11 | 검증 3: 격자 크기와 가열/냉각, 검증 4: 평형화 길이 |
| 12 | Clausius–Clapeyron: B = 0에서 측정한 ΔU, ΔM, ΔS로 무한 결정의 기울기 계산 |
| 13 | 유한 격자 효과를 식으로 세워(맞춤 매개변수 없음) 시뮬레이션과 비교, 실험과 비교 |

## 그림과 데이터
노트북을 실행하면 다음 파일이 생성된다.
- `figures/fig1_validation_exact.png`: L = 2 엄밀해 곡선과 Monte Carlo, (MC − 엄밀해)/SE
- `figures/fig2_order_vs_T.png`: B = 0 T에서 온도에 따른 FM/AFM 질서도
- `figures/fig_snapshots.png`: 세 온도에서의 실제 스핀 배열 (한 층)
- `figures/fig3a_Tt_vs_B_data.png`: T_t(B)와 1 T 폭 구간 기울기
- `figures/fig3_Tt_vs_B.png`: T_t(B) 시뮬레이션, 유한 격자 식, 무한 결정 직선, 실험 기울기 비교
- `figures/fig4_size_check.png`: 격자 크기(L = 4, 6, 8)와 가열/냉각 비교
- `figures/fig_order_all_fields.png`: 자기장 13개 각각의 질서도 곡선
- `ferh_field_scan.npz`, `ferh_size_check.npz`, `ferh_equil_check.npz`, `ferh_clausius_clapeyron.npz`: 원자료

## 주요 결과
- B = 0: T_t = 288.4 ± 1.2 K (실험 350–370 K)
- T_t(B)는 직선이 아니다. 1 T 폭 구간 기울기가 -17 K/T(0–1 T)에서 -46 K/T(2–3 T)까지 변한다.
- 낮은 자기장의 완만함은 작은 격자 효과(FM의 위/아래 두 방향)로, 맞춤 매개변수 없는 식이 0–2 T의 시뮬레이션을 재현한다.
- 실험과 비교할 무한 결정의 기울기: -23.7 K/T (Clausius–Clapeyron). 실험(-8 K/T)보다 약 3배 가파르며, 원인은 모델의 엔트로피 부족이다.
- 모델의 전이 엔트로피 0.115 k_B/f.u.(약 6 J/kg·K)는 Gruner et al.이 보고한 "이 모델의 엔트로피 = 실험의 30–45%"와 일치한다.

## 검증과 한계
- 검증 1: 계산에 쓰는 자기장(0–3 T)마다 L = 6 무작위 배열 200개에서 빠른 에너지 계산 = 전체 에너지 차이 (최대 차이 1e-12 mRy)
- 검증 2: L = 2 엄밀해를 B = 0, 2 T에서 Monte Carlo가 재현 (30개 중 90%가 ±2 SE 안, 최대 2.1 SE)
- 검증 3: L = 4, 6, 8과 가열/냉각 비교. L = 6에서 가열과 냉각의 T_t가 약 5 K 안에서 일치
- 검증 4: 평형화 100 / 300 / 1000 sweep에서 T_t(B=0)가 오차 범위 안에서 같음
- 한계: L = 8 가열에서 일부 실행이 AFM에 갇힘, L >= 10 미확인, 2 T 이상의 휘어짐은 정량적으로 설명하지 못함, 전자·격자 엔트로피 없음

## 발표 자료
- `FeRh_midterm_slides_KR.pptx`: 중간 발표 (한국어 본문, 영어 제목). 모델 슬라이드의 그림 4개는 클릭하면 하나씩 나타난다.
- 다시 만들기: 노트북 실행 → `python make_slide_figures.py` (figures/slides/) → `python make_slides.py` (python-pptx 필요)

## 참고문헌
- M. E. Gruner, E. Hoffmann, P. Entel, Phys. Rev. B 67, 064415 (2003)
- S. Maat, J.-U. Thiele, E. E. Fullerton, Phys. Rev. B 72, 214432 (2005)
- J. S. Kouvel, C. C. Hartelius, J. Appl. Phys. 33, 1343 (1962)
- N. Metropolis et al., J. Chem. Phys. 21, 1087 (1953)
- C. J. Lewis, C. H. Marrows, S. Langridge, J. Phys. D 49, 323002 (2016)
- J. A. Cooke et al., Phys. Rev. Lett. 109, 255901 (2012)
- J. Barker, R. W. Chantrell, Phys. Rev. B 92, 094402 (2015)
