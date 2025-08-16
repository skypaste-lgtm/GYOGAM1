<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>Google Sheets Message</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: #f5f5f5;
      margin: 0;
    }
    .card {
      background: white;
      padding: 20px;
      border-radius: 16px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      width: 500px;
    }
    .text {
      font-size: 28px;
      line-height: 1.2;
      font-weight: 700;
      word-break: keep-all;
      text-align: left;       /* 왼쪽 정렬 */
      white-space: pre-line;  /* 줄바꿈 적용 */
    }
    /* 깜박임 애니메이션 */
    .blink {
      animation: blink 1.5s step-start infinite;
    }
    @keyframes blink {
      50% { opacity: 0; }
    }
  </style>
</head>
<body>
  <div class="card">
    <div id="text" class="text blink"></div>
  </div>

  <script>
    /* ===== 샘플 데이터 (구글 시트 값 대신 예시) ===== */
    const A1 = 5;  // 색상 결정용 (1=빨강, 2=파랑, 3=검정, 4=초록, 5=보라)
    const B1 = "안녕하세요/반갑습니다";
    const B2 = "오늘도 좋은 하루/되세요";
    const B3 = "행복/가득하세요";

    /* ===== 메시지 합치고 줄바꿈 처리 ===== */
    let message = [B1, B2, B3].join("\n"); 
    message = message.replace(/\//g, "\n"); // "/" → 줄바꿈

    const $text = document.getElementById("text");
    $text.textContent = message;

    /* ===== 색상 지정 ===== */
    let color = "black";
    if (A1 === 1) color = "red";
    else if (A1 === 2) color = "blue";
    else if (A1 === 3) color = "black";
    else if (A1 === 4) color = "green";
    else if (A1 === 5) color = "purple";

    $text.style.color = color;

    /* ===== 스페이스바로 깜박임 제어 ===== */
    let blinking = true;
    window.addEventListener("keydown", (e) => {
      if (e.code === "Space" || e.key === " ") {
        e.preventDefault(); // 스크롤 방지
        blinking = !blinking;
        if (blinking) {
          $text.classList.add("blink");
        } else {
          $text.classList.remove("blink");
        }
      }
    });
  </script>
</body>
</html>
