<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pharmacy Chat with Voice Notes</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .chat-container {
            width: 100%;
            max-width: 400px;
            background-color: #ffffff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            border-radius: 10px;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }
        .chat-header {
            background-color: #4CAF50;
            color: white;
            padding: 15px;
            text-align: center;
        }
        .chat-messages {
            padding: 20px;
            flex-grow: 1;
            overflow-y: auto;
            background-color: #f9f9f9;
        }
        .chat-input-container {
            display: flex;
            padding: 10px;
            border-top: 1px solid #ddd;
            background-color: #f1f1f1;
        }
        .chat-input {
            flex-grow: 1;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
        }
        .send-button, .voice-button {
            background-color: #4CAF50;
            border: none;
            color: white;
            padding: 10px;
            margin-left: 5px;
            cursor: pointer;
            border-radius: 5px;
        }
        .send-button:hover, .voice-button:hover {
            background-color: #45a049;
        }
        .user-message {
            font-weight: bold;
            margin: 10px 0;
        }
        .server-message {
            margin: 10px 0;
            color: #333;
        }
    </style>
</head>
<body>

    <div class="chat-container">
        <div class="chat-header">
            Pharmacy Chat
        </div>
        <div class="chat-messages" id="chatMessages">
            <!-- Messages will be displayed here -->
        </div>
        <div class="chat-input-container">
            <input type="text" class="chat-input" id="chatInput" placeholder="Type a message..." />
            <button class="send-button" onclick="sendMessage()">Send</button>
            <button class="voice-button" id="voiceButton" onclick="toggleRecording()">🎤</button>
        </div>
    </div>

    <script>
        let mediaRecorder;
        let audioChunks = [];
        let isRecording = false;
        const chatHistory = ["Show me all available medicines.", "What medicines are out of stock?"]; // Initial history

        function sendMessage() {
            const input = document.getElementById('chatInput');
            const query = input.value.trim();
            if (query) {
                const sessionId = "user-1234"; // Hardcoded session ID
                const payload = {
                    session_id: sessionId,
                    query: query,
                    history: chatHistory // Add previous chat history
                };

                // Send POST request to the Flask API
                fetch('http://localhost:5000/chat/', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(payload),
                })
                .then(response => response.json())
                .then(data => {
                    displayMessage(query, true); // Display the sent message (bold for user)
                    displayMessage(data.response, false); // Display the response from server
                    chatHistory.push(query); // Add the query to the history
                })
                .catch((error) => {
                    console.error('Error:', error);
                });

                input.value = ''; // Clear the input field
            }
        }

        function displayMessage(message, isUser) {
            const chatMessages = document.getElementById('chatMessages');
            const messageElement = document.createElement('div');
            messageElement.classList.add(isUser ? 'user-message' : 'server-message'); // Add different styles for user and server
            messageElement.textContent = message;
            chatMessages.appendChild(messageElement);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function toggleRecording() {
            if (!isRecording) {
                startRecording();
            } else {
                stopRecording();
            }
        }

        function startRecording() {
            navigator.mediaDevices.getUserMedia({ audio: true })
                .then(stream => {
                    mediaRecorder = new MediaRecorder(stream);
                    mediaRecorder.start();
                    isRecording = true;
                    document.getElementById('voiceButton').textContent = "⏹️"; // Change to stop icon

                    mediaRecorder.ondataavailable = event => {
                        audioChunks.push(event.data);
                    };

                    mediaRecorder.onstop = () => {
                        const audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
                        audioChunks = [];
                        const audioUrl = URL.createObjectURL(audioBlob);
                        const audio = document.createElement('audio');
                        audio.src = audioUrl;
                        audio.controls = true;

                        const chatMessages = document.getElementById('chatMessages');
                        chatMessages.appendChild(audio);
                        chatMessages.scrollTop = chatMessages.scrollHeight;
                    };
                })
                .catch(error => {
                    console.error("Error accessing microphone:", error);
                });
        }

        function stopRecording() {
            mediaRecorder.stop();
            isRecording = false;
            document.getElementById('voiceButton').textContent = "🎤"; // Change back to mic icon
        }
    </script>

</body>
</html>
