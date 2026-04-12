# Generate Rules — 반복된 실수 방지

이 문서는 과거 8번의 사이클에서 반복적으로 발생한 문제를 정리한 것입니다.
**generate.md 실행 시 반드시 이 문서를 먼저 읽고 시작하세요.**

---

## INSTANT REJECT — 이것만 하면 무조건 탈락

### 1. 텍스트 없는 슬라이드 (7연속 탈락)
- **모든 슬라이드에 영어 텍스트가 있어야 한다**
- 이미지만 있고 텍스트가 없는 "갤러리" 포맷은 **영구 금지**
- Instagram 캐러셀 스타일(이미지 중심, 텍스트 최소)은 TikTok에서 안 통함
- slide 2~6에 text_en이 빈 문자열("")이면 즉시 탈락

### 2. 훅(Slide 1) 10단어 초과 (5연속 탈락)
- Slide 1의 텍스트는 **최대 10단어**
- 15~16단어 훅이 반복적으로 탈락함
- 예시:
  - BAD: "This 600-year-old painting contains a secret that changed art history forever" (12단어)
  - GOOD: "A painter hid himself in this painting." (8단어)

### 3. CTA가 질문이 아님 (4연속 탈락)
- Slide 7은 반드시 **열린 질문**이어야 한다
- 선언문("death hiding at their feet")은 탈락
- 예시:
  - BAD: "He painted his wife with the same care he gave to saints."
  - GOOD: "What else is he hiding in this painting?"

---

## 크롭 관련 — 가장 빈번한 실패 원인

### 4. 텍스트와 이미지 불일치 (매 사이클 발생)
- 텍스트가 "거울을 줌인하라"면 이미지에 **거울이 보여야** 한다
- 텍스트가 "지구본"이면 이미지에 **지구본이 보여야** 한다
- 크롭 좌표를 정할 때 **텍스트에서 언급하는 대상이 프레임의 30% 이상**을 차지해야 함

### 5. 인접 슬라이드 크롭 겹침 (매 사이클 발생)
- 연속된 두 슬라이드가 같은 영역을 보여주면 탈락
- Slide 1→2는 **반드시 극적인 시각 전환**이어야 함 (전체→얼굴 줌인, 인물→배경 등)
- 크롭 좌표가 70% 이상 겹치면 탈락

### 6. 어두운/빈 영역만 보이는 크롭
- 옷, 배경 등 어두운 영역만 채운 슬라이드는 탈락
- **하늘/구름/빈 배경도 동일 실패** — 밝아도 콘텐츠가 없으면 이탈
- 크롭 영역에 **의미 있는 시각 요소**(얼굴, 손, 물건, 디테일)가 있어야 함
- **사례 (R20)**: God Speed — "얼굴 클로즈업" 크롭 [0.1,0.02,0.65,0.35]이 실제로는 성문/포트컬리스(어두운 아치)에 착지. 여자 얼굴이 아니라 90% 검은 화면. 크롭 좌표를 정할 때 원본 이미지에서 해당 영역이 어두운지 반드시 확인할 것
- **사례 (wedding_cana A/B/C)**: 3개 포스트 모두 slide 2가 하늘/구름 크롭 → avg_slides 2.0~2.7. 훅 직후 슬라이드가 빈 영역이면 즉시 이탈. **특히 slide 2는 반드시 인물/디테일/액션이 있는 영역이어야 함**

---

## 작품 선택 관련

### 7. 가로 풍경화는 9:16에 부적합
- Ophelia(Millais) — 전부 풀숲으로 탈락 **(R12, R20 두 번 사용, 두 번 탈락)**
- 가로 비율이 극단적인 작품은 피할 것
- **세로 또는 정사각형에 가까운 작품**이 9:16 크롭에 유리
- **Ophelia는 영구 금지** — 규칙에 명시되어 있음에도 R20에서 재사용되어 동일 사유로 탈락

### 8. 이전에 painting_suitability FAIL 판정받은 작품 재사용 금지
- R22에서 Lady of Shalott가 가로 풍경화로 탈락 → R23에서 Proserpine으로 교체하여 3/3 통과
- R25에서 Lady of Shalott를 다시 plan에 넣어 또 탈락
- **한번 부적합 판정받은 작품은 재사용하지 말 것** — "원본 계정이 성공했으니 가능"이라는 논리는 통하지 않음. 원본은 텍스트 없는 갤러리 포맷이고, 우리는 텍스트 오버레이 슬라이드 포맷. 크롭 요구사항이 다름.
- 금지 목록: Ophelia (Millais), Lady of Shalott (Waterhouse)

### 9. 어두운 작품은 슬라이드에서 안 보임
- Caravaggio의 chiaroscuro(명암법) 작품 — 화면이 너무 어두워서 탈락
- 밝은 영역이 충분한 작품을 선택할 것

### 9-a. 가로 밀집 프레스코(다인물 와이드)는 9:16 부적합 (R27 School of Athens 3/3 탈락)
- School of Athens 같은 50+ 인물 가로 프레스코는 **개별 인물을 9:16으로 isolate 불가**
- 크롭을 넓게 잡으면 건축/하늘이 화면을 지배 (인물이 <20%)
- 크롭을 좁히면 해상도 붕괴 또는 텍스트가 가리키는 인물이 프레임 밖으로 이탈
- **사례 (R27)**: Post A slide 5 "Look at Plato's face" → 크롭 [0.4,0.18,0.56,0.55]가 아치+하늘 착지, Plato는 좌하단 5% 미만. Post B slide 3 "look where she's looking" → 크롭이 Hypatia가 아닌 옆 병사에 착지. Post A slide 1도 인물 작고 건축 지배.
- **금지 목록 추가**: The School of Athens (Raphael). 동일 이유로 Last Judgment (Michelangelo), The Last Supper (Leonardo wide shot) 주의.
- **규칙**: "50+ figures in a wide mural" 포맷은 영구 금지. 1-3 dominant figures in vertical/square composition만 선택.

### 9-d. Planner의 허구 기반 작품 서술 금지 (R29 Vigée Le Brun 3/3 탈락)
- Planner가 plan.json의 `artwork_fits_criteria` / `crop_description`에 적은 **색상·구도·배경 서술이 실제 원본 이미지와 다를 때** 스토리 전체가 무너진다
- **사례 (R29)**: Vigée Le Brun "Self-Portrait with Her Daughter, Julie" (1786, Louvre) — Planner는 "붉은 드레스, 밝은 따뜻한 배경, 어두운 void 없음"이라 서술했으나 실제 작품은 (a) 엄마가 **흰색/아이보리** 드레스 착용, (b) 배경이 dark brown/olive로 프레임의 ~50%, (c) 우측 절반이 검은 void. Post B의 중심 스토리 "Red was the color Marie Antoinette chose... Vigée Le Brun painted herself in the queen's color"는 **존재하지 않는 붉은 옷**에 기반 → slide 5가 완전히 허구.
- **사례 (R29)**: 같은 Planner가 "Self-Portrait in a Straw Hat"의 sash를 "red sash, Marie Antoinette's favorite color"로 서술. 실제 sash는 **골드/yellow**. Post C slide 4 전체 내러티브가 허구 → accuracy 탈락.
- **규칙**: Planner는 plan.json 작성 전 원본 이미지를 **실제로 열어서** 확인할 것. 특히 hook이나 핵심 반전이 "특정 색/물건/포즈"에 의존한다면 그 요소가 이미지에 **실제로 보이는지** 반드시 검증.
- **규칙**: Evaluator는 각 포스트의 hook과 answer slide에서 언급된 시각적 디테일(색, 물건 이름)이 원본 이미지에 실제 존재하는지 체크한다. 없으면 accuracy FAIL.

### 9-e. 단일 작품 2포스트(A+B) 공유 시 최소 요건 재확인 (R29 재발)
- R28에서 이미 규칙화("5개 이상 시각적 구별 영역 필요")했으나 R29에서 동일 실수 — Vigée Le Brun 모녀 초상을 A+B에 공유 → 실질 구별 영역 3개뿐(엄마얼굴/포옹/Julie얼굴)
- Post A는 slide 1/3/5/7 네 장이 모두 엄마 얼굴 close-up (dark void 포함)으로 결론 — 7장 중 4장 동일 화면
- **강화 규칙**: 단일 작품을 2포스트 14 슬라이드로 쪼갤 거면, 원본에서 5개 이상 "시각적으로 극적으로 다른" 영역을 먼저 리스트업 → 실패 시 C처럼 **다른 작품**으로 분리하거나, 14 슬라이드 중복을 감수하지 말고 2포스트 대신 1포스트로 축소

