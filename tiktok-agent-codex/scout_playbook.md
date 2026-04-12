# Scout Playbook

매 라운드 시작 시 이 문서를 읽고, 라운드 종료 시 배운 것을 추가한다.
이 문서는 라운드를 거듭하며 성장하는 운영 노하우의 축적이다.

---

## Navigation Know-how

### 검색 진입
- TikTok 홈(FYP)에서 **뒤로가기(keyevent 4)**를 누르면 이전 검색 결과 페이지로 돌아감 (R1에서 발견)
- 검색창 위치: 상단 중앙 **(454, 155)** — 탭하면 편집 모드
- 검색어 입력: `adb_text()` 사용. 기존 검색어 삭제는 `keyevent 123`(MOVE_END) + `keyevent 67`(DEL) x40회
- ⚠️ **R7 발견**: open_tiktok() 후 네이버 지도가 열릴 수 있음. FYP에서 검색 진입은 **하단 "검색 · ..." 바 (약 404, 2015)** 탭이 가장 확실. 검색 결과 페이지로 바로 이동됨. 여기서 검색창(454,155) 탭 → 검색어 입력.
- **"사진" 탭 필터 위치: (246, 275)** — 검색 결과에서 탭 바의 2번째 ⚠️ R2에서 수정: 탭 순서가 R1 기록과 다름
- 탭 바 전체 (R2 UI dump 기준): 상위(110,275) | **사진(246,275)** | 동영상(391,275) | 사용자(545,275) | 사운드(699,275) | 라이브(853,275)
- ⚠️ 탭 순서가 검색어/상황에 따라 바뀔 수 있음. UI dump로 확인하는 것이 안전
- R4 확인: "사진" 탭이 (400, 275)에 위치 — 이전 (246, 275)에서 이동. **반드시 UI dump에서 el['text']=='사진' 찾아서 bounds.cx, bounds.cy로 탭**

### 포스트 열기 — 그리드 모드
- 사진 탭의 검색 결과는 **2열 Masonry 그리드** 형태
- 왼쪽 열 중심 x ≈ 225, 오른쪽 열 중심 x ≈ 680
- 첫 번째 행 y ≈ 450 (탭 바 아래)
- 탭 후 로딩 대기: **2.5초** 필요
- 포스트 열림 확인: `qmd` resource_id (좋아요 버튼) 존재 여부로 판단
- ⚠️ **문제점**: 그리드 셀 좌표가 정확하지 않아 같은 포스트를 반복 탭하는 경우 다수 발생 (R1에서 23회 탭 → 10개 유니크). R2에서 개선 필요: UI dump에서 그리드 셀 bounds를 직접 읽어서 좌표 결정하거나, 이미 본 caption 추적하여 중복 스킵

### 포스트 열기 — 피드(캐러셀) 모드
- 키워드 변경 후 사진 탭에서 스크롤하면 **피드 모드**로 전환될 수 있음 (그리드가 아닌 전체화면 슬라이드뷰)
- 피드 모드에서는 engagement 버튼의 resource_id가 다름 → account/likes 수집 실패
- ⚠️ R2에서 피드 모드 감지 및 데이터 수집 로직 보완 필요

### 슬라이드 넘기기
- 좌로 스와이프: `adb_swipe(700, 1000, 200, 1000, 300)` — 작동 확인 (R1 후반부에서 slide_count=2 감지됨)
- 슬라이드 인디케이터: `N/M` 형태 텍스트를 elements에서 검색 (예: "2/5")
- ⚠️ **문제점**: 대부분 slide_count=1로 기록됨. 가능한 원인:
  - 실제로 1장짜리 포스트가 많았을 수 있음
  - 스와이프가 동작했지만 인디케이터 감지 실패
  - R2에서 스크린샷으로 확인하는 방법 고려

### 뒤로가기
- `adb_keyevent(4)` (BACK) — 포스트에서 그리드로 복귀
- 뒤로간 후 **그리드 위치가 리셋되는 경향** 있음 → 같은 포스트를 다시 탭하게 됨
- 대기시간: 1.5초

---

## Search Strategy

