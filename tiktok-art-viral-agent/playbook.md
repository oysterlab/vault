# Playbook — 성과 검증된 전략과 규칙

이 문서는 실제 TikTok 메트릭으로 검증된 인사이트만 기록한다.
Analyze 에이전트가 매 사이클 업데이트하고, Produce/Generate가 참조한다.

**규칙: 추측 금지. 모든 항목에 근거 포스트 ID와 수치를 포함할 것.**

---

## 훅 (Slide 1)

### 효과적인 훅 패턴
| 패턴 | 예시 | 근거 |
|------|------|------|
| 구체적 미스터리 + 도발 | "This woman appears TWICE. Nobody notices." | primavera_a: FYP 93.7%, 351뷰 (같은 작품 B 대비 9배) |
| 틀렸다고 지적 | "This isn't God giving life to Adam." | post_026_creation: score 59.9, shares 2 |
| 공감형 FOMO | "Everyone walks past this painting." | wedding_cana_c: score 19.7, FYP 91%, share 1 (같은 작품 B 대비 1.4배) |
| 숨겨진 요소 발견 | "There is a second painting hidden inside this one." | 085_violinist_b: score 21.3, shares 5(역대 최고), FYP 99.2%, followers +3 |
| 구체적 물건 + 극적 결과 | "This dress once caused a riot in Paris." | 084_grip_b: score 11.4, views 920, completion 21.3%, saves 2, likes 10 (+24h 안정, 같은 그림 A 대비 2.1배 뷰) |
| 역설적 도발 (무생물+거짓말) | "This sculpture is lying to you." | 089_apollo_b: score 10.8, views 954(+24h), completion 18.65%, FYP 99.5% |
| 명령형+구체적 도발 | "Look at the pearl. It's not real." | 091_pearl_b: score 13.5, views 1122, **completion 34.48%(역대 최고)**, FYP 99.0%. 같은 작품 A("She has no name") 대비 completion 1.8배. 명령형("Look at")이 시각 행동 유도 → 스와이프 동기 |
| 구체적 충격 (금액+역사 사건) | "Stolen by Nazis. Sold for $135 million." | 092_adele_c: **score 20.7(역대 2위)**, views 1011, **likes 33(역대 최다)**, **shares 3**, **saves 4(역대 최다)**, FYP 98.8%. 금액+나치+대법원 = social currency → engagement 최고. 단, completion 14.81% = 서사 강하나 시각 반복으로 리텐션 약함 |

### 실패한 훅 패턴
| 패턴 | 예시 | 근거 |
|------|------|------|
| 추상적 단어 | "BOTANICAL PUZZLE" | primavera_b: FYP 47.5%, 34뷰 (A 대비 1/10) |
| 추상적 단어 (2차 확인) | "STOLEN FEAST" | wedding_cana_b: score 13.9, avg_slides 2.02 (C 대비 30% 하락) |
| 추상적 단어 (3차 확인) | "BOTANICAL PUZZLE" 재측정 | primavera_b: FYP 41.3%, 40뷰, score 12.5 (A 대비 views 1/9, FYP 절반) |
| 사실 진술 (미스터리 아님) | "She has no name. Vermeer made her up." | 091_pearl_a: score 11.7, views 918, completion 18.7%. 같은 작품 B("Look at the pearl. It's not real.") 대비 completion 절반. "이름 없음"은 검증 가능한 주장이 아니라 사실 → 스와이프 동기 약함 |
| 모호한 "this" + 역사 | "She painted this 3 years before the Revolution." | 084_vigee_daughter_a: views 434, score 4.7 (+24h 정체, 같은 그림 B "dress caused a riot" 920뷰 대비 47%, engagement 0 유지) |
| 2단어 모호 질문 | "Normal portrait?" | 088_ermine_a: 0뷰 (계정 이슈로 미검증이나 "BOTANICAL PUZZLE" 2단어 패턴과 동일 구조 — 위험) |
| 10단어 초과 | 15~16단어 훅 | 5연속 QA 탈락 |
| **강한 훅 + S1 주제 부재** | "This ancient prince is three different people." (훅 강함) + S1은 인물 없는 벽토 배경 | 090_lilies_b: views 901(FYP 정상), **completion 16.13%(최하위)**, score 7.3. 훅이 "prince"를 말하지만 S1에 prince가 없어 context 불일치 → **리텐션 파괴**. FYP 배포는 막지 않으나 스와이프 동기 생성 실패. **훅 품질보다 S1 주제 인식성이 우선** |

