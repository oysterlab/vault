# Playbook — 성과 검증된 전략과 규칙

이 문서는 실제 TikTok 메트릭으로 검증된 인사이트만 기록한다.
Analyze 에이전트가 매 사이클 업데이트하고, Produce/Generate가 참조한다.

**규칙: 추측 금지. 모든 항목에 근거 포스트 ID와 수치를 포함할 것.**

---

## 훅 (Slide 1)

### 효과적인 훅 패턴
| 패턴 | 예시 | 근거 |
|------|------|------|
| 구체적 미스터리 + 도발 | "This woman appears TWICE. Nobody notices." | primavera_a: FYP 94.7%, 347뷰 |
| 틀렸다고 지적 | "This isn't God giving life to Adam." | post_026_creation: score 59.9, shares 2 |

### 실패한 훅 패턴
| 패턴 | 예시 | 근거 |
|------|------|------|
| 추상적 단어 | "BOTANICAL PUZZLE" | primavera_b: FYP 47.5%, 34뷰 (A 대비 1/10) |
| 10단어 초과 | 15~16단어 훅 | 5연속 QA 탈락 |

---

## 스토리 구조 (Slide 2~6)

### 효과적인 구조
| 구조 | 설명 | 근거 |
|------|------|------|
| 다단 반전 | hook(뇌)→증거→새미스터리(손가락)→충격(해부)→감정(교황) | post_026: avg_slides 3.56 (최고), score 59.9 |

### 실패한 구조
| 구조 | 설명 | 근거 |
|------|------|------|
| 조기 답 공개 | slide 3에서 훅의 답이 나옴 → 4~7 볼 이유 없음 | primavera_a: avg_slides 3.03, completion 15% |
| 인물 도감 | slide 4~6이 각각 다른 인물 소개, 스토리와 단절 | primavera_a: slide 4(Three Graces)에서 이탈 |
| 시각 반복 | 7장 중 4장이 같은 풀샷 | the_kiss: avg_slides 2.91, completion 2.84% |

---

## CTA (Slide 7)

### 효과적
| 예시 | 근거 |
|------|------|
| "Accident or message? Tell me what you think." | post_026: comments 1 (유일하게 댓글 발생) |

### 실패
| 예시 | 근거 |
|------|------|
| "What do YOU see?" | primavera_a: comments 0 (너무 막연) |
| 선언문 (질문이 아닌 것) | 4연속 QA 탈락 |

---

## 작품 선택

### 좋은 작품 특성
- 세로/정사각형 구도 → 9:16 크롭 유리
- 디테일이 풍부 → 줌인 시 각 슬라이드가 달라 보임
- 밝은 색감 → 모바일에서 눈에 띔

### 피할 작품 특성
- 가로 풍경화 → 크롭 제한 (great_wave: 7장이 같은 파도)
- 극단적 명암 (Caravaggio) → 어두운 크롭 양산
- 단순 구도 → 줌인할 디테일 부족

---

## A/B/C 전략별 성과

### Type A (카피 — 스카웃 Top 1 그대로)
- (데이터 축적 중)

### Type B (같은 작품 + 다른 전략)
- (데이터 축적 중)

### Type C (다른 작품 + 다른 전략)
- (데이터 축적 중)

---

## 벤치마크 (현재 최고 성과)

| 지표 | 최고값 | 포스트 | 날짜 |
|------|--------|--------|------|
| Score | 59.9 | post_026_creation | 2026-02-22 |
| Completion | 28.15% | hybrid_pearl_01 | 2026-03-11 |
| FYP% | 94.7% | primavera_a | 2026-04-01 |
| Shares | 2 | post_026_creation | 2026-02-22 |
| Comments | 1 | post_026_creation | 2026-02-22 |

---

*마지막 업데이트: 2026-04-05 by manual analysis*