### Keyword Experiments
| Keyword | Round | Unique Hits | Tried | Hit Rate | Top Likes | Notes |
|---------|-------|-------------|-------|----------|-----------|-------|
| famous painting facts | R1 | 9 | 18 | 100% (art match) | 30.8만 (Van Gogh Gallery) | 매우 효율적. 거의 모든 결과가 고전미술. 중복이 문제. |
| classical art explained | R1 | 1+ | 5 | 100% | ? | 키워드 변경 후 피드 모드로 전환됨. 데이터 수집 불완전. |
| famous painting facts | R2 | 4 | 10 | 40% (R1 중복 제외) | N/A (engagement 수집 실패) | R1 결과와 대부분 중복. 새 포스트 발견율 낮음. 2회차부터는 다른 키워드가 효율적. |
| hidden meaning art | R2 | 1 | 5 | 20% | N/A | 그리드 셀 탐지 실패 다수. 1개 발견했으나 20슬라이드짜리 양질 콘텐츠. |
| art history secrets | R2 | 2 | 4 | 50% | N/A | 가장 높은 히트율. van Eyck, 미스터리 초상화 등 양질 콘텐츠. R3에서 우선 사용 추천. |
| art history secrets | R3 | 3 | 6 | 50% | 31.6만 (The Culturist) | R2와 동일 히트율. 그리드 중복 탭으로 실제 유니크는 3개. 31.6만 likes 포스트 발견. |
| renaissance art | R3 | 2 | 4 | 50% | ? (engagement 미수집) | 좋은 히트율. 르네상스 관련 양질 콘텐츠. |
| masterpiece analysis | R3 | 2 | 4 | 50% | ? (engagement 미수집) | 미술 분석/비평 관련 콘텐츠. 히트율 양호. |
| 명화 해설 | R3 | 2 | 3 | 67% | ? (engagement 미수집) | 한국어 키워드 최고 히트율! 영어 결과도 섞여 나옴. R4에서 우선 사용 추천. |
| 명화 해설 | R4 | 2 | 4 | 50% | 42.1만 (Yapper) | TikTok이 "Art lore"로 자동 번역. 판타지/OC 콘텐츠도 섞임. 순수 고전미술 히트율 낮아짐. |
| 명화 숨은 의미 | R4 | 2 | 5 | 40% | 363 (Tacit Art) | "art consulting" 관련 결과. 고전미술 직접 히트 낮음. 기대 이하. |
| painting hidden details | R4 | 2 | 4 | 50% | 14.7만 (historybyart) | 양질 콘텐츠! Arnolfini Portrait 해석 등. "hidden meaning art"보다 효율적. |
| baroque painting | R4 | 2 | 4 | 50% | 1,559 (Arthub.guy) | Rembrandt 등 바로크 화가 콘텐츠. 시대별 키워드 유효. |
| #classicalart | R4 | 2 | 4 | 50% | 169 (the_inspire_gallery) | 해시태그 검색 효과적. Leopold Schmutzler, Dutch painter 등 양질. |
| old master painting | R4 | 2 | 4 | 50% | 29.4만 (Lynx) | "artists until you find ones you love" 시리즈. Fragonard 등 고전화가. |
| art history explained | R5 | 2 | 4 | 50% | 23만 (history boy) | "art history secrets" 변형. 양질 콘텐츠 (Arnolfini Portrait 등). |
| museum masterpiece | R5 | 2 | 4 | 50% | 124 (Japan Wonder Travel) | Row 1에서 비고전미술(현대 전시) 혼입. Row 2에서 히트. |
| impressionist painting | R5 | 4 | 4 | 100% | 20.6만 (soo_art_decoo) | ★최고 히트율! 모든 4개 유니크. Monet, 인상파 콘텐츠 풍부. R6 우선 사용. |
| famous portrait history | R5 | 4 | 4 | 100% | 4,535 (emalamenart) | ★100% 히트율. 초상화 특화. Mona Lisa, 역사적 초상화 등. |
| Dutch Golden Age art | R5 | 4 | 4 | 100% | 6.2만 (pocket.art.gallery) | ★100% 히트율. Vermeer, Dutch masters 콘텐츠. 시대별 키워드 매우 유효. |
| pre raphaelite art | R6 | 4 | 4 | 100% | 1.8만 (pocket.art.gallery) | ★100% 히트율! Waterhouse, Hughes 등 라파엘 전파 콘텐츠 풍부. |
| Caravaggio painting | R6 | 4 | 4 | 100% | 1.3만 (sich) | ★100% 히트율! 화가명 키워드 매우 유효. Magritte도 혼입되나 전부 고전미술. |
| Vermeer painting | R6 | 3 | 4 | 75% | 2,234 (GALERIA DE ARTE) | R5 "Dutch Golden Age art" 결과와 1개 중복. Girl with a Pearl Earring 등. |
| art mystery solved | R6 | 4 | 4 | 100% | 13만 (PhilosoForum) | ★100% 히트율! 13만 likes 대형 포스트. Art Newspaper도 발견. |
| famous sculpture history | R6 | 4 | 4 | 100% | 181 (the dictionary of culture) | ★100% 히트율! 조각 특화 콘텐츠. art_atlas 크리에이터 2개. |
| 미술사 | R6 | 2 | 2 | 100% | 178 (The Velvet Edit) | 시간 제한으로 Row 1만 시도. Wall Art 스타일링 등 혼합 결과. |
| Botticelli painting | R7 | 3 | 4 | 75% | 2,969 (Rebecca) | Primavera/Venus 관련 콘텐츠 풍부. R2R에서 R4 중복 1개. |
| Michelangelo art | R7 | 4 | 4 | 100% | 72.2만 (🧿) | ★100% 히트! 72.2만 likes 대형 포스트. Sistine Chapel, David 등. |
| rococo painting | R7 | 3 | 4 | 75% | 7.1만 (elle) | Marie Antoinette/로코코 패션. R2L에서 R4 중복 1개. |
| symbolism art history | R7 | 3 | 4 | 75% | 1,434 (Ysn) | Alphonse Osbert, Odilon Redon 등 상징주의 화가 발견. Ysn 크리에이터 양질. |
| gothic art medieval | R7 | 3 | 4 | 75% | 4,857 (Drivel) | gothic/medieval 판타지 아트 혼입. 순수 고전미술보다 판타지 비중 높음. |
| art restoration before after | R7 | 2 | 2 | 100% | 6,377 (História Academia) | 건축/복원 콘텐츠. 시간 부족으로 Row 1만 시도. |
| Raphael painting | R8 | 4 | 4 | 100% | 53 (HistorywithBethan) | ★100% 히트! oysterlab 3개 + HistorywithBethan 1개. School of Athens 등. |
| Titian painting | R8 | 0 | 4 | 0% | — | ❌ 완전 실패. 포스트 열기 실패 또는 네비게이션 오류. R9에서 재시도 불요. |
| El Greco art | R8 | 4 | 4 | 100% | 65 (PeinseurYT) | ★100% 히트! 매너리즘 화가 콘텐츠. PeinseurYT, 5G Kills 등. |
| Hieronymus Bosch | R8 | 4 | 4 | 100% | 9,903 (Imshpo) | ★100% 히트! Bosch 전문 콘텐츠 풍부. Imshpo 9.9K, pocket.art.gallery 재등장. |
| neoclassical painting | R8 | 4 | 4 | 100% | 1,659 (_puroarte1) | ★100% 히트! 신고전주의 시대별 키워드 유효. |
| art heist history | R8 | 4 | 4 | 100% | 900 (caro) | ★100% 히트! 미술 도난 역사 콘텐츠. Art History for Dummies 크리에이터 발견. |
| Monet water lilies | R8 | 4 | 4 | 100% | 5.9만 (unknown) | ★100% 히트! 5.9만 likes 대형 포스트. emalamenart 재등장(5,530). 화가명+작품명 조합 유효. |
| Rembrandt painting | R9 | 4 | 4 | 100% | 169 (01001010) | ★100% 히트! Night Watch 해설, watercolor drawing 등. 다양한 콘텐츠. |
| Da Vinci secrets | R9 | 3 | 4 | 75% | 59.6만 (Ariel Omar) | ★★ 59.6만 likes 대형 포스트! Note from Jesus 21.9만도. mystery 키워드 engagement 최고. R5 중복 1개. |
| Klimt painting | R9 | 3 | 4 | 75% | 2.1만 (art from bobby) | Golden Period 콘텐츠. art.studio.daily 자연 테마 Klimt. R5 중복 1개. |
| Goya painting | R9 | 4 | 4 | 100% | 7,167 (pocket.art.gallery) | ★100% 히트! Black Paintings 시리즈. pocket.art.gallery + Ysn 크리에이터 재등장. |
| Manet painting | R9 | 0 | 4 | 0% | — | ❌ 완전 실패. R8 "Titian painting"과 동일 현상. 포스트 열기 불가. |
| ancient Greek art | R9 | 2 | 2 | 100% | 1,481 (Time Traveler Tales) | 100% 히트! 고대 미술 키워드 유효. 시간 부족으로 Row 1만 시도. |
| Velazquez painting | R10 | 0 | 2 | 0% | — | ❌ 포스트 열기 실패. Row 1에서 2회 시도 모두 실패. 사진 탭 진입 후 그리드 좌표 불일치 추정. |
| Degas ballerina | R10 | 3 | 4 | 75% | 641 (Jingus Bingus) | 화가명+주제 조합 유효. geniodoro 2개(16, 10 likes). Jingus Bingus 365일 시리즈. |
| art forgery history | R10 | 0 | 2 | 0% | — | ❌ 포스트 열기 실패. R8 "art heist history" 100% 성공과 대조적. 사진 탭 결과가 적을 수 있음. |
| Turner painting | R10 | 4 | 4 | 100% | 1,133 (Ysn) | ★100% 히트! Ysn 5번째 등장. art.studio.daily, Ty, Fragments of Art. 풍경화 키워드 안정적. |
| Frida Kahlo art | R10 | 0 | 2 | 0% | — | ❌ 포스트 열기 실패. 현대 아트/일러스트 결과가 많아 사진 탭 그리드 구조 다를 수 있음. |
| medieval manuscript art | R10 | 0 | 2 | 0% | — | ❌ 포스트 열기 실패. 3단어 키워드 + 니치 주제로 사진 탭 결과 부족 추정. |
| Cézanne painting | R10 | 0 | 2 | 0% | — | ❌ 포스트 열기 실패. 특수문자(é) 입력 문제 가능성. R11에서 "Cezanne painting" 시도. |
| Caravaggio painting | R11 | 2 | 4 | 50% | 10.4만 (adelina_) | R6(100%)보다 히트율 하락. 2개 R6 중복. 10.4만 likes 콜라주 포스트 발견. |
| Rubens painting | R11 | 4 | 4 | 100% | 2,911 (the light she brings) | ★100% 히트! 신규 키워드 대성공. Judith, Head of Medusa 등 바로크 작품. |
| Van Eyck painting | R11 | 4 | 4 | 100% | 139.2만 (PhilosoForum) | ★★★ 100% 히트 + 139.2만 likes 초대형 포스트! proven 리스트 추가. |
| ancient Roman art | R11 | 4 | 4 | 100% | 1.2만 (PhilosoForum) | ★100% 히트! 고대 로마 미술 키워드 안정적. R9 Greek과 동일 패턴. |
| impressionist painting | R11 | 0 | 0 | 0% | — | ❌ 사진 탭 미발견. R5에서 100% 성공했던 키워드. 검색 결과 로딩 타이밍 문제 추정. |
| Delacroix painting | R11 | 0 | 0 | 0% | — | ❌ 사진 탭 미발견. 신규 키워드 테스트 실패. 재시도 필요. |
| Picasso cubism | R11 | 0 | 0 | 0% | — | ❌ 사진 탭 미발견. 신규 키워드 테스트 실패. 재시도 필요. |
| Rembrandt painting | R12 | 3 | 4 | 75% | 3,342 (OrchadéLiSa) | R9 이후 3라운드 간격. 양호. anatomy lesson 포스트 발견. |
| Goya painting | R12 | 1 | 4 | 25% | 150 (Lamp) | R9 대비 히트율 하락. 대부분 중복. 3라운드 간격도 부족. |
| pre raphaelite art | R12 | 2 | 4 | 50% | 6만 (Footnotes in Time) | R6 이후 6라운드 간격. 6만 likes 포스트! 중복 2개. |
| art mystery solved | R12 | 0 | 4 | 0% | — | R6 이후 6라운드 간격인데도 전부 중복. 인기 키워드라 결과 고정 추정. |
| Botticelli painting | R12 | 3 | 4 | 75% | 63 (Rebecca) | R7 이후 5라운드 간격. Birth of Venus 등 양질. |
| Cezanne painting | R12 | 3 | 4 | 75% | 111 (BrushAndTaless) | ✅ ASCII 재시도 성공! R10 특수문자 실패 해결. proven 리스트 추가. |
| Hokusai wave | R12 | 4 | 4 | 100% | 257 (JPNstudio) | ★100% 히트! 동양미술 신규 키워드 대성공. proven 리스트 추가. |
| Vermeer painting | R13 | 1 | 4 | 25% | 58 (@White_Heart_Stone) | R6 이후 7라운드 간격. 대부분 중복. 간격 더 필요. |
| Waterhouse painting | R13 | 2 | 4 | 50% | 2,631 (pocket.art.gallery) | 신규 키워드. Pre-Raphaelite 화가명 유효. blueberry timmy(5428)도 발견됨 중복 제외. |
| Hiroshige ukiyo-e | R13 | 4 | 4 | 100% | 141 (Jingus Bingus) | ★100% 히트! 동양미술 확장 성공. proven 리스트 추가. |
| Dutch Golden Age art | R13 | 2 | 4 | 50% | 371 (the_inspire_gallery) | R5 이후 8라운드 간격. 중복 2개. 히트율 하락. |
| Rodin sculpture | R13 | 4 | 4 | 100% | 495 ($) | ★100% 히트! 조각 키워드 대성공. proven 리스트 추가. |
| Michelangelo art | R13 | 1 | 4 | 25% | 30 (sach_hoi_cu) | R7 이후 6라운드 간격. 대부분 중복. 간격 더 필요. |
| Renaissance Madonna | R13 | 4 | 4 | 100% | 9,875 (Julia) | ★★ 100% 히트 + 9.9K likes! 종교화+모티프 키워드 대성공. proven 리스트 추가. |
| Bernini sculpture | R14 | 4 | 4 | 100% | 221 (Manahil K.) | ★100% 히트! 22장 갤러리 포스트. 건축+조각 믹스. proven 리스트 추가. |
| Delacroix painting | R14 | 0 | 0 | 0% | — | ❌ 3회 연속 사진탭 미발견. failed 리스트 추가. |
| Chinese landscape painting | R14 | 4 | 4 | 100% | 9 (happyendin...) | ★100% 히트! 동양미술 3단어 키워드 성공. Qi Baishi $140.8M 포스트 발견. proven 추가. |
| baroque still life | R14 | 4 | 4 | 100% | 555 (interiordispl) | ★100% 히트이나 인테리어 디자인 콘텐츠 혼입 많음. 고전미술 순도 낮음. |
| Klimt painting | R14 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. R9+R14 2회 실패. failed 리스트 추가. |
| Morisot painting | R14 | 3 | 4 | 75% | 1,641 (the_inspire_gallery, Phase 2 발견) | ★★ 1,641 likes 대형 포스트! proven 리스트 추가. |
| Giotto fresco | R14 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. 니치 키워드 실패. |
| ancient Egyptian art | R15 | 4 | 4 | 100% | 5,752 (Sarah) | ★100% 히트! 파윰 초상화 5.7K + 무덤 벽화 277. proven 리스트 추가. |
| Mary Cassatt painting | R15 | 0 | 4 | 0% | — | ❌ 전부 비미술 콘텐츠 (한국어 텍스트/영어 학습). failed 리스트 추가. |
| vanitas painting | R15 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. |
| famous portrait history | R15 | 2 | 4 | 50% | 6,819 (ForTimeTra...) | R5 이후 10라운드 간격. 6.8K likes 대형 포스트! 중복 2개. |
| art heist history | R15 | 1 | 4 | 25% | 0 | R8 이후 7라운드 간격. 스토리/일러스트 혼입 심각. 순도 하락. |
| Da Vinci secrets | R15 | 0 | 4 | 0% | — | R9 이후 6라운드 간격. AI 인테리어 디자인 콘텐츠로 오염. |
| impressionist painting | R15 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. R11에 이어 2회 연속 실패. failed 리스트 추가. |
| Fayum portrait | R16 | 4 | 4 | 100% | 1,029 (azizam) | ★100% 히트! 신규 키워드 대성공. R15 파윰 초상화 5.7K에 이어 검증. proven 추가. |
| Bernini sculpture | R16 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. R14 100% 성공에서 2라운드 후 실패. |
| Morisot painting | R16 | 2 | 4 | 50% | 90 (art history) | R14 이후 2라운드 간격. 중복 2개. 양호. |
| Chinese landscape painting | R16 | 4 | 4 | 100% | 145 (diary pena) | ★100% 히트이지만 인니어/자바 콘텐츠 혼입. 고전미술 순도 낮음. |
| Tiepolo ceiling | R16 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. 신규 키워드 실패. |
| Caravaggio chiaroscuro | R16 | 4 | 4 | 100% | 150 (art.studio.daily) | ★100% 히트! 화가명+기법 조합 유효. proven 추가. |
| Rodin sculpture | R16 | 0 | 0 | 0% | — | ❌ 사진탭 미발견. R13 100% 성공에서 3라운드 후 실패. |
| Egyptian tomb painting | R17 | 4 | 4 | 100% | 278 (HimHerandMore) | ★100% 히트! 신규 키워드. R15 "ancient Egyptian art" 변형. proven 추가. |
| Vermeer girl pearl earring | R17 | 3 | 4 | 75% | unknown (art history) | 화가명+작품명 조합 유효. 1개 R6/R13 중복. proven 추가. |
| Rembrandt night watch | R17 | 0 | 0 | 0% | — | ❌ 포스트 열기 실패 2회. failed 리스트 추가. |
| Rodin sculpture | R17 | 2 | 4 | 50% | low (Wylie Graham) | R16 사진탭 실패 → R17 부활. 2개 중복. |
| Raphael school athens | R17 | 4 | 4 | 100% | 4,967 (vi_art_journey) | ★100% 히트! 화가명+작품명 대성공. proven 추가. |
| historical portrait colorized | R17 | 4 | 4 | 100% | 1.2만 (evelyn maude) | ★★ 100% 히트 + 1.2만 likes! Share Rate 23%. proven 추가. |
| Mantegna fresco | R17 | 0 | 0 | 0% | — | ❌ 포스트 열기 실패. Giotto fresco(R14)와 동일 패턴. failed 추가. |
| women in art history | R18 | 4 | 4 | 100% | 3.7만 (ravenghp) | ★★ 100% 히트! NEW 키워드. 3.7만+2.4만 대형 2개. proven 추가. |
| Monet water lilies | R18 | 4 | 4 | 100% | 5.9만 (bujema_s co) | ★100% 히트! R8 이후 10라운드 간격. 5.9만 재발견. |
| El Greco art | R18 | 4 | 4 | 100% | 65 (unknown) | ★100% 히트! R8 이후 10라운드 간격. 안정적. |
| neoclassical painting | R18 | 4 | 4 | 100% | 1,662 (_puroarte1) | ★100% 히트! R8 이후 10라운드 간격. |
| Hieronymus Bosch | R18 | 4 | 4 | 100% | 9,903 (Imshpo) | ★100% 히트! R8 이후 10라운드 간격. Imshpo 재등장. |
| Van Eyck painting | R18 | 4 | 4 | 100% | 31.6만 (The Culturist) | ★★★ 100% 히트 + 31.6만+24.3만! R11 이후 7라운드 간격. |
| Rubens painting | R18 | 4 | 4 | 100% | 2,911 (the light she brings) | ★100% 히트! R11 이후 7라운드 간격. |
| Arnolfini Portrait | R19 | 1 | 4 | 25% | 3.7만 (riri) | 작품명 키워드. 대부분 R18 중복. 1개 신규가 3.7만 대형! 간격 부족. |
| Lapis lazuli art history | R19 | 4 | 4 | 100% | 1.7만 (Julia) | ★★ 100% 히트! 안료 기반 신규 키워드 대성공. Steven Universe 1개 혼입. proven 추가. |
| art color theory history | R19 | 3 | 4 | 75% | 1.9만 (archt.huma) | 75% 히트. 건축 콘텐츠 혼입 1개(1.9만). Seurat, Holbein 등 양질. |
| Rembrandt painting | R19 | 3 | 4 | 75% | 1,968 (BrushAndTales) | R12 이후 7라운드 간격. 중복 1개. BrushAndTales 1,968. |
| Caravaggio chiaroscuro | R19 | 2 | 4 | 50% | 82 (Il taccuino) | R16 이후 3라운드 간격. 중복 2개. 히트율 하락. |
| Fayum portrait | R19 | 0 | 4 | 0% | — | R16 이후 3라운드 간격. 전부 중복! 간격 더 필요. |
| ancient Greek art | R19 | 3 | 4 | 75% | 4.6만 (Noctorya) | ★★ R9 이후 10라운드 간격. 4.6만+2.5만 대형 2개! 중복 1개. |