### 9-c. 인물 뒤에 거대한 단색 배경(벽/천/void)이 있는 초상화는 크롭 지뢰 (R28 Artemisia Allegory of Painting 3/3 탈락)
- 세로 구도이고 단일 인물이어도, 인물이 프레임의 60% 미만이고 나머지가 어두운 벽/단색 배경이면 **크롭 좌표가 배경에 착지하는 사고가 반복 발생**
- **사례 (R28)**: Artemisia Gentileschi "Self-Portrait as the Allegory of Painting" — 인물 뒤 ~50%가 dark brown wall. 크롭 [0.55,0.02,1.0,0.4] (팔/붓 의도) → 95% dark wall, 붓 전혀 안 보임. 크롭 [0.15,0.0,0.6,0.3] (머리 의도) → 90% dark wall. 크롭 [0.5,0.0,1.0,0.4] (팔 의도) → 90% dark wall. 3개 포스트 중 2개가 동일 작품을 공유했는데 두 포스트 모두 같은 사고가 반복됨.
- **판정 방법 (Planner 작품 선정 시)**: 원본 이미지를 열어 인물이 차지하는 영역을 측정. **인물이 전체 프레임의 60% 이상을 차지하지 않으면 SKIP**. 특히 y<0.3 또는 x>0.7 영역이 단색 배경이면 해당 사분면을 쓰는 크롭은 전부 실패한다.
- **단일 작품을 2개 포스트에 쓰는 경우 최소 5개의 시각적으로 구별되는 영역이 필요하다** — Artemisia Allegory는 3개만 가능(얼굴 프로필, 가슴/펜던트, 드레스) → 14개 unique 크롭 생성 불가.
- **금지 목록 추가**: Artemisia "Self-Portrait as the Allegory of Painting" (double-post 용도).

### 9-b. 그래픽 폭력/유혈 장면은 TikTok 콘텐츠 정책 위반 (R27 Judith Slaying Holofernes 탈락)
- **참수/유혈/고문 장면을 중심 이미지로 사용 금지** — TikTok 커뮤니티 가이드라인 위반 가능성 + 시청자 즉시 이탈
- 아무리 미술사적으로 유명해도 안 됨
- **금지 작품 카테고리**:
  - Judith Slaying Holofernes (Artemisia Gentileschi, Caravaggio 버전 모두)
  - David with the Head of Goliath (Caravaggio)
  - Salome with the Head of John the Baptist
  - The Massacre of the Innocents
  - Saturn Devouring His Son (Goya)
  - 모든 십자가형/순교 장면의 클로즈업
- "옷/배경/손만 크롭하면 괜찮지 않나?" → 안 됨. 그 경우 텍스트가 말하는 폭력 내용과 이미지가 괴리되어 story 실패. 둘 다 실패.
- **사례 (R27)**: Judith Slaying Holofernes — slide 2/4/6이 참수된 목/피 클로즈업. slide 1/5/7은 "안전한" Abra 크롭을 3번 반복해 시각 단조로움. 양쪽 다 fail.

---

## 성과 기반 규칙 — TikTok 메트릭에서 배운 것

### 10. Slide 3 이전에 답을 주지 말 것 (completion rate 급락 원인)
- 훅에서 던진 미스터리의 답이 slide 2~3에서 나오면 avg_slides_viewed = 3.0에서 이탈
- **답은 slide 5~6으로 미뤄야 한다**
- 중간에 새로운 미스터리를 추가해서 궁금증을 유지할 것
- 예시:
  - BAD: slide 1 "이 여자가 두 번 나온다" → slide 3 "Chloris = Flora" (답 나옴, 이탈)
  - GOOD: slide 1 "뇌가 숨겨져 있다" → slide 3 "손가락이 안 닿는다" (새 미스터리) → slide 5 "시체를 해부했다" (또 새 반전)

### 11. 7장이 하나의 스토리 아크여야 한다 (인물 도감 금지)
- Slide 4~6이 메인 스토리와 관계없는 새 인물/팩트를 나열하면 이탈
- 하나의 중심 미스터리를 7장에 걸쳐 풀어야 함
- "그림 속 인물 소개" 형식 금지 — 시청자는 가이드투어를 원하지 않음

### 12. 훅은 반드시 구체적이어야 한다 (3회 확인, views 최대 9배 차이)
- 같은 그림, 같은 캡션이어도 훅이 구체적이면 FYP 94%, 모호하면 41%
- "BOTANICAL PUZZLE" → 40뷰, FYP 41.3% vs "This woman appears TWICE" → 351뷰, FYP 93.7% (같은 Primavera, views 9배 차이)
- "STOLEN FEAST" → score 13.9, avg_slides 2.02 vs "Everyone walks past this painting" → score 19.7 (같은 Wedding at Cana)
- "Only 1% notice she appears TWICE" → 328뷰, FYP 91.4% (구체적이지만 A의 직접적 표현보다 약간 약함)
- **항상 구체적 사실 + 도발** 조합: "숨겨진 OOO", "두 번 나오는 OOO", "OOO가 아니다"
- 공감형 FOMO도 효과적: "Everyone walks past..." (개인적 관련성 + 구체적 상황)
- **추상적 2단어 훅은 절대 금지** — 3회 연속 실패 확인. FYP 알고리즘 자체가 푸시를 거부함.

### 13. CTA는 답할 수 있는 질문이어야 한다 (comments 유도)
- "What do YOU see?" → 너무 막연, 댓글 0
- "이 그림에서 ___를 찾았어?" → 구체적, 답할 수 있음
- 질문이 slide 1~6에서 다룬 내용과 직접 연결되어야 함

---

### 14. "줌인 = 다른 슬라이드" 착각 금지 (연속 3장 같은 영역)
- 같은 영역을 전체→중간→타이트로 3장 연속 크롭하면, 만든 사람은 "다른 슬라이드"라고 느끼지만 시청자는 **같은 화면 3번**으로 인식
- **사례 (R21)**: God Speed — 슬라이드 3 [0.25,0.35,0.9,0.7] (mid-body 전체) → 슬라이드 4 [0.42,0.40,0.66,0.56] (같은 영역 타이트) → 슬라이드 5 [0.50,0.38,0.72,0.55] (또 같은 영역). 3장이 전부 드레스+팔+사시 영역.
- **해결책**: 연속 3장이 같은 영역이면, 가운데 슬라이드를 **완전히 다른 영역**으로 교체 (예: 배경, 반대편 인물, 소품 등)
- 크롭 좌표의 x/y 범위가 이전 슬라이드와 50% 이상 겹치면서 다음 슬라이드와도 50% 이상 겹치면 → 가운데 슬라이드 교체 필수

### 16. 크롭 좌표 시각 검증 의무 (R25~R29-retry 4사이클 연속 루트 원인)
- 좌표를 **짐작으로 지정하지 말 것**. "눈 부위는 대충 y=0.15~0.30" 같은 추측은 반복적으로 실패함
- **필수 프로세스**: plan.json에서 crop 좌표를 지정한 후, 생성된 `crop_N.jpg`를 **실제로 열어** 다음을 확인:
  1. 텍스트가 말하는 대상(얼굴/손/물건)이 프레임 안에 있는가?
  2. 대상이 프레임의 30% 이상을 차지하는가?
  3. dark void / 빈 하늘이 40% 미만인가?
