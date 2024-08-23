<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Upload HEX File to Micro:bit</title>
</head>
<body>
    <h1>Upload .hex File to Micro:bit</h1>
    <input type="file" id="fileInput" accept=".hex">
    <button id="uploadButton">Upload to Micro:bit</button>

    <script>
        document.getElementById('uploadButton').addEventListener('click', async () => {
            const fileInput = document.getElementById('fileInput');
            const file = fileInput.files[0];

            if (!file) {
                alert('Please select a .hex file to upload.');
                return;
            }

            try {
                // Request the micro:bit USB device
                const device = await navigator.usb.requestDevice({ filters: [{ vendorId: 0x0D28 }] });
                await device.open();
                
                // Select configuration and claim interface
                await device.selectConfiguration(1);

                try{
                await device.claimInterface(0);
                }
                catch(error){
                console.log(error)
                }
                try{
                await device.claimInterface(1);
                }
                catch(error){
                console.log(error)
                }
                try{
                await device.claimInterface(3);
                }
                catch(error){
                console.log(error)
                }
                try{
                await device.claimInterface(2);
                }
                catch(error){
                console.log(error)
                }
               

                // Convert file to ArrayBuffer and send it in chunks
                const arrayBuffer = await file.arrayBuffer();
                const data = new Uint8Array(arrayBuffer);
                const chunkSize = 64;  // Max packet size
                const endpointNumber = 2;  // The typical endpoint number for bulk transfers

                for (let i = 0; i < data.length; i += chunkSize) {
                    const chunk = data.slice(i, i + chunkSize);
                    await device.transferOut(endpointNumber, chunk);
                }

                await device.close();
                alert('File uploaded successfully!');
            } catch (error) {
                console.error('Error during upload:', error);
                alert('An error occurred during file upload.');
            }
        });
    </script>
</body>
</html>
