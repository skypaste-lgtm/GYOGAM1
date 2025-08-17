<업그레이드소식>
<html lang="ko">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>구글 시트 → 문구 표시</title>
<style>
  :root {
    --font: system-ui, AppleSDGothicNeo, "Malgun Gothic", Arial, sans-serif;
  }
  html, body {
    height: 100%;
    margin: 0;
  }
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
    text-align: left;
  }
  .label {
    font-size: 14px;
    color: #666;
    margin-bottom: 8px;
  }
  .text {
    font-size: 28px;
    line-height: 1.1;        /* 줄간격 축소 */
    font-weight: 700;
    word-break: keep-all;
    white-space: pre-line;   /* 줄바꿈 유지 */
    text-align: left;        /* 줄바뀔때도 왼쪽 정렬 */
  }
  .meta {
    margin-top: 12px;
    font-size: 12px;
    color: #999;
  }
  .error {
    color: #b00020;
    font-weight: 600;
  }

  /* 깜박임 애니메이션 */
  @keyframes blink {
    0%, 50%, 100% { opacity: 1; }
    25%, 75% { opacity: 0; }
  }
  .blink {
    animation: blink 1s infinite;
  }
</style>
</head>
<body>
<div class="card">
  <div class="label">시트 값에 따른 표시</div>
  <div id="text" class="text">불러오는 중…</div>
  <div id="meta" class="meta"></div>
</div>

<script>
const SHEET_ID = "16_aHITP-iPWE57OWnv85gw60qTN6Rhfo-41G1_rQpT0";
const SHEET_NAME = "시트1";
const RANGE = "A1:B3";
const REFRESH_MS = 5000;

const GVIZ_URL =
  `https://docs.google.com/spreadsheets/d/${encodeURIComponent(SHEET_ID)}/gviz/tq?` +
  `tqx=out:json&sheet=${encodeURIComponent(SHEET_NAME)}&range=${encodeURIComponent(RANGE)}`;

const $text = document.getElementById("text");
const $meta = document.getElementById("meta");

function parseGviz(text) {
  const start = text.indexOf("{");
  const end = text.lastIndexOf("}");
  if (start === -1 || end === -1) throw new Error("GViz 응답 파싱 실패");
  return JSON.parse(text.slice(start, end + 1));
}

function applyData(rows) {
  const A1 = rows?.[0]?.c?.[0]?.v ?? null;
  const B1 = rows?.[0]?.c?.[1]?.v ?? "";
  const B2 = rows?.[1]?.c?.[1]?.v ?? "";
  const B3 = rows?.[2]?.c?.[1]?.v ?? "";

  if (!A1 || String(A1).length < 2) {
    $text.textContent = "(표시할 문구가 없습니다)";
    return;
  }

  const colorCode = String(A1)[0]; // 첫 자리 숫자
  const textCode  = String(A1)[1]; // 두 번째 숫자

  // 두 번째 숫자에 따라 B1~B3 선택
  let message = "";
  if (textCode === "1") message = B1;
  else if (textCode === "2") message = B2;
  else if (textCode === "3") message = B3;

  // 색상 지정
  let color = "black";
  switch (colorCode) {
    case "1": color = "red"; break;
    case "2": color = "blue"; break;
    case "3": color = "black"; break;
    case "4": color = "green"; break;
    case "5": color = "purple"; break;
    case "6": color = "orange"; break;
  }

  // "/" 기준 줄바꿈 → 첫 줄만 깜박이게
  const lines = (message || "").split("/");
  if (lines.length > 0) {
    lines[0] = `<span class="blink">${lines[0]}</span>`;
  }
  $text.innerHTML = lines.join("<br>");
  $text.style.color = color;

  $meta.textContent = `A1=${A1} · ${new Date().toLocaleString()}`;
}

async function loadOnce() {
  try {
    $meta.textContent = "불러오는 중…";
    const res = await fetch(GVIZ_URL, { cache: "no-store" });
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const raw = await res.text();
    const json = parseGviz(raw);
    applyData(json.table.rows);
  } catch (err) {
    console.error(err);
    $text.textContent = "데이터를 불러오지 못했습니다";
    $text.classList.add("error");
    $meta.textContent = `${new Date().toLocaleString()}`;
  }
}

loadOnce();
if (REFRESH_MS > 0) {
  setInterval(loadOnce, REFRESH_MS);
}
</script>
</body>
</html>