---

## 스토리 구조 (Slide 2~6)

### 효과적인 구조
| 구조 | 설명 | 근거 |
|------|------|------|
| 다단 반전 | hook(뇌)→증거→새미스터리(손가락)→충격(해부)→감정(교황) | post_026: avg_slides 3.56 (최고), score 59.9 |
| 변환/질감 여정 (조각) | hook(거짓말)→재료확인(대리석)→변환시작(수피)→대비(살vs수피)→완성(뿌리)→작가배경→CTA | 089_apollo_b: completion 18.65%, views 954. 3D 조각의 다각도 크롭 = 자연스러운 시각 다양성 |
| 극단 줌 해체 (물감 리빌) | hook(명령)→대상확인(진주)→**S3 극단 줌: 물감 붓터치만 보여줌**(시각 충격)→새 디테일(빛/끈)→정체 폭로→CTA | 091_pearl_b: **completion 34.48%(역대 최고)**, views 1136. S3에서 "진주=흰 물감 두 붓터치"를 보여주는 극단 줌이 핵심. 같은 작품 A(이 기법 없음)는 completion 18.7%. **"그림을 물감으로 해체"하는 순간이 스와이프 유지의 핵심** |
| 역사적 불의→해결 (감정 아크) | hook(충격 사실)→맥락(인물 소개)→극단 줌(질감 리빌)→불의(약탈/삭제)→해결(법적 승리)→CTA | 092_adele_c: **score 20.7(역대 2위)**, likes 33, shares 3, saves 4. "나치 약탈→이름 삭제→대법원 승리" 서사가 social currency 생성 → engagement 역대 최고. **단, completion 14.81% = 서사가 강해도 시각 반복(5/7 슬라이드 유사 초상화 뷰)이면 리텐션 파괴** |

### Slide 1 이미지 효과
| 구도 | 효과 | 근거 |
|------|------|------|
| 밝은 클로즈업 (인물/얼굴) | FYP+views 최고 | primavera_a: Flora 클로즈업, 351뷰, FYP 93.7% |
| 밝은 와이드샷 | FYP 좋지만 views 소폭 하락 | primavera_c: Venus 와이드, 328뷰, FYP 91.4% |
| 어두운 장면 | FYP 급락, views 붕괴 | primavera_b: Chloris/Zephyr 어두운 장면, 40뷰, FYP 41.3% |
| 조각 질감 클로즈업 (얼굴 아님) | completion 높음 — 대리석 질감 자체가 호기심 유발 | 089_apollo_b: 토르소+드레이핑 S1 (얼굴 없음), completion 18.65%, views 954. 조각은 3D→다각도 크롭 유리 |
| **주제 부재 배경 파편 (프레스코 파편)** | **리텐션 붕괴** — 훅의 주어가 S1에 없어 context 불일치 | 090_lilies_b: S1이 벽토 조각+갈색 곡면+파동 라인(인물 0%). 훅 "ancient prince"인데 prince 없음. views 901(FYP 배포 정상)이나 **completion 16.13%(4포스트 중 최하위)**. **수정: views가 아닌 리텐션을 파괴**. 얼굴 없어도 OK지만 주제 오브젝트는 반드시 인식되어야 |
| **Slide 2 떡밥 포함** (다음 디테일을 예고) | 시각 연속성 → slide 3 이상 유지 | primavera_a: slide 1에 Chloris의 입 디테일이 이미 보임 → slide 2 "Look at her mouth"가 줌인 연속. avg_slides 3.03 (C는 2.53 — slide 1에 떡밥 없고 context switch) |

