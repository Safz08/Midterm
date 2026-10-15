# Field-Driven Shift of the AFM–FM Transition in FeRh
Spin-1 Ising 모델 Monte Carlo 시뮬레이션 (PHY4008 전산물리 II 중간 프로젝트)

## 질문과 답
**Question:** 자기장이 변하면 FeRh의 반강자성(AFM) → 강자성(FM) 전이온도 T_t는 어떻게, 얼마나 변하는가?

**답:** 자기장은 자화가 있는 FM에만 에너지 이득을 줘서 T_t를 낮춘다. 크기는 dT_t/dB = −ΔM/ΔS로 정해지고, 이 모델에서 1 T당 약 24 K(−23.7 K/T)다. 실험(약 −8 K/T, Maat et al. 2005)보다 약 3배 크며, 스핀만 있는 모델이라 전자·격자 엔트로피가 빠진 것이 원인으로 보인다.

Gruner et al. (2003)의 spin-1 Ising 모델에 자기장(Zeeman) 항을 더해 계산했다. 모델 매개변수는 논문이 B = 0의 실험 온도들(Néel 온도, T_C, T_m)에 맞춰 정한 값 그대로이고, 자기장 의존성은 맞추지 않았다.

## 파일
| 파일 | 내용 |
|---|---|
| `Computational_Physics2_midterm.pptx` | 발표 슬라이드 (18장) |
| `Computational_Physics2_midterm.ipynb` | **발표 내용만 담은 코드** (이 README의 대상) |
| `ferh_field_scan.npz` | 본 계산 결과 (온도 × 자기장 스캔). 노트북이 불러온다 |
| `ferh_clausius_clapeyron.npz` | Step 12 결과 (노트북 실행 시 다시 만들어짐) |
| `figures/` | 노트북이 그린 그래프 |
| `Computational_Physics2_midterm_backup.pptx`, `..._backup.ipynb` | 질문 대비 백업 슬라이드와 백업 내용까지 포함한 전체 노트북 (`ferh_size_check.npz`, `ferh_equil_check.npz` 사용) |

## 실행 방법
1. `pip install -r requirements.txt` (또는 numpy, matplotlib, numba 설치)
2. `Computational_Physics2_midterm.ipynb`를 열고 위에서부터 모든 셀을 실행(Run All)한다. 약 2–5분 걸린다.

가장 오래 걸리는 본 계산(Step 9)은 기본값이 저장된 결과(`ferh_field_scan.npz`) 불러오기다. 파일이 없거나 `RUN_MAIN_SCAN = True`로 바꾸면 처음부터 다시 계산한다(약 15–25분). seed가 고정돼 있어 다시 계산해도 같은 숫자가 나온다.

## 노트북 구성 (발표 순서와 같음)
| Step | 내용 | 슬라이드 |
|---|---|---|
| 1–3 | Model: 매개변수, 격자(L = 6, 주기 경계), 해밀토니안 + Zeeman 항 | 4–5 |
| 4–6 | Workflow: Metropolis + 집단 이동, 150 → 350 K 가열과 측정, T_t 판정(m_FM이 m_AF를 추월하는 온도) | 6–8 |
| 7 | Validation ①: 빠른 ΔE 계산 = 전체 에너지 차이 (B = 0–3 T, 무작위 배열 200개) | 9 |
| 8 | Validation ②: L = 2 엄밀해(1,679,616개 배열 전부)와 Monte Carlo 비교 (B = 0, 2 T) | 10 |
| 9 | 본 계산: 자기장 0–3 T 13개 × 독립 실행 10번, 실제 스핀 배열 그림 | 7 |
| 10 | Result ①: B = 0에서 AFM → FM 전이 | 11 |
| 11 | Result ②: 자기장이 T_t를 낮춘다 (실험 기울기와 비교) | 12 |
| 12 | Interpretation ①: G = U − TS − BM, B = 0에서 잰 ΔU, ΔM, ΔS로 dT_t/dB = −ΔM/ΔS 계산 | 13 |
| 13 | Interpretation ②: 왜 직선이 아닌가 (세 구간 그림과 구간별 기울기) | 14 |
| 14 | 한계, 결론과 다음 단계, 참고문헌 | 15–17 |

