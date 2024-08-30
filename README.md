<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Micro:bit Python to HEX Generator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        textarea {
            width: 100%;
            height: 200px;
            font-family: monospace;
            font-size: 14px;
            margin-bottom: 20px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Micro:bit Python to HEX Generator</h1>
    <p>Write your Python code below and click "Generate HEX File" to download it as a HEX file for the micro:bit.</p>

    <textarea id="pythonCode" placeholder="Write your Python code here..."></textarea>
    <button id="generateHexButton">Generate HEX File</button>

    <!-- Include the microbit-micropython library via a CDN -->
    <script src="https://cdn.jsdelivr.net/npm/microbit-micropython"></script>

    <script>
        document.getElementById("generateHexButton").addEventListener("click", async function() {
            // Get the Python code from the textarea
            const pythonCode = document.getElementById("pythonCode").value;

            try {
                // Generate the HEX file using microbit-micropython
                const hex = await MicrobitHex.generateHex(pythonCode);

                // Create a Blob from the hex string
                const blob = new Blob([hex], { type: 'text/plain' });

                // Create a link to download the file
                const link = document.createElement('a');
                link.href = URL.createObjectURL(blob);
                link.download = 'microbit.hex';
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
            } catch (error) {
                console.error("Error generating HEX file:", error);
                alert("Failed to generate HEX file. Please check your Python code and try again.");
            }
        });
    </script>
</body>
</html>
