<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>哈利波特 FlashCard 遊戲</title>
  <style>
    body {
      margin: 0;
      font-family: "Noto Sans TC", sans-serif;
      background: linear-gradient(135deg, #1a1a2e, #16213e);
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      overflow: hidden;
    }

    .container {
      width: 90%;
      max-width: 700px;
      text-align: center;
    }

    h1 {
      font-size: 2.5rem;
      margin-bottom: 10px;
      letter-spacing: 2px;
    }

    .subtitle {
      opacity: 0.8;
      margin-bottom: 30px;
    }

    .card {
      background: rgba(255,255,255,0.08);
      backdrop-filter: blur(8px);
      border-radius: 24px;
      padding: 40px;
      min-height: 250px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      box-shadow: 0 10px 30px rgba(0,0,0,0.4);
      transition: transform 0.4s ease;
      position: relative;
    }

    .card:hover {
      transform: translateY(-4px);
    }

    .question {
      font-size: 1.5rem;
      line-height: 1.8;
      margin-bottom: 20px;
    }

    .options {
      display: flex;
      flex-direction: column;
      gap: 12px;
      width: 100%;
      margin-top: 20px;
    }

    .option-btn {
      background: rgba(255,255,255,0.12);
      color: white;
      border: 1px solid rgba(255,255,255,0.15);
      padding: 12px;
      border-radius: 14px;
      cursor: pointer;
      transition: 0.25s;
      font-size: 1rem;
    }

    .option-btn:hover {
      background: rgba(255,255,255,0.2);
    }

    .correct {
      background: #2ecc71 !important;
    }

    .wrong {
      background: #e74c3c !important;
    }

    .answer {
      font-size: 1.3rem;
      color: #ffd369;
      display: none;
      margin-top: 10px;
    }

    .controls {
      margin-top: 25px;
      display: flex;
      justify-content: center;
      gap: 15px;
      flex-wrap: wrap;
    }

    button {
      border: none;
      border-radius: 999px;
      padding: 14px 24px;
      font-size: 1rem;
      cursor: pointer;
      transition: all 0.25s ease;
      font-weight: bold;
    }

    button:hover {
      transform: scale(1.05);
    }

    .show-btn {
      background: #ffd369;
      color: #222;
    }

    .next-btn {
      background: #4ecdc4;
      color: white;
    }

    .shuffle-btn {
      background: #ff6b6b;
      color: white;
    }

    .counter {
      margin-top: 18px;
      opacity: 0.8;
    }

    .sparkle {
      position: absolute;
      width: 6px;
      height: 6px;
      background: white;
      border-radius: 50%;
      animation: float 6s linear infinite;
      opacity: 0.5;
    }

    @keyframes float {
      from {
        transform: translateY(100vh);
      }
      to {
        transform: translateY(-100vh);
      }
    }

    @media (max-width: 600px) {
      .card {
        padding: 24px;
      }

      .question {
        font-size: 1.2rem;
      }

      .answer {
        font-size: 1.1rem;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>⚡ 哈利波特 FlashCard ⚡</h1>
    <div class="subtitle">QuizGPT 魔法知識挑戰</div>

    <div class="card">
      <div class="question" id="question"></div>
      <div id="options" class="options"></div>
      <div class="answer" id="answer"></div>
    </div>

    <div class="controls">
      
      <button class="next-btn" onclick="nextCard()">下一題</button>
      <button class="shuffle-btn" onclick="shuffleCards()">隨機洗牌</button>
    </div>

    <div class="counter" id="counter"></div>
    <div class="counter" id="scoreBoard">目前分數：0 分</div>
  </div>

  <script>
    const flashcards = [
      {
        q: '哈利波特額頭上的傷疤是什麼形狀？',
        options: ['星形', '閃電形', '圓形', '火焰形'],
        a: '閃電形'
      },
      {
        q: '哈利就讀的魔法學校叫什麼名字？',
        options: ['波巴洞', '德姆蘭', '霍格華茲', '魔法塔'],
        a: '霍格華茲'
      },
      {
        q: '哈利的兩位摯友是誰？',
        options: ['奈威與露娜', '榮恩與妙麗', '石內卜與海格', '跩哥與金妮'],
        a: '榮恩與妙麗'
      },
      {
        q: '霍格華茲的校長是誰？',
        options: ['石內卜', '海格', '鄧不利多', '麥教授'],
        a: '鄧不利多'
      },
      {
        q: '哈利所屬的學院是哪一個？',
        options: ['史萊哲林', '赫夫帕夫', '雷文克勞', '葛來分多'],
        a: '葛來分多'
      },
      {
        q: '哈利的貓頭鷹叫什麼名字？',
        options: ['嘿美', '斑斑', '歪腿', '娜吉妮'],
        a: '嘿美'
      },
      {
        q: '誰是混血王子？',
        options: ['佛地魔', '路平', '石內卜', '天狼星'],
        a: '石內卜'
      },
      {
        q: '哈利的護法是什麼動物？',
        options: ['狼', '獅子', '牡鹿', '老鷹'],
        a: '牡鹿'
      },
      {
        q: '佛地魔的本名是什麼？',
        options: ['湯姆・瑞斗', '賽佛勒斯・石內卜', '詹姆・波特', '阿不思・鄧不利多'],
        a: '湯姆・瑞斗'
      },
      {
        q: '死神的聖物共有哪三樣？',
        options: ['魔杖、掃帚、斗篷', '接骨木魔杖、重生石、隱形斗篷', '戒指、項鍊、王冠', '地圖、斗篷、魔杖'],
        a: '接骨木魔杖、重生石、隱形斗篷'
      }
    ];

    let current = 0;
    let score = 0;

    const questionEl = document.getElementById('question');
    const answerEl = document.getElementById('answer');
    const counterEl = document.getElementById('counter');

    const optionsEl = document.getElementById('options');

    function renderCard() {
      const card = flashcards[current];
      questionEl.textContent = card.q;
      answerEl.style.display = 'none';
      answerEl.textContent = '正確答案：' + card.a;
      optionsEl.innerHTML = '';

      card.options.forEach(option => {
        const btn = document.createElement('button');
        btn.className = 'option-btn';
        btn.textContent = option;

        btn.onclick = () => {
          const allBtns = document.querySelectorAll('.option-btn');
          allBtns.forEach(b => b.disabled = true);

          if(option === card.a) {
            btn.classList.add('correct');
            score += 5;
          } else {
            btn.classList.add('wrong');
            allBtns.forEach(b => {
              if(b.textContent === card.a) {
                b.classList.add('correct');
              }
            });
          }

          document.getElementById('scoreBoard').textContent = `目前分數：${score} 分`;
          answerEl.style.display = 'block';
        };

        optionsEl.appendChild(btn);
      });

      counterEl.textContent = `第 ${current + 1} / ${flashcards.length} 題`;
    }

    

    function nextCard() {
      current++;

      if(current >= flashcards.length) {
        questionEl.innerHTML = '🎉 測驗完成！';
        optionsEl.innerHTML = '';
        answerEl.style.display = 'block';
        answerEl.innerHTML = `你的總分是：<strong>${score}</strong> / ${flashcards.length * 5} 分`;
        counterEl.textContent = '魔法測驗結束';
        return;
      }

      renderCard();
    }

    function shuffleCards() {
      score = 0;
      document.getElementById('scoreBoard').textContent = '目前分數：0 分';
      for (let i = flashcards.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [flashcards[i], flashcards[j]] = [flashcards[j], flashcards[i]];
      }
      current = 0;
      renderCard();
    }

    renderCard();

    for (let i = 0; i < 30; i++) {
      const star = document.createElement('div');
      star.classList.add('sparkle');
      star.style.left = Math.random() * 100 + 'vw';
      star.style.animationDuration = (Math.random() * 5 + 3) + 's';
      star.style.opacity = Math.random();
      document.body.appendChild(star);
    }
  </script>
</body>
</html>