### Method Experiments
| Method | Round | Efficiency | Notes |
|--------|-------|-----------|-------|
| keyword_search → 사진 tab | R1 | 10 unique / 5분 | 기본 전략으로 유효. 그리드 좌표 정확도가 핵심 병목. |
| keyword_search → 사진 tab | R2 | 7 unique / 4.5분 | 3개 키워드 시도. engagement 데이터 수집 실패가 핵심 문제. |
| keyword_search → 사진 tab | R3 | 9 unique / 3.5분 | 4개 키워드 시도. engagement resource_id 해독 성공. 그리드 중복 여전히 병목. |
| keyword_search → 사진 tab (빠른 전환) | R4 | 12 unique / 5분 | 6개 키워드, 키워드당 max 4회. 중복 감지+빠른 전환 전략으로 최고 효율 달성. engagement v2 수집 성공. |
| keyword_search → 사진 tab (빠른 전환 + Row 2 스크롤) | R5 | 16 unique / 5분 | 5개 키워드, 키워드당 max 4회. Row 2 스크롤 성공으로 키워드당 4개 확보. 신기록. |
| keyword_search → 사진 tab (6 keywords + Row 2 스크롤) | R6 | 21 unique / 5분 | 6개 키워드 (시간 내 완료!), 화가명/시대별 100% 히트. 🏆 신기록. |
| keyword_search → 사진 tab (6 keywords + Row 2 스크롤) | R7 | 18 unique / 5분 | 6개 키워드 (7번째 미시도). 초기 네비게이션 실패로 ~1분 손실. 실질 탐색 ~4분. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R8 | 24 unique / 4.8분 | 🏆🏆🏆 신기록! 7개 키워드 시도 성공. "Titian painting" 0/4 제외하면 6개 키워드 100% 히트. |
| keyword_search → 사진 tab (6 keywords + Row 2 스크롤) | R9 | 16 unique / 5분 | 6개 키워드 시도. "Manet painting" 0/4 실패. 59.6만 likes 대형 포스트 발견. "ancient Greek art"는 시간 부족 Row 1만. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R10 | 7 unique / 5분 | 7개 키워드 시도했으나 5개 0% 실패. "Turner painting" 100%, "Degas ballerina" 75%만 성공. 포스트 열기 실패가 핵심 병목. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R11 | 14 unique / 3.5분 | 7개 키워드 중 4개 성공, 3개 사진탭 미발견. 검색 아이콘(830,162) 진입 방식 사용. R10 대비 2배 회복. 139.2만 likes 포스트 발견! |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R12 | 16 unique / 5분 | 7개 키워드 전부 사진탭 진입 성공. "art mystery solved" 0개(전부 중복), "Goya" 1개. 나머지 5개 75-100%. 6만 likes 포스트 발견. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R13 | 18 unique / 5분 | 7개 키워드 전부 사진탭 진입 성공. 신규 키워드 4개(Hiroshige, Rodin, Waterhouse, Renaissance Madonna) 100%. 기존 키워드(Vermeer, Michelangelo) 중복 많음. 9.9K likes 포스트 발견. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R14 | 15 unique / 2.9분 | 7개 키워드 중 4개 사진탭 진입 성공, 3개 실패(Delacroix, Klimt, Giotto). 성공한 4개는 75-100%. Phase 2에서 1,641 likes 대형 포스트 발견. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R15 | 7 art hits / 3.8분 | 7개 키워드 중 5개 사진탭 성공, 2개 실패. 키워드 순도 문제: 3개 키워드에서 비미술 콘텐츠 대량 혼입. 실질 art hit 7개. 6.8K + 5.7K likes 발견. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R16 | 14 unique / 3.2분 | 7개 키워드 중 4개 사진탭 성공, 3개 실패(Bernini, Tiepolo, Rodin). "Fayum portrait" 신규 키워드 100% 대성공. "Caravaggio chiaroscuro" 100%. 1K likes 포스트 발견. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R17 | 17 unique / 4.2분 | 7개 키워드 중 5개 사진탭 성공, 2개 실패(Rembrandt night watch, Mantegna fresco). 1.2만 likes 대형 포스트 발견! 화가명+작품명 키워드 3/4 성공. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R18 | 28 unique / 4.8분 | 🏆🏆🏆🏆 역대 최고! 7/7 키워드 전부 4/4 100%. R8(24개) 기록 대폭 경신. 10라운드 간격 proven 키워드 전략 대성공. 31.6만+24.3만 likes 초대형 2개 발견. |
| keyword_search → 사진 tab (7 keywords + Row 2 스크롤) | R19 | 16 unique / 4.5분 | 7/7 키워드 사진탭 성공. 신규 3개 중 "Lapis lazuli" 100% 대성공. "Fayum portrait" 전부 중복(0개). 4.6만+3.7만+2.5만 대형 3개 발견. |

