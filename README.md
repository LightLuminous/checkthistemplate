<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IELTS Writing Score Simulator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow-x: hidden;
            background-color: #f4f4f4;
        }
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            background-color: #6c9d87; /* Oxley color */
            color: white;
        }
        #title {
            font-size: 20px;
            color: white;
        }
        #visitorCount {
            font-size: 16px;
            text-align: center;
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            color: white;
        }
        #wordCount {
            background-color: #d3d3d3; /* Light grey background */
            padding: 5px;
            border-radius: 5px;
            font-size: 16px;
            margin: 10px;
            display: inline;
        }
        #fontSizeControl {
            margin-left: 10px;
            font-size: 12px;
            padding: 5px;
        }
        #downloadDocButton, #downloadJpgButton {
            padding: 8px 16px;
            background-color: #a9a9a9; /* Dark grey background */
            color: white; /* White text */
            border: none;
            border-radius: 4px;
            cursor: pointer;
            margin-left: 5px;
        }
        #downloadDocButton:hover, #downloadJpgButton:hover {
            background-color: #808080; /* Slightly darker grey on hover */
        }
        textarea {
            flex-grow: 1;
            width: 100%;
            padding: 20px;
            font-family: 'Times New Roman', Times, serif; /* Times New Roman */
            border: none;
            outline: none;
            resize: none;
        }
        footer {
            display: flex;
            justify-content: center; /* Centered content */
            align-items: center;
            padding: 10px;
            background-color: #f4f4f4;
            flex-direction: column;
        }
        #timerSection {
            display: flex;
            align-items: center;
            justify-content: center; /* Centered content */
            margin-top: 10px;
        }
        #timer {
            font-size: 18px;
            color: black; /* Black timer color */
            margin-left: 10px;
        }
        #author {
            font-size: 12px;
            color: gray;
            margin-top: 5px; /* Lower the position */
        }
        #rewardButton {
            display: none;
            margin-top: 10px;
            padding: 8px 16px;
            background-color: #6c9d87; /* Reward button color */
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        #rewardButton:hover {
            background-color: #5b8c76; /* Darker reward button on hover */
        }
    </style>
</head>
<body>
    <header>
        <div id="title">IELTS Writing Score Simulator</div>
        <div id="visitorCount">Visitors: 0</div>
        <div>
            <button id="downloadDocButton">Download as DOC</button>
            <button id="downloadJpgButton">Download as JPG</button>
        </div>
    </header>

    <div>
        <div id="wordCount">Word Count: 0</div>
        <select id="fontSizeControl">
            <option value="12px">12</option>
            <option value="14px">14</option>
            <option value="16px" selected>16</option>
            <option value="18px">18</option>
            <option value="20px">20</option>
            <option value="24px">24</option>
        </select>
    </div>

    <textarea id="textInput" placeholder="Start writing here..."></textarea>

    <button id="rewardButton">Congratulations, here is your reward!</button>

    <footer>
        <div id="scoreSection">
            Writing Band Score: <span id="ieltsScore">0</span> / 9
        </div>
        <div id="timerSection">
            <label for="timerControl">Timer: </label> <span>&nbsp;</span>
            <select id="timerControl">
                <option value="900">15 min</option>
                <option value="1800">30 min</option>
                <option value="2700">45 min</option>
                <option value="3600">60 min</option>
            </select>
            <button id="startTimerButton">Start Timer</button>
            <div id="timer">00:00</div>
        </div>
        <div id="author">Author: LightLuminous</div>
    </footer>

    <script>
        const textInput = document.getElementById('textInput');
        const wordCount = document.getElementById('wordCount');
        const downloadDocButton = document.getElementById('downloadDocButton');
        const downloadJpgButton = document.getElementById('downloadJpgButton');
        const ieltsScore = document.getElementById('ieltsScore');
        const visitorCount = document.getElementById('visitorCount');
        const fontSizeControl = document.getElementById('fontSizeControl');
        const timerControl = document.getElementById('timerControl');
        const timerDisplay = document.getElementById('timer');
        const startTimerButton = document.getElementById('startTimerButton');
        const rewardButton = document.getElementById('rewardButton');

        let timerInterval;
        let timerTimeLeft = 0;

        // Function to count only English words
        function countEnglishWords(text) {
            const englishWords = text.match(/\b[a-zA-Z]+\b/g);
            return englishWords ? englishWords.length : 0;
        }

        // IELTS score simulation based on word count and structure
        function simulateIELTSScore(wordCount) {
            let score = 0;
            if (wordCount < 150) {
                score = 4;
            } else if (wordCount < 200) {
                score = 5.5;
            } else if (wordCount < 250) {
                score = 6.5;
            } else if (wordCount < 300) {
                score = 7.5;
            } else {
                score = 8.5;
            }
            return Math.min(score.toFixed(1), 9);
        }

        // Change font size based on user selection
        fontSizeControl.addEventListener('change', () => {
            const selectedSize = fontSizeControl.value;
            textInput.style.fontSize = selectedSize;
        });

        textInput.addEventListener('input', () => {
            const text = textInput.value.trim();
            const englishWordCount = countEnglishWords(text);
            wordCount.textContent = `Word Count: ${englishWordCount}`;

            // Show reward button if word count reaches 350
            if (englishWordCount >= 350) {
                rewardButton.style.display = 'block';
            } else {
                rewardButton.style.display = 'none';
            }

            // Calculate IELTS score
            const ieltsScoreValue = simulateIELTSScore(englishWordCount);
            ieltsScore.textContent = ieltsScoreValue;
        });

        // Download as DOC file
        downloadDocButton.addEventListener('click', () => {
            const text = textInput.value;
            const blob = new Blob([text], { type: 'application/msword' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'myText.doc';
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);

            alert(`Your Writing Band Score: ${ieltsScore.textContent}/9`);
        });

        // Download as JPG
        downloadJpgButton.addEventListener('click', () => {
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            canvas.width = 1080;  // High resolution width
            canvas.height = 1920; // High resolution height
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.font = '18px Times New Roman';
            ctx.fillStyle = 'black';
            ctx.fillText(textInput.value, 10, 30);
            canvas.toBlob(function(blob) {
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = 'myText.jpg';
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
            }, 'image/jpeg', 1.0); // High quality JPG
        });

        // Visitor counter using localStorage
        window.onload = function() {
            let count = localStorage.getItem('visitorCount');
            if (!count) {
                count = 0;
            }
            count++;
            localStorage.setItem('visitorCount', count);
            visitorCount.textContent = `Visitors: ${count}`;
        };

        // Timer functionality
        startTimerButton.addEventListener('click', () => {
            timerTimeLeft = parseInt(timerControl.value);
            timerDisplay.textContent = formatTime(timerTimeLeft);
            clearInterval(timerInterval); // Clear any existing intervals
            timerInterval = setInterval(updateTimer, 1000);
        });

        function updateTimer() {
            if (timerTimeLeft <= 0) {
                clearInterval(timerInterval);
                alert("Time's up!");
                return;
            }
            timerTimeLeft--;
            timerDisplay.textContent = formatTime(timerTimeLeft);
        }

        function formatTime(seconds) {
            const minutes = Math.floor(seconds / 60);
            const remainingSeconds = seconds % 60;
            return `${String(minutes).padStart(2, '0')}:${String(remainingSeconds).padStart(2, '0')}`;
        }
    </script>
</body>
</html>
 