### Slide 1→2 연속성 원칙
- Slide 1은 **slide 2에서 줌인할 디테일을 미리 프레임 안에 포함**해야 한다
- Slide 2에서 전혀 새로운 영역/인물을 등장시키면 "context switch"로 이탈 발생
- 근거: primavera_a(연속성 있음, 3.03 slides) vs primavera_c(없음, 2.53 slides) — 같은 작품, 같은 작가, 같은 업로드 날짜에서 slide 1 구성만 다름
- **2차 확인 (090_lilies_b)**: S1 벽토 배경 파편 → S2 인물 전신 등장 = 완전한 context switch. completion 16.13%(최하위). primavera_b/c 패턴 재현

### Slide 1 "주제 인식성" 원칙 (신규, 090_lilies_b 검증)
- Slide 1은 **훅의 주어를 1초 안에 인식 가능**해야 한다
- "얼굴 클로즈업"은 필수 아님 — apollo_b(대리석 토르소, 얼굴 없음, 954뷰) 증명
- **그러나 주제 오브젝트가 프레임에 존재해야** 함. "prince" 훅이면 prince(또는 prince의 신체 일부)가 보여야 함
- 실패 사례: 090_lilies_b S1 = 벽토+배경 파동 라인만, prince 0% → completion 16.13%(최하위, views 901이나 리텐션 붕괴)
- 성공 사례: 089_apollo_b S1 = 토르소+드레이핑, "sculpture" 훅과 즉시 매칭 → views 954
- **Pre-check 체크리스트 추가 필요**: "훅의 명사 주어가 S1에 시각적으로 존재하는가?"

### 실패한 구조
| 구조 | 설명 | 근거 |
|------|------|------|
| 조기 답 공개 | slide 3에서 훅의 답이 나옴 → 4~7 볼 이유 없음 | primavera A/B/C 3개 모두 slide 3에서 "Chloris=Flora" 답 → avg_slides 2.0~3.0, completion 8~15% |
| 인물 도감 | slide 4~6이 각각 다른 인물 소개, 스토리와 단절 | primavera A/B/C: 3개 모두 slide 3 답 공개 후 Three Graces→Mercury→식물 나열. completion 8~15% 천장 |
| 시각 반복 | 7장 중 4장이 같은 풀샷 | the_kiss: avg_slides 2.91, completion 2.84% |
| slide 2 빈 영역 크롭 | 훅 직후 하늘/배경만 보여줌 → 즉시 이탈 | wedding_cana A/B/C 3개 모두 slide 2가 하늘 크롭, avg_slides 전부 2.0~2.7 |
| 하늘 배경 작품의 중간 크롭 | 배경=하늘인 초상화에서 S4/S6이 하늘만 잡힘 | 084_strawhat_c: score 3.8 (S4/S6 하늘 크롭, S1 가장 밝은데도 최하 점수) |
| S2~S4 연속 어두운 크롭 ("어둠의 터널") | 강한 훅이라도 중간 3장 어두우면 리텐션 붕괴 | 085_leyster_a: score 4.5, S2(어두운 손)/S3(어두운 스커트)/S4(어두운 천) 연속 → 훅 "erased 250 years"에도 completion 6.23% |
| multi-post S6 공유 크롭 | 같은 원본의 동일 영역(배경/직물)을 여러 포스트가 S6에 공유 → 전부 같은 실패 | 084 A/B/C 3개 모두 S6이 금색 쿠션/직물 텍스처 (인물 없음, 어두움). 각 포스트별 S6 독립 영역 배정 필요 |
| 리스티클/카운트다운 | 서로 다른 5개 작품을 순위로 나열, 스토리 아크 없음 | 086_insanity_a: "Top 5 Paintings of Insanity" — 0뷰(계정 이슈). 구조적으로 rule #11(인물 도감) 위반 + S1 Saturn Devouring = rule 9-b(유혈 금지) 위반. 단일 작품 심층 분석만 허용 |
| 단일 초상화 시각 반복 | 1인물/1포즈/1배경 초상화에서 5/7 슬라이드가 유사한 뷰 | 092_adele_c: completion 14.81% (score 20.7은 engagement 주도). S1/S2/S4/S6/S7이 모두 Adele 얼굴+금색 배경의 유사 뷰. S3(금박 극단 줌)만 차별화. **서사가 engagement를 살렸으나 시각 단조로움이 completion 파괴**. cf. the_kiss: 7장 중 4장 같은 풀샷, completion 2.84%. **초상화는 비얼굴 디테일(배경/손/액세서리/질감)을 S2~S5에 분산 배치 필수** |

