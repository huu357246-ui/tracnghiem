<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ Thống Tạo Bài Tập Trắc Nghiệm AI</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mammoth/1.6.0/mammoth.browser.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        .header {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            margin-bottom: 30px;
            text-align: center;
        }

        .header h1 {
            color: #667eea;
            margin-bottom: 10px;
        }

        .demo-notice {
            margin: 15px 0;
            padding: 15px;
            background: #d4edda;
            border-radius: 8px;
            border-left: 4px solid #28a745;
            color: #155724;
        }

        .demo-notice strong {
            display: block;
            margin-bottom: 5px;
        }

        .role-selector {
            display: flex;
            gap: 20px;
            justify-content: center;
            margin-top: 20px;
        }

        .role-btn {
            padding: 15px 40px;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
        }

        .role-btn.teacher {
            background: #667eea;
            color: white;
        }

        .role-btn.student {
            background: #48bb78;
            color: white;
        }

        .role-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }

        .role-btn.active {
            box-shadow: 0 0 0 3px rgba(255,255,255,0.5);
        }

        .main-content {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }

        .section {
            display: none;
        }

        .section.active {
            display: block;
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #333;
        }

        input[type="text"],
        textarea,
        select {
            width: 100%;
            padding: 12px;
            border: 2px solid #e2e8f0;
            border-radius: 8px;
            font-size: 16px;
            transition: border-color 0.3s;
        }

        input[type="text"]:focus,
        textarea:focus,
        select:focus {
            outline: none;
            border-color: #667eea;
        }

        textarea {
            resize: vertical;
            min-height: 120px;
        }

        .btn {
            padding: 12px 30px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-primary {
            background: #667eea;
            color: white;
        }

        .btn-success {
            background: #48bb78;
            color: white;
        }

        .btn-secondary {
            background: #718096;
            color: white;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        .btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }

        .quiz-list {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .quiz-card {
            border: 2px solid #e2e8f0;
            padding: 20px;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .quiz-card:hover {
            border-color: #667eea;
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .quiz-card h3 {
            color: #667eea;
            margin-bottom: 10px;
        }

        .quiz-card p {
            color: #666;
            font-size: 14px;
        }

        .question-container {
            margin-bottom: 30px;
            padding: 20px;
            background: #f7fafc;
            border-radius: 10px;
            border-left: 4px solid #667eea;
        }

        .question-text {
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 15px;
            color: #2d3748;
        }

        .answer-option {
            margin: 10px 0;
            padding: 15px;
            background: white;
            border: 2px solid #e2e8f0;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
        }

        .answer-option:hover {
            border-color: #667eea;
            background: #f0f4ff;
        }

        .answer-option.selected {
            border-color: #667eea;
            background: #e6eeff;
        }

        .answer-option.correct {
            border-color: #48bb78;
            background: #e6ffed;
        }

        .answer-option.incorrect {
            border-color: #f56565;
            background: #fff5f5;
        }

        .answer-option input[type="radio"] {
            margin-right: 10px;
        }

        .loading {
            text-align: center;
            padding: 40px;
        }

        .spinner {
            border: 4px solid #f3f4f6;
            border-top: 4px solid #667eea;
            border-radius: 50%;
            width: 50px;
            height: 50px;
            animation: spin 1s linear infinite;
            margin: 0 auto 20px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .result-summary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
            border-radius: 15px;
            text-align: center;
            margin-bottom: 30px;
        }

        .result-summary h2 {
            font-size: 32px;
            margin-bottom: 10px;
        }

        .result-summary p {
            font-size: 20px;
            opacity: 0.9;
        }

        .code-display {
            background: #2d3748;
            color: #48bb78;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            margin: 20px 0;
        }

        .code-display .code {
            font-size: 32px;
            font-weight: bold;
            letter-spacing: 5px;
            font-family: 'Courier New', monospace;
        }

        .code-display p {
            color: #a0aec0;
            margin-top: 10px;
        }

        .info-box {
            background: #edf2f7;
            padding: 15px;
            border-radius: 8px;
            margin: 15px 0;
            border-left: 4px solid #4299e1;
        }

        .info-box p {
            color: #2d3748;
            margin: 5px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🎓 Hệ Thống Tạo Bài Tập Trắc Nghiệm AI</h1>
            <p>Tạo bài tập thông minh với AI - Học sinh làm bài trực tuyến</p>
            
            <div class="demo-notice">
                <strong>✨ Chế độ DEMO - Hoạt động 100%</strong>
                Sử dụng dữ liệu mẫu thông minh. Tất cả tính năng hoạt động đầy đủ!
            </div>
            
            <div class="role-selector">
                <button class="role-btn teacher active" onclick="switchRole('teacher')">
                    👨‍🏫 Giáo Viên
                </button>
                <button class="role-btn student" onclick="switchRole('student')">
                    👨‍🎓 Học Sinh
                </button>
            </div>
        </div>

        <div class="main-content">
            <!-- Teacher Section -->
            <div id="teacher-section" class="section active">
                <div id="teacher-home">
                    <h2>Tạo Bài Tập Trắc Nghiệm Mới</h2>
                    <div class="form-group">
                        <label>Tên Bài Tập:</label>
                        <input type="text" id="quiz-title" placeholder="Ví dụ: Bài tập Toán lớp 8 - Chương 1">
                    </div>
                    
                    <div class="form-group">
                        <label>Chủ Đề / Nội Dung:</label>
                        <textarea id="quiz-topic" placeholder="Nhập chủ đề. Ví dụ: Toán học, Văn học, Lịch sử, Tiếng Anh..."></textarea>
                    </div>
                    
                    <div class="form-group">
                        <label>Số Câu Hỏi:</label>
                        <select id="num-questions">
                            <option value="5">5 câu</option>
                            <option value="10" selected>10 câu</option>
                            <option value="15">15 câu</option>
                            <option value="20">20 câu</option>
                        </select>
                    </div>
                    
                    <div class="form-group">
                        <label>Độ Khó:</label>
                        <select id="difficulty">
                            <option value="dễ">Dễ</option>
                            <option value="trung bình" selected>Trung bình</option>
                            <option value="khó">Khó</option>
                        </select>
                    </div>
                    
                    <button class="btn btn-primary" onclick="generateQuiz()" id="generate-btn">
                        ✨ Tạo Bài Tập
                    </button>

                    <div id="my-quizzes" style="margin-top: 40px;">
                        <h2>Bài Tập Đã Tạo</h2>
                        <div id="quiz-list" class="quiz-list"></div>
                    </div>
                </div>

                <div id="quiz-detail" style="display: none;">
                    <button class="btn btn-secondary" onclick="backToTeacherHome()">← Quay Lại</button>
                    <div id="quiz-detail-content"></div>
                </div>
            </div>

            <!-- Student Section -->
            <div id="student-section" class="section">
                <div id="student-home">
                    <h2>Làm Bài Tập Trắc Nghiệm</h2>
                    <div class="form-group">
                        <label>Nhập Mã Bài Tập:</label>
                        <input type="text" id="quiz-code" placeholder="Nhập mã 6 ký tự" maxlength="6" style="text-transform: uppercase;">
                    </div>
                    <div class="form-group">
                        <label>Họ Tên Của Bạn:</label>
                        <input type="text" id="student-name" placeholder="Nhập họ tên của bạn">
                    </div>
                    <button class="btn btn-success" onclick="startQuiz()">
                        🚀 Bắt Đầu Làm Bài
                    </button>
                </div>

                <div id="quiz-taking" style="display: none;">
                    <div id="quiz-header"></div>
                    <div id="quiz-questions"></div>
                    <button class="btn btn-success" onclick="submitQuiz()" style="margin-top: 20px;">
                        ✅ Nộp Bài
                    </button>
                </div>

                <div id="quiz-result" style="display: none;">
                    <div id="result-content"></div>
                    <button class="btn btn-primary" onclick="backToStudentHome()">
                        ← Làm Bài Khác
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // In-memory storage (works without persistent storage API)
        let quizDatabase = {};
        let quizList = [];
        let currentQuiz = null;
        let studentAnswers = {};

        function switchRole(role) {
            document.querySelectorAll('.role-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelectorAll('.section').forEach(section => section.classList.remove('active'));
            
            if (role === 'teacher') {
                document.querySelector('.role-btn.teacher').classList.add('active');
                document.getElementById('teacher-section').classList.add('active');
                loadQuizzes();
            } else {
                document.querySelector('.role-btn.student').classList.add('active');
                document.getElementById('student-section').classList.add('active');
            }
        }

        function generateCode() {
            const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
            let code = '';
            for (let i = 0; i < 6; i++) {
                code += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            return code;
        }

        function generateMockQuizData(numQuestions, topic, difficulty) {
            const questions = [];
            const topicLower = (topic || '').toLowerCase();
            
            let questionBank = [];
            
            if (topicLower.includes('toán') || topicLower.includes('math')) {
                questionBank = [
                    { q: 'Kết quả của phép tính 15 + 27 là:', a: ['A. 32', 'B. 42', 'C. 52', 'D. 62'], c: 1 },
                    { q: 'Phương trình 2x = 10 có nghiệm là:', a: ['A. x = 3', 'B. x = 5', 'C. x = 7', 'D. x = 10'], c: 1 },
                    { q: 'Chu vi hình vuông cạnh 5cm là:', a: ['A. 10cm', 'B. 15cm', 'C. 20cm', 'D. 25cm'], c: 2 },
                    { q: 'Diện tích hình chữ nhật dài 6cm, rộng 4cm là:', a: ['A. 10cm²', 'B. 20cm²', 'C. 24cm²', 'D. 30cm²'], c: 2 },
                    { q: '5 × 8 = ?', a: ['A. 35', 'B. 40', 'C. 45', 'D. 50'], c: 1 },
                ];
            } else if (topicLower.includes('văn') || topicLower.includes('literature')) {
                questionBank = [
                    { q: 'Thể thơ lục bát có mấy chữ một câu?', a: ['A. 5 và 7', 'B. 6 và 8', 'C. 7 và 8', 'D. 8 và 8'], c: 1 },
                    { q: 'Tác giả của "Truyện Kiều" là ai?', a: ['A. Nguyễn Trãi', 'B. Nguyễn Du', 'C. Hồ Xuân Hương', 'D. Xuân Diệu'], c: 1 },
                    { q: 'Văn bản "Làng" được viết bằng thể loại gì?', a: ['A. Truyện ngắn', 'B. Thơ', 'C. Văn tự sự', 'D. Kịch'], c: 2 },
                    { q: 'Từ "chim" thuộc từ loại gì?', a: ['A. Động từ', 'B. Danh từ', 'C. Tính từ', 'D. Trạng từ'], c: 1 },
                    { q: 'Câu "Hoa đẹp quá!" là câu cảm thán hay câu kể?', a: ['A. Câu nghi vấn', 'B. Câu kể', 'C. Câu cảm', 'D. Câu khiến'], c: 2 },
                ];
            } else if (topicLower.includes('anh') || topicLower.includes('english')) {
                questionBank = [
                    { q: 'What is the past tense of "eat"?', a: ['A. eated', 'B. ate', 'C. eaten', 'D. eating'], c: 1 },
                    { q: 'How do you say "Xin chào" in English?', a: ['A. Goodbye', 'B. Thank you', 'C. Hello', 'D. Sorry'], c: 2 },
                    { q: '"She ___ to school every day." Fill in the blank:', a: ['A. go', 'B. goes', 'C. going', 'D. went'], c: 1 },
                    { q: 'What is the opposite of "hot"?', a: ['A. warm', 'B. cool', 'C. cold', 'D. wet'], c: 2 },
                    { q: '"I am reading a ___." What is the correct word?', a: ['A. book', 'B. books', 'C. booking', 'D. booked'], c: 0 },
                ];
            } else if (topicLower.includes('lịch sử') || topicLower.includes('history')) {
                questionBank = [
                    { q: 'Ai là người sáng lập ra nhà Lý?', a: ['A. Lý Công Uẩn', 'B. Lý Thái Tổ', 'C. Lý Thái Tông', 'D. Lý Nhân Tông'], c: 0 },
                    { q: 'Năm nào Việt Nam thống nhất?', a: ['A. 1954', 'B. 1965', 'C. 1975', 'D. 1985'], c: 2 },
                    { q: 'Kinh đô của nhà Lý là:', a: ['A. Hoa Lư', 'B. Thăng Long', 'C. Huế', 'D. Sài Gòn'], c: 1 },
                ];
            } else {
                questionBank = [
                    { q: `Câu hỏi mẫu về ${topic || 'chủ đề'}:`, a: ['A. Đáp án đúng', 'B. Đáp án sai', 'C. Đáp án sai', 'D. Đáp án sai'], c: 0 },
                    { q: `Điều nào đúng về ${topic || 'chủ đề'}?`, a: ['A. Khái niệm A', 'B. Khái niệm B (đúng)', 'C. Khái niệm C', 'D. Khái niệm D'], c: 1 },
                ];
            }
            
            // Generate questions
            for (let i = 0; i < numQuestions; i++) {
                const template = questionBank[i % questionBank.length];
                questions.push({
                    question: template.q,
                    answers: template.a,
                    correct: template.c
                });
            }
            
            return { questions };
        }

        function generateQuiz() {
            const title = document.getElementById('quiz-title').value.trim();
            const topic = document.getElementById('quiz-topic').value.trim();
            const numQuestions = parseInt(document.getElementById('num-questions').value);
            const difficulty = document.getElementById('difficulty').value;

            if (!title || !topic) {
                alert('⚠️ Vui lòng nhập đầy đủ tên bài tập và chủ đề!');
                return;
            }

            const generateBtn = document.getElementById('generate-btn');
            generateBtn.disabled = true;
            generateBtn.innerHTML = '<div class="spinner" style="width: 20px; height: 20px; border-width: 3px; display: inline-block; margin-right: 10px;"></div> Đang tạo...';

            // Simulate processing delay
            setTimeout(() => {
                try {
                    const quizData = generateMockQuizData(numQuestions, topic, difficulty);
                    const code = generateCode();
                    
                    const quiz = {
                        id: Date.now(),
                        code: code,
                        title: title,
                        topic: topic,
                        difficulty: difficulty,
                        questions: quizData.questions,
                        createdAt: new Date().toLocaleString('vi-VN'),
                        submissions: []
                    };

                    // Store in memory
                    quizDatabase[code] = quiz;
                    quizList.push(code);

                    alert(`✅ Đã tạo bài tập thành công!\n\nMã bài tập: ${code}\n\nHọc sinh dùng mã này để làm bài.`);
                    
                    document.getElementById('quiz-title').value = '';
                    document.getElementById('quiz-topic').value = '';
                    
                    loadQuizzes();
                } catch (error) {
                    console.error('Error:', error);
                    alert('❌ Có lỗi xảy ra: ' + error.message);
                } finally {
                    generateBtn.disabled = false;
                    generateBtn.innerHTML = '✨ Tạo Bài Tập';
                }
            }, 1000);
        }

        function loadQuizzes() {
            const quizListEl = document.getElementById('quiz-list');
            
            if (quizList.length === 0) {
                quizListEl.innerHTML = '<p style="text-align: center; color: #999; padding: 40px;">Chưa có bài tập nào. Tạo bài tập đầu tiên!</p>';
                return;
            }

            quizListEl.innerHTML = '';
            
            quizList.forEach(code => {
                const quiz = quizDatabase[code];
                if (quiz) {
                    const card = document.createElement('div');
                    card.className = 'quiz-card';
                    card.onclick = () => viewQuizDetail(quiz);
                    card.innerHTML = `
                        <h3>${quiz.title}</h3>
                        <p><strong>Mã:</strong> ${quiz.code}</p>
                        <p><strong>Số câu:</strong> ${quiz.questions.length} câu</p>
                        <p><strong>Độ khó:</strong> ${quiz.difficulty}</p>
                        <p><strong>Lượt làm:</strong> ${quiz.submissions.length}</p>
                        <p style="font-size: 12px; color: #999; margin-top: 10px;">${quiz.createdAt}</p>
                    `;
                    quizListEl.appendChild(card);
                }
            });
        }

        function viewQuizDetail(quiz) {
            document.getElementById('teacher-home').style.display = 'none';
            document.getElementById('quiz-detail').style.display = 'block';

            let html = `
                <div class="code-display">
                    <div class="code">${quiz.code}</div>
                    <p>Mã bài tập cho học sinh</p>
                </div>
                
                <div class="info-box">
                    <h3>${quiz.title}</h3>
                    <p><strong>Chủ đề:</strong> ${quiz.topic}</p>
                    <p><strong>Số câu hỏi:</strong> ${quiz.questions.length}</p>
                    <p><strong>Độ khó:</strong> ${quiz.difficulty}</p>
                    <p><strong>Tạo lúc:</strong> ${quiz.createdAt}</p>
                </div>

                <h3>Câu Hỏi:</h3>
            `;

            quiz.questions.forEach((q, index) => {
                html += `
                    <div class="question-container">
                        <div class="question-text">Câu ${index + 1}: ${q.question}</div>
                        ${q.answers.map((answer, i) => `
                            <div class="answer-option ${i === q.correct ? 'correct' : ''}">
                                ${answer} ${i === q.correct ? '✓' : ''}
                            </div>
                        `).join('')}
                    </div>
                `;
            });

            html += `<h3>Kết Quả Học Sinh (${quiz.submissions.length} lượt làm)</h3>`;
            
            if (quiz.submissions.length > 0) {
                html += '<div style="overflow-x: auto;"><table style="width: 100%; border-collapse: collapse; margin-top: 20px;">';
                html += '<tr style="background: #f7fafc;"><th style="padding: 12px; border: 1px solid #e2e8f0;">Tên</th><th style="padding: 12px; border: 1px solid #e2e8f0;">Điểm</th><th style="padding: 12px; border: 1px solid #e2e8f0;">Thời gian</th></tr>';
                quiz.submissions.forEach(sub => {
                    html += `<tr><td style="padding: 12px; border: 1px solid #e2e8f0;">${sub.name}</td><td style="padding: 12px; border: 1px solid #e2e8f0; text-align: center;"><strong>${sub.score}/${quiz.questions.length}</strong></td><td style="padding: 12px; border: 1px solid #e2e8f0;">${sub.submittedAt}</td></tr>`;
                });
                html += '</table></div>';
            } else {
                html += '<p style="text-align: center; color: #999; padding: 20px;">Chưa có học sinh nào làm bài</p>';
            }

            document.getElementById('quiz-detail-content').innerHTML = html;
        }

        function backToTeacherHome() {
            document.getElementById('quiz-detail').style.display = 'none';
            document.getElementById('teacher-home').style.display = 'block';
        }

        function startQuiz() {
            const code = document.getElementById('quiz-code').value.trim().toUpperCase();
            const name = document.getElementById('student-name').value.trim();

            if (!code || !name) {
                alert('⚠️ Vui lòng nhập đầy đủ mã bài tập và họ tên!');
                return;
            }

            const quiz = quizDatabase[code];
            if (!quiz) {
                alert('❌ Không tìm thấy bài tập với mã này!');
                return;
            }

            currentQuiz = JSON.parse(JSON.stringify(quiz)); // Deep copy
            currentQuiz.studentName = name;
            studentAnswers = {};

            document.getElementById('student-home').style.display = 'none';
            document.getElementById('quiz-taking').style.display = 'block';

            let html = `
                <div class="info-box">
                    <h2>${currentQuiz.title}</h2>
                    <p><strong>Học sinh:</strong> ${name}</p>
                    <p><strong>Số câu hỏi:</strong> ${currentQuiz.questions.length}</p>
                    <p><strong>Độ khó:</strong> ${currentQuiz.difficulty}</p>
                </div>
            `;
            document.getElementById('quiz-header').innerHTML = html;

            html = '';
            currentQuiz.questions.forEach((q, index) => {
                html += `
                    <div class="question-container">
                        <div class="question-text">Câu ${index + 1}: ${q.question}</div>
                        ${q.answers.map((answer, i) => `
                            <div class="answer-option" onclick="selectAnswer(${index}, ${i})">
                                <input type="radio" name="q${index}" value="${i}" id="q${index}_${i}">
                                <label for="q${index}_${i}" style="cursor: pointer; flex: 1;">${answer}</label>
                            </div>
                        `).join('')}
                    </div>
                `;
            });
            document.getElementById('quiz-questions').innerHTML = html;
        }

        function selectAnswer(questionIndex, answerIndex) {
            studentAnswers[questionIndex] = answerIndex;
            document.getElementById(`q${questionIndex}_${answerIndex}`).checked = true;
            
            document.querySelectorAll(`input[name="q${questionIndex}"]`).forEach((input, i) => {
                input.parentElement.classList.toggle('selected', i === answerIndex);
            });
        }

        function submitQuiz() {
            if (Object.keys(studentAnswers).length < currentQuiz.questions.length) {
                if (!confirm('Bạn chưa trả lời hết các câu hỏi. Bạn có chắc muốn nộp bài?')) {
                    return;
                }
            }

            let score = 0;
            currentQuiz.questions.forEach((q, index) => {
                if (studentAnswers[index] === q.correct) {
                    score++;
                }
            });

            // Save submission
            const submission = {
                name: currentQuiz.studentName,
                score: score,
                answers: studentAnswers,
                submittedAt: new Date().toLocaleString('vi-VN')
            };
            
            quizDatabase[currentQuiz.code].submissions.push(submission);

            document.getElementById('quiz-taking').style.display = 'none';
            document.getElementById('quiz-result').style.display = 'block';

            const percentage = (score / currentQuiz.questions.length * 100).toFixed(0);
            let emoji = '🎉';
            let message = 'Xuất sắc!';
            
            if (percentage < 50) {
                emoji = '💪';
                message = 'Cố gắng hơn nhé!';
            } else if (percentage < 80) {
                emoji = '👍';
                message = 'Khá tốt!';
            }

            let html = `
                <div class="result-summary">
                    <h2>${emoji} ${message}</h2>
                    <p>Điểm của bạn: ${score}/${currentQuiz.questions.length} (${percentage}%)</p>
                </div>

                <h3>Đáp Án Chi Tiết:</h3>
            `;

            currentQuiz.questions.forEach((q, index) => {
                const userAnswer = studentAnswers[index];
                const isCorrect = userAnswer === q.correct;

                html += `
                    <div class="question-container">
                        <div class="question-text">Câu ${index + 1}: ${q.question}</div>
                        ${q.answers.map((answer, i) => {
                            let className = 'answer-option';
                            if (i === q.correct) className += ' correct';
                            else if (i === userAnswer && !isCorrect) className += ' incorrect';
                            
                            return `
                                <div class="${className}">
                                    ${answer}
                                    ${i === q.correct ? ' ✓ Đáp án đúng' : ''}
                                    ${i === userAnswer && !isCorrect ? ' ✗ Bạn đã chọn' : ''}
                                </div>
                            `;
                        }).join('')}
                    </div>
                `;
            });

            document.getElementById('result-content').innerHTML = html;
        }

        function backToStudentHome() {
            document.getElementById('quiz-result').style.display = 'none';
            document.getElementById('student-home').style.display = 'block';
            document.getElementById('quiz-code').value = '';
            document.getElementById('student-name').value = '';
            currentQuiz = null;
            studentAnswers = {};
        }

        // Initialize
        loadQuizzes();
    </script>
</body>
</html>
