
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <title>Ставки на пол ребенка</title>
  <style>
    body { font-family: sans-serif; max-width: 600px; margin: 0 auto; padding: 20px; }
    .bet-option { margin-bottom: 10px; }
    .result { margin-top: 20px; padding: 10px; border: 1px solid #ccc; }
    label { margin-right: 10px; }
  </style>
</head>
<body>
  <h1>Гендер-пати: угадай пол ребенка</h1>

  <form id="bet-form">
    <div class="bet-option">
      <label>
        <input type="radio" name="gender" value="boy" checked>
        Мальчик
      </label>
      <label>
        <input type="radio" name="gender" value="girl">
        Девочка
      </label>
    </div>

    <div class="bet-option">
      <label>
        Сумма «ставки»:
        <input type="number" id="stake-amount" value="100" min="1">
      </label>
    </div>

    <button type="submit">Сделать ставку</button>
  </form>

  <div class="result">
    <h2>Статистика</h2>
    <p>Всего ставок: <span id="total-bets">0</span></p>
    <p>На мальчика: <span id="boy-count">0</span></p>
    <p>На девочку: <span id="girl-count">0</span></p>

    <h3>Коэффициенты (decimal)</h3>
    <p>Мальчик: <span id="boy-odds">-</span></p>
    <p>Девочка: <span id="girl-odds">-</span></p>

    <h3>Ваш потенциальный «выигрыш»</h3>
    <p>
      Если угадаете: 
      <span id="payout">-</span>
    </p>
  </div>

  <script>
    // Простое хранилище «ставок» в памяти
    const bets = {
      boy: 0,
      girl: 0
    };

    const form = document.getElementById('bet-form');
    const totalBetsEl = document.getElementById('total-bets');
    const boyCountEl = document.getElementById('boy-count');
    const girlCountEl = document.getElementById('girl-count');
    const boyOddsEl = document.getElementById('boy-odds');
    const girlOddsEl = document.getElementById('girl-odds');
    const payoutEl = document.getElementById('payout');
    const stakeInput = document.getElementById('stake-amount');

    form.addEventListener('submit', (e) => {
      e.preventDefault();

      const formData = new FormData(form);
      const gender = formData.get('gender'); // 'boy' или 'girl'
      const stake = parseFloat(stakeInput.value) || 0;

      // Увеличиваем счетчик ставок
      if (gender === 'boy') {
        bets.boy += 1;
      } else if (gender === 'girl') {
        bets.girl += 1;
      }

      updateStatsAndOdds(gender, stake);
    });

    function updateStatsAndOdds(lastGender, stake) {
      const total = bets.boy + bets.girl;

      totalBetsEl.textContent = total;
      boyCountEl.textContent = bets.boy;
      girlCountEl.textContent = bets.girl;

      if (total === 0) {
        boyOddsEl.textContent = '-';
        girlOddsEl.textContent = '-';
        payoutEl.textContent = '-';
        return;
      }

      // Доли ставок
      const pBoy = bets.boy / total;
      const pGirl = bets.girl / total;

      // Минимальная защита от деления на 0
      const boyOdds = pBoy > 0 ? (1 / pBoy) : 0;
      const girlOdds = pGirl > 0 ? (1 / pGirl) : 0;

      // Можно округлять до двух знаков
      boyOddsEl.textContent = boyOdds ? boyOdds.toFixed(2) : '∞';
      girlOddsEl.textContent = girlOdds ? girlOdds.toFixed(2) : '∞';

      // Потенциальный «выигрыш» для последней ставки
      let currentOdds = lastGender === 'boy' ? boyOdds : girlOdds;
      if (!currentOdds || !stake) {
        payoutEl.textContent = '-';
      } else {
        const payout = stake * currentOdds; // ставка * коэффициент
        payoutEl.textContent = payout.toFixed(2);
      }
    }
  </script>
</body>
</html>