## 계산 조건
- 교환상수 J_FF = −1.00, J_FR = +2.13, D_Rh = −11.10 mRy (Gruner et al. 2003, Table II)
- 자기모멘트 μ_Fe = 3.2 μ_B, μ_Rh = 0.9 μ_B (Lewis et al. 2016)
- 격자 L = 6 (Fe 216개 + Rh 216개), 주기 경계, AFM에서 시작
- 온도 150–350 K (5 K 간격), 온도마다 300 sweep 버린 뒤 표본 1000개
- 자기장 0–3 T (0.25 T 간격, 13개), 자기장마다 독립 실행 10번, 에러바 = 표준오차

## 주요 결과
| 항목 | 값 |
|---|---|
| T_t (B = 0) | 288 ± 1 K |
| T_t (B = 3 T) | 201 ± 2 K |
| Validation ① 최대 차이 | 약 1 × 10⁻¹² mRy (반올림 오차) |
| Validation ② | (MC − 엄밀해)/SE가 30개 중 90% ±2 안 |
| dT_t/dB = −ΔM/ΔS (B = 0 값으로 계산) | −23.7 K/T |
| 구간별 기울기 0–0.5 / 0.5–2 / 2–3 T | −7 ± 6 / −26 ± 1 / −46 ± 2 K/T |
| 실험 (Maat et al. 2005) | 약 −8 K/T |

## 한계와 다음 단계
- 실험보다 기울기 약 3배: 스핀만 있는 모델 → 전자·격자 엔트로피를 고려하지 않음
- 작은 격자 (L ≤ 8): L = 8 가열에서 갇힘, L ≥ 10 미확인
- 2 T 이상에서 더 가파른 이유는 추정 단계 (ΔS 감소)
- 다음 단계 (기말): 빠진 엔트로피 추가, 더 좋은 샘플링으로 L ≥ 10, 박막 효과(변형·두께)와 실제 데이터 비교

## 참고문헌
1. M. E. Gruner, E. Hoffmann, P. Entel, Phys. Rev. B 67, 064415 (2003) — 모델과 매개변수
2. S. Maat, J.-U. Thiele, E. E. Fullerton, Phys. Rev. B 72, 214432 (2005) — 실험 dT_t/dB
3. J. S. Kouvel, C. C. Hartelius, J. Appl. Phys. 33, 1343 (1962) — 벌크 FeRh 전이
4. N. Metropolis et al., J. Chem. Phys. 21, 1087 (1953) — Metropolis 알고리즘
5. W. K. Hastings, Biometrika 57, 97 (1970) — 일반화된 제안 방식 (집단 이동)
6. C. J. Lewis, C. H. Marrows, S. Langridge, J. Phys. D 49, 323002 (2016) — FeRh 리뷰
7. J. A. Cooke et al., Phys. Rev. Lett. 109, 255901 (2012) — 전이의 열역학 측정
8. J. Barker, R. W. Chantrell, Phys. Rev. B 92, 094402 (2015) — Heisenberg형 FeRh 모델

## AI 사용
코드 구현, 주석, 그림 작성, 노트북을 발표 순서로 정리하는 데 AI(Claude)를 사용했다. 연구 주제(FeRh)와 프로젝트 구성(물리적 동기 → 모델 → 검증 → 해석)은 내가 정했고, 자기장 효과와 비교 논문은 AI와 논의해 정했다. 검증 보완(자기장별 에너지 검사, 엄밀해 곡선 비교, 중간 자기장 추가와 직선 근사 재검토)은 내가 첫 결과를 검토하면서 제기한 문제를 바탕으로 했다. 모든 셀은 직접 실행해 결과를 확인했다.
