<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Google Veo 3 Prompt Generator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
            box-sizing: border-box;
        }
        .container {
            background-color: #ffffff;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            padding: 40px;
            max-width: 800px;
            width: 100%;
            display: flex;
            flex-direction: column;
            gap: 25px;
        }
        .form-group {
            display: flex;
            flex-direction: column;
            gap: 8px;
            position: relative; /* Added for copy button positioning */
        }
        label {
            font-weight: 600;
            color: #333;
        }
        input[type="text"],
        textarea,
        select,
        input[type="file"] { /* Added file input to styling */
            padding: 12px 15px;
            border: 1px solid #e0e0e0;
            border-radius: 10px;
            font-size: 16px;
            color: #555;
            transition: all 0.2s ease-in-out;
            background-color: #f9f9f9;
        }
        input[type="text"]:focus,
        textarea:focus,
        select:focus,
        input[type="file"]:focus {
            outline: none;
            border-color: #6366f1; /* Indigo 500 */
            box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2); /* Indigo 500 with transparency */
        }
        textarea {
            min-height: 100px;
            resize: vertical;
        }
        button {
            background-color: #6366f1; /* Indigo 500 */
            color: #ffffff;
            padding: 14px 25px;
            border-radius: 10px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
            border: none;
            box-shadow: 0 5px 15px rgba(99, 102, 241, 0.3);
        }
        button:hover {
            background-color: #4f46e5; /* Indigo 600 */
            transform: translateY(-2px);
        }
        button:active {
            transform: translateY(0);
        }
        .output-section {
            background-color: #f0f4f8;
            border: 1px solid #d1d9e6;
            border-radius: 10px;
            padding: 20px;
            word-wrap: break-word;
            white-space: pre-wrap;
            font-size: 16px;
            color: #333;
            min-height: 80px;
            display: flex;
            align-items: center;
            justify-content: center;
            text-align: center;
            position: relative;
        }
        .copy-button {
            background-color: #10b981; /* Emerald 500 */
            color: #ffffff;
            padding: 8px 12px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: background-color 0.2s ease-in-out;
            border: none;
            position: absolute;
            top: 10px;
            right: 10px;
            opacity: 0;
            pointer-events: none;
        }
        .output-section:hover .copy-button,
        .form-group:hover .copy-button-ai { /* Added hover for AI description copy button */
            opacity: 1;
            pointer-events: auto;
        }
        .copy-button:hover,
        .copy-button-ai:hover { /* Added hover for AI description copy button */
            background-color: #059669; /* Emerald 600 */
        }
        .message-box {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background-color: #333;
            color: #fff;
            padding: 10px 20px;
            border-radius: 8px;
            z-index: 1000;
            opacity: 0;
            transition: opacity 0.3s ease-in-out;
            pointer-events: none;
        }
        .message-box.show {
            opacity: 1;
        }
        .image-upload-section {
            display: flex;
            flex-direction: column;
            gap: 15px;
            align-items: center;
            padding: 20px;
            border: 1px dashed #d1d9e6;
            border-radius: 10px;
            background-color: #f8fafd;
        }
        .image-preview {
            max-width: 100%;
            max-height: 200px;
            border-radius: 10px;
            object-fit: contain;
            display: none; /* Hidden by default */
        }
        .loading-spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #6366f1;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            display: none; /* Hidden by default */
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .button-group {
            display: flex;
            gap: 15px;
            justify-content: center;
        }
        .button-group button {
            flex-grow: 1;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-3xl font-bold text-center text-gray-800 mb-6">Google Veo 3 Prompt Generator</h1>

        <div class="image-upload-section">
            <label for="imageUpload" class="font-semibold text-gray-700">Unggah Gambar untuk Analisis:</label>
            <input type="file" id="imageUpload" accept="image/*" class="w-full">
            <img id="imagePreview" class="image-preview" alt="Pratinjau Gambar">
            <button id="analyzeImage" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-lg shadow-md">Analisis Gambar</button>
            <div id="loadingSpinner" class="loading-spinner"></div>
        </div>

        <div class="form-group">
            <label for="imageDescription">Deskripsi Gambar (Otomatis dari AI):</label>
            <textarea id="imageDescription" readonly placeholder="Deskripsi gambar yang dianalisis oleh AI akan muncul di sini."></textarea>
            <button class="copy-button copy-button-ai" id="copyImageDescription">Salin</button> <!-- New copy button -->
        </div>

        <div class="form-group">
            <label for="subject">Subjek/Objek Utama:</label>
            <input type="text" id="subject" placeholder="Contoh: seekor anjing golden retriever">
        </div>

        <div class="form-group">
            <label for="action">Aksi/Perilaku:</label>
            <input type="text" id="action" placeholder="Contoh: berlari di padang rumput">
        </div>

        <div class="form-group">
            <label for="setting">Pengaturan/Latar Belakang:</label>
            <input type="text" id="setting" placeholder="Contoh: dengan matahari terbit di cakrawala">
        </div>

        <div class="form-group">
            <label for="style">Gaya Visual:</label>
            <select id="style">
                <option value="">Pilih Gaya</option>
                <option value="fotorealistik">Fotorealistik</option>
                <option value="animasi 3D">Animasi 3D</option>
                <option value="lukisan cat air">Lukisan Cat Air</option>
                <option value="gaya kartun">Gaya Kartun</option>
                <option value="sinematik">Sinematik</option>
                <option value="minimalis">Minimalis</option>
                <option value="futuristik">Futuristik</option>
                <option value="vintage">Vintage</option>
                <option value="abstrak">Abstrak</option>
                <option value="surealis">Surealis</option>
                <option value="piksel art">Piksel Art</option>
                <option value="gaya komik/manga">Gaya Komik/Manga</option>
                <option value="sketsa pensil">Sketsa Pensil</option>
                <option value="lukisan minyak">Lukisan Minyak</option>
                <option value="gaya impresionis">Gaya Impresionis</option>
                <option value="gaya ekspresionis">Gaya Ekspresionis</option>
                <option value="gaya kubisme">Gaya Kubisme</option>
                <option value="gaya pop art">Gaya Pop Art</option>
                <option value="gaya cyberpunk">Gaya Cyberpunk</option>
                <option value="gaya steampunk">Gaya Steampunk</option>
                <option value="gaya fantasi gelap">Gaya Fantasi Gelap</option>
                <option value="gaya sci-fi">Gaya Sci-Fi</option>
                <option value="dokumenter">Dokumenter</option>
                <option value="slow motion">Slow Motion</option>
                <option value="timelapse">Timelapse</option>
                <option value="hyperlapse">Hyperlapse</option>
                <option value="stop motion">Stop Motion</option>
                <option value="gaya anime">Gaya Anime</option>
                <option value="gaya isometrik">Gaya Isometrik</option>
                <option value="gaya low poly">Gaya Low Poly</option>
            </select>
        </div>

        <div class="form-group">
            <label for="mood">Suasana/Emosi:</label>
            <select id="mood">
                <option value="">Pilih Suasana</option>
                <option value="ceria dan menyenangkan">Ceria dan Menyenangkan</option>
                <option value="tenang dan damai">Tenang dan Damai</option>
                <option value="dramatis dan intens">Dramatis dan Intens</option>
                <option value="misterius dan gelap">Misterius dan Gelap</option>
                <option value="inspiratif dan heroik">Inspiratif dan Heroik</option>
                <option value="sedih dan melankolis">Sedih dan Melankolis</option>
                <option value="tegang dan menegangkan">Tegang dan Menegangkan</option>
                <option value="romantis dan hangat">Romantis dan Hangat</option>
                <option value="komedi dan lucu">Komedi dan Lucu</option>
                <option value="horor dan menakutkan">Horor dan Menakutkan</option>
                <option value="fantasi dan magis">Fantasi dan Magis</option>
                <option value="petualangan dan eksplorasi">Petualangan dan Eksplorasi</option>
                <option value="futuristik dan canggih">Futuristik dan Canggih</option>
                <option value="retro dan nostalgia">Retro dan Nostalgia</option>
                <option value="surreal dan abstrak">Surreal dan Abstrak</option>
            </select>
        </div>

        <div class="form-group">
            <label for="lighting">Pencahayaan:</label>
            <select id="lighting">
                <option value="">Pilih Pencahayaan</option>
                <option value="cahaya alami">Cahaya Alami</option>
                <option value="cahaya keemasan senja">Cahaya Keemasan Senja</option>
                <option value="cahaya terang siang hari">Cahaya Terang Siang Hari</option>
                <option value="cahaya redup malam">Cahaya Redup Malam</option>
                <option value="cahaya dramatis">Cahaya Dramatis</option>
                <option value="cahaya lembut">Cahaya Lembut</option>
                <option value="cahaya neon">Cahaya Neon</option>
                <option value="cahaya studio">Cahaya Studio</option>
                <option value="cahaya remang-remang">Cahaya Remang-remang</option>
                <option value="cahaya latar (backlight)">Cahaya Latar (Backlight)</option>
                <option value="cahaya depan (frontlight)">Cahaya Depan (Frontlight)</option>
                <option value="cahaya samping (side light)">Cahaya Samping (Side Light)</option>
                <option value="cahaya kontras tinggi">Cahaya Kontras Tinggi</option>
                <option value="cahaya rendah (low key)">Cahaya Rendah (Low Key)</option>
                <option value="cahaya tinggi (high key)">Cahaya Tinggi (High Key)</option>
                <option value="cahaya pantulan">Cahaya Pantulan</option>
                <option value="cahaya buatan">Cahaya Buatan</option>
                <option value="cahaya lilin">Cahaya Lilin</option>
                <option value="cahaya api">Cahaya Api</option>
            </select>
        </div>

        <div class="form-group">
            <label for="camera_angle">Sudut Kamera:</label>
            <select id="camera_angle">
                <option value="">Pilih Sudut Kamera</option>
                <option value="sudut lebar (wide shot)">Sudut Lebar (Wide Shot)</option>
                <option value="medium shot">Medium Shot</option>
                <option value="close-up">Close-up</option>
                <option value="extreme close-up">Extreme Close-up</option>
                <option value="long shot">Long Shot</option>
                <option value="extreme long shot">Extreme Long Shot</option>
                <option value="over-the-shoulder shot">Over-the-Shoulder Shot</option>
                <option value="point of view (POV) shot">Point of View (POV) Shot</option>
                <option value="sudut tinggi (high angle)">Sudut Tinggi (High Angle)</option>
                <option value="sudut rendah (low angle)">Sudut Rendah (Low Angle)</option>
                <option value="dutch angle (canted angle)">Dutch Angle (Canted Angle)</option>
                <option value="bird's eye view (overhead shot)">Bird's Eye View (Overhead Shot)</option>
                <option value="worm's eye view">Worm's Eye View</option>
                <option value="tracking shot">Tracking Shot</option>
                <option value="dolly shot">Dolly Shot</option>
                <option value="zoom in">Zoom In</option>
                <option value="zoom out">Zoom Out</option>
                <option value="pan kiri ke kanan">Pan Kiri ke Kanan</option>
                <option value="tilt atas ke bawah">Tilt Atas ke Bawah</option>
            </select>
        </div>

        <div class="form-group">
            <label for="video_duration">Durasi Video (detik):</label>
            <select id="video_duration">
                <option value="">Pilih Durasi</option>
                <option value="3 detik">3 detik</option>
                <option value="5 detik">5 detik</option>
                <option value="10 detik">10 detik</option>
                <option value="15 detik">15 detik</option>
                <option value="20 detik">20 detik</option>
                <option value="30 detik">30 detik</option>
                <option value="45 detik">45 detik</option>
                <option value="60 detik">60 detik</option>
            </select>
        </div>

        <div class="form-group">
            <label for="aspect_ratio">Rasio Aspek:</label>
            <select id="aspect_ratio">
                <option value="">Pilih Rasio Aspek</option>
                <option value="16:9 (horizontal)">16:9 (Horizontal)</option>
                <option value="9:16 (vertikal)">9:16 (Vertikal)</option>
                <option value="1:1 (persegi)">1:1 (Persegi)</option>
                <option value="4:3 (standar)">4:3 (Standar)</option>
                <option value="21:9 (ultrawide)">21:9 (Ultrawide)</option>
            </select>
        </div>

        <div class="form-group">
            <label for="motion_type">Jenis Gerakan/Pergerakan:</label>
            <select id="motion_type">
                <option value="">Pilih Jenis Gerakan</option>
                <option value="gerakan lambat (slow motion)">Gerakan Lambat (Slow Motion)</option>
                <option value="gerakan cepat (fast motion)">Gerakan Cepat (Fast Motion)</option>
                <option value="gerakan dinamis">Gerakan Dinamis</option>
                <option value="gerakan statis">Gerakan Statis</option>
                <option value="panoramik">Panoramik</option>
                <option value="tilt">Tilt</option>
                <option value="zoom">Zoom</option>
                <option value="dolly">Dolly</option>
                <option value="track">Track</option>
                <option value="stabil">Stabil</option>
                <option value="goyangan kamera (handheld)">Goyangan Kamera (Handheld)</option>
                <option value="transisi mulus">Transisi Mulus</option>
                <option value="transisi cepat">Transisi Cepat</option>
            </select>
        </div>

        <div class="form-group">
            <label for="additional_details">Detail Tambahan (Manual):</label>
            <textarea id="additional_details" placeholder="Contoh: bulu anjing berkilauan, daun berguguran"></textarea>
        </div>

        <div class="button-group">
            <button id="generatePrompt">Hasilkan Prompt</button>
            <button id="resetForm" class="bg-gray-500 hover:bg-gray-600">Reset Formulir</button>
        </div>

        <div class="output-section">
            <p id="generatedPrompt">Prompt Anda akan muncul di sini.</p>
            <button class="copy-button" id="copyPrompt">Salin</button>
        </div>
    </div>

    <div id="messageBox" class="message-box"></div>

    <script>
        // Fungsi untuk menampilkan pesan sementara
        function showMessage(message) {
            const messageBox = document.getElementById('messageBox');
            messageBox.textContent = message;
            messageBox.classList.add('show');
            setTimeout(() => {
                messageBox.classList.remove('show');
            }, 2000);
        }

        // Fungsi untuk menghasilkan prompt yang lebih naratif
        function generateCombinedPrompt() {
            const subject = document.getElementById('subject').value.trim();
            const action = document.getElementById('action').value.trim();
            const setting = document.getElementById('setting').value.trim();
            const style = document.getElementById('style').value.trim();
            const mood = document.getElementById('mood').value.trim();
            const lighting = document.getElementById('lighting').value.trim();
            const cameraAngle = document.getElementById('camera_angle').value.trim();
            const videoDuration = document.getElementById('video_duration').value.trim();
            const aspectRatio = document.getElementById('aspect_ratio').value.trim();
            const motionType = document.getElementById('motion_type').value.trim();
            const additionalDetails = document.getElementById('additional_details').value.trim();
            const imageDescription = document.getElementById('imageDescription').value.trim();

            let promptNarrative = [];

            // Bagian 1: Deskripsi Utama (dari gambar atau input manual)
            if (imageDescription) {
                promptNarrative.push(`Visual awal: ${imageDescription}.`);
            } else if (subject || action || setting) {
                let mainScene = [];
                if (subject) mainScene.push(subject);
                if (action) mainScene.push(action);
                if (setting) mainScene.push(`di ${setting}`);
                if (mainScene.length > 0) {
                    promptNarrative.push(`Skenario utama menampilkan ${mainScene.join(' ')}.`);
                }
            }

            // Bagian 2: Gaya dan Suasana
            let styleMoodSentence = [];
            if (style) styleMoodSentence.push(`Dengan gaya visual ${style}`);
            if (mood) styleMoodSentence.push(`dan suasana ${mood}`);
            if (styleMoodSentence.length > 0) {
                promptNarrative.push(styleMoodSentence.join(' ') + '.');
            }

            // Bagian 3: Teknis Kamera dan Pencahayaan
            let technicalSentence = [];
            if (lighting) technicalSentence.push(`Pencahayaan: ${lighting}`);
            if (cameraAngle) technicalSentence.push(`Sudut kamera: ${cameraAngle}`);
            if (technicalSentence.length > 0) {
                promptNarrative.push(technicalSentence.join('. ') + '.');
            }

            // Bagian 4: Durasi, Rasio Aspek, dan Gerakan
            let productionDetails = [];
            if (videoDuration) productionDetails.push(`Durasi video: ${videoDuration}`);
            if (aspectRatio) productionDetails.push(`Rasio aspek: ${aspectRatio}`);
            if (motionType) productionDetails.push(`Pergerakan: ${motionType}`);
            if (productionDetails.length > 0) {
                promptNarrative.push(productionDetails.join('. ') + '.');
            }

            // Bagian 5: Detail Tambahan
            if (additionalDetails) {
                promptNarrative.push(`Detail tambahan yang perlu diperhatikan: ${additionalDetails}.`);
            }

            let finalPrompt = promptNarrative.join('\n\n'); // Menggunakan double newline untuk paragraf

            if (finalPrompt.trim() === '') {
                finalPrompt = "Silakan isi beberapa bidang atau unggah gambar untuk menghasilkan prompt yang terperinci.";
            } else {
                // Kapitalisasi huruf pertama dari prompt keseluruhan
                finalPrompt = finalPrompt.charAt(0).toUpperCase() + finalPrompt.slice(1);
            }

            document.getElementById('generatedPrompt').textContent = finalPrompt;
        }

        // Event listener untuk tombol "Hasilkan Prompt"
        document.getElementById('generatePrompt').addEventListener('click', generateCombinedPrompt);

        // Fungsi untuk menyalin prompt ke clipboard
        document.getElementById('copyPrompt').addEventListener('click', () => {
            const promptText = document.getElementById('generatedPrompt').textContent;
            if (promptText === "Prompt Anda akan muncul di sini." || promptText === "Silakan isi beberapa bidang atau unggah gambar untuk menghasilkan prompt yang terperinci.") {
                showMessage("Tidak ada prompt untuk disalin.");
                return;
            }
            // Menggunakan document.execCommand untuk kompatibilitas iframe
            const textarea = document.createElement('textarea');
            textarea.value = promptText;
            document.body.appendChild(textarea);
            textarea.select();
            try {
                const successful = document.execCommand('copy');
                if (successful) {
                    showMessage("Prompt disalin!");
                } else {
                    showMessage("Gagal menyalin prompt.");
                }
            } catch (err) {
                showMessage("Gagal menyalin prompt: " + err);
            }
            document.body.removeChild(textarea);
        });

        // Fungsi untuk menyalin deskripsi gambar AI ke clipboard
        document.getElementById('copyImageDescription').addEventListener('click', () => {
            const imageDescriptionText = document.getElementById('imageDescription').value;
            if (imageDescriptionText === "" || imageDescriptionText === "Menganalisis gambar..." || imageDescriptionText === "Gagal menganalisis gambar. Coba lagi." || imageDescriptionText === "Terjadi kesalahan saat menganalisis gambar.") {
                showMessage("Tidak ada deskripsi gambar untuk disalin.");
                return;
            }
            const textarea = document.createElement('textarea');
            textarea.value = imageDescriptionText;
            document.body.appendChild(textarea);
            textarea.select();
            try {
                const successful = document.execCommand('copy');
                if (successful) {
                    showMessage("Deskripsi gambar disalin!");
                } else {
                    showMessage("Gagal menyalin deskripsi gambar.");
                }
            } catch (err) {
                showMessage("Gagal menyalin deskripsi gambar: " + err);
            }
            document.body.removeChild(textarea);
        });


        // Fungsi untuk mereset formulir
        document.getElementById('resetForm').addEventListener('click', () => {
            document.getElementById('imageUpload').value = '';
            document.getElementById('imagePreview').src = '';
            document.getElementById('imagePreview').style.display = 'none';
            document.getElementById('imageDescription').value = '';
            document.getElementById('subject').value = '';
            document.getElementById('action').value = '';
            document.getElementById('setting').value = '';
            document.getElementById('style').value = '';
            document.getElementById('mood').value = '';
            document.getElementById('lighting').value = '';
            document.getElementById('camera_angle').value = '';
            document.getElementById('video_duration').value = ''; // Reset new fields
            document.getElementById('aspect_ratio').value = '';   // Reset new fields
            document.getElementById('motion_type').value = '';    // Reset new fields
            document.getElementById('additional_details').value = '';
            document.getElementById('generatedPrompt').textContent = "Prompt Anda akan muncul di sini.";
            showMessage("Formulir direset.");
            base64ImageData = ''; // Clear base64 image data
        });

        // Image Upload and Analysis Logic
        const imageUpload = document.getElementById('imageUpload');
        const imagePreview = document.getElementById('imagePreview');
        const analyzeImageButton = document.getElementById('analyzeImage');
        const loadingSpinner = document.getElementById('loadingSpinner');
        const imageDescriptionTextarea = document.getElementById('imageDescription');

        let base64ImageData = ''; // To store the base64 string of the image

        imageUpload.addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    imagePreview.src = e.target.result;
                    imagePreview.style.display = 'block'; // Show the image preview
                    base64ImageData = e.target.result.split(',')[1]; // Get only the base64 part
                };
                reader.readAsDataURL(file);
            } else {
                imagePreview.src = '';
                imagePreview.style.display = 'none';
                base64ImageData = '';
            }
        });

        analyzeImageButton.addEventListener('click', async () => {
            if (!base64ImageData) {
                showMessage("Silakan unggah gambar terlebih dahulu.");
                return;
            }

            loadingSpinner.style.display = 'block'; // Show spinner
            analyzeImageButton.disabled = true; // Disable button during analysis
            imageDescriptionTextarea.value = 'Menganalisis gambar...'; // Clear previous description

            try {
                // Prompt AI untuk menghasilkan deskripsi yang lebih naratif dan berorientasi video
                const prompt = "Sebagai seorang penulis naskah atau sutradara, jelaskan gambar ini secara detail dan naratif. Fokus pada subjek utama, aksi, latar belakang, suasana hati keseluruhan, gaya visual yang paling cocok, dan potensi pergerakan kamera atau elemen dalam video. Gunakan bahasa Indonesia yang kaya dan deskriptif, seolah-olah ini adalah bagian dari naskah film.";
                let chatHistory = [];
                chatHistory.push({ role: "user", parts: [{ text: prompt }] });
                const payload = {
                    contents: [
                        {
                            role: "user",
                            parts: [
                                { text: prompt },
                                {
                                    inlineData: {
                                        mimeType: "image/png", // Assuming PNG, adjust if needed
                                        data: base64ImageData
                                    }
                                }
                            ]
                        }
                    ],
                };
                const apiKey = ""; // If you want to use models other than gemini-2.0-flash or imagen-3.0-generate-002, provide an API key here. Otherwise, leave this as-is.
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const result = await response.json();

                if (result.candidates && result.candidates.length > 0 &&
                    result.candidates[0].content && result.candidates[0].content.parts &&
                    result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    imageDescriptionTextarea.value = text;
                    showMessage("Gambar berhasil dianalisis!");
                    generateCombinedPrompt(); // Update the main prompt after analysis
                } else {
                    imageDescriptionTextarea.value = "Gagal menganalisis gambar. Coba lagi.";
                    showMessage("Gagal menganalisis gambar.");
                }
            } catch (error) {
                console.error("Error analyzing image:", error);
                imageDescriptionTextarea.value = "Terjadi kesalahan saat menganalisis gambar.";
                showMessage("Terjadi kesalahan saat menganalisis gambar.");
            } finally {
                loadingSpinner.style.display = 'none'; // Hide spinner
                analyzeImageButton.disabled = false; // Enable button
            }
        });
    </script>
</body>
</html>