### 효율 높은 키워드 후보 (R20에서 시도)
- "Lapis lazuli art history" → R19 proven (4/4 100%, 1.7만). R21+ 재시도.
- "women in art history" → R18 proven. R20 재시도 (2라운드 간격).
- "Botticelli painting" → R12 이후 8라운드 간격. 적합.
- "Hokusai wave" → R12 이후 8라운드 간격. 적합.
- "historical portrait colorized" → R17 이후 3라운드 간격. 적합.
- "Raphael school athens" → R17 이후 3라운드 간격. 적합.
- "Egyptian tomb painting" → R17 이후 3라운드 간격. 적합.
- **⚠️ R19 교훈**: 단일 작품 줌인 내러티브 = 대형 engagement (Noctorya 4.6만, riri 3.7만). 텍스트 최소화.
- **⚠️ R19 교훈**: 좌우 비교 포맷 3장 = 높은 Share Rate 7.1% (SAINT 2.5만).
- **⚠️ R19 교훈**: "Fayum portrait" 3라운드 간격으로 전부 중복. 최소 5라운드 간격 필요.
- **⚠️ R17 교훈**: "Mantegna fresco" + "Rembrandt night watch" 포스트 열기 실패. 초기 르네상스/3단어 조합 불안정.
- **⚠️ R17 교훈**: @evelyn maude 색상별 초상화 콜라주(1.2만) — 텍스트 없는 순수 이미지 큐레이션도 강력.

