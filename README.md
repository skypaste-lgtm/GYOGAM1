<업그레이드소식>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>Google Sheets 데이터 표시</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #f0f0f0;
      margin: 0;
    }
    .card {
      background: white;
      padding: 30px;
      border-radius: 20px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
      text-align: left;   /* 카드 안 전체 왼쪽 정렬 */
      width: 500px;
      max-width: 90%;
    }
    .text {
      font-size: 28px;
      line-height: 1.2;      /* 줄 간격 줄이기 */
      font-weight: 700;
      word-break: keep-all;
      display: block;
      text-align: left;      /* 줄바꿈된 행도 왼쪽 정렬 */
      white-space: pre-line; /* 줄바꿈 반영 */
    }
    .blink {
      animation: blink 1.5s step-start infinite; /* 깜박임 속도 약간 느리게 */
    }
    @keyframes blink {
      50% { visibility: hidden; }
    }
    .label {
      font-size: 20px;
      margin-top: 15px;
      font-weight: 500;
      color: #555;
    }
  </style>
</head>
<body>
  <div class="card">
    <div id="text1" class="text blink">불러오는 중…</div>
    <div id="label1" class="label">B1 값</div>
    <div id="text2" class="text blink">불러오는 중…</div>
    <div id="label2" class="label">B2 값</div>
    <div id="text3" class="text blink">불러오는 중…</div>
    <div id="label3" class="label">B3 값</div>
  </div>

  <script>
    const sheetUrl = "👉 여기에 웹으로 공개한 구글시트 CSV 주소 붙여넣기 👈";

    const $text1 = document.getElementById("text1");
    const $text2 = document.getElementById("text2");
    const $text3 = document.getElementById("text3");

    async function loadData() {
      try {
        const res = await fetch(sheetUrl);
        const data = await res.text();
        const rows = data.split("\n").map(r => r.split(","));

        // B1, B2, B3 값 가져오기 (첫 행이 헤더일 경우 2행부터 시작)
        let b1 = rows[1][1] || "";
        let b2 = rows[2][1] || "";
        let b3 = rows[3][1] || "";

        // / → 줄바꿈 처리
        b1 = b1.replace(/\//g, "\n");
        b2 = b2.replace(/\//g, "\n");
        b3 = b3.replace(/\//g, "\n");

        $text1.textContent = b1;
        $text2.textContent = b2;
        $text3.textContent = b3;

        // 숫자 값이면 색상 변경
        if (parseInt(b1) === 4) $text1.style.color = "green";
        else if (parseInt(b1) === 5) $text1.style.color = "purple";

        if (parseInt(b2) === 4) $text2.style.color = "green";
        else if (parseInt(b2) === 5) $text2.style.color = "purple";

        if (parseInt(b3) === 4) $text3.style.color = "green";
        else if (parseInt(b3) === 5) $text3.style.color = "purple";

      } catch (e) {
        console.error("데이터 불러오기 오류:", e);
      }
    }

    // 최초 실행
    loadData();

    // 스페이스바로 깜박임 제어
    document.addEventListener("keydown", (e) => {
      if (e.code === "Space") {
        [$text1, $text2, $text3].forEach($el => {
          $el.classList.toggle("blink");
        });
        e.preventDefault(); // 스크롤 방지
      }
    });
  </script>
</body>
</html>