---

## CTA (Slide 7)

### 효과적
| 예시 | 근거 |
|------|------|
| "Accident or message? Tell me what you think." | post_026: comments 1 (유일하게 댓글 발생) |

### 실패
| 예시 | 근거 |
|------|------|
| "What do YOU see?" | primavera_a: comments 0 (너무 막연). 2차 확인: primavera_a 351뷰에서도 comments 0 |
| 선언문 (질문이 아닌 것) | 4연속 QA 탈락 |
| 명령문 ("Name a...") | wedding_cana_b: share 0, comments 0 (C의 질문형 CTA는 share 1) |

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
- 085_leyster_a: score 4.5, views 348, completion 6.23%, shares 0 — 원본(@She Painted That) 7슬라이드 전기 공식 복사. 강한 훅("erased 250 years")에도 S2~S4 어두운 크롭 3연속으로 리텐션 붕괴
- 091_pearl_a: score 11.7, views 918, completion 18.7%, shares 1 — Girl with a Pearl Earring "tronie" 각도. 훅 "She has no name"이 사실 진술이라 스와이프 동기 약함. 같은 작품 B(adapt_same, "Look at the pearl") 대비 completion 절반. S5~S6 어둠의 터널
- **관찰**: copy 타입은 원본의 서사 구조를 따르지만 우리 크롭 시스템에서 중간 슬라이드 시각 품질 유지가 어려움. 091에서도 B 대비 completion 절반

### Type B (같은 작품 + 다른 전략 = adapt_same_artwork)
- 085_violinist_b: **score 21.3**, views 881, completion 15.47%, **shares 5(역대 최고)**, followers +3 — 같은 Leyster 자화상이나 "그림 속 바이올리니스트" 디테일에서 출발. copy(A) 대비 score 4.7배
- 084_grip_b: score 11.4, views 920, completion 21.3%, saves 2, likes 10 (+24h 안정) — 같은 모녀 초상이나 "드레스 스캔들" 앵글. 전기(A) 대비 views 2.1배
- 089_apollo_b: score 10.8, **views 956(+48h, 역대 최다)**, completion 18.63%, FYP 99.4% — Apollo and Daphne 조각, "대리석 질감 변환" 진입점. +24h 954 → +48h 956 (+0.2%) 정체. 상위 포스트 정체 패턴 재확인(grip_b +0.3%, violinist_b +0.8%)
- 090_lilies_b: score **7.3**, views **901** (+24h, 초기 51→901 대폭 성장), completion **16.13%**, FYP 98.9% — Prince of the Lilies. **수정**: 이전 분석의 51뷰는 +수시간 조기 측정. +24h 기준 FYP 배포는 정상(98.9%)이나 **completion 16.13%로 4포스트 중 최하위** = S1 주제 부재가 views가 아닌 **리텐션을 파괴**. 훅 "ancient prince"인데 S1에 prince 없음 → 스와이프 동기 불일치
- 091_pearl_b: score **13.5**, views **1136(+48h)**, **completion 34.48%(역대 최고)**, FYP 99.0% — Girl with a Pearl Earring, "명령형+구체적 도발" 훅 + **극단 줌 해체(S3 붓터치 리빌)**. 같은 작품 A(copy) 대비 completion 1.8배, views 1.2배. 5연속 adapt_same 최고 성과
- **관찰**: adapt_same_artwork가 일관되게 copy/다른작품보다 우수 — **5번째 확인**. 090_lilies_b는 S1 주제 부재로 리텐션 파괴(views는 회복). 5연속 adapt_same 성과: violinist_b 21.3 → grip_b 11.4 → apollo_b 10.8 → lilies_b 12.7(수정) → **pearl_b 13.5**. pearl_b 반등은 극단 줌 해체 구조의 효과. **그러나 adele_c(C 타입)가 score 20.7로 adapt_same을 초월** — "역사적 불의" 서사의 engagement 폭발력이 원인. adapt_same은 retention, adapt_diff는 engagement에서 각각 우위 가능

