<업데이트소식>
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
      text-align: left;
      width: 500px;
      max-width: 90%;
    }
    .text {
      font-size: 28px;
      line-height: 1.2;
      font-weight: 700;
      word-break: keep-all;
      display: block;
      text-align: left;
      white-space: pre-line; /* / → 줄바꿈 */
      margin-bottom: 15px;
    }
    .blink {
      animation: blink 1.5s step-start infinite;
    }
    @keyframes blink {
      50% { visibility: hidden; }
    }
  </style>
</head>
<body>
  <div class="card">
    <div id="text1" class="text blink">불러오는 중…</div>
    <div id="text2" class="text blink">불러오는 중…</div>
    <div id="text3" class="text blink">불러오는 중…</div>
  </div>

  <script>
    const sheetUrl = "👉 여기에 공개한 구글시트 CSV 주소 붙여넣기 👈";

    const $text1 = document.getElementById("text1");
    const $text2 = document.getElementById("text2");
    const $text3 = document.getElementById("text3");

    async function loadData() {
      try {
        const res = await fetch(sheetUrl);
        const data = await res.text();
        const rows = data.split("\n").map(r => r.split(","));

        // A1 ~ B3 값 가져오기
        let a1 = rows[1][0] || "";  // A열 1행
        let b1 = rows[1][1] || "";
        let b2 = rows[2][1] || "";
        let b3 = rows[3][1] || "";

        // 줄바꿈 처리
        b1 = b1.replace(/\//g, "\n");
        b2 = b2.replace(/\//g, "\n");
        b3 = b3.replace(/\//g, "\n");

        // 표시 조건
        $text1.style.display = "none";
        $text2.style.display = "none";
        $text3.style.display = "none";

        if (a1 == "1") {
          $text1.style.display = "block";
          $text1.textContent = b1;
        } else if (a1 == "2") {
          $text2.style.display = "block";
          $text2.textContent = b2;
        } else if (a1 == "3") {
          $text3.style.display = "block";
          $text3.textContent = b3;
        } else {
          // 0 또는 기타 → 모두 표시
          $text1.style.display = "block";
          $text2.style.display = "block";
          $text3.style.display = "block";
          $text1.textContent = b1;
          $text2.textContent = b2;
          $text3.textContent = b3;
        }

        // 색상 조건 (4=녹색, 5=보라색)
        [$text1, $text2, $text3].forEach(($el, idx) => {
          let val = parseInt([b1, b2, b3][idx]);
          if (val === 4) $el.style.color = "green";
          else if (val === 5) $el.style.color = "purple";
          else $el.style.color = "black";
        });

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
        e.preventDefault();
      }
    });
  </script>
</body>
</html>