- 하나라도 실패 → 원본에서 대상의 실제 픽셀 좌표 (px, py)를 측정 → (px/W, py/H)로 정규화 → ±0.15 마진으로 크롭 재계산
- **사례 (R29-retry post_084_vigee_grip_b slide 3)**: 텍스트 "She's looking straight at you"를 위해 crop [0.38,0.14,0.66,0.3]을 지정 → 실제로는 **터번 뒤통수**에 착지, 눈 전혀 없음. 엄마의 실제 눈 위치는 y≈0.22~0.28이었음. 좌표를 시각 검증했다면 즉시 발견됐을 오류
- **사례 (R29-retry post_084_vigee_daughter_a slide 3 & vigee_grip_b slide 4)**: 둘 다 Julie 얼굴을 잡으려 했으나 같은 좌표 miss 패턴. Julie의 실제 픽셀 위치를 측정하지 않고 추측
- **사례 (R29-retry post_084_strawhat_c slide 6)**: "left-side face + pearl earring"을 위해 [0.12,0.2,0.45,0.42] → 하늘+모자 뒷면만 잡힘. 얼굴 측면은 x≈0.35~0.60에 있음

### 18. Crop aspect ratio는 반드시 9:16이어야 한다 (R31 신설, 4사이클 연속 루트 원인)
- 슬라이드 렌더러의 출력 aspect는 9:16 (0.5625). Planner가 지정하는 crop 좌표의 width/height 비율이 이보다 크면 (즉 landscape/square에 가까우면) 렌더 시 letterbox/shrink가 발생하여 피사체 위아래에 거대한 빈 공간이 생기고, 얼굴/대상은 프레임의 25% 이하로 축소된다
- **사례 (R31 post_085_leyster_a slide 1)**: crop [0.18, 0.07, 0.48, 0.44] → width 0.30, height 0.37, aspect 0.81 (가로가 더 넓음). 9:16 렌더 후 얼굴이 프레임 하단 1/3에 위치, 입이 잘림, 상단 ~50%가 올리브 배경 void. Planner는 "face fills ~55%"를 약속했으나 실제 ~25%.
- **사례 (R31 post_085_leyster_violinist_b slide 3)**: 동일 crop [0.18, 0.07, 0.48, 0.44] 재사용 → 동일 fail. slide 7 [0.15, 0.05, 0.98, 0.60] (aspect 1.51, 와이드) → 70% 올리브 void.
- **사례 (R31 post_085_morisot_cradle_c slide 2)**: crop [0.42, 0.02, 0.90, 0.28] (aspect 1.85) → 중앙 세로 dark void의 추상 천 크롭. 핑크 리본이 프레임 밖.
- **HARD RULE**: 모든 crop 좌표는 `(x2-x1) / (y2-y1) ≈ 0.5625 × (W_original/H_original)` 을 만족해야 한다. 원본이 0.88 aspect라면 crop의 width/height 비율이 0.5625 / 0.88 × ... 즉 정규화 좌표에서 **(x2-x1) ≈ 0.5625 × (y2-y1) × (H_orig/W_orig)** 를 만족시켜야 한다. 실용적으로는: crop을 9:16 세로 박스로 먼저 그리고, 그 안에 subject가 들어오도록 위치 조정.
- **규칙**: Generator는 Planner의 crop 좌표를 받으면 각각에 대해 aspect 계산 → 9:16에서 벗어나면 즉시 REJECT하고 재작성 요청.

### 17. Emotional/historical content는 face/hand로 carry해야 한다 (R30 신설)
- 텍스트가 감정/역사적 무게를 다룰 때(예: "12 years of exile", "Robespierre, Napoleon", "1789 escape"), 크롭 이미지는 **인간 요소(face, hand, eyes, embrace)**여야 그 감정이 시각적으로 전달된다.
- 추상적 직물 텍스처(gold cushion, drape folds, fabric only) 크롭은 텍스트가 사실/연도를 나열할 때만 한정 사용 가능. 감정/내러티브 클라이맥스에는 절대 금지.
- 근거: R30 post_084_vigee_daughter_a/grip_b slide 6 — 동일한 gold cushion abstract crop ([0.15,0.74,0.85,0.99]). 텍스트는 망명 12년 + Robespierre + Napoleon. 직물 텍스처가 그 무게를 carry 못함. 두 포스트 모두 통과는 했으나 slide 6만 fail.

### 19. 연속 2장 이상 어두운/빈 크롭 금지 — "어둠의 터널" (R32b 성과 분석, 4회 확인)
- Slide 2~4 구간에서 연속 2장 이상 어두운(>50% dark) 또는 빈(하늘/배경) 크롭이 오면 리텐션 붕괴
- 훅이 아무리 강해도 중간 슬라이드가 연속으로 시각적으로 죽으면 이탈 불가피
- **사례 1 (085_leyster_a)**: 훅 "erased from art history for 250 years"(강력) → S2 어두운 손, S3 어두운 스커트, S4 어두운 천 = 3연속 어두운 크롭 → completion 6.23%, score 4.5. 같은 작품 B(바이올리니스트 진입점)는 밝은 S1~S3 → score 21.3 (4.7배)
- **사례 2 (084_strawhat_c)**: S4 하늘+꽃, S6 하늘 블러 = 빈 크롭 2장 → score 3.8. S1이 5개 중 가장 밝았는데도 최하
- **사례 3 (wedding_cana A/B/C)**: S2가 모두 하늘 크롭 → 3개 전부 avg_slides 2.0~2.7
- **사례 4 (084_vigee_daughter_a)**: S1 어두운 반쪽 얼굴, S3 어두운 배경의 Julie, S6 금쿠션 → score 4.9
- **규칙**: 인접한 2장(특히 S2~S4)이 모두 >50% 어둡거나 빈 배경이면 → 하나를 밝은 디테일(얼굴/손/물건)로 교체 필수. 밝은 크롭과 어두운 크롭을 반드시 교대 배치.

### 21. 훅이 가리키는 요소가 표면에 보이지 않는 작품 금지 (R34 신설, post_088_unicorn_c)
- 작품의 hook이 "X가 숨겨져 있다"인데 그 X가 **현재 캔버스 표면에 시각적으로 존재하지 않으면** 7장 전체가 무너진다
- X-ray/적외선/덧칠 아래만 존재하는 요소(overpainted layers, pentimenti)는 미술사적으로 매혹적이지만 **TikTok 슬라이드 포맷에서는 절대 안 됨** — 시청자는 X-ray를 못 보고, 우리는 Wikimedia 표면 이미지만 사용
- **사례 (R34 post_088_unicorn_c "Lady with a Unicorn" by Raphael)**: 훅 "This unicorn was hiding under a saint" — 실제 캔버스에는 (a) unicorn 안 보임 (덧칠로 가려짐), (b) 원래 그려진 dog도 안 보임, (c) saint Catherine attributes(wheel/palm)도 18세기에 다시 제거됨. 7장 모두에서 hook이 약속한 요소가 한 번도 등장하지 않음. CTA "Dog, unicorn, or saint — which version?"인데 셋 다 보이지 않음
- **판정 방법 (Planner)**: 작품 후보를 정할 때 hook의 핵심 명사가 원본 Wikimedia 이미지에서 **눈에 띄게 보이는지** 먼저 확인. 안 보이면 다른 작품으로 교체
- **금지 작품**: Lady with a Unicorn (Raphael, Galleria Borghese) — pentimenti 기반 스토리 사용 불가
- **예외**: 요소가 표면에 부분적으로라도 보이면 OK (예: Lady with an Ermine의 ermine은 명확히 보임). 완전히 덧칠로 가려진 경우만 금지

