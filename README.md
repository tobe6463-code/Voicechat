<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>المحادثة الصوتية والمرئية</title>
    <style>
        body { font-family: Tahoma, sans-serif; background: #f9fbfd; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; margin: 0; }
        .container { background: #fff; padding: 25px; border-radius: 14px; box-shadow: 0 4px 20px rgba(0,0,0,0.08); text-align: center; width: 90%; max-width: 450px; border: 1px solid #e1e4e8; }
        h3 { color: #1a73e8; margin-bottom: 8px; }
        p { color: #5f6368; font-size: 13px; margin-bottom: 20px; }
        button { background: #1a73e8; color: white; border: none; padding: 12px 20px; font-size: 15px; border-radius: 25px; cursor: pointer; font-weight: bold; width: 100%; margin-top: 10px; transition: 0.3s; box-shadow: 0 2px 5px rgba(26,115,232,0.3); }
        button.stop { background: #d93025; box-shadow: 0 2px 5px rgba(217,48,37,0.3); }
        video { width: 100%; max-height: 200px; background: #000; border-radius: 8px; margin-top: 15px; display: none; object-fit: cover; }
        #status { margin-top: 12px; font-size: 13px; color: #202124; font-weight: bold; }
    </style>
</head>
<body>
    <div class="container">
        <h3>🎙️ المحادثة الصوتية والمرئية</h3>
        <p>تواصل مع المعلم بالصوت أو الفيديو مباشرة داخل المنصة</p>
        
        <button id="audioBtn" onclick="toggleAudio()">تشغيل المايك 🎙️</button>
        <button id="videoBtn" onclick="toggleVideo()" style="background: #0f9d58;">تشغيل الكاميرا 📹</button>
        
        <video id="webcam" autoplay playsinline></video>
        <div id="status">الأجهزة في وضع الاستعداد</div>
    </div>

    <script>
        let audioActive = false, videoActive = false;
        let mediaRecorder, audioChunks = [];
        let videoStream;

        async function toggleAudio() {
            const btn = document.getElementById('audioBtn');
            const status = document.getElementById('status');
            if (!audioActive) {
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                    mediaRecorder = new MediaRecorder(stream);
                    audioChunks = [];
                    mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                    mediaRecorder.onstop = () => {
                        const blob = new Blob(audioChunks, { type: 'audio/wav' });
                        status.innerHTML = `✅ تم حفظ الصوت <br><audio controls src="${URL.createObjectURL(blob)}" style="margin-top:8px; width:100%;"></audio>`;
                    };
                    mediaRecorder.start();
                    audioActive = true;
                    btn.textContent = "إيقاف المايك ⏹️";
                    btn.className = "stop";
                    status.textContent = "جاري التسجيل الصوتي...";
                } catch (e) {
                    alert("يرجى السماح بالوصول للمايك من إعدادات المتصفح.");
                }
            } else {
                mediaRecorder.stop();
                audioActive = false;
                btn.textContent = "تشغيل المايك 🎙️";
                btn.className = "";
            }
        }

        async function toggleVideo() {
            const btn = document.getElementById('videoBtn');
            const video = document.getElementById('webcam');
            const status = document.getElementById('status');
            if (!videoActive) {
                try {
                    videoStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
                    video.srcObject = videoStream;
                    video.style.display = "block";
                    videoActive = true;
                    btn.textContent = "إيقاف الكاميرا ⏹️";
                    btn.className = "stop";
                    status.textContent = "الكاميرا والصوت يعملان الآن مباشرة!";
                } catch (e) {
                    alert("يرجى السماح بالوصول للكاميرا من المتصفح.");
                }
            } else {
                videoStream.getTracks().forEach(track => track.stop());
                video.style.display = "none";
                videoActive = false;
                btn.textContent = "تشغيل الكاميرا 📹";
                btn.className = "";
                status.textContent = "تم إيقاف الكاميرا.";
            }
        }
    </script>
</body>
</html>
