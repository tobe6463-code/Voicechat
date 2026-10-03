<div style="background: #ffffff; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.08); text-align: center; max-width: 450px; margin: auto; font-family: Tahoma, sans-serif; border: 1px solid #e1e4e8;">
    <h3 style="color: #1a73e8; margin-bottom: 10px;">🎙️ المحادثة الصوتية التفاعلية</h3>
    <p style="color: #5f6368; font-size: 14px; margin-bottom: 20px;">اضغط على الزر أدناه لتفعيل الميكروفون والتحدث مباشرة داخل المنصة:</p>
    
    <button id="micActionBtn" onclick="toggleAudio()" style="background: #1a73e8; color: white; border: none; padding: 12px 25px; font-size: 16px; border-radius: 30px; cursor: pointer; font-weight: bold; box-shadow: 0 2px 5px rgba(26,115,232,0.3); transition: 0.3s;">
        تشغيل المايك 🎙️
    </button>
    
    <div id="statusText" style="margin-top: 15px; font-size: 14px; color: #202124; font-weight: bold;">الميكروفون في وضع الاستعداد</div>
</div>

<script>
    let isRecording = false;
    let mediaRecorder;
    let audioChunks = [];

    async function toggleAudio() {
        const btn = document.getElementById('micActionBtn');
        const status = document.getElementById('statusText');

        if (!isRecording) {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];

                mediaRecorder.ondataavailable = event => {
                    audioChunks.push(event.data);
                };

                mediaRecorder.onstop = () => {
                    const audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
                    const audioUrl = URL.createObjectURL(audioBlob);
                    status.innerHTML = `✅ تم تسلم صوتك بنجاح! <br><audio controls src="${audioUrl}" style="margin-top:10px; width:100%;"></audio>`;
                };

                mediaRecorder.start();
                isRecording = true;
                btn.textContent = "إيقاف التسجيل ⏹️";
                btn.style.background = "#d93025";
                status.textContent = "جاري الاستماع والتسجيل الآن...";
            } catch (err) {
                alert("يرجى السماح للمتصفح بالوصول إلى الميكروفون من إعدادات المتصفح.");
            }
        } else {
            mediaRecorder.stop();
            isRecording = false;
            btn.textContent = "تشغيل المايك 🎙️";
            btn.style.background = "#1a73e8";
        }
    }
</script>