### 26. 실내 건축 장면의 "상단 ceiling/벽 void 45%" 트랩 (R27 post_091_meninas_c 신설)
- Las Meninas처럼 실내 scene인 작품은 caution — 작품 상단의 architectural negative space (천장, 대들보, 어두운 후벽)가 캔버스의 40~50%를 차지할 수 있다. 이런 작품에 "full painting" S1 크롭을 y=0.05~0.90으로 잡으면 상단 절반이 검은 void가 되고 figures는 하단 1/3에 쥐어짜진다 (rule #6 + #15-b 동시 위반)
- **사례 (R27 post_091_meninas_c)**: Las Meninas (Velázquez 1656). Figures(왕녀, menina, Velázquez, 도어맨)가 실제로 y≈0.55~0.95에 몰려있고 상단 0.00~0.55는 어두운 천장+빈 후벽+샹들리에만. Planner는 plan.json에서 Velázquez 얼굴을 y=0.35, Margarita를 y=0.50, 도어맨을 y=0.25로 추정했으나 실제는 모두 +0.20~0.35 아래. S1~S6 6장 전부 렌더 결과가 dark void 대량 착지 (S5/S6는 거의 100% 검정). 1/7 slides만 통과
- **판정 방법 (Planner)**: 실내 scene 작품 후보를 정할 때 figure 영역의 y-bottom boundary를 측정. figures가 y>0.5에 몰려있으면 S1 크롭은 y1>=0.35, y2<=0.97로 제한해서 상단 ceiling void를 잘라낼 것. 세부 크롭(S2~S6)도 figures의 실제 y-range 안에서만 좌표 지정
- **금지 작품 카테고리**: 상단 >40%가 어두운 건축 void인 실내 장면. Las Meninas는 크롭 정밀도 요구가 극단적이므로 pixel-measure 없이는 영구 금지
- **대안**: 실내 scene은 Caravaggio Calling of Saint Matthew(figures 전면 분포), Vermeer 단일 인물 실내(The Milkmaid, The Geographer)가 더 안전

### 27. Extreme zoom 크롭(<15% frame width)은 pixel-measure 필수 (R27 post_091 pearl A+B 신설)
- <15% width 또는 <15% height의 극단 줌 크롭은 fractional 추측으로는 ±0.02 오차만으로도 대상(진주, 눈, 작은 물건)이 프레임을 완전히 벗어난다
- **사례 (R27 post_091_pearl_a S3)**: 크롭 [0.43,0.48,0.53,0.63] width 0.10 — "fake pearl" 훅의 답 슬라이드인데 크롭이 진주를 비켜가 craquelure 볼/목 텍스처에 착지. 진주 0%. 원본 12285×14550에서 진주의 실제 pixel 중심(px, py)을 측정했다면 즉시 잡을 수 있었을 오류
- **사례 (R27 post_091_pearl_b S3)**: 크롭 [0.38,0.44,0.50,0.62] — 동일 실패. 입+볼만 잡고 진주 0%. A/B 두 포스트가 **같은 좌표 추측 실수를 병렬로 반복**
- **판정 방법**: (x2-x1)<0.15 또는 (y2-y1)<0.15인 모든 크롭은 Planner가 원본 이미지에서 대상 중심 픽셀 (cx, cy)을 측정 → 정규화 (cx/W, cy/H) → ±0.06 margin으로 crop 박스 계산. fractional 추측 금지
- **체크**: 답 공개(reveal) 슬라이드가 extreme zoom이라면 pixel-measure는 hard requirement. reveal 크롭이 대상을 놓치면 포스트 전체가 death — "The answer is right here"라고 말하는 순간 보이지 않으면 영원히 복구 불가

### 22. 동일 painting source 저해상도 사전 검증 (R34 보강)
- 작품을 정한 후 Wikimedia 원본 파일의 해상도를 확인 — long edge가 **2700px 미만**이면 슬라이드 close-up이 흐릿해짐
- **사례 (R34 post_088_unicorn_c)**: Lady with a Unicorn 원본이 저해상도 → slide 4/5/6/7에서 픽셀이 보일 정도로 흐림
- **규칙**: Planner는 image_url을 plan.json에 적기 전 long edge >= 3000px 확인. 미달이면 다른 source 또는 다른 작품

### 20. 같은 작품 A/B 포스트 간 동일 크롭 좌표 최대 2개 (R33 신설)
- 같은 그림을 A/B 두 포스트로 나눌 때, **동일 crop 좌표를 공유하는 슬라이드는 최대 2개** (full reveal + 1개까지만 허용)
- 3개 이상 동일 좌표를 쓰면 시청자가 두 포스트를 보았을 때 "같은 콘텐츠 리오더"로 인식 → 채널 신뢰도 하락
- **사례 (R33)**: Bronzino Allegory A/B — S2=A의 S1 [0.28,0.08,0.48,0.36], S3=A의 S2 [0.58,0.16,0.81,0.48], S5=A의 S5 [0.0,0.0,1.0,1.0], S7=A의 S7 [0.15,0.72,0.35,1.0]. **4/7 동일** → A/B 차별화 실패
- **규칙**: B 포스트 설계 시, A에서 사용한 크롭 목록을 확인하고 겹치는 좌표가 2개를 초과하면 B만의 독자적 영역(배경 디테일, 미사용 인물, 소품 등)으로 교체

### 23. Hook 슬라이드와 Answer 슬라이드 시각 중복 금지 (R34-retry-3 신설)
- 답 공개 슬라이드(보통 slide 5)가 hook 슬라이드(slide 1)와 거의 동일한 crop을 사용하면, 시청자는 "답이 이미 1번 슬라이드에 다 있었다 = 중간 슬라이드 볼 이유 없었다"고 느낀다
- Narratively 'callback'으로 의도해도, 시각적으로는 progression 단절. 7장 중 1장이 죽는 효과
- **사례 (R34-retry-3 post_088_arnolfini_c)**: S1 [0.359,0.23,0.5207,0.44]와 S5 [0.363,0.235,0.517,0.435]가 좌표 차이 0.005 이내, 렌더 결과 사실상 동일 이미지. 답 공개 슬라이드인데 새 시각 정보 없음
- **규칙**: Answer 슬라이드는 hook 슬라이드와 다른 zoom level + 다른 angle을 강제 — 예: hook이 wide shot이면 answer는 같은 영역의 tight crop, hook이 tight crop이면 answer는 1단계 zoom out + 인접 디테일 추가
- **체크**: Planner가 plan.json 작성 시 S1과 S5의 좌표 차이가 모든 축에서 ±0.05 이내면 즉시 재작성

### 24. 이전 사이클 Jin 권고 좌표는 반드시 적용 + 재검증 (R34-retry-4 신설)
- **사례 (R34-retry-4 post_088_ermine_b S7)**: 이전 사이클(R34-retry-3)에서 Jin이 "S7 ermine 영역 좌표 오프셋 — 텍스트 'ermine is the joke'인데 진주 목걸이가 dominant. 권고 [0.40, 0.55, 0.62, 0.85]"로 마이너 issue 명시. v4 재생성 시 Maya가 plan.json 좌표를 그대로 두고 동일 fail 재발 → 6/7로 한 단계 강등
- **사례 (R34-retry-4 post_088_ermine_a S1)**: 이전 사이클 마이너 권고 "x_min 0.27 → 0.30+ 우측 이동"도 미적용. 좌측 dark void 30% 잔존
- **루트 원인**: notes.md의 "다음 사이클 마이너 issue" 섹션을 Maya가 plan.json 좌표 수정 항목으로 자동 변환하지 않음
- **규칙**: Generator(Maya)는 재생성 전에 반드시 직전 verdict.json + notes.md의 마이너 이슈/권고 좌표를 모두 확인하고, 권고 좌표를 plan.json에 반영한 뒤 PIL probe로 재검증해야 한다
- **체크**: 같은 post_id가 2 사이클 연속 동일 슬라이드에서 같은 종류 fail이면 자동 reject 사유

### 28. 렌더 후 slide_N.png에 텍스트 오버레이가 실제 존재하는지 probe 검증 필수 (R27-retry post_091 신설)
- Generator가 slide 렌더를 마친 후 slide_N.png의 텍스트 밴드 영역을 PIL로 probe (또는 OCR)해서 **텍스트 레이어가 실제로 렌더링됐는지** 확인하지 않으면, 텍스트 레이어가 silently drop 되는 사고를 catch 할 수 없다
- **사례 (R27-retry post_091_milkmaid_c S7)**: 슬라이드 1~6은 모두 정상 렌더. 그러나 slide_7.png는 painting 이미지 + 우측 상단 watermark만 있고 **CTA 텍스트 "The Milkmaid, Vermeer (c. 1658). What else did he hide?"가 완전히 누락**. posts.json에는 text_en이 있으나 이미지에는 없음. text_existence.text_visible_on_image FAIL → INSTANT REJECT entire post. 나머지 6장은 good execution이라 S7 하나 때문에 전체 폐기.
- **사례 (R27-retry post_091_pearl_a S7)**: 동일 사이클에서 pearl_a S7의 title-card 텍스트가 dark 배경 위에 low-contrast white로 렌더되어 borderline illegible. 텍스트 레이어는 존재하지만 readability가 사실상 fail. "렌더 완료 ≠ 가독 완료".
- **판정 방법**: 매 슬라이드 렌더 후, 텍스트가 배치된 밴드(예: bottom 15% 또는 bottom 25%)의 픽셀 색 분포를 PIL로 샘플링 — 만약 전체 픽셀이 배경 단일 색상(std < threshold)이면 텍스트 미렌더. 또는 가장 밝은 픽셀과 평균 밝기 차이가 작으면 low-contrast → 재렌더
- **강화**: 타이틀 카드(S7 CTA) 슬라이드는 반드시 텍스트 밴드에 semi-opaque dark overlay 또는 gradient를 강제하여 white text의 가독성 보장
- **규칙**: Produce 파이프라인에 "post-render verify" 단계 추가 — slide_N.png별 텍스트 region probe 결과를 metadata.json에 text_render_ok: true/false로 기록하고, 하나라도 false면 Produce가 스스로 재시도

### 29. Retry 후 plan.json playbook_check 필드를 반드시 최신 콘텐츠에 맞게 업데이트 (R27-retry post_091_pearl_a 신설)
- **사례 (R27-retry post_091_pearl_a)**: Maya가 retry 시 hook 텍스트를 "The Most Beautiful Painting?" (4w, 약한 훅) → "She has no name. Vermeer made her up." (8w, 주어 명확)로 재작성하고 answer_reveal_slide를 3 → 6으로 지연. 그러나 plan.json의 playbook_check 필드는 여전히 `hook_avoids_failed: false, answer_reveal_slide: 3`으로 남겨짐. Jin QA 프로토콜 step 3이 plan.json 값으로 auto-reject → on-disk 콘텐츠가 실제로는 fix 되었음에도 auto-reject 발동
- **루트 원인**: Maya가 slide 파일과 posts.json만 업데이트하고, plan.json의 meta 필드(playbook_check)를 함께 업데이트하지 않음. 결과적으로 "plan과 실제 콘텐츠가 불일치"하는 상태
- **규칙**: Generator(Maya)는 retry 시 content를 수정할 때마다 plan.json의 playbook_check 필드(hook_avoids_failed, answer_reveal_slide, cta_is_specific_question, failed_pattern_avoided, story_structure 등)를 함께 갱신해야 한다
- **규칙**: plan.json에 post_id가 바뀌었거나 작품이 교체된 경우(meninas_c → milkmaid_c)에도 plan.json의 해당 post 엔트리를 새 painting/id로 교체해야 한다. 기존 엔트리를 남기고 output 디렉토리만 교체하면 Jin의 plan vs produce cross-check가 실패
- **체크**: Evaluator는 verdict.json 첫 단계에서 plan.json의 각 post id가 content/output/에 실존하는지 cross-check하고, playbook_check 필드가 실제 slide 1 텍스트(hook)와 매칭하는지 확인

### 30. 캡션은 plan.json 것을 그대로 사용 — 제네릭 템플릿 금지 (R28 post_093 신설)
- Generator가 plan.json에 작성된 상세 캡션을 무시하고 "Everything you thought you knew about X is wrong." 같은 제네릭 템플릿을 적용하는 패턴 반복
- **사례 (R27 post_092_adele_c)**: plan.json에 나치 약탈 스토리 캡션이 있었으나 "Everything you thought you knew about Portrait of Adele..." 제네릭 사용. QA에서 경고
- **사례 (R28 post_093_mona_a, mona_b)**: 동일 제네릭 템플릿 "Everything you thought you knew about Mona Lisa is wrong." 재사용. plan.json에는 mona_a용 "SECRETS ABOUT THE MONA LISA" 캡션, mona_b용 도난 스토리 상세 캡션이 존재
- **규칙**: Generator는 plan.json의 caption_en 필드를 그대로 posts.json에 복사해야 한다. "Everything you thought you knew about X" 패턴은 영구 금지
- **체크**: posts.json의 caption_en이 plan.json의 caption_en과 일치하는지 diff

### 25. 리스티클/카운트다운 포맷 금지 (R24 성과 분석 신설)
- "Top N Paintings of X" 같은 여러 작품을 순위로 나열하는 포맷은 영구 금지
- 단일 작품 심층 분석 = 스토리 아크 유지 가능. 여러 작품 나열 = rule #11(인물 도감) 위반
- **사례 (post_086_insanity_a)**: "Top 5 Paintings of Insanity" — 6슬라이드에 5개 다른 작품 나열. S1=S6 동일 이미지(Saturn). 0뷰(계정 이슈)이나 rule 9-b(유혈 금지, Saturn Devouring) + rule 11(인물 도감) 이중 위반
- **규칙**: Planner는 단일 작품(또는 같은 화가의 2작품까지)만 사용. 3개 이상 다른 작품을 한 포스트에 나열 금지

### 15. Slide 1은 반드시 밝은 클로즈업 + 훅의 주어 포함이어야 한다 (FYP + 리텐션 결정적)
- 같은 그림에서 slide 1 구도만 다르게 한 A/B/C 비교 결과:
  - 밝은 클로즈업 (primavera_a: Flora 정면) → FYP 93.7%, 351뷰
  - 밝은 와이드샷 (primavera_c: Venus 중심) → FYP 91.4%, 328뷰
  - 어두운 장면 (primavera_b: Chloris/Zephyr) → FYP 41.3%, 40뷰
- **어두운 slide 1은 FYP 알고리즘 자체가 거부** — 밝은 버전 대비 views 1/9
- Slide 1 크롭은 반드시: (1) 밝은 영역, (2) 주제 오브젝트가 30% 이상 차지, (3) 시선을 잡는 색감
- **15-b. 훅의 명사 주어가 S1에 시각적으로 존재해야 함 (신규, 090_lilies_b 검증)**
  - "얼굴 클로즈업" 자체는 필수 아님 — 089_apollo_b(대리석 토르소, 얼굴 없음) 954뷰로 증명
  - **그러나 훅의 핵심 명사(prince, woman, sculpture, dress, crown 등)가 프레임에 시각적으로 존재해야 함**
  - **성과 근거 (090_lilies_b)**: 훅 "This ancient prince is three different people" + S1은 벽토 배경+곡면+파동 라인만(prince 0%, 인물 0%) → **views 51**, completion 12.24%, score 4.4. 같은 사이클 adapt_same 타입 apollo_b(954뷰)의 **1/18.7**. 훅 품질, 스토리 아크, 전략 타입 모두 동등한 조건에서 S1 이미지 차이만으로 views 18배 격차
  - **QA 근거 (090_liberty_c)**: S1이 깃발+하늘만, 리버티 얼굴 0% → QA reject (4/7)
  - **QA 근거 (088_arnolfini_c)**: 훅 "ghost in this mirror"인데 S1에 거울 없음 → QA reject
  - **Pre-check 필수 질문**: "이 S1 이미지만 보고, 훅의 주어(명사)가 무엇인지 1초 안에 가리킬 수 있는가?" No이면 즉시 재크롭
  - **프레스코 파편 작품 주의**: Knossos/Minoan 프레스코처럼 원본이 figure + 배경 파편으로 이루어진 경우, 9:16 크롭이 배경 파편에 착지하기 쉬움. Planner는 작품 선정 시 "figure 영역이 9:16 세로 박스 안에 온전히 들어오는가"를 확인

---

## 크롭 좌표 체크리스트

슬라이드를 생성하기 전에 각 크롭에 대해 확인:

1. 이 크롭 안에 텍스트에서 언급하는 대상이 보이는가?
2. 이 크롭이 이전 슬라이드와 70% 이상 겹치지 않는가?
3. 이 크롭이 어두운 영역만으로 채워지지 않았는가?
4. 이 크롭에서 주요 대상이 프레임의 30% 이상을 차지하는가?
5. **(S1 전용) 훅의 명사 주어(prince/woman/sculpture/dress 등)가 S1 이미지에 시각적으로 존재하는가?** — rule #15-b, 090_lilies_b 51뷰 실패 근거

**5개 중 하나라도 NO면 크롭 좌표를 수정한 후 생성하세요.**

---

## 실패 이력 요약

| 사이클 | 탈락 사유 | 재시도 | 결과 |
|--------|----------|--------|------|
| R12 (4/2) | 크롭 불일치, Ophelia 부적합 | 없음 | 0/3 업로드 |
| R13 (4/2) | 텍스트 없는 갤러리 x2 | 없음 | 1/3 업로드 |
| R14 (4/3) | 크롭 불일치, 풍경화 부적합 | 없음 | 0/3 (폰 문제) |
| R15 (4/3) | 크롭 불일치 (Mona Lisa 미소→가슴) | 없음 | 0/3 |
| R16 (4/4) | 텍스트 없는 갤러리, 어두운 작품 | 없음 | 0/3 |
| R17 (4/4) | 훅 길이 초과, CTA 선언문 | 없음 | 0/3 |
| R18 (4/5) | 크롭 불일치, Wikimedia URL 오류 | 없음 | 0/3 (폰 문제) |
| R19 (4/5) | 훅 길이 초과, CTA 선언문, 크롭 불일치 | 없음 | 0/3 |
| R20 (4/5) | 크롭 좌표 오류(어두운 영역 착지 x2), Ophelia 재사용(규칙 무시) | 없음 | 0/3 |
| R21 (4/5) | playbook_check 누락, 3연속 같은 영역 크롭(규칙 #13), 어두운 slide 6, Ophelia 3번째 시도 | v4 크롭 수정(얼굴 성공) | 0/3 |
| R22 (4/5) | Post B slide 3 크롭 과도 하향(말 다리/돌 기반 착지), Ophelia 잔존 파일 | R21 피드백 반영(slide 4/6 수정 성공) | **1/3 업로드 (Post A 통과!)** |
| R23 (4/6) | Post C slide 5 어두운 드레스 원단만(인물 텍스트와 불일치) — 통과는 했으나 약점 | Post B slide 3 상향 수정 성공, Shalott→Proserpine 교체 | **3/3 업로드 (역대 첫 전원 통과!)** |

| R24 (4/7) | 없음 (R23 동일 콘텐츠 재확인) | 없음 | **3/3 업로드 (R23 재확인)** |
| R25 (4/7) | Lady of Shalott 재사용(R22 탈락 작품, 규칙 #8 신설), Post B 어두운 slide 1, 슬라이드 미생성 | 없음 | 0/3 (작품 교체 필요) |
| R26 (4/7) | Post B(La Belle Dame) slide 1 어두운 숲 나무(요정 안 보임, 규칙 #15), Post A slides 5-6 R23 크롭 미수정(경고) | Post A/C 교체 성공, Post B 크롭 오류 | **2/3 업로드** |
| R27 (4/7) | School of Athens 2개 포스트(A/B) 가로 밀집 프레스코 부적합(규칙 #9-a 신설), Judith Slaying Holofernes(C) 그래픽 폭력 + 어두운 작품(규칙 #9-b 신설). 3개 모두 text-image 불일치 다발 | 없음 | **0/3 (작품 선정 실패)** |
| R28 (4/7) | Artemisia Allegory 인물 뒤 dark wall 50% → 크롭 지뢰 반복(규칙 #9-c 신설), post A/B 둘 다 slide 3 empty void 착지. post C(Vigée Le Brun)는 작품은 좋으나 slide 1/5/6/7 동일 face+hat 크롭 4회 반복(규칙 #14 위반), slide 3 sky crop(규칙 #6 위반), slide 4 text-image 불일치(규칙 #4 위반) | 없음 | **0/3 (Planner 교체 + Generator 크롭 재생성 필요)** |
| R29-retry (4/7 11:30) | Planner가 R29 금지 권고 무시 후 동일 작품 유지(Vigée Le Brun 모녀). Post A slide 1 얼굴 half-cut+dark void, slide 3 Julie 좌표 miss, slide 5=7 동일 풀샷. Post B slide 2 얼굴 half-cut, slide 3 "eyes tight" 의도가 터번 뒤통수 착지(좌표 miss), slide 4 Julie 좌표 miss. 4사이클 연속 "크롭 좌표 시각 검증 없음" 루트 원인(규칙 #16 신설). Post C(Straw Hat)는 slide 6 얼굴 측면 의도가 하늘 착지 except 1군데 결함 | Maya가 크롭만 수정(작품 유지) | **1/3 업로드 (post_084_strawhat_c)** — 3주 만에 첫 업로드 |
| R29 (4/7) | **Planner 허구 서술(규칙 #9-d 신설)**: Vigée Le Brun 모녀 초상을 "붉은 드레스, 밝은 배경"으로 잘못 서술(실제는 흰 드레스+dark brown 배경), Post B의 "red sash/queen's color" 스토리가 존재하지 않는 옷에 기반. Post C 'Straw Hat'의 sash를 red로 서술했으나 실제는 gold. Post A slide 1/3/5/7 네 장이 모두 엄마 얼굴+dark void(규칙 #14 재위반), Post B는 slide 1=slide 7 동일 크롭 + slide 4 subject prominence 실패, Post C slide 3/6 sash 영역 중복 + slide 7 빈 하늘 크롭(규칙 #6 재위반) | 없음 | **0/3 (Planner 작품 서술 허구화 — 5 사이클 연속 저조)** |
| R30 (4/7 12:30) | A/B slide 6 gold cushion 추상 텍스처 크롭 — 텍스트(12년 망명/Robespierre)와 직물 텍스처 시각 매칭 약함(규칙 #17 신설: emotional content는 face/hand로 carry, abstract texture 금지). Post C slide 6은 R28~R29~R30 3사이클 연속 동일 좌표 miss — 한 슬라이드 visual verification 누락 패턴(규칙 #16 강화). C slide 5=7 풀샷 visual repetition. C slide 4 60% sky. | Maya의 R25-retry 4단계 크롭 검증 프로세스(grid+probe+effective-cover-sim+visual verify) 적용 | **3/3 업로드 (3주 만의 전원 통과 회복!)** |
| R31 (4/7 14:00) | **Crop aspect ratio 미준수(규칙 #18 신설)** — 3 포스트 전부 crop이 landscape/square aspect(~0.81~1.85)로 지정됨 → 9:16 렌더 시 letterbox/shrink → 얼굴이 하단 1/3에 위치, 입 잘림, 상단 50% void. Leyster A: 5/7 fail(slide 1/2/3/4/7 전부 dead). Leyster B: 4/7 fail (slide 3 동일 crop 재사용, slide 4 85% dark void=wedding_cana 실패 패턴 재현, slide 7 70% void). Morisot C: slide 2 추상 천 void(playbook wedding_cana slide 2 실패 패턴 정확 재현), slide 4 slide 1과 같은 얼굴 프로필(hand on cheek 약속했으나 손 없음). 4 사이클 연속 크롭 aspect/visual verification 루트 원인 | 없음 | **0/3 (crop aspect 규칙 부재 — 규칙 #18 강제 적용 필요)** |
| R31-retry (4/7 15:30) | 없음 (R31 재시도 — Maya의 v1~v5 PIL probe + y strip 20% 검증 + 7 크롭 재조정으로 규칙 #18 준수 달성). 약점: Leyster A slide 3 핑크 스커트 어두움, Leyster B slide 4 브라스 체어 기둥 50% 어두움(텍스트 재작성으로 rule 17 범주 내), Leyster B slide 7 CTA 텍스트 "painter AND subject" vs 이미지 subject만, Morisot C slide 2 좌측 40% 어두움 — 4개 모두 marginal 통과, reject 수준 아님 | Maya의 crop v5 성공 | **3/3 업로드 (5 사이클 만에 2번째 전원 통과)** — 규칙 #18 효과 입증 |
| R32 (4/7 16:45) | 없음 (R31-retry 동일 콘텐츠 재검증 — Maya가 12:19 및 12:30 v5 재생성, Jin이 21 슬라이드 직접 이미지 검증). 약점 4개는 R31-retry와 동일하게 잔존(Leyster A s3 skirt dark, Leyster B s4 brass post dark + s7 painter AND subject text-image mismatch, Morisot C s2 left-side void). 신규 실패 패턴 없음 — 기존 rule #17/#18로 설명됨. | 없음 | **3/3 업로드 (3 사이클 연속 전원 통과 — R30/R31-retry/R32, 안정화 진입)** |
| R32b (4/7, Jin strict re-QA) | Morisot C 재평가에서 REJECT — S2 좌측 40% dark void(rule #6 wedding_cana 재현), S3 가우즈 뒤 아기 low-contrast invisible (subject prominence rule #4), S4 "look at Edma" 텍스트인데 Edma 얼굴이 상단 크롭 밖(subject prominence rule #4). 3개 실패 모두 기존 rule로 설명 — **신규 패턴 없음**. Leyster A/B는 변화 없이 유지(A 7/7, B 6/7 S4 dark flagged but pass). Morisot Cradle 작품 자체가 muted impressionist로 '밝은 saturated crop' 플레이북 패턴에 부적합 — 다음 사이클 C 작품 교체 권고 (Mary Cassatt The Child's Bath 등). | 없음 | **2/3 업로드 (A, B 통과 / C 탈락)** |
| R33 (4/8 03:30) | Post B(Bronzino) S1 크롭 [0.68,0.22,0.87,0.48]이 Deceit 소녀 대신 **Folly 아이를 주 피사체로 잡음** — 인접 인물 혼선(rule #4+#16 재위반). 4/7 크롭이 Post A와 동일 좌표(규칙 #20 위반). Post C(Crivelli) S1 훅 "God burned a hole"인데 이미지에 **빛줄기/구멍 없음** — 갈색 수도승이 프레임 지배(rule #4+#16 재위반). S4 "peacock" 텍스트인데 공작새 없음(rule #4). | B/C 크롭 재조정 필요 | **1/3 업로드 (Post A 통과, B/C 탈락)** |
| R34 (4/8 09:25) | post_088 사이클. **크롭 좌표 vs 렌더 결과 광범위한 불일치(rule #16 4사이클 재발)** — ermine_a S1/S5, ermine_b S6 모두 plan은 face/eyes 의도였으나 실제 렌더는 어두운 베일/목걸이/어깨에 착지(eyes 전무). Generator의 크롭 파이프라인 자체 의심(y좌표 또는 aspect 보정 버그). **3연속 동일 영역 크롭(rule #14 재위반)** — ermine_a S2/5/6/7 모두 동일 목걸이/가슴, ermine_b S2/4/5 동일, unicorn_c S2/4/5 동일. **post_088_unicorn_c는 painting not viable(rule #21 신설)** — Lady with a Unicorn은 hook이 약속한 unicorn/dog/saint가 표면에 없음(덧칠 아래만 존재). 추가로 저해상도(rule #22 신설). | 없음 | **0/3 (전원 탈락 — Generator crop pipeline + Planner 작품 선정 동시 실패)** |
| R34-retry (4/8 10:15) | Maya가 aspect 공식 반전 발견(0.5625×W/H → 0.5625×H/W) 및 21 크롭 재계산. unicorn_c → arnolfini_c 교체. **ermine_a 통과 (5/7 marginal)** — slide 1 face crop은 R34 fail 대비 개선되어 forehead+headband+single eye 가시. 그러나 **ermine_b S6 동일 실패 재발** — "animal's eye and Cecilia's eye" 텍스트인데 실제 crop은 95% 검은 void, 두 눈 모두 안 보임. 즉 aspect 공식 수정만으로는 부족하고 y좌표 자체가 측정 없이 추측됨(눈은 y≈0.18-0.22인데 plan은 0.07-0.29). **arnolfini_c 4/7 fail** — S1 훅("ghost in this mirror")인데 거울 안 보이고 신부 얼굴만, S3 "raised hand" 텍스트인데 손 없음, S4 강아지 너무 작고 어두움, S7 CTA에서 손은 상단 1/4 밖에 없고 빨간 쿠션이 화면 지배. **5사이클 연속 rule #16 root cause** — Generator/Planner가 매 슬라이드의 crop_N.jpg를 직접 열어 텍스트 명사가 30%+ 차지하는지 검증하지 않음. | 없음 | **1/3 업로드 (ermine_a만 통과)** |
| R34-retry-3 (4/8 10:18) | post_088 v3. Maya가 좌표 측정+직접 검증(PIL probe). **6사이클 연속 실패 cycle 종료** — 3/3 ALL PASS. ermine_a 6/7 (S1 좌측 dark void 40% 마진), ermine_b 6/7 (S6 6사이클 만에 통과 — Cecilia+ermine 둘 다 visible. S7 ermine 영역 좌표 오프셋 — 진주 목걸이 dominant), arnolfini_c 5/7 (S1 거울 dominant 통과! S3 hand 10% 미만, S5가 S1과 동일 crop — rule #23 신설). 신규 rule 1개 — answer 슬라이드 = hook 슬라이드 시각 중복 금지 | 없음 | **3/3 업로드 (3/3 전원 통과 — 7사이클 만의 회복)** |
| R34-retry-2 (4/8 11:00) | post_088 v2 재시도. **ermine_a 이번엔 REJECT** — Jin 엄격 재평가에서 S1 [0.284,0.0,0.556,0.36]이 forehead/headband/half-eye만 잡음(face가 아닌 hairline). 이전 사이클 marginal pass가 사실은 fail이었음. **ermine_b 통과 (5/7)** — S1 ermine 머리 강하게 dominant(역대 최고 ermine_b S1), S2~S5 모두 작동. 단 S6는 또 ermine 없이 face only(rule #4 재발 + S5와 거의 동일), S7는 ermine eye 의도였으나 chest/necklace 착지. **arnolfini_c REJECT** — S1 거울 25%만 차지(50% 약속), S3 raised hand가 좌하단 5% 코너 detail(rule #4 prominence), S4 dark void with barely-visible dog(playbook 어두운 중간 슬라이드 kill 패턴). 신규 패턴 없음 — 모두 기존 rule #4/#16/#17/playbook 어두운 중간으로 설명. **6사이클 연속 동일 root cause** — Generator가 좌표를 측정 없이 추측, crop_N.jpg 시각 검증 누락. ermine_b 1개만 ermine 자체가 frame에 잡힌 결정적 차이. | 없음 | **1/3 업로드 (ermine_b만 통과)** |
| R34-retry-4 (4/8 10:35) | post_088 v4 (Maya 10:26 regen — 좌표 유지). 3/3 ALL PASS. **ermine_a 7/7 첫 perfect score** (S1 face features 모두, S6 ermine visible). ermine_b 6/7 — **S7 ermine 부재 mismatch (이전 사이클 권고 미수정, 규칙 #24 신설)**. arnolfini_c 5/7 임계값 정확 — S4 강아지 dark void 재현(playbook #1 경고), S5 답 공개 슬라이드인데 거울 안 figures 5% 미만(rule #4). 신규 패턴 1개: **이전 QA 사이클의 마이너 권고를 다음 사이클이 무시 → 동일 fail 재발**. Maya가 plan.json만 업데이트하고 Jin의 마이너 issue 좌표를 반영하지 않음. | 없음 | **3/3 업로드** |
| R24 post_089 (4/9 03:30) | apollo_a S6 **박물관 천장 크롭**(rule #6+#16 재발) — 텍스트 "손가락이 월계수 잎" 클라이맥스인데 이미지가 갤러리아 보르게세 천장 장식 85%. S5도 수피 변환 안 보임(매끈한 다리). milkmaid_c S1 **빈 벽면 크롭**(rule #6+#16 재발) — 훅 "앞치마가 금보다 비쌌다"인데 프레임 80%가 회벽. 크롭 y-range가 인물 위 벽면에 착지. S6도 동일 벽면 문제. apollo_b(6/7) S3 다리/수피 텍스트인데 팔+빈 공간(rule #4). 신규 패턴 없음 — 전부 기존 rule #4/#6/#16. 조각상 사진에서 박물관 배경에 착지하는 변형 + 인물 위 빈 벽면에 착지하는 변형 | 없음 | **1/3 업로드 (apollo_b만 통과)** |
| R25 post_090 (4/10 03:15) | lilies_a(4/7): S4 "왕관" 텍스트인데 80% 빈 빨간 배경(rule #4), S6 어두운 다리 실루엣(rule #6), 스토리가 교육형 팩트 나열(playbook '인물 도감' 패턴). liberty_c(2/7): S1 얼굴 없이 깃발+하늘만(rule #15), S3 "소년" 텍스트인데 리버티 드레스 천(rule #4), S4 70% 연기/하늘(rule #6), S5 "모자 남자" 텍스트인데 리버티 상체(rule #4). 가로 밀집 구성 그림에서 9:16 크롭 한계. 신규 패턴 없음 — 전부 기존 rule #4/#6/#15 | 없음 | **1/3 업로드 (lilies_b만 통과)** |
| R27 post_091 (4/10) | **0/3 전원 탈락 — rule #16 6+사이클 재발**. pearl_a(3/7): playbook auto-reject(hook_avoids_failed=false, answer_reveal=3) + S3 진주 극단 zoom miss(craquelure 볼 착지) + S5 "no string to ear"인데 귀 없음 + S1=S6=S7 bust 3연속 반복. pearl_b(4/7): S3 동일 진주 zoom miss(A와 병렬 실패) + S5 귀 없음 + S6 S1과 중복. meninas_c(1/7, 재앙): Las Meninas 상단 45% 어두운 천장 void — Planner가 figure y-range를 y≈0.2~0.8로 가정했으나 실제 y≈0.55~0.95. S1~S6 6장이 모두 dark void 착지(S5/S6는 ~100% 검정). 신규 rule #26(실내 건축 ceiling void 트랩) + rule #27(extreme zoom pixel-measure 의무) 추가 | 없음 | **0/3 (pearl pixel 미측정 + Meninas y-shift 측정 미완)** |
| R27-retry post_091 (4/10 08:08) | pearl_a REJECT (6/7): plan.json playbook_check 미갱신(hook_avoids_failed=false, answer_reveal_slide=3 잔존 → Jin auto-reject 발동) + S7 text dim(dark bg low-contrast) + S1↔S7 full bust 시각 redundancy. pearl_b PASS (7/7): pearl pixel 재측정 + S3 진주 극단 zoom 성공 + S5 ear zone 진주 가시 + 7장 모두 distinct. milkmaid_c REJECT (6/7): S1~S6 **전부 solid** (Cupid tile reveal 성공, foot warmer 성공) — 그러나 **slide_7.png에 CTA 텍스트 오버레이 완전 누락**(renderer silent failure). text_existence.text_visible_on_image INSTANT REJECT. 신규 rule #28(렌더 후 텍스트 probe 필수) + rule #29(retry 시 plan.json playbook_check 갱신 의무) 추가 | 없음 | **1/3 업로드 (pearl_b만 통과)** |
| R27-retry-2 post_091 (4/10 08:40) | **rule #29 재발 (3번째)**. pearl_a REJECT (5/7): 또다시 plan.json playbook_check 미갱신(hook_avoids_failed=false, answer_reveal_slide=3 잔존) — Maya가 텍스트만 재생성하고 plan 동기화를 연속 스킵. 추가로 S5 [0.55,0.37,0.72,0.625] 이 shadow-side neck에 착지해 ~85% 검은 void + S4와 연속 dark tunnel (085_leyster_a 패턴 재현). S3 pearl zoom은 pearl_b S3와 near-identical(multi-post shared crop 재발, rule #20). pearl_b PASS (6/7): S1 hook "Look at the pearl" proven 패턴, S2~S4/S6/S7 전부 distinct. S5만 dark(flag, reject 아님). milkmaid_c REJECT (6/7): **rule #29 변종 재발** — Planner는 Las Meninas(meninas_c)를 plan.json에 기재했으나 Produce가 The Milkmaid로 **작품 전체를 무단 교체**. plan.json에 milkmaid_c 엔트리 부재 → playbook_check 자체 존재 안 함. 콘텐츠 품질은 6/7(S1 iconic milkmaid 최상급, Cupid tile reveal 작동)인데 프로세스 위반만으로 REJECT. S7 CTA 텍스트는 R27-retry 대비 정상 렌더됨(rule #28 효과). 신규 패턴 없음 — 전부 rule #29/#20/dark tunnel 재발 | 없음 | **1/3 업로드 (pearl_b만 통과)** |
| R27-retry-4 post_091 (4/10 10:15) | 없음 — 3/3 ALL PASS. 3포스트 전부 6/7. 공통 약점: S6 어둠 (pearl_a/b 터번 배경 40-55% dark, milkmaid_c 풋워머 70% dark). 작품 구조적 한계(Girl with Pearl Earring 어두운 배경, Milkmaid 어두운 코너)로 reject 수준 아님. pearl_a hook↔S6 reveal 정보 중복 flag. 신규 실패 패턴 없음 — 모두 기존 rule #19(어두운 터널)로 설명. | Maya의 PIL pixel-measure + root plan.json 정상 동기화 | **3/3 업로드 (R27 0/3 → 5사이클 만에 전원 통과)** |
| R25 post_090 성과 (4/10 +24h) | **lilies_b 51뷰 실패 (rule #15-b 신규 검증)** — QA는 통과했으나 S1이 프레스코 벽토 배경 파편에 착지(prince 0%). 훅 "ancient prince" 주어와 S1 이미지 불일치로 views 51 (같은 사이클 apollo_b 954의 1/18.7). QA 크롭 체크리스트에 "밝기"만 있고 "훅 주어 존재" 항목이 없어 통과되었으나 성과에서 치명. **rule #15를 #15-b로 확장 (훅 명사 주어 = S1 시각 존재 의무)** | 없음 | 성과 0/1 |
| R27 post_092 (4/11 03:45) | swing_a S1/S3 **나무 캐노피에 착지**(rule #16 재발) — 여자/그네 S1에 전무, "신발" S3에 전무. swing_b 동일 패턴(S1 "Look at her shoe"인데 shoe 0%, S3 극단 줌도 나뭇가지, S7 dark void). **The Swing은 캔버스 70-80%가 어두운 정원 나뭇잎 — 작은 밝은 주체(핑크 드레스 여자)를 PIL 없이 추정하면 나무에 착지**. plan.json crop NOTE에 "needs pixel-measurement" 기재했으나 미이행. adele_c(Klimt 금색)는 7/7 통과 — 밝은 금색 초상 작품은 크롭 실패율 극저. 신규 패턴 없음 — 전부 기존 rule #16. | 없음 | **1/3 업로드 (adele_c만 통과)** |
| R28 post_093 (4/12 03:45) | **type="wide" 슬라이드 텍스트 미렌더 (rule #28 재발, 확대)** — mona_a S7, mona_b S2+S7, napoleon_c S2+S7 = 5개 슬라이드에 텍스트 오버레이 완전 누락. S7 mandatory fail로 3포스트 전부 자동 탈락. R27-retry의 S7 단건 누락이 이번엔 type="wide" 전체로 확산. 추가: mona_a 훅 "Normal Painting?" = playbook 실패 패턴 "2단어 모호 질문"(rule #12 위반, 088_ermine_a 동일). mona_a 4/7 face-centric 시각 반복(playbook 초상화 반복). napoleon_c S4 "soldiers/cannons" 텍스트인데 말 배 착지(rule #4). 캡션 제네릭 템플릿 재사용(rule #30 신설). | 없음 | **0/3 (렌더러 수정 + mona_a 훅 교체 필요)** |

**R23~R24: 2연속 3/3 전원 통과!** 10+ 사이클 반복 피드백이 결실. 크롭 정밀도 + 부적합 작품 교체가 핵심.

### 성과 분석 (wedding_cana A/B/C, 2026-04-07)
| 포스트 | 훅 유형 | Score | Avg Slides | 공통 문제 |
|--------|---------|-------|------------|-----------|
| wedding_cana_a | 구체적 도발 | 16.7 | 2.72 | slide 2 하늘 크롭 |
| wedding_cana_b | 추상적("STOLEN FEAST") | 13.9 | 2.02 | slide 2 하늘 + 추상 훅 |
| wedding_cana_c | 공감형 FOMO | **19.7** | 2.69 | slide 2 하늘 크롭 |
- **핵심 교훈**: FYP 91~94%로 분배 우수하나 slide 2 하늘 크롭으로 전부 리텐션 실패. 추상 훅(B)은 구체적 훅(C) 대비 score 30% 하락.
