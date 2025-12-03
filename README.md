<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kuis Nahwu: Na'at & Man'ut</title>
    
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #e6f0f8; /* Warna latar belakang lembut */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .container {
            background-color: #ffffff;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
            width: 90%;
            max-width: 650px;
        }

        h1 {
            text-align: center;
            color: #007bff;
            margin-bottom: 25px;
            border-bottom: 2px solid #007bff;
            padding-bottom: 10px;
        }

        h2 {
            color: #333;
            font-size: 1.5em;
        }

        #question-text {
            font-size: 1.2em;
            margin-bottom: 25px;
            font-weight: bold;
            color: #555;
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 12px;
            margin-bottom: 20px;
        }

        .option-button {
            background-color: #f1f7fe;
            color: #444;
            border: 2px solid #a8c4e0;
            padding: 15px;
            text-align: left;
            border-radius: 10px;
            cursor: pointer;
            transition: background-color 0.3s, border-color 0.3s, transform 0.1s;
            font-size: 1em;
        }

        .option-button:hover:not(.disabled) {
            background-color: #d8e6f7;
            border-color: #007bff;
        }

        /* Status Jawaban */
        .correct {
            background-color: #28a745 !important;
            color: white;
            border-color: #1e7e34 !important;
        }

        .incorrect {
            background-color: #dc3545 !important;
            color: white;
            border-color: #c82333 !important;
        }

        .disabled {
            pointer-events: none;
            opacity: 0.8;
        }

        /* Tombol Lanjut/Restart */
        button {
            width: 100%;
            padding: 12px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            font-size: 1.1em;
            font-weight: bold;
            transition: background-color 0.3s;
            margin-top: 15px;
        }

        button:hover:not(:disabled) {
            background-color: #0056b3;
        }

        button:disabled {
            background-color: #a0c4ff;
            cursor: not-allowed;
        }

        /* Ranking List Styling */
        .ranking-container {
            margin-top: 20px;
            border: 1px solid #ced4da;
            border-radius: 10px;
            padding: 15px;
            background-color: #f8f9fa;
        }

        .rank-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 0;
            border-bottom: 1px dashed #ced4da;
            font-size: 1em;
        }

        .rank-item:nth-child(1) {
            font-weight: bold;
            color: #ffc107; /* Emas untuk juara 1 */
        }
        .rank-item:nth-child(2) {
            font-weight: bold;
            color: #adb5bd; /* Perak untuk juara 2 */
        }
        .rank-item:nth-child(3) {
            font-weight: bold;
            color: #bd906b; /* Perunggu untuk juara 3 */
        }
        
        .rank-item:last-child {
            border-bottom: none;
        }

        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Kuis Nahwu: Na'at & Man'ut (10 Soal)</h1>
        
        <div id="quiz-screen">
            <h2 id="question-text"></h2>
            <div id="options-container" class="options-grid">
                </div>
            <button id="next-button" disabled>Lanjut</button>
        </div>

        <div id="result-screen" class="hidden">
            <h2>🎉 Selesai! Skor Anda: <span id="final-score"></span>/10</h2>
            
            <h3>🏆 Papan Peringkat Lokal (Tersimpan di Browser)</h3>
            <div id="ranking-list" class="ranking-container">
                </div>
            <button id="restart-button">Coba Lagi</button>
        </div>
    </div>
    
    <script>
        // --- 1. DATA KUIS (10 SOAL) ---
        const quizData = [
            {
                question: "Secara etimologi atau bahasa (lughawi), kata 'Na'at' memiliki makna dasar yang spesifik. Apa arti kata tersebut?",
                options: ["Sifat, pujian, atau penjelasan", "Sandaran atau tempat bersandar", "Baris atau tanda baca", "Kata kerja lampau"],
                answer: "Sifat, pujian, atau penjelasan"
            },
            {
                question: "Manakah di antara pilihan berikut yang BUKAN merupakan aspek yang harus disesuaikan antara Na'at dan Man'ut?",
                options: ["Jumlah huruf (Kammiyah)", "I'rab (Rafa, Nasab, Jar)", "Jenis (Muzakkar/Muannats)", "Kejelasan (Ma'rifah/Nakirah)"],
                answer: "Jumlah huruf (Kammiyah)"
            },
            {
                question: "Apa istilah untuk jenis Na'at yang mensifati kata lain yang memiliki hubungan (dhamir) dengan Man'ut tersebut, bukan Man'ut secara langsung?",
                options: ["Na'at Sababi", "Na'at Haqiqi", "Na'at Maqthu'", "Na'at Jumlah"],
                answer: "Na'at Sababi"
            },
            {
                question: "Na'at dikategorikan sebagai 'At-Tabi' (pengikut). Apa maksud dari istilah At-Tabi' dalam konteks ini?",
                options: ["Kata yang menjadi pengikut bagi kata sebelumnya", "Kata yang menjadi inti kalimat", "Kata depan yang mengubah baris", "Kata benda yang berdiri sendiri"],
                answer: "Kata yang menjadi pengikut bagi kata sebelumnya"
            },
            {
                question: "Apa tujuan utama atau fungsi dari adanya Na'at setelah Man'ut dalam sebuah kalimat sempurna?",
                options: ["Untuk menyempurnakan makna dengan menjelaskan sifat atau keadaannya", "Untuk mengubah jenis kata dari benda menjadi kerja", "Untuk menunjukkan waktu terjadinya perbuatan", "Untuk meniadakan hukum pada kalimat sebelumnya"],
                answer: "Untuk menyempurnakan makna dengan menjelaskan sifat atau keadaannya"
            },
            {
                question: "Perhatikan kalimat: 'سَجَنَتِ الدَّوْلَةُ السَّارِقَ الْمَعْرُوفَ بِجَرِيمَتِهِ'. Kata manakah yang berkedudukan sebagai Na'at?",
                options: ["الْمَعْرُوفَ (Al-Ma'rufa)", "السَّارِقَ (As-Sariqa)", "الدَّوْلَةُ (Ad-Daulatu)", "سَجَنَتِ (Sajanat)"],
                answer: "الْمَعْرُوفَ (Al-Ma'rufa)"
            },
            {
                question: "Dalam 'عَاقَبَ الْقَاضِي الرَّجُلَ الْمُجْرِمَ', mengapa 'Al-Mujrima' (الْمُجْرِمَ) dibaca nasab (fathah)?",
                options: ["Karena mengikuti i'rab dari Man'ut-nya yaitu 'Ar-Rajula'", "Karena diawali oleh huruf Jar", "Karena berkedudukan sebagai Subjek (Fa'il)", "Karena merupakan kata benda jamak"],
                answer: "Karena mengikuti i'rab dari Man'ut-nya yaitu 'Ar-Rajula'"
            },
            {
                question: "Dalam 'وَاتَّقُوا اللَّهَ الْعَزِيزَ الْحَكِيمَ', kata manakah yang berfungsi sebagai Na'at yang kedua?",
                options: ["الْحَكِيمَ (Al-Hakima)", "الْعَزِيزَ (Al-Aziza)", "اتَّقُوا (Ittaqu)", "اللَّهَ (Allaha)"],
                answer: "الْحَكِيمَ (Al-Hakima)"
            },
            {
                question: "Jika kalimatnya 'جَاءَتِ الْبِنْتُ ...', kata sifat manakah yang paling tepat untuk melengkapi bagian yang kosong?",
                options: ["الْجَمِيلَةُ (Al-Jamilatu)", "الْجَمِيلُ (Al-Jamilu)", "جَمِيلَةٌ (Jamilatun)", "الْجَمِيلِيْنَ (Al-Jamilina)"],
                answer: "الْجَمِيلَةُ (Al-Jamilatu)"
            },
            {
                question: "Pada ayat QS. Nūḥ: 28 'وَلِمَنْ دَخَلَ بَيْتِيَ مُؤْمِنًا', siapakah yang dijelaskan (Man'ut) oleh kata 'mu'minan'?",
                options: ["Kata 'Man' (مَنْ - siapa saja)", "Kata 'Baiti' (بَيْتِيَ - rumahku)", "Kata 'Dakhala' (دَخَلَ - masuk)", "Diri Nabi Nuh sendiri"],
                answer: "Kata 'Man' (مَنْ - siapa saja)"
            }
        ];

        // --- 2. VARIABEL GLOBAL & DOM ELEMENTS ---
        let currentQuestionIndex = 0;
        let score = 0;
        let isAnswered = false;

        const quizScreen = document.getElementById('quiz-screen');
        const resultScreen = document.getElementById('result-screen');
        const questionText = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const nextButton = document.getElementById('next-button');
        const finalScoreSpan = document.getElementById('final-score');
        const rankingList = document.getElementById('ranking-list');
        const restartButton = document.getElementById('restart-button');

        // --- 3. FUNGSI UTAMA KUIS ---

        function startQuiz() {
            currentQuestionIndex = 0;
            score = 0;
            quizScreen.classList.remove('hidden');
            resultScreen.classList.add('hidden');
            loadQuestion();
        }

        function loadQuestion() {
            isAnswered = false;
            nextButton.disabled = true;
            optionsContainer.innerHTML = '';

            if (currentQuestionIndex < quizData.length) {
                const currentQuestion = quizData[currentQuestionIndex];
                // Tampilkan nomor dan teks pertanyaan
                questionText.textContent = `Soal ${currentQuestionIndex + 1}/10: ${currentQuestion.question}`;

                // Buat tombol untuk setiap opsi
                currentQuestion.options.forEach(option => {
                    const button = document.createElement('button');
                    button.textContent = option;
                    button.classList.add('option-button');
                    button.addEventListener('click', () => selectAnswer(button, option, currentQuestion.answer));
                    optionsContainer.appendChild(button);
                });
            } else {
                // Jika semua soal selesai, tampilkan hasil
                showResults();
            }
        }

        function selectAnswer(selectedButton, selectedOption, correctAnswer) {
            if (isAnswered) return;
            isAnswered = true;

            // Nonaktifkan semua tombol setelah memilih
            document.querySelectorAll('.option-button').forEach(btn => {
                btn.classList.add('disabled');
            });

            if (selectedOption === correctAnswer) {
                selectedButton.classList.add('correct');
                score++;
            } else {
                selectedButton.classList.add('incorrect');
                // Tandai jawaban benar (untuk feedback)
                document.querySelectorAll('.option-button').forEach(btn => {
                    if (btn.textContent === correctAnswer) {
                        btn.classList.add('correct');
                    }
                });
            }
            nextButton.disabled = false;
        }

        function showResults() {
            quizScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
            finalScoreSpan.textContent = score;

            // Prompt nama, simpan skor, dan tampilkan peringkat
            promptAndSaveScore(score);
            displayRanking();
        }

        // --- 4. FUNGSI RANKING LOKAL (Local Storage) ---

        function getRanking() {
            // Ambil data dari Local Storage atau buat array kosong jika tidak ada
            const ranking = JSON.parse(localStorage.getItem('quizRanking')) || [];
            return ranking;
        }

        function saveRanking(ranking) {
            localStorage.setItem('quizRanking', JSON.stringify(ranking));
        }

        function promptAndSaveScore(newScore) {
            // Meminta nama pemain
            const playerName = prompt("Selamat! Masukkan nama Anda untuk Papan Peringkat (maks 15 karakter):") || "Anonim";
            const clippedName = playerName.substring(0, 15);
            
            const ranking = getRanking();
            
            // Tambahkan skor baru
            ranking.push({ name: clippedName, score: newScore, date: new Date().toLocaleString('id-ID', {day:'2-digit', month:'short', year:'numeric'}) });

            // Urutkan (tertinggi ke terendah)
            ranking.sort((a, b) => b.score - a.score);

            // Batasi papan peringkat (maksimal 10 skor tertinggi)
            const limitedRanking = ranking.slice(0, 10);
            
            saveRanking(limitedRanking);
        }

        function displayRanking() {
            const ranking = getRanking();
            rankingList.innerHTML = '';

            if (ranking.length === 0) {
                rankingList.innerHTML = '<p style="text-align: center; color: #6c757d;">Belum ada skor yang tercatat. Silakan coba kuis ini!</p>';
                return;
            }

            ranking.forEach((item, index) => {
                const rankItem = document.createElement('div');
                rankItem.classList.add('rank-item');
                // Format tanggal untuk tampilan yang lebih rapi
                const dateDisplay = item.date.split(',')[0]; 
                rankItem.innerHTML = `
                    <span>#${index + 1} - <strong>${item.name}</strong></span>
                    <span>${item.score}/10 (${dateDisplay})</span>
                `;
                rankingList.appendChild(rankItem);
            });
        }

        // --- 5. EVENT LISTENERS ---
        nextButton.addEventListener('click', () => {
            if (isAnswered) {
                currentQuestionIndex++;
                loadQuestion();
            }
        });

        restartButton.addEventListener('click', startQuiz);

        // Mulai kuis saat halaman dimuat
        startQuiz();
    </script>
</body>
</html>