---

## Known Issues & Fixes

### 1. 그리드 중복 탭 (Critical)
- **문제**: 고정 좌표로 탭하면 스크롤 후에도 같은 포스트를 반복 탭
- **원인**: 스크롤 후 그리드 위치가 리셋되거나, 좌표가 실제 셀과 안 맞음
- **해결안 (R2)**:
  - 탭 전 `adb_ui_dump()`에서 그리드 셀의 실제 bounds 추출
  - 이미 수집한 caption set으로 중복 감지 → 즉시 back

### 2. 피드 모드 전환 시 데이터 수집 실패
- **문제**: 키워드 변경 후 사진 탭에서 피드 모드로 전환, engagement 데이터 못 읽음
- **해결안 (R2)**: 피드 모드의 resource_id 패턴 파악, 별도 수집 로직

### 3. 슬라이드 카운트 부정확
- **문제**: 대부분 1로 기록됨
- **R2 결과**: "N/M" 패턴 검색 방식은 작동함 (20슬라이드 포스트 감지 성공). 하지만 많은 포스트에서 인디케이터가 UI dump에 안 잡힘.
- **해결안 (R3)**: 스와이프 후 UI dump 재확인, 또는 스크린샷에서 OCR

### 4. Engagement 데이터 수집 ✅ SOLVED in R3
- **문제**: R2에서 모든 포스트의 likes/comments/shares/saves가 빈 값
- **원인**: collect_post_data()가 "like", "comment" 등 영문 키워드로 resource_id를 찾았으나, 실제 TikTok resource_id는 짧은 코드
- **해결 (R3)**: 포스트 상세 UI dump 분석으로 정확한 resource_id 매핑 확인:
  - **`qmd`** = 좋아요 수 (likes) — 예: "31.6만"
  - **`qm_`** = 댓글 수 (comments) — 예: "1,995"
  - **`qmi`** = 공유 수 (shares) — 예: "4.3만"
  - **`qml`** = 저장 수 (saves) — 예: "2.2만"
  - **`izl`** = 계정 이름 (account name)
  - **`qo5`** = 포스트 제목 (title)
  - **`qn5`** = 포스트 캡션 (caption/description)
  - **`s6o`** = 팔로우 버튼
  - **`qmr`** = 사운드/음악 정보
  - **`qo4`** = 게시 날짜
