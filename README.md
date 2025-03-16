# quizforkja2
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>세로 모드 퀴즈</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 100vw;
            height: 100dvh;
            background-color: #000; /* 여백이 생길 경우 검은색 배경 유지 */
            position: relative;
            touch-action: manipulation;
        }
        img {
            width: 100vw;
            height: 100dvh;
            object-fit: contain; /* 이미지가 잘리지 않고 비율 유지 */
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%); /* 중앙 정렬 */
            margin: 0;
            padding: 0;
        }
        .error-message {
            display: none;
            color: white;
            font-size: 20px;
            text-align: center;
        }
        .answer-box {
            position: absolute;
            bottom: 8%;
            right: 5%;
            background-color: rgba(255, 255, 255, 0.9);
            padding: 10px;
            border-radius: 8px;
            display: flex;
            gap: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            z-index: 10;
        }
        .answer-box input {
            padding: 8px;
            width: 130px;
            font-size: 16px;
        }
        .answer-box button {
            padding: 8px 12px;
            font-size: 16px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .answer-box button:hover {
            background-color: #45a049;
        }
        @media (orientation: landscape) {
            body::before {
                content: "화면을 세로로 돌려주세요!";
                position: absolute;
                top: 50%;
                left: 50%;
                transform: translate(-50%, -50%);
                color: white;
                font-size: 20px;
                text-align: center;
                z-index: 20;
            }
            img {
                display: none;
            }
        }
        /* 아이폰 14 Pro 크기 최적화 */
        @media only screen and (device-width: 393px) and (device-height: 852px) and (-webkit-device-pixel-ratio: 3) {
            body {
                width: 393px;
                height: 852px;
            }
            img {
                max-width: 393px; /* 최대 너비를 393px로 제한 */
                max-height: 852px; /* 최대 높이를 852px로 제한 */
                object-fit: contain; /* 비율 유지하며 잘리지 않도록 */
            }
        }
    </style>
</head>
<body>
    <img id="mainImage" src="https://gi.esmplus.com/varzz1/%EB%A9%94%EC%9D%B8%20%EC%9D%B4%EB%AF%B8%EC%A7%80.jpeg" alt="메인 이미지">
    <div class="error-message" id="errorMessage">이미지를 로드할 수 없습니다。</div>

    <script>
        const image = document.getElementById('mainImage');
        let currentQuestion = 0;
        let answerBox = null;

        const questions = [
            {
                background: 'https://gi.esmplus.com/varzz1/%ED%80%B4%EC%A6%88%201.jpeg',
                problem: 'https://gi.esmplus.com/varzz1/%EC%B2%AB%EB%B2%88%EC%A7%B8%20%EB%AC%B8%EC%A0%9C.jpeg',
                answer: '김진아'
            },
            {
                background: 'https://gi.esmplus.com/varzz1/%ED%80%B4%EC%A6%88%202.jpeg',
                problem: 'https://gi.esmplus.com/varzz1/%EB%91%90%EB%B2%88%EC%A7%B8%20%EB%AC%B8%EC%A0%9C.jpeg',
                answer: '2'
            },
            {
                background: 'https://gi.esmplus.com/varzz1/%ED%80%B4%EC%A6%88%203.jpeg',
                problem: 'https://gi.esmplus.com/varzz1/%EC%84%B8%EB%B2%88%EC%A7%B8%20%EB%AC%B8%EC%A0%9C.jpeg',
                answer: '고구마 고구마'
            },
            {
                background: 'https://gi.esmplus.com/varzz1/%ED%80%B4%EC%A6%88%204.jpeg',
                problem: 'https://gi.esmplus.com/varzz1/%EB%84%A4%EB%B2%88%EC%A7%B8%20%EB%AC%B8%EC%A0%9C.jpeg',
                answer: null
            }
        ];
        const successImage = 'https://gi.esmplus.com/varzz1/%EB%AC%B8%EC%A0%9C%ED%95%B4%EA%B2%B0.jpeg';
        const failImage = 'https://gi.esmplus.com/varzz1/%EC%8B%A4%ED%8C%A8.jpeg';

        image.addEventListener('touchstart', function() {
            if (image.src.includes('%EB%A9%94%EC%9D%B8%20%EC%9D%B4%EB%AF%B8%EC%A7%80.jpeg')) {
                image.src = questions[0].background;
                currentQuestion = 1;
            } 
            else if (questions.some(q => q.background === image.src)) {
                const questionIndex = questions.findIndex(q => q.background === image.src);
                image.src = questions[questionIndex].problem;
                currentQuestion = questionIndex + 1;
            } 
            else if (questions.some(q => q.problem === image.src)) {
                if (currentQuestion <= 3) {
                    showAnswerBox();
                }
            } 
            else if (image.src === successImage) {
                if (currentQuestion < 4) {
                    image.src = questions[currentQuestion].background;
                    currentQuestion++;
                } else {
                    image.src = 'https://gi.esmplus.com/varzz1/%EB%A9%94%EC%9D%B8%20%EC%9D%B4%EB%AF%B8%EC%A7%80.jpeg';
                    currentQuestion = 0;
                }
            } 
            else if (image.src === failImage) {
                image.src = questions[currentQuestion - 1].problem;
            }
        });

        function showAnswerBox() {
            if (answerBox) return;

            answerBox = document.createElement('div');
            answerBox.classList.add('answer-box');

            const input = document.createElement('input');
            input.type = 'text';
            input.placeholder = '정답을 입력하세요';
            input.id = 'answerInput';

            const button = document.createElement('button');
            button.textContent = '제출';
            button.onclick = checkAnswer;

            answerBox.appendChild(input);
            answerBox.appendChild(button);
            document.body.appendChild(answerBox);

            input.focus();
        }

        function checkAnswer() {
            if (currentQuestion > 3) return;
            
            const userAnswer = document.getElementById('answerInput').value.trim();
            const correctAnswer = questions[currentQuestion - 1].answer;

            image.src = userAnswer === correctAnswer ? successImage : failImage;
            removeAnswerBox();
        }

        function removeAnswerBox() {
            if (answerBox) {
                document.body.removeChild(answerBox);
                answerBox = null;
            }
        }
    </script>
</body>
</html>