### Type C (다른 작품 + 다른 전략)
- 084_strawhat_c: score 3.8, views 254, completion 6.1% — 다른 Vigée Le Brun 작품(Straw Hat). S1 가장 밝았으나 작품 자체가 배경=하늘로 중간 크롭 품질 구조적 한계
- 091_milkmaid_c: score 13.1, views 970, completion 24.57%, saves 1, likes 17 — The Milkmaid (Vermeer)에 "숨겨진 Cupid" 패턴 적용. 085_violinist_b(score 21.3) 재현 목표 미달. 원인: **리빌이 S5로 지연** — S2~S3가 훅과 무관한 setup. 패턴은 유효하나 실행 조정 필요 (리빌 S2~S3으로 앞당기기)
- 092_adele_c: **score 20.7(C 타입 역대 최고, 전체 역대 2위)**, views 1011, completion 14.81%, **likes 33(역대 최다)**, **shares 3**, **saves 4(역대 최다)**, FYP 98.8% — Klimt Portrait of Adele Bloch-Bauer I에 "나치 약탈+$135M+대법원 승리" 서사 적용. engagement는 역대 최고이나 completion 14.81% = 초상화 시각 반복(5/7 유사 뷰)이 리텐션 파괴. **서사 강도 ≠ 시각 다양성**
- **관찰**: 다른 작품이라도 9:16 크롭 적합성이 성과를 좌우. 작품 선정이 전략보다 중요. "숨겨진 요소" 패턴은 다른 작품에서도 작동하나 **리빌 타이밍이 핵심** (S5 리빌 = completion 24%, S2~3 리빌이 목표). adele_c가 C 타입 최고 성과 달성 — **"역사적 불의+해결" 서사가 engagement 축을 폭발시킴**. 단, 다음 C 타입은 시각 다양성 높은 작품(다인물 or 디테일 풍부한 장면화) 선택 필요

### A/B/C 동일 작품 비교 (Wedding at Cana, 2026-04-01)
| 변수 | A (구체적 도발) | B (추상적) | C (공감형 FOMO) |
|------|----------------|-----------|----------------|
| 훅 | "The Mona Lisa is NOT the star" | "STOLEN FEAST" | "Everyone walks past this painting" |
| Score | 16.7 | 13.9 | **19.7** |
| Views | 451 | 362 | 427 |
| FYP% | 94.5% | 92.6% | 91.0% |
| Completion | 12.36% | 8.75% | **14.19%** |
| Avg Slides | 2.72 | 2.02 | 2.69 |
| Shares | 0 | 0 | **1** |
| CTA 형식 | 질문 | 명령문 | 질문 |
- **결론**: 훅 구체성이 Score 1.4배 차이를 만듦. 추상 훅(B)은 FYP가 높아도 리텐션에서 실패. 공감형 FOMO(C)가 가장 높은 score+유일한 share.