- **R4부터 적용**: `collect_engagement_v2()` 함수로 rid.endswith("/qmd") 등으로 정확히 매칭

### 5. 그리드 중복 탭 — R5에서 Row 2 스크롤 성공 ✅✅
- **문제**: 고정 좌표 탭 시 뒤로가기 후 같은 포스트 반복 열림
- **R4 해결 전략**: 키워드당 max 4회 → Row 1만 유효 (2개)
- **R5 개선**: Row 2 탭 전에 `adb_swipe(450, 1200, 450, 600, 400)` 스크롤 다운 추가
  - Row 1 (y=450): 2개 유니크 ✅
  - 스크롤 후 Row 2 (y=700): 2개 추가 유니크 ✅
  - **결론**: 키워드당 4개 확보 가능. 5개 키워드 × ~3.2 = 16 유니크 (신기록)
- **R6 시도**: Row 3 (스크롤 2회 + y=450)도 시도해볼 것

### 6. 한국어 키워드 자동 번역 (R4 발견)
- **문제**: "명화 해설" → TikTok이 "Art lore"로 자동 번역. "명화 숨은 의미" → 번역 결과 미확인
- **영향**: 한국어 키워드의 히트율이 R3(67%)보다 R4(50%)로 하락. 번역 결과가 의도와 다를 수 있음.
- **해결안**: 한국어보다 영어 키워드가 더 예측 가능. 한국어는 보조적으로만 사용.

---

## Round-by-Round Learnings

### R1 (2026-04-01)
- **Duration**: 295초 | **Found**: 10 unique (23 raw) | **Skipped**: 0 | **CAPTCHA**: No
- **Keywords**: "famous painting facts" (9 unique), "classical art explained" (1+)
- **핵심 교훈**:
  1. "famous painting facts" + 사진 탭 = 고전미술 적중률 ~100%. 매우 좋은 시작점.
  2. 그리드 좌표 하드코딩은 비효율적 → 중복률 ~57% (23 raw → 10 unique)
  3. 뒤로가기 후 그리드 위치가 유지되지 않음 → 스크롤 기반 탐색 전략 재고 필요
  4. 키워드 전환 시 피드 모드로 빠질 수 있음 → 모드 감지 필요
  5. 포스트당 ~10초면 열고 데이터 수집 가능 (포스트 열기 2.5s + 읽기 + 백 1.5s)
  6. **Top performer**: @The Van Gogh Gallery (30.8만 likes) — 고전미술 전문 크리에이터, 프로필 탐색 가치 있음

### R2 (2026-04-01)
- **Duration**: 271초 | **Found**: 7 unique | **Skipped**: 0 | **CAPTCHA**: No
- **Keywords**: "famous painting facts" (4 new/10 tried), "hidden meaning art" (1/5), "art history secrets" (2/4)
- **핵심 교훈**:
  1. **사진 탭 위치가 R1과 달랐다**: (246, 275)이 정확. 탭 순서가 바뀔 수 있으므로 UI dump 확인 필수.
  2. **"famous painting facts"는 R1 결과와 중복 많음** → 같은 키워드 2회 이상은 비효율. 새 키워드로 전환이 낫다.
  3. **"art history secrets"가 가장 높은 히트율 (50%)** → R3에서 우선 사용 + 변형 시도.
  4. **Engagement 데이터 전혀 수집 못함** → resource_id 매핑 재조사 필요. 이것이 R3의 #1 우선순위.
  5. **"hidden meaning art"에서 20슬라이드 포스트 발견** → 슬라이드 인디케이터 "N/M" 감지는 작동함.
  6. **그리드 셀 UI dump 탐지 시도했으나 불안정** → image/cover/thumbnail resource_id 기반 탐지가 잘 안 됨. R3에서 다른 접근 필요 (예: RecyclerView 자식 노드).
  7. **캡션 기반 중복 감지는 효과적** → R1 캡션도 포함하여 cross-round 중복 방지 작동 확인.

### R3 (2026-04-01)
- **Duration**: 207초 | **Found**: 9 unique | **Skipped**: 0 | **CAPTCHA**: No
- **Keywords**: "art history secrets" (3/6), "renaissance art" (2/4), "masterpiece analysis" (2/4), "명화 해설" (2/3)
- **핵심 교훈**:
  1. **✅ Engagement resource_id 완전 해독**: `qmd`=likes, `qm_`=comments, `qmi`=shares, `qml`=saves, `izl`=account, `qo5`=title, `qn5`=caption. R4부터 정확한 engagement 수집 가능.
  2. **"명화 해설" 한국어 키워드가 67% 히트율로 최고 효율**. 영어 결과도 섞여 나와 다양성 높음.
  3. **4개 키워드 모두 50%+ 히트율** → 키워드 다양화 전략이 효과적. 한 키워드에서 4개 이상 시도하지 않고 빠르게 전환.
  4. **그리드 중복 탭 문제 여전** → 고정 좌표 4개로는 최대 2-3개 유니크. R4에서: ① 스크롤 후 탭 ② 더 많은 y좌표 시도 ③ 키워드 빠른 전환으로 우회.
  5. **engagement 재수집 테스트**: 검색→포스트 재오픈으로 engagement 수집 성공 (31.6만, 14.7만 likes). R4에서 첫 오픈 시 바로 수집하도록 개선.
  6. **R3 Top performer**: @The Culturist "Arnolfini Portrait" 31.6만 likes, @historybyart "Mysterious Portrait" 14.7만 likes — 미술사 전문 크리에이터들.

