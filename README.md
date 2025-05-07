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

  <div class="item">
    <h2>🎁 특품 말스폰알</h2>
    <p class="price">가격: 50 HBC</p>
    <button onclick="buyItem('특품 말스폰알', 50)">구매</button>
  </div>

  <div class="item">
    <h2>🐪 낙타</h2>
    <p class="price">가격: 45 HBC</p>
    <button onclick="buyItem('낙타', 45)">구매</button>
  </div>

  <div class="gift">
    <h2>🎁 코인 선물</h2>
    <input id="targetUser" placeholder="상대 이름">
    <input id="amount" type="number" placeholder="수량">
    <button onclick="gift()">선물</button>
  </div>

  <div class="gift">
    <h2>🔧 관리자 지급</h2>
    <input id="adminTarget" placeholder="유저 이름">
    <input id="adminAmount" type="number" placeholder="코인 수량 (예: 10)">
    <button onclick="adminGiveCoin()">코인 지급</button>
    <button onclick="adminGiveBox()">랜덤상자 지급</button>
    <input id="adminPass" type="password" placeholder="관리자 비밀번호">
  </div>

  <div class="history">
    <h2>📜 구매 내역</h2>
    <pre id="historyLog"></pre>
  </div>

  <div class="leaderboard">
    <h2>🏆 코인 보유 순위</h2>
    <pre id="leaderboardLog"></pre>
  </div>

  <div>
    <p class="small">현재 사용자: <span id="username"></span></p>
    <p class="small">잔액: <span id="balance"></span> HBC</p>
  </div>

  <div class="history">
    <h2>🗞 공용 상점 로그</h2>
    <pre id="globalLog"></pre>
  </div>

  <div id="purchaseEffect" class="purchase-effect">구매 중...</div>

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

    function logGlobal(message) {
      const now = new Date().toLocaleString();
      let log = storage.getItem("global_log") || "";
      let logLines = log.split("\n").filter(line => line.trim() !== "");
      logLines.push(`[${now}] ${message}`);
      if (logLines.length > 50) logLines = logLines.slice(-50);
      storage.setItem("global_log", logLines.join("\n"));
    }

    function showHistory() {
      const user = getUser();
      const history = storage.getItem(user + "_history") || "(없음)";
      document.getElementById("historyLog").innerText = history;
    }

    function showGlobalLog() {
      document.getElementById("globalLog").innerText = storage.getItem("global_log") || "(아직 로그 없음)";
    }

    function updateDisplay() {
      const user = getUser();
      document.getElementById("balance").innerText = getBalance(user);
    }

    function updateLeaderboard() {
      const keys = Object.keys(storage);
      const users = keys.filter(k => k.endsWith("_balance"))
        .map(k => ({
          name: k.replace("_balance", ""),
          bal: parseFloat(storage.getItem(k))
        }))
        .sort((a, b) => b.bal - a.bal)
        .slice(0, 5);
      document.getElementById("leaderboardLog").innerText = users.map(u => `${u.name}: ${u.bal} HBC`).join("\n");
    }

    function buyItem(item, cost) {
      const user = getUser();
      let balance = getBalance(user);
      if (balance >= cost) {
        const purchaseEffect = document.getElementById("purchaseEffect");
        purchaseEffect.innerText = `${item}을(를) 구매 중...`;
        purchaseEffect.style.display = "block";
        
        setTimeout(() => {
          purchaseEffect.style.display = "none";
        }, 2000);

        setBalance(user, balance - cost);
        logPurchase(user, item, cost);
        logGlobal(`🛒 ${user}님이 '${item}'을(를) ${cost} HBC에 구매했습니다.`);
        alert(`${item}을(를) 구매했습니다.`);
        updateDisplay();
        showHistory();
        showGlobalLog();
        updateLeaderboard();
      } else {
        alert("HBC가 부족합니다.");
      }
    }

    function gift() {
      const user = getUser();
      const target = document.getElementById("targetUser").value.trim();
      const amount = parseFloat(document.getElementById("amount").value);
      if (!target || isNaN(amount) || amount <= 0) return alert("올바르게 입력해주세요.");
      if (target === user) return alert("자기 자신에게는 보낼 수 없습니다.");
      if (getBalance(user) < amount) return alert("잔액 부족!");

      setBalance(user, getBalance(user) - amount);
      setBalance(target, getBalance(target) + amount);
      logGlobal(`🎁 ${user}님이 ${target}님에게 ${amount} HBC를 선물했습니다.`);
      alert(`${target}에게 ${amount} HBC를 보냈습니다.`);
      updateDisplay();
      showGlobalLog();
      updateLeaderboard();
    }

    function adminAuth() {
      return document.getElementById("adminPass").value === PASSWORD;
    }

    function adminGiveCoin() {
      if (!adminAuth()) return alert("관리자 비밀번호가 틀렸습니다.");
      const target = document.getElementById("adminTarget").value;
      const amount = parseFloat(document.getElementById("adminAmount").value);
      if (!target || isNaN(amount) || amount <= 0) return alert("입력 오류");

      setBalance(target, getBalance(target) + amount);
      logGlobal(`🔧 관리자님이 ${target}에게 ${amount} HBC를 지급했습니다.`);
      alert(`${target}에게 ${amount} HBC 지급 완료`);
      updateLeaderboard();
    }

    function adminGiveBox() {
      if (!adminAuth()) return alert("관리자 비밀번호가 틀렸습니다.");
      const target = document.getElementById("adminTarget").value;
      if (!target) return alert("유저 이름을 입력해주세요.");

      logGlobal(`🔧 관리자님이 ${target}에게 랜덤상자를 지급했습니다.`);
      let history = storage.getItem(target + "_history") || "";
      history += `✅ 관리자 지급 → 랜덤상자\n`;
      storage.setItem(target + "_history", history);

      alert(`${target}에게 랜덤상자 지급 완료`);
      openRandomBox(target);  // 랜덤상자 자동 개봉
    }

    function openRandomBox(target) {
      const items = ["황금사과", "럭잼", "경험치 병"];
      const randomItem = items[Math.floor(Math.random() * items.length)];
      const cost = randomItem === "황금사과" ? 40 : randomItem === "럭잼" ? 20 : 10;
      logPurchase(target, randomItem, cost);
      setBalance(target, getBalance(target) + cost); // 상자에서 받은 아이템의 가격을 추가
      logGlobal(`${target}님이 랜덤상자를 개봉하여 '${randomItem}'을(를) 얻었습니다.`);
    }

    window.onload = function () {
      getUser();
      updateDisplay();
      showHistory();
      showGlobalLog();
      updateLeaderboard();
    };
  </script>
</body>
</html>
