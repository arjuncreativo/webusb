<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pharmacy Chat</title>
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
            <button class="voice-button" onclick="recordVoice()">🎤</button>
        </div>
    </div>

    <script>
        function sendMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();
            if (message) {
                const chatMessages = document.getElementById('chatMessages');
                const messageElement = document.createElement('div');
                messageElement.textContent = message;
                chatMessages.appendChild(messageElement);
                input.value = '';
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
        }

        function recordVoice() {
            alert("Voice recording functionality is not yet implemented.");
        }
    </script>

</body>
</html>