### R4 (2026-04-01)
- **Duration**: 299초 | **Found**: 12 unique | **Skipped**: 2 | **CAPTCHA**: No
- **Keywords**: "명화 해설" (2/4), "명화 숨은 의미" (2/5), "painting hidden details" (2/4), "baroque painting" (2/4), "#classicalart" (2/4), "old master painting" (2/4)
- **핵심 교훈**:
  1. **✅ 최고 효율 달성: 12 unique / 5분**. 빠른 키워드 전환(6개 키워드, 각 max 4회) + cross-round 중복 감지 전략이 효과적.
  2. **✅ Engagement v2 수집 성공**: 42.1만, 29.4만, 15.7만, 14.7만 등 대형 포스트 engagement 정확히 수집.
  3. **모든 키워드 50% 히트율**: 다양한 영어 키워드가 고르게 효율적. 특정 키워드에 의존할 필요 없음.
  4. **한국어 키워드 효율 하락**: "명화 해설"→"Art lore"로 자동 번역되어 판타지/OC 콘텐츠 혼입. 영어가 더 예측 가능.
  5. **그리드 Row 1만 유효**: Row 2(y=700)는 항상 Row 1 중복. 키워드 전환이 스크롤보다 효율적.
  6. **새 키워드 발견**: "painting hidden details"와 "#classicalart"가 양질 고전미술 콘텐츠. "old master painting"도 Fragonard 등 발견.
  7. **R4 Top performers**: @Yapper 42.1만 likes, @Lynx 29.4만 likes, @★L★ 15.7만 likes, @historybyart 14.7만 likes — 다양한 크리에이터 발견.

### R5 (2026-04-01)
- **Duration**: 293초 | **Found**: 16 unique (신기록!) | **Skipped**: 2 | **CAPTCHA**: No
- **Keywords**: "art history explained" (2/4), "museum masterpiece" (2/4), "impressionist painting" (4/4), "famous portrait history" (4/4), "Dutch Golden Age art" (4/4) — "미술사"는 시간 부족으로 미시도
- **핵심 교훈**:
  1. **🏆 신기록 16 unique / 5분**. Row 2 스크롤 전략 성공으로 키워드당 최대 4개 확보. R4(12개) 대비 33% 향상.
  2. **시대별/장르별 키워드가 100% 히트율**: "impressionist painting", "famous portrait history", "Dutch Golden Age art" 모두 4/4. 구체적 시대/장르가 필터 역할.
  3. **Row 2 스크롤 전략 유효**: `adb_swipe(450, 1200, 450, 600, 400)` 후 (225,700)/(680,700) 탭 → Row 1과 다른 포스트 확보.
  4. **"museum masterpiece"는 50%**: 현대 전시/인테리어 콘텐츠 혼입. 박물관 키워드는 비효율.
  5. **대형 포스트 발견**: @history boy 23만, @soo_art_decoo 20.6만, @pocket.art.gallery 6.2만, @emalamenart 4,535.
  6. **사진 탭 위치 여전히 가변**: (246,275) 또는 (400,275). UI dump 확인 필수.
  7. **R5 Top performers**: @history boy 23만 likes (art history explained), @soo_art_decoo 20.6만 (impressionist), @pocket.art.gallery 6.2만 (Dutch Golden Age)

### R6 (2026-04-01)
- **Duration**: 291초 | **Found**: 21 unique (🏆 신기록!) | **Skipped**: 0 | **CAPTCHA**: No
- **Keywords**: "pre raphaelite art" (4/4), "Caravaggio painting" (4/4), "Vermeer painting" (3/4), "art mystery solved" (4/4), "famous sculpture history" (4/4), "미술사" (2/2)
- **핵심 교훈**:
  1. **🏆🏆 신기록 21 unique / 5분**. R5(16개) 대비 31% 향상. 6개 키워드를 시간 내 모두 소화.
  2. **화가명 키워드 = 100% 히트율**: "pre raphaelite art"(4/4), "Caravaggio painting"(4/4). 구체적 화가명/사조명이 가장 효율적인 필터.
  3. **"art mystery solved" 대형 히트**: 13만 likes PhilosoForum 포스트 발견. mystery 관련 키워드가 engagement 높은 콘텐츠와 매칭.
  4. **"famous sculpture history" 성공**: 조각 특화 키워드도 100% 히트. art_atlas 크리에이터 2개, Degas 조각 해설 등. 장르 확장 유효.
  5. **"Vermeer painting" R5 중복 1개**: Dutch Golden Age art와 겹침. 같은 계열 키워드는 라운드 간격을 두는 게 좋음.
  6. **"미술사" 시간 제한으로 Row 1만**: 2/2 히트. Wall Art 스타일링 혼입 — 순수 미술사보다 인테리어 콘텐츠도 포함.
  7. **R6 전략 정리**: 키워드당 ~48초. 6개 키워드 × ~3.5 유니크 = 21. R7에서 7개 키워드 시도하면 24+ 가능.
  8. **R6 Top performers**: @PhilosoForum 13만 likes (art mystery), @pocket.art.gallery 1.8만 (pre raphaelite), @sich 1.3만 (Caravaggio)

### R7 (2026-04-02)
- **Duration**: 308초 | **Found**: 18 unique | **Skipped**: 1 | **CAPTCHA**: No
- **Keywords**: "Botticelli painting" (3/4), "Michelangelo art" (4/4), "rococo painting" (3/4), "symbolism art history" (3/4), "gothic art medieval" (3/4), "art restoration before after" (2/2)
- **핵심 교훈**:
  1. **초기 네비게이션 실패로 ~1분 손실**: open_tiktok() 후 네이버 지도가 표시됨. FYP 하단 "검색 · ..." 바를 탭하여 검색 결과로 진입 성공. **교훈: open_tiktok() 후 반드시 UI dump로 TikTok 확인. 다른 앱이면 수동 전환.**
  2. **"Michelangelo art" 100% 히트 (4/4)**: 72.2만 likes 대형 포스트 발견. 화가명 키워드 여전히 최강.
  3. **상징주의/로코코 등 사조명 키워드 75% 히트**: R6의 100%보다 낮음. 판타지/현대 아트 혼입 발생. "gothic art medieval"은 판타지 타투 등 혼입 주의.
  4. **"art restoration before after" 유효**: 건축 복원 콘텐츠. 2/2 히트이나 시간 부족으로 Row 1만. R8에서 완전 시도 필요.
  5. **Ysn 크리에이터 발견**: symbolism 키워드로 Alphonse Osbert, Odilon Redon 포스트 2개. 상징주의 전문 크리에이터. 프로필 탐색 가치.
  6. **사진 탭 위치**: (400,275) 또는 (246,275). 검색어에 따라 변동. UI dump에서 text=='사진' 찾기 필수.
  7. **"Raphael painting" 미시도**: 시간 초과. R8 우선 시도.
  8. **R7 Top performers**: @🧿 72.2만 likes (Michelangelo), @elle 7.1만 (rococo), @História Academia 6,377 (restoration)

