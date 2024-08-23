<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Upload to Micro:bit via WebUSB</title>
</head>
<body>
    <h1>Upload .hex File to Micro:bit via WebUSB</h1>
    <input type="file" id="fileInput" accept=".hex">
    <button id="uploadButton">Upload to Micro:bit</button>
    <p id="status"></p>

    <script>
        async function uploadFile() {
            const statusElement = document.getElementById('status');
            statusElement.textContent = '';

            const fileInput = document.getElementById('fileInput');
            const file = fileInput.files[0];

            if (!file) {
                alert('Please select a .hex file to upload.');
                return;
            }

            try {
                // Request the USB device
                const device = await navigator.usb.requestDevice({ filters: [{ vendorId: 0x0D28 }] });
                await device.open();

                // Log available configurations, interfaces, and endpoints
                const configuration = device.configurations[0];
                console.log('Configuration:', configuration);

                let interfaceNumber = null;
                let endpointOut = null;

                // Iterate through interfaces to find the correct one
                for (const iface of configuration.interfaces) {
                    console.log(`Interface Number: ${iface.interfaceNumber}, Class: ${iface.descriptor.interfaceClass}`);
                    if (iface.descriptor.interfaceClass === 2) { // Typically for CDC (Communications)
                        interfaceNumber = iface.interfaceNumber;
                        for (const alternate of iface.alternates) {
                            for (const endpoint of alternate.endpoints) {
                                if (endpoint.direction === 'out') {
                                    endpointOut = endpoint.endpointNumber;
                                    console.log(`Found OUT Endpoint: ${endpointOut}`);
                                    break;
                                }
                            }
                        }
                    }
                }

                if (interfaceNumber === null || endpointOut === null) {
                    throw new Error('No valid OUT endpoint found. Please check your USB device connection.');
                }

                // Claim the correct interface
                await device.claimInterface(interfaceNumber);
                console.log(`Claimed Interface Number: ${interfaceNumber}`);

                // Select the correct alternate setting
                const alternateSetting = 0; // Typically you might want to select the first alternate
                await device.selectAlternateInterface(interfaceNumber, alternateSetting);
                console.log(`Selected Alternate Setting: ${alternateSetting}`);

                // Read the file and send it to the device in chunks
                const arrayBuffer = await file.arrayBuffer();
                const data = new Uint8Array(arrayBuffer);
                const chunkSize = 64;  // Adjust based on device's max packet size

                for (let i = 0; i < data.length; i += chunkSize) {
                    const chunk = data.slice(i, i + chunkSize);
                    await device.transferOut(endpointOut, chunk);
                }

                await device.close();
                statusElement.textContent = 'File uploaded successfully!';
            } catch (error) {
                console.error('Error during upload:', error);
                statusElement.textContent = 'An error occurred during file upload.';
            }
        }

        document.getElementById('uploadButton').addEventListener('click', uploadFile);
    </script>
</body>
</html>
