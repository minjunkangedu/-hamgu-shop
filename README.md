<script>
  const PASSWORD = "komq3244";
  const storage = window.localStorage;

  function getUser() {
    let name = storage.getItem("current_user");
    if (!name) {
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
    const target = document.getElementById("targetUser").value;
    const amount = parseFloat(document.getElementById("amount").value);
    if (!target || isNaN(amount) || amount <= 0) return alert("올바르게 입력해주세요.");
    if (getBalance(user) < amount) return alert("잔액 부족!");

    setBalance(user, getBalance(user) - amount);
    setBalance(target, getBalance(target) + amount);
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
  }

  // 사용자 이름 입력 및 초기 UI 업데이트
  window.onload = function () {
    getUser();          // 이름 입력
    updateDisplay();    // 잔액 표시
    showHistory();      // 구매내역
    showGlobalLog();    // 글로벌 로그
    updateLeaderboard();// 순위
  };
</script>