### R8 (2026-04-02)
- **Duration**: 287초 | **Found**: 24 unique (🏆🏆🏆 신기록!) | **Skipped**: 0 | **CAPTCHA**: No
- **Keywords**: "Raphael painting" (4/4), "Titian painting" (0/4), "El Greco art" (4/4), "Hieronymus Bosch" (4/4), "neoclassical painting" (4/4), "art heist history" (4/4), "Monet water lilies" (4/4)
- **핵심 교훈**:
  1. **🏆🏆🏆 신기록 24 unique / 4.8분**. R6(21개) 대비 14% 향상. 7개 키워드를 시간 내 모두 소화. 이전 검색 결과 페이지에서 시작하여 네비게이션 시간 절약.
  2. **"Titian painting" 완전 실패 (0/4)**: 포스트 열기 자체가 안 된 것으로 추정. 검색 결과가 적거나 그리드 좌표 불일치. 모든 검색 결과가 없는 키워드가 있을 수 있음 — 빠르게 건너뛰는 전략 필요.
  3. **화가명+작품명 조합 유효**: "Monet water lilies"가 5.9만 likes 대형 포스트 + 100% 히트. 구체적 조합이 engagement 높은 콘텐츠로 이어짐.
  4. **"Hieronymus Bosch" 높은 engagement**: Imshpo 9,903 likes, pocket.art.gallery 1,965. 특이한/판타지적 중세 화가가 engagement 높음.
  5. **pocket.art.gallery 크리에이터 3번째 등장**: R5 Dutch Golden Age, R6 pre raphaelite, R8 Bosch. 다양한 고전미술 포스트를 올리는 핵심 크리에이터.
  6. **슬라이드 감지 여전히 1로 기록**: 모든 포스트에서 slides=1. UI dump에서 N/M 인디케이터 감지 실패 지속. 스와이프 + 스크린샷 비교로 개선 필요.
  7. **oysterlab 크리에이터 3개 포스트**: Raphael 키워드에서 같은 크리에이터 3개. 크리에이터 프로필 탐색으로 효율 높일 수 있음.
  8. **R8 Top performers**: unknown 5.9만 likes (Monet), @Imshpo 9,903 (Bosch), @emalamenart 5,530 (Monet), @pocket.art.gallery 1,965 (Bosch)

### R9 (2026-04-02)
- **Duration**: 304초 | **Found**: 16 unique | **Skipped**: 4 | **CAPTCHA**: No
- **Keywords**: "Rembrandt painting" (4/4), "Da Vinci secrets" (3/4), "Klimt painting" (3/4), "Goya painting" (4/4), "Manet painting" (0/4), "ancient Greek art" (2/2)
- **핵심 교훈**:
  1. **"Da Vinci secrets" 59.6만 likes — 역대 최대 engagement 포스트**: mystery+대형 화가 키워드가 viral 콘텐츠와 매칭. Note from Jesus 21.9만도 발견. "Da Vinci"+"secrets/mystery" 조합이 engagement 극대화.
  2. **"Manet painting" 완전 실패 (0/4)**: R8 "Titian painting"과 동일 현상. 포스트 열기 자체 불가. **패턴: 일부 화가명 키워드에서 사진 탭 그리드가 표시되지 않거나 좌표 불일치**. 빠르게 건너뛰는 전략 필요 (2회 실패 시 즉시 다음 키워드).
  3. **"Goya painting" 100% 히트 + pocket.art.gallery 4번째 등장**: R5, R6, R8에 이어 또 등장. 이 크리에이터는 거의 모든 화가 키워드에 노출됨 — 프로필 탐색으로 대량 수집 가능.
  4. **"ancient Greek art" 고대미술 유효**: 시간 부족으로 Row 1만 시도했지만 100% 히트. R10에서 완전 시도 추천.
  5. **16 unique은 R8(24)보다 낮음**: "Manet" 0/4 실패 + 시간 내 6개 키워드만 소화. 실패 키워드 빠르게 스킵하면 7-8개 가능.
  6. **Ysn 크리에이터 재등장**: R7 symbolism → R9 Goya. 다양한 미술사 포스트 올리는 크리에이터.
  7. **bernarda._.art 0 likes 포스트**: Rembrandt watercolor drawing — 현대 모사 작품. 고전미술 "해설"보다는 "모사" 콘텐츠. 필터링 강화 필요.
  8. **R9 Top performers**: @Ariel Omar 59.6만 likes (Da Vinci), @Note from Jesus 21.9만 (Da Vinci), @art from bobby 2.1만 (Klimt)

### R10 (2026-04-02)
- **Duration**: 300초 | **Found**: 7 unique | **Skipped**: 2 | **CAPTCHA**: No
- **Keywords**: "Velazquez painting" (0/2), "Degas ballerina" (3/4), "art forgery history" (0/2), "Turner painting" (4/4), "Frida Kahlo art" (0/2), "medieval manuscript art" (0/2), "Cézanne painting" (0/2)
- **핵심 교훈**:
  1. **포스트 열기 실패가 핵심 병목 — 7개 키워드 중 5개 0% 실패**: R8 Titian, R9 Manet에서 간헐적이던 현상이 R10에서 대규모 발생. **패턴 분석 필요**: 사진 탭 그리드가 정상 표시되는지, 그리드 좌표(225/680, 450)가 실제 셀에 맞는지 UI dump로 확인 필요.
  2. **"Turner painting" 100% 히트 (4/4)**: 유일하게 완벽 작동한 키워드. Ysn 크리에이터 5번째 등장 (R7 symbolism, R9 Goya, R10 Turner). Ysn은 최다 출현 크리에이터.
  3. **"Degas ballerina" 75% 히트**: 화가명+주제 조합은 여전히 유효하나 geniodoro 2개는 저조한 engagement (16, 10 likes).
  4. **특수문자 키워드 주의**: "Cézanne painting"의 é가 adb_text()에서 정상 입력되지 않았을 가능성. R11에서 "Cezanne painting"으로 재시도.
  5. **0% 실패 패턴 가설**:
     - 사진 탭 결과가 적어 그리드 첫 행이 y=450보다 아래에 위치?
     - 사진 탭 진입 자체가 안 되고 "상위" 탭에 머물러 있을 수 있음?
     - **R11 개선안**: 사진 탭 진입 후 UI dump로 그리드 셀 존재 확인. 없으면 즉시 스킵 (현재 ~40초 낭비 방지).
  6. **Ysn 크리에이터 프로필 탐색 가치**: R7, R9, R10에서 3라운드 연속 등장. 프로필 방문으로 대량 수집 기회.
  7. **R10 Top performers**: @Jingus Bingus 641 likes (Degas), @Ysn 1,133 likes (Turner), @art.studio.daily 198 likes (Turner)
