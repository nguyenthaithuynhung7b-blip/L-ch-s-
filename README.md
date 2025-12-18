<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Hành Trình Cách Mạng Tư Sản</title>
  <style>
    body {
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
      background: linear-gradient(135deg, #1e3c72, #2a5298);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
    }
    .game {
      background: rgba(255,255,255,0.12);
      backdrop-filter: blur(8px);
      width: 100%;
      max-width: 420px;
      border-radius: 20px;
      padding: 20px;
      box-shadow: 0 20px 40px rgba(0,0,0,0.3);
    }
    h1 { text-align: center; margin-top: 0; }
    .story { font-size: 0.95rem; margin-bottom: 12px; }
    .question { font-size: 1.1rem; margin: 15px 0; }
    .answers button {
      width: 100%;
      margin-bottom: 10px;
      padding: 12px;
      border: none;
      border-radius: 14px;
      font-size: 1rem;
      background: #f1f3f9;
      color: #000;
    }
    .answers button.correct { background: #4caf50; color: #fff; }
    .answers button.wrong { background: #f44336; color: #fff; }
    .top {
      display: flex;
      justify-content: space-between;
      font-size: 0.9rem;
    }
    .hidden { display: none; }
    input {
      width: 100%;
      padding: 10px;
      border-radius: 10px;
      border: none;
      margin-bottom: 10px;
    }
    .leaderboard li { font-size: 0.9rem; margin-bottom: 4px; }
  </style>
</head>
<body>
  <div class="game">
    <h1>⚔️ Hành Trình Cách Mạng</h1>

    <div id="start">
      <p class="story">
        Bạn là một nhà sử học trẻ, du hành qua các cuộc cách mạng tư sản tiêu biểu
        ở Anh, Bắc Mỹ và Pháp. Mỗi quyết định đúng sẽ giúp lịch sử tiến lên.
      </p>
      <input id="playerName" placeholder="Nhập tên người chơi" />
      <button onclick="startGame()">Bắt đầu hành trình</button>
    </div>

    <div id="quiz" class="hidden">
      <div class="top">
        <span id="progress"></span>
        <span>⭐ <span id="score">0</span></span>
      </div>
      <div class="question" id="question"></div>
      <div class="answers" id="answers"></div>
    </div>

    <div id="end" class="hidden">
      <h2>Kết thúc hành trình</h2>
      <p id="finalScore"></p>
      <h3>🏆 Bảng thành tích</h3>
      <ul class="leaderboard" id="leaderboard"></ul>
      <button onclick="location.reload()">Chơi lại</button>
    </div>
  </div>

<script>
const allQuestions = [
  {q:"Mục tiêu chung của các cuộc cách mạng tư sản là gì?", a:["Lật đổ chế độ phong kiến, thiết lập nền thống trị của giai cấp tư sản, mở đường cho chủ nghĩa tư bản phát triển","Lật đổ chế độ nô lệ","Thiết lập chế độ xã hội chủ nghĩa","Mở rộng lãnh thổ quốc gia"], c:0},
  {q:"Nhiệm vụ dân tộc của cách mạng tư sản là gì?", a:["Xóa bỏ tình trạng phong kiến cát cứ, đánh đuổi thực dân, thống nhất thị trường dân tộc","Giải quyết vấn đề ruộng đất triệt để","Xây dựng nhà nước xã hội chủ nghĩa","Phát triển giáo dục"], c:0},
  {q:"Nhiệm vụ dân chủ của cách mạng tư sản là gì?", a:["Xóa bỏ chế độ phong kiến chuyên chế, xác lập nền dân chủ tư sản","Thiết lập nền quân chủ chuyên chế","Thực hiện bình đẳng tuyệt đối","Xóa bỏ giai cấp"], c:0},
  {q:"Cách mạng tư sản Anh lật đổ chế độ phong kiến dưới triều đại nào?", a:["Vua Sác-lơ I (triều Stuart)","Vua Lu-i XVI","Nữ hoàng Elizabeth I","Vua Na-pô-lê-ông"], c:0},
  {q:"Kết quả của Cách mạng tư sản Anh là gì?", a:["Thiết lập chế độ quân chủ lập hiến","Thiết lập chế độ cộng hòa","Duy trì chế độ phong kiến","Thiết lập đế quốc"], c:0},
  {q:"Chiến tranh giành độc lập của 13 thuộc địa Anh diễn ra ở đâu?", a:["Bắc Mỹ","Châu Âu","Nam Mỹ","Châu Phi"], c:0},
  {q:"Mục tiêu của Chiến tranh giành độc lập của 13 thuộc địa Anh ở Bắc Mỹ là gì?", a:["Lật đổ ách thống trị của thực dân Anh, giành độc lập dân tộc","Thiết lập chế độ phong kiến mới","Mở rộng thuộc địa","Xây dựng chủ nghĩa xã hội"], c:0},
  {q:"Kết quả của Chiến tranh giành độc lập của 13 thuộc địa Anh là gì?", a:["Thành lập Hợp chủng quốc Hoa Kỳ","Thành lập Canada","Duy trì thuộc địa Anh","Thành lập Liên bang Bắc Mỹ"], c:0},
  {q:"Giai cấp lãnh đạo Chiến tranh giành độc lập của 13 thuộc địa Anh là ai?", a:["Tư sản và chủ nô","Nông dân","Công nhân","Quý tộc phong kiến"], c:0},
  {q:"Cách mạng tư sản Pháp đã lật đổ chế độ phong kiến dưới triều đại nào?", a:["Vua Lu-i XVI","Vua Sác-lơ I","Vua Phơ-ri-đrích II","Vua Vin-hem"], c:0},
  {q:"Mục tiêu của Cách mạng tư sản Pháp là gì?", a:["Lật đổ chế độ phong kiến, thiết lập nền thống trị của giai cấp tư sản","Giành độc lập dân tộc","Xây dựng xã hội không giai cấp","Mở rộng lãnh thổ"], c:0},
  {q:"Kết quả của Cách mạng tư sản Pháp là gì?", a:["Lật đổ chế độ phong kiến, thiết lập chế độ cộng hòa","Thiết lập quân chủ lập hiến","Thiết lập đế quốc","Duy trì phong kiến"], c:0},
  {q:"Giai cấp lãnh đạo Cách mạng tư sản Pháp là ai?", a:["Giai cấp tư sản","Nông dân","Công nhân","Quý tộc"], c:0},
  {q:"Động lực chính của các cuộc cách mạng tư sản là ai?", a:["Quần chúng nhân dân","Giai cấp quý tộc","Vua chúa","Giáo sĩ"], c:0},
  {q:"Ý nghĩa của Cách mạng tư sản Anh là gì?", a:["Mở đường cho kinh tế tư bản chủ nghĩa phát triển","Giải phóng dân tộc","Xóa bỏ giai cấp","Thống nhất châu Âu"], c:0},
  {q:"Ý nghĩa của Chiến tranh giành độc lập của 13 thuộc địa Anh ở Bắc Mỹ là gì?", a:["Giải phóng nhân dân Bắc Mỹ, cổ vũ phong trào đấu tranh giành độc lập","Xóa bỏ chế độ phong kiến châu Âu","Thống nhất thế giới","Thiết lập CNXH"], c:0},
  {q:"Ý nghĩa của Cách mạng tư sản Pháp là gì?", a:["Lật đổ và xóa bỏ tàn dư phong kiến, mở ra thời đại thắng lợi của CNTB","Thiết lập quân chủ chuyên chế","Giữ nguyên trật tự phong kiến","Chấm dứt cách mạng tư sản"], c:0},
  {q:"Điểm chung về kết quả của các cuộc cách mạng tư sản là gì?", a:["Lật đổ chế độ phong kiến, thiết lập chế độ tư bản chủ nghĩa","Thiết lập xã hội không giai cấp","Giải phóng hoàn toàn người lao động","Xây dựng nhà nước xã hội chủ nghĩa"], c:0},
  {q:"Điểm chung về ý nghĩa của các cuộc cách mạng tư sản là gì?", a:["Thúc đẩy lực lượng sản xuất phát triển, xác lập quan hệ sản xuất tư bản chủ nghĩa","Chấm dứt chiến tranh trên thế giới","Xóa bỏ bóc lột","Thiết lập hòa bình vĩnh viễn"], c:0},
  {q:"Nguyên tắc tổ chức nhà nước phổ biến sau các cuộc cách mạng tư sản là gì?", a:["Tam quyền phân lập","Tập quyền phong kiến","Quân chủ chuyên chế","Độc quyền lãnh đạo"], c:0}
];

let questions = [];
let current = 0;
let score = 0;
let player = "";

function startGame() {
  player = document.getElementById('playerName').value || 'Người chơi';
  questions = [...allQuestions].sort(() => Math.random() - 0.5).slice(0,10);
  document.getElementById('start').classList.add('hidden');
  document.getElementById('quiz').classList.remove('hidden');
  showQuestion();
}

function showQuestion() {
  const q = questions[current];
  document.getElementById('progress').textContent = `Câu ${current+1}/10`;
  document.getElementById('question').textContent = q.q;
  document.getElementById('score').textContent = score;
  const answers = document.getElementById('answers');
  answers.innerHTML = '';
  q.a.forEach((text, i) => {
    const btn = document.createElement('button');
    const label = String.fromCharCode(65 + i); // A, B, C, D
    btn.textContent = label + '. ' + text;
    btn.onclick = () => selectAnswer(btn, i === q.c);
    answers.appendChild(btn);
  });
}

function selectAnswer(btn, correct) {
  if (correct) { score++; btn.classList.add('correct'); }
  else btn.classList.add('wrong');
  setTimeout(() => {
    current++;
    if (current < questions.length) showQuestion();
    else endGame();
  }, 600);
}

function endGame() {
  document.getElementById('quiz').classList.add('hidden');
  document.getElementById('end').classList.remove('hidden');
  document.getElementById('finalScore').textContent = `${player} đạt ${score}/10 điểm`;

  const board = JSON.parse(localStorage.getItem('leaderboard') || '[]');
  board.push({player, score});
  board.sort((a,b)=>b.score-a.score);
  localStorage.setItem('leaderboard', JSON.stringify(board.slice(0,5)));

  const ul = document.getElementById('leaderboard');
  ul.innerHTML = '';
  board.slice(0,5).forEach(e=>{
    const li = document.createElement('li');
    li.textContent = `${e.player}: ${e.score} điểm`;
    ul.appendChild(li);
  });
}
</script>
</body>
</html>