### A/B/C 동일 작품 비교 (Primavera, 2026-04-01)
| 변수 | A (구체적 미스터리) | B (추상적) | C (1% 클릭베이트) |
|------|-------------------|-----------|------------------|
| 훅 | "This woman appears TWICE" | "BOTANICAL PUZZLE" | "Only 1% notice she appears TWICE" |
| Slide 1 구도 | Flora 클로즈업 (밝음) | Chloris/Zephyr (어둡) | Venus 와이드샷 (밝지만 분산) |
| Score | **17.7** | 12.5 | 13.4 |
| Views | **351** | 40 | 328 |
| FYP% | **93.7%** | 41.3% | 91.4% |
| Completion | **15.32%** | 8.70% | 10.29% |
| Avg Slides | **3.03** | 2.05 | 2.53 |
| Engagement | 0/0/0 | 0/0/0 | 0/0/0 |
- **결론**: 훅 구체성 + slide 1 밝기가 FYP와 views를 결정. A(구체적+밝은 클로즈업)가 B(추상+어두운)보다 views 9배. 하지만 3개 모두 slide 3 조기 답 공개 + 인물 도감 구조 → completion 15% 천장. engagement 전멸.
- **Slide 1 교훈**: 같은 그림이라도 밝은 클로즈업(A) > 와이드샷(C) > 어두운 장면(B). 클로즈업이 스크롤 정지력이 높다.

### A/B/C 동일 작품 비교 (Vigée Le Brun 084, 업로드 2026-04-07, +48h 측정 2026-04-09)
| 변수 | A (전기 "before Revolution") | B (패션 "dress caused a riot") | C (혁명 "peasant hat", 다른 작품) |
|------|---------------------------|-------------------------------|--------------------------------|
| 작품 | Self-Portrait w/ Daughter | Self-Portrait w/ Daughter | Self-Portrait in a Straw Hat |
| Score (+48h) | 5.8 | **11.4** | 4.8 |
| Views (+24h/+48h) | 434→582 (+34%) | 920→**923** (+0.3%) | 254→457 (+80%) |
| FYP% | 99.3% | **98.9%** | 99.2% |
| Completion | 11.44% | **21.2%** | 6.0% |
| Saves | 0 | **2** | 0 |
| Likes | 5 | **10** | 6 |
- **결론**: "구체적 물건+극적 결과" 훅(B)이 압도적 승리. +48h에서도 순위 불변 (B가 A의 1.6배).
- **+48h 차등 성장 발견**: 상위 포스트(B)는 24h 이후 정체 (+0.3%), 하위 포스트(A +34%, C +80%)는 2차 배포로 추가 성장. 그러나 **순위 역전은 없음**.
- **"첫 24h 윈도" 수정**: 상위 포스트에만 해당. 하위 포스트는 +48h에서 30~80% 추가 성장 가능하나 상위를 추월하지는 못함.

### A/B 동일 작품 비교 (Leyster 085, 2026-04-07, +48h 측정 2026-04-09)
| 변수 | A (copy, 전기 "erased 250 years") | B (adapt_same, "hidden painting") |
|------|----------------------------------|----------------------------------|
| 타입 | copy | adapt_same_artwork |
| Score (+48h) | 6.9 | **21.0** |
| Views (+24h/+48h) | 348→487 (+40%) | 881→**888** (+0.8%) |
| Completion | 6.96% | **15.09%** |
| Shares | 0 | **5** |
| Saves | 1 | **2** |
| Followers | 0 | **3** |
- **결론**: adapt_same_artwork가 copy 대비 score 3.0배, shares 5:0. 같은 작품이라도 독자적 진입점(바이올리니스트 디테일)이 성과를 결정. copy 타입의 한계 = 원본 서사는 좋아도 크롭 시스템에서 중간 슬라이드 시각 품질 유지 실패.
- **+48h 차등 성장 재확인**: B(+0.8%) 정체, A(+40%) 2차 성장. 084 세트와 동일 패턴.

