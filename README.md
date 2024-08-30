<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Micro:bit HEX File Uploader</title>
</head>
<body>
    <h1>Micro:bit HEX File Uploader</h1>
    <input type="file" id="hexFileInput" accept=".hex" />
    <button id="uploadButton">Upload to Micro:bit</button>

    <script>
        const connectToMicrobit = async () => {
            try {
                const device = await navigator.usb.requestDevice({
                    filters: [{ vendorId: 0x0D28 }] // Vendor ID for the micro:bit
                });
                await device.open();
                await device.selectConfiguration(1);
                await device.claimInterface(0);
                return device;
            } catch (error) {
                console.error("Error connecting to micro:bit:", error);
                alert("Failed to connect to micro:bit. Make sure it's plugged in and try again.");
            }
        };

        const uploadHexFile = async (device, file) => {
            try {
                const reader = new FileReader();
                reader.onload = async (event) => {
                    const data = new Uint8Array(event.target.result);
                    await device.transferOut(4, data);
                    alert("HEX file uploaded successfully!");
                };
                reader.readAsArrayBuffer(file);
            } catch (error) {
                console.error("Error uploading HEX file:", error);
                alert("Failed to upload HEX file.");
            }
        };

        document.getElementById("uploadButton").addEventListener("click", async () => {
            const fileInput = document.getElementById("hexFileInput");
            if (fileInput.files.length === 0) {
                alert("Please select a HEX file to upload.");
                return;
            }
            const file = fileInput.files[0];
            const device = await connectToMicrobit();
            if (device) {
                await uploadHexFile(device, file);
                device.close();
            }
        });
    </script>
</body>
</html>
