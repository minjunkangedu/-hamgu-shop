<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>햄구신교 상점</title>
  <style>
    body { font-family: 'Arial', sans-serif; background: #fffaf0; color: #333; text-align: center; padding: 20px; }
    h1 { font-size: 2.5em; margin-bottom: 10px; }
    .item, .gift, .history, .leaderboard {
      border: 2px solid #ccc; padding: 15px; margin: 10px auto;
      width: 300px; border-radius: 10px; background: #fdfdfd;
    }
    .price { font-weight: bold; color: #d2691e; }
    input, button { padding: 8px; margin-top: 5px; }
    .small { font-size: 0.9em; color: #888; }
    /* 애니메이션 효과 */
    .purchase-effect {
      font-size: 1.2em;
      color: #fff;
      background-color: #4CAF50;
      padding: 10px;
      border-radius: 10px;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      display: none;
      z-index: 999;
      animation: fadeInOut 2s ease-in-out;
    }

    @keyframes fadeInOut {
      0% { opacity: 0; }
      50% { opacity: 1; }
      100% { opacity: 0; }
    }
  </style>
</head>
<body>

  <h1>✨ 햄구신교 상점 ✨</h1>
  <p>Hamgu는 거룩할지어다!</p>
  <p><strong>⚠️ 접속 시 디스코드 닉네임으로 이름을 작성해주세요! ⚠️</strong></p>

  <div class="item">
    <h2>🍎 황금사과</h2>
    <p class="price">가격: 40 HBC</p>
    <button onclick="buyItem('황금사과', 40)">구매</button>
  </div>

  <div class="item">
    <h2>🍀 럭잼 (10~30%)</h2>
    <p class="price">가격: 20 HBC</p>
    <button onclick="buyItem('럭잼', 20)">구매</button>
  </div>

  <div class="item">
    <h2>🧪 경험치 병</h2>
    <p class="price">가격: 10 HBC</p>
    <button onclick="buyItem('경험치 병', 10)">구매</button>
  </div>

  <div class="item">
    <h2>🎉 이벤트 티켓</h2>
    <p class="price">가격: 20 HBC</p>
    <button onclick="buyItem('이벤트 티켓', 20)">구매</button>
  </div>

  <!-- 구매 효과 텍스트 -->
  <div id="purchaseEffect" class="purchase-effect">구매 중...</div>

  <div>
    <p class="small">현재 사용자: <span id="username"></span></p>
    <p class="small">잔액: <span id="balance"></span> HBC</p>
  </div>

  <script>
    const PASSWORD = "komq3244";
    const storage = window.localStorage;

    function getUser() {
      let name = storage.getItem("current_user");
      if (!name) {
        alert("⚠️ 디스코드 닉네임으로 정확하게 입력해주세요! 이후 변경할 수 없습니다. ⚠️");
        name = prompt("당신의 이름을 입력하세요.\n\n⚠️ *디스코드 닉네임으로 작성해주세요!* ⚠️");
        if (!name) name = "이름없음";
        storage.setItem("current_user", name);
      }
      document.getElementById("username").innerText = name;
      return name;
    }

    function getBalance(user) {
      return parseFloat(storage.getItem(user + "_balance") || "1");
    }

    function setBalance(user, amount) {
      storage.setItem(user + "_balance", amount.toString());
    }

    function logPurchase(user, item, cost) {
      let history = storage.getItem(user + "_history") || "";
      history += `✅ ${item} (${cost} HBC)\n`;
      storage.setItem(user + "_history", history);
    }

    function showHistory() {
      const user = getUser();
      const history = storage.getItem(user + "_history") || "(없음)";
      document.getElementById("historyLog").innerText = history;
    }

    function updateDisplay() {
      const user = getUser();
      document.getElementById("balance").innerText = getBalance(user);
    }

    function buyItem(item, cost) {
      const user = getUser();
      let balance = getBalance(user);
      if (balance >= cost) {
        // 구매 효과 표시
        const purchaseEffect = document.getElementById("purchaseEffect");
        purchaseEffect.innerText = `${item}을(를) 구매 중...`;
        purchaseEffect.style.display = "block";
        
        setTimeout(() => {
          purchaseEffect.style.display = "none";  // 2초 후 구매 효과 숨기기
        }, 2000);

        setBalance(user, balance - cost);
        logPurchase(user, item, cost);
        alert(`${item}을(를) 구매했습니다.`);
        updateDisplay();
        showHistory();
      } else {
        alert("HBC가 부족합니다.");
      }
    }

    window.onload = function () {
      getUser();
      updateDisplay();
    };
  </script>
</body>
</html>