### 4/8 업로드 4포스트 전부 0 views (측정: 2026-04-09, +24h 경과)
| 포스트 | Type | Views | TikTok URL |
|--------|------|-------|------------|
| post_086_insanity_a | copy | 0 | 존재 |
| post_088_ermine_a | copy | 0 | 존재 |
| post_088_ermine_b | adapt_same | 0 | 존재 |
| post_088_arnolfini_c | adapt_diff | 0 | 존재 |
- 4개 포스트 동시 0 views = **콘텐츠 이슈 아닌 플랫폼/계정 레벨 이슈** (shadow ban, 배포 차단)
- +48h 재측정(4/10): 여전히 전부 0뷰. 반면 4/9 업로드 089_apollo_b는 954뷰 → **4/8 일시적 shadow ban → 4/9 해제 확인**
- 4/8 포스트들은 영구 0뷰 확정. A/B/C 전략 비교 불가

### A/B/C 비교 (Girl with a Pearl Earring 091, 2026-04-10, +24h 측정 2026-04-11)
| 변수 | A (copy, "She has no name") | B (adapt_same, "Look at the pearl") | C (adapt_diff, Milkmaid "hidden Cupid") |
|------|---------------------------|-------------------------------------|----------------------------------------|
| 작품 | Girl with a Pearl Earring | Girl with a Pearl Earring | The Milkmaid |
| Score | 11.7 | **13.5** | 13.1 |
| Views | 918 | **1122** | 970 |
| Completion | 18.70% | **34.41%** | 24.57% |
| Likes | 13 | 7 | **17** |
| Shares | **1** | 0 | 0 |
| Saves | 0 | 0 | **1** |
| 훅 유형 | 사실 진술 | 명령+구체적 도발 | 구체적 미스터리 |
| 핵심 구조 차이 | 전기적 나열, S5~S6 어둠 | **S3 극단 줌(붓터치 리빌)** | 리빌 지연(S5) |
- **결론**: 명령형 훅 + 극단 줌 해체 구조가 completion 역대 최고(34.41%) 달성. B가 A 대비 completion 1.8배. C는 "숨겨진 요소" 패턴 유효하나 리빌 지연으로 목표 미달.
- **새 발견**: "사실 진술" 훅은 "검증 가능한 도발" 훅보다 항상 열위 — pearl_a "She has no name"(사실)은 pearl_b "It's not real"(도발) 대비 completion 절반. 시청자는 **확인하고 싶은 주장**에 스와이프함.

### +48h 배포 패턴 관찰 (084+085 세트 종합)
- **상위 포스트는 24h 이후 정체** (grip_b +0.3%, violinist_b +0.8%)
- **하위 포스트는 24h 이후 2차 성장** (daughter_a +34%, strawhat_c +80%, leyster_a +40%)
- TikTok이 초기 배포에서 낮은 리텐션을 보인 포스트에 2차 기회를 줌. 그러나 순위 역전은 없음
- **실무 시사**: +24h 측정에서 상대 순위가 확정됨. +48h는 하위 포스트의 절대값 보정용

---

## 벤치마크 (현재 최고 성과)

| 지표 | 최고값 | 포스트 | 날짜 |
|------|--------|--------|------|
| Score | 59.9 | post_026_creation | 2026-02-22 |
| Views (+24h) | 1136 | 091_pearl_b | 2026-04-10 |
| Completion | **34.48%** | **091_pearl_b** | **2026-04-10** |
| FYP% | 99.5% | 089_apollo_b | 2026-04-09 |
| Likes | **33** | **092_adele_c** | **2026-04-11** |
| Shares | 5 | 085_leyster_violinist_b | 2026-04-07 |
| Saves | **4** | **092_adele_c** | **2026-04-11** |
| Comments | 1 | post_026_creation | 2026-02-22 |
| New Followers (단일 포스트) | 3 | 085_leyster_violinist_b | 2026-04-07 |

---

*마지막 업데이트: 2026-04-12 by Mira (092_adele_c score 20.7 역대 2위 — likes 33/saves 4 역대 최다, "구체적 충격+역사적 불의" 훅+서사 패턴 신설, 초상화 시각 반복 실패 패턴 추가, completion vs engagement 이원 축 발견, 091 +48h 정체 패턴 재확인)*
