<업그레이드소식>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>구글 시트 → 깜박이는 문구</title>
  <style>
    :root { --font: system-ui, AppleSDGothicNeo, "Malgun Gothic", Arial, sans-serif; }
    html, body { height: 100%; margin: 0; }
    body {
      display: grid;
      place-items: center;
      font-family: var(--font);
      background: #fff;
    }
    .card {
      min-width: 280px;
      max-width: 90vw;
      padding: 24px 28px;
      border: 1px solid #e8e8e8;
      border-radius: 16px;
      box-shadow: 0 6px 20px rgba(0,0,0,.06);
      text-align: left; /* 카드 전체 왼쪽 정렬 */
    }
    .text {
      font-size: 28px;
      line-height: 1.15;        /* 줄간격 살짝 촘촘하게 */
      font-weight: 700;
      word-break: keep-all;
      white-space: pre-line;     /* \n 줄바꿈 반영 */
      text-align: left;          /* 줄마다 왼쪽 정렬 */
    }
    /* 깜박임 효과 */
    .blink { animation: blink 1.2s step-start infinite; }
    @keyframes blink { 50% { visibility: hidden; } }

    .error { color: #b00020; font-weight: 600; }
  </style>
</head>
<body>
  <div class="card">
    <!-- 깜박이는 문자만 표시 -->
    <div id="text" class="text blink">불러오는 중…</div>
  </div>

  <script>
    /* ========================= 설정 =========================
     * 시트를 "링크가 있는 모든 사용자(보기)"로 공개하세요.
     * SHEET_NAME은 실제 탭 이름으로 입력하세요.
     */
    const SHEET_ID   = "16_aHITP-iPWE57OWnv85gw60qTN6Rhfo-41G1_rQpT0";
    const SHEET_NAME = "시트1";
    const RANGE      = "A1:B3";         // A1, B1~B3까지
    const REFRESH_MS = 5000;            // 5초마다 갱신

    const GVIZ_URL =
      `https://docs.google.com/spreadsheets/d/${encodeURIComponent(SHEET_ID)}/gviz/tq?` +
      `tqx=out:json&sheet=${encodeURIComponent(SHEET_NAME)}&range=${encodeURIComponent(RANGE)}`;

    const $text = document.getElementById("text");
    let isBlinking = true; // 스페이스바로 제어

    function parseGviz(text) {
      const start = text.indexOf("{");
      const end   = text.lastIndexOf("}");
      if (start === -1 || end === -1) throw new Error("GViz 응답 파싱 실패");
      return JSON.parse(text.slice(start, end + 1));
    }

    function getColor(code) {
      switch (code) {
        case "1": return "red";     // 빨강
        case "2": return "blue";    // 파랑
        case "3": return "black";   // 검정
        case "4": return "green";   // 녹색
        case "5": return "purple";  // 보라
        case "6": return "orange";  // 주황
        default:  return "black";
      }
    }

    function applyData(rows) {
      const A1 = rows?.[0]?.c?.[0]?.v ?? "";
      const B1 = rows?.[0]?.c?.[1]?.v ?? "";
      const B2 = rows?.[1]?.c?.[1]?.v ?? "";
      const B3 = rows?.[2]?.c?.[1]?.v ?? "";

      const A1str = String(A1 ?? "");
      const colorCode = A1str.charAt(0); // 앞자리: 색상
      const textCode  = A1str.charAt(1); // 둘째 자리: 표시셀

      // 표시할 문구 선택
      let message = "";
      if (textCode === "1") message = B1;
      else if (textCode === "2") message = B2;
      else if (textCode === "3") message = B3;

      // "/" → 줄바꿈으로 변환 (왼쪽 정렬은 CSS에서 처리)
      message = String(message ?? "").replace(/\//g, "\n");

      // 화면에 적용
      $text.textContent = message || "(표시할 문구가 없습니다)";
      $text.style.color = getColor(colorCode);
    }

    async function loadOnce() {
      try {
        const res = await fetch(GVIZ_URL, { cache: "no-store" });
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        const raw  = await res.text();
        const json = parseGviz(raw);
        applyData(json.table.rows);
      } catch (err) {
        console.error(err);
        $text.textContent = "데이터를 불러오지 못했습니다";
        $text.classList.remove("blink");
        $text.classList.add("error");
      }
    }

    // 최초 로드 & 주기적 갱신
    loadOnce();
    if (REFRESH_MS > 0) setInterval(loadOnce, REFRESH_MS);

    /* ===== 스페이스바로 깜박임 정지/재개 (브라우저 호환 보강) ===== */
    function toggleBlink() {
      isBlinking = !isBlinking;
      if (isBlinking) {
        $text.classList.add("blink");
      } else {
        $text.classList.remove("blink");
        // 정지 직후에도 항상 보이도록 보정
        $text.style.visibility = "visible";
      }
    }

    // window & document 모두 리스닝, code/key 양쪽 체크
    function onKey(e) {
      if (e.code === "Space" || e.key === " ") {
        e.preventDefault(); // 페이지 스크롤 방지
        toggleBlink();
      }
    }
    window.addEventListener("keydown", onKey);
    document.addEventListener("keydown", onKey);
  </script>
</body>
</html>
