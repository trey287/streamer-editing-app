# streamer-editing-app
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Streamer's Creative Hub</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter font from Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a202c; /* Dark background */
            color: #e2e8f0; /* Light text */
        }
        .container {
            max-width: 1200px;
        }
        .card {
            background-color: #2d3748; /* Slightly lighter dark for cards */
            border-radius: 0.75rem; /* Rounded corners */
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 16px;
            height: 16px;
            border-radius: 50%;
            background: #63b3ed; /* Blue thumb */
            cursor: pointer;
            box-shadow: 0 0 0 3px rgba(99, 179, 237, 0.5);
        }
        input[type="range"]::-moz-range-thumb {
            width: 16px;
            height: 16px;
            border-radius: 50%;
            background: #63b3ed;
            cursor: pointer;
            box-shadow: 0 0 0 3px rgba(99, 179, 237, 0.5);
        }
        .tab-button.active {
            background-color: #4a5568; /* Active tab background */
            color: #a0aec0; /* Active tab text */
        }
        /* Custom scrollbar for better aesthetics */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #2d3748;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #4a5568;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #63b3ed;
        }
    </style>
</head>
<body class="p-6">
    <div class="container mx-auto p-4">
        <h1 class="text-4xl font-bold text-center mb-8 text-blue-400">Streamer's Creative Hub</h1>

        <!-- Tab Navigation -->
        <div class="flex justify-center mb-8">
            <button id="thumbnailTabBtn" class="tab-button px-6 py-3 rounded-l-lg text-lg font-semibold bg-gray-700 text-gray-300 hover:bg-gray-600 focus:outline-none transition-colors duration-200 active">AI Thumbnail Generator</button>
            <button id="editorTabBtn" class="tab-button px-6 py-3 rounded-r-lg text-lg font-semibold bg-gray-700 text-gray-300 hover:bg-gray-600 focus:outline-none transition-colors duration-200">Video Editor</button>
        </div>

        <!-- AI Thumbnail Generator Section -->
        <div id="thumbnailSection" class="tab-content card p-6 mb-8">
            <h2 class="text-2xl font-semibold mb-4 text-blue-300">AI Thumbnail Generator</h2>
            <p class="text-gray-400 mb-6">
                Capture your face, upload a style image from another streamer, and conceptually generate a thumbnail.
                (Note: Full AI style transfer and face detection are complex and typically require server-side processing or advanced WebAssembly libraries. This is a conceptual UI.)
            </p>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Webcam Feed -->
                <div class="flex flex-col items-center">
                    <h3 class="text-xl font-medium mb-3">Your Face Capture</h3>
                    <video id="webcamFeed" class="w-full max-w-md rounded-lg border-2 border-gray-600 bg-black" autoplay playsinline></video>
                    <button id="captureFaceBtn" class="mt-4 px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50">
                        Capture Face
                    </button>
                </div>

                <!-- Style Image Upload -->
                <div class="flex flex-col items-center">
                    <h3 class="text-xl font-medium mb-3">Upload Streamer Style Thumbnail</h3>
                    <div class="w-full max-w-md h-64 flex items-center justify-center border-2 border-dashed border-gray-600 rounded-lg bg-gray-800 mb-4">
                        <input type="file" id="styleImageInput" accept="image/*" class="hidden" onchange="previewStyleImage(event)">
                        <label for="styleImageInput" class="cursor-pointer text-gray-400 hover:text-blue-400 transition-colors duration-200">
                            <span class="text-5xl">+</span><br>
                            Click to Upload Style Image
                        </label>
                        <img id="styleImagePreview" class="hidden w-full h-full object-cover rounded-lg" alt="Style Preview">
                    </div>
                    <p class="text-sm text-gray-500">Upload a thumbnail from another streamer to mimic its style.</p>
                </div>
            </div>

            <!-- Generated Thumbnail Preview -->
            <div class="mt-8 text-center">
                <h3 class="text-xl font-medium mb-3">Generated Thumbnail Preview</h3>
                <canvas id="thumbnailCanvas" class="w-full max-w-lg h-64 rounded-lg border-2 border-gray-600 bg-gray-800 mx-auto"></canvas>
                <button id="generateThumbnailBtn" class="mt-6 px-8 py-4 bg-green-600 text-white text-xl font-bold rounded-lg hover:bg-green-700 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-opacity-50">
                    Generate Thumbnail
                </button>
            </div>
        </div>

        <!-- Video Editing Section -->
        <div id="editorSection" class="tab-content card p-6 mb-8 hidden">
            <h2 class="text-2xl font-semibold mb-4 text-blue-300">Video Editor</h2>
            <p class="text-gray-400 mb-6">
                Upload your long-form video, mark funny moments, and add conceptual memes/sound effects.
                (Note: Actual video processing and rendering in the browser are highly complex. This UI simulates the editing workflow.)
            </p>

            <!-- Video Player -->
            <div class="mb-6">
                <input type="file" id="videoInput" accept="video/*" class="hidden" onchange="loadVideo(event)">
                <label for="videoInput" class="block text-center px-6 py-3 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 cursor-pointer transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-opacity-50">
                    Upload Your Stream Video
                </label>
                <video id="videoPlayer" class="w-full mt-4 rounded-lg border-2 border-gray-600 bg-black" controls></video>
            </div>

            <!-- Video Controls and Timeline -->
            <div class="flex items-center justify-between mb-4">
                <button id="playPauseBtn" class="px-4 py-2 bg-gray-600 text-white rounded-lg hover:bg-gray-700 transition-colors duration-200">Play/Pause</button>
                <span id="currentTime" class="text-lg font-mono">00:00 / 00:00</span>
                <button id="markMomentBtn" class="px-4 py-2 bg-yellow-600 text-white rounded-lg hover:bg-yellow-700 transition-colors duration-200">Mark Funny Moment</button>
            </div>
            <input type="range" id="videoProgress" value="0" min="0" max="100" class="w-full h-2 bg-gray-700 rounded-lg appearance-none cursor-pointer mb-6">

            <!-- Meme & SFX Controls -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
                <div class="card p-4">
                    <h3 class="text-xl font-medium mb-3">Add Meme (Image/Video)</h3>
                    <input type="file" id="memeInput" accept="image/*,video/*" class="hidden" onchange="addMeme(event)">
                    <label for="memeInput" class="block text-center px-4 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 cursor-pointer transition-colors duration-200">
                        Upload Meme
                    </label>
                    <p class="text-sm text-gray-500 mt-2">Will be conceptually added at current video time.</p>
                </div>
                <div class="card p-4">
                    <h3 class="text-xl font-medium mb-3">Add Sound Effect</h3>
                    <input type="file" id="sfxInput" accept="audio/*" class="hidden" onchange="addSFX(event)">
                    <label for="sfxInput" class="block text-center px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 cursor-pointer transition-colors duration-200">
                        Upload Sound Effect
                    </label>
                    <p class="text-sm text-gray-500 mt-2">Will be conceptually added at current video time.</p>
                </div>
            </div>

            <!-- Editing Timeline / Events List -->
            <div class="card p-6 mb-6">
                <h3 class="text-xl font-medium mb-4">Editing Timeline / Events</h3>
                <div id="editEventsList" class="max-h-64 overflow-y-auto pr-2">
                    <p class="text-gray-500 text-center">No events added yet.</p>
                </div>
            </div>

            <!-- Output Generation -->
            <div class="flex flex-col sm:flex-row justify-center gap-4">
                <button id="generateLongFormBtn" class="px-8 py-4 bg-teal-600 text-white text-xl font-bold rounded-lg hover:bg-teal-700 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-opacity-50">
                    Generate Long Form (Conceptual)
                </button>
                <button id="generateShortFormBtn" class="px-8 py-4 bg-orange-600 text-white text-xl font-bold rounded-lg hover:bg-orange-700 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-orange-500 focus:ring-opacity-50">
                    Generate Short Form (Conceptual)
                </button>
            </div>
            <div id="outputMessage" class="mt-6 text-center text-lg font-semibold text-gray-300"></div>
        </div>
    </div>

    <script>
        // --- Global Variables ---
        let webcamStream;
        let capturedFaceImage = null;
        let styleImage = null;
        let videoPlayer = document.getElementById('videoPlayer');
        let videoProgress = document.getElementById('videoProgress');
        let currentTimeSpan = document.getElementById('currentTime');
        let editEvents = []; // Stores objects like { type: 'moment'/'meme'/'sfx', time: '00:00', content: '...' }

        // --- Utility Functions ---
        function formatTime(seconds) {
            const minutes = Math.floor(seconds / 60);
            const remainingSeconds = Math.floor(seconds % 60);
            return `${String(minutes).padStart(2, '0')}:${String(remainingSeconds).padStart(2, '0')}`;
        }

        function showMessage(message, type = 'info') {
            const outputMessage = document.getElementById('outputMessage');
            outputMessage.textContent = message;
            outputMessage.className = `mt-6 text-center text-lg font-semibold rounded-md p-3`;
            if (type === 'success') {
                outputMessage.classList.add('bg-green-800', 'text-green-200');
            } else if (type === 'error') {
                outputMessage.classList.add('bg-red-800', 'text-red-200');
            } else {
                outputMessage.classList.add('bg-blue-800', 'text-blue-200');
            }
            setTimeout(() => {
                outputMessage.textContent = '';
                outputMessage.className = `mt-6 text-center text-lg font-semibold text-gray-300`;
            }, 5000);
        }

        // --- Tab Switching Logic ---
        const thumbnailTabBtn = document.getElementById('thumbnailTabBtn');
        const editorTabBtn = document.getElementById('editorTabBtn');
        const thumbnailSection = document.getElementById('thumbnailSection');
        const editorSection = document.getElementById('editorSection');

        function switchTab(tabName) {
            if (tabName === 'thumbnail') {
                thumbnailSection.classList.remove('hidden');
                editorSection.classList.add('hidden');
                thumbnailTabBtn.classList.add('active');
                editorTabBtn.classList.remove('active');
                startWebcam(); // Start webcam when thumbnail tab is active
            } else {
                thumbnailSection.classList.add('hidden');
                editorSection.classList.remove('hidden');
                thumbnailTabBtn.classList.remove('active');
                editorTabBtn.classList.add('active');
                stopWebcam(); // Stop webcam when editor tab is active
            }
        }

        thumbnailTabBtn.addEventListener('click', () => switchTab('thumbnail'));
        editorTabBtn.addEventListener('click', () => switchTab('editor'));

        // --- AI Thumbnail Generator Functions ---

        // Start webcam feed
        async function startWebcam() {
            const webcamFeed = document.getElementById('webcamFeed');
            try {
                webcamStream = await navigator.mediaDevices.getUserMedia({ video: true });
                webcamFeed.srcObject = webcamStream;
                webcamFeed.play();
            } catch (err) {
                console.error("Error accessing webcam: ", err);
                showMessage("Could not access webcam. Please grant camera permissions.", 'error');
            }
        }

        // Stop webcam feed
        function stopWebcam() {
            if (webcamStream) {
                webcamStream.getTracks().forEach(track => track.stop());
                webcamStream = null;
            }
        }

        // Capture face from webcam
        document.getElementById('captureFaceBtn').addEventListener('click', () => {
            const webcamFeed = document.getElementById('webcamFeed');
            const canvas = document.createElement('canvas');
            canvas.width = webcamFeed.videoWidth;
            canvas.height = webcamFeed.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(webcamFeed, 0, 0, canvas.width, canvas.height);
            capturedFaceImage = new Image();
            capturedFaceImage.src = canvas.toDataURL('image/png');
            showMessage("Face captured! Now upload a style image.", 'success');
        });

        // Preview uploaded style image
        function previewStyleImage(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const styleImagePreview = document.getElementById('styleImagePreview');
                    styleImagePreview.src = e.target.result;
                    styleImagePreview.classList.remove('hidden');
                    // Hide the label text
                    event.target.previousElementSibling.classList.add('hidden');
                    styleImage = new Image();
                    styleImage.src = e.target.result;
                    showMessage("Style image uploaded! Click 'Generate Thumbnail'.", 'success');
                };
                reader.readAsDataURL(file);
            }
        }

        // Generate conceptual thumbnail
        document.getElementById('generateThumbnailBtn').addEventListener('click', () => {
            const thumbnailCanvas = document.getElementById('thumbnailCanvas');
            const ctx = thumbnailCanvas.getContext('2d');

            // Clear canvas
            ctx.clearRect(0, 0, thumbnailCanvas.width, thumbnailCanvas.height);
            ctx.fillStyle = '#2d3748'; // Card background color
            ctx.fillRect(0, 0, thumbnailCanvas.width, thumbnailCanvas.height);

            if (!capturedFaceImage) {
                showMessage("Please capture your face first!", 'error');
                return;
            }
            if (!styleImage) {
                showMessage("Please upload a style image first!", 'error');
                return;
            }

            // Conceptual generation: Draw style image, then overlay face
            // This is a placeholder for actual AI style transfer
            const aspectRatio = styleImage.width / styleImage.height;
            let drawWidth = thumbnailCanvas.width;
            let drawHeight = thumbnailCanvas.width / aspectRatio;

            if (drawHeight > thumbnailCanvas.height) {
                drawHeight = thumbnailCanvas.height;
                drawWidth = thumbnailCanvas.height * aspectRatio;
            }

            const offsetX = (thumbnailCanvas.width - drawWidth) / 2;
            const offsetY = (thumbnailCanvas.height - drawHeight) / 2;

            ctx.drawImage(styleImage, offsetX, offsetY, drawWidth, drawHeight);

            // Overlay captured face (simplified: just draw it on top)
            // In a real AI scenario, the face would be integrated into the style.
            const faceSize = Math.min(thumbnailCanvas.width, thumbnailCanvas.height) * 0.5; // Adjust size as needed
            const faceX = (thumbnailCanvas.width - faceSize) / 2;
            const faceY = (thumbnailCanvas.height - faceSize) / 2;
            ctx.drawImage(capturedFaceImage, faceX, faceY, faceSize, faceSize);

            // Add some conceptual text overlay
            ctx.fillStyle = 'white';
            ctx.font = 'bold 24px Inter';
            ctx.textAlign = 'center';
            ctx.fillText("Your AI Thumbnail!", thumbnailCanvas.width / 2, 30);
            ctx.fillText("Styled by [Streamer Name]", thumbnailCanvas.width / 2, thumbnailCanvas.height - 20);

            showMessage("Conceptual thumbnail generated!", 'success');
        });

        // --- Video Editor Functions ---

        // Load video from input
        function loadVideo(event) {
            const file = event.target.files[0];
            if (file) {
                const videoURL = URL.createObjectURL(file);
                videoPlayer.src = videoURL;
                videoPlayer.load();
                videoPlayer.onloadedmetadata = () => {
                    videoProgress.max = videoPlayer.duration;
                    currentTimeSpan.textContent = `${formatTime(0)} / ${formatTime(videoPlayer.duration)}`;
                    showMessage("Video loaded! Ready for editing.", 'success');
                };
            }
        }

        // Play/Pause video
        document.getElementById('playPauseBtn').addEventListener('click', () => {
            if (videoPlayer.paused) {
                videoPlayer.play();
            } else {
                videoPlayer.pause();
            }
        });

        // Update progress bar and time display
        videoPlayer.addEventListener('timeupdate', () => {
            videoProgress.value = videoPlayer.currentTime;
            currentTimeSpan.textContent = `${formatTime(videoPlayer.currentTime)} / ${formatTime(videoPlayer.duration)}`;
        });

        // Seek video on progress bar change
        videoProgress.addEventListener('input', () => {
            videoPlayer.currentTime = videoProgress.value;
        });

        // Mark funny moment
        document.getElementById('markMomentBtn').addEventListener('click', () => {
            if (videoPlayer.src) {
                const currentTime = videoPlayer.currentTime;
                const formattedTime = formatTime(currentTime);
                editEvents.push({ type: 'moment', time: formattedTime, timestamp: currentTime });
                updateEditEventsList();
                showMessage(`Funny moment marked at ${formattedTime}!`, 'info');
            } else {
                showMessage("Please upload a video first!", 'error');
            }
        });

        // Add meme (conceptual)
        function addMeme(event) {
            if (!videoPlayer.src) {
                showMessage("Please upload a video first!", 'error');
                return;
            }
            const file = event.target.files[0];
            if (file) {
                const currentTime = videoPlayer.currentTime;
                const formattedTime = formatTime(currentTime);
                editEvents.push({ type: 'meme', time: formattedTime, timestamp: currentTime, file: file.name });
                updateEditEventsList();
                showMessage(`Meme '${file.name}' added conceptually at ${formattedTime}.`, 'info');
            }
        }

        // Add sound effect (conceptual)
        function addSFX(event) {
            if (!videoPlayer.src) {
                showMessage("Please upload a video first!", 'error');
                return;
            }
            const file = event.target.files[0];
            if (file) {
                const currentTime = videoPlayer.currentTime;
                const formattedTime = formatTime(currentTime);
                editEvents.push({ type: 'sfx', time: formattedTime, timestamp: currentTime, file: file.name });
                updateEditEventsList();
                showMessage(`Sound effect '${file.name}' added conceptually at ${formattedTime}.`, 'info');
            }
        }

        // Update the list of editing events
        function updateEditEventsList() {
            const listContainer = document.getElementById('editEventsList');
            listContainer.innerHTML = ''; // Clear existing list

            if (editEvents.length === 0) {
                listContainer.innerHTML = '<p class="text-gray-500 text-center">No events added yet.</p>';
                return;
            }

            // Sort events by timestamp
            editEvents.sort((a, b) => a.timestamp - b.timestamp);

            editEvents.forEach((event, index) => {
                const item = document.createElement('div');
                item.className = 'flex items-center justify-between p-3 mb-2 rounded-md bg-gray-700 hover:bg-gray-600 transition-colors duration-200';
                let icon = '';
                let text = '';
                let textColor = 'text-gray-300';

                if (event.type === 'moment') {
                    icon = '😂';
                    text = `Funny Moment at ${event.time}`;
                    textColor = 'text-yellow-300';
                } else if (event.type === 'meme') {
                    icon = '🖼️';
                    text = `Meme (${event.file}) at ${event.time}`;
                    textColor = 'text-purple-300';
                } else if (event.type === 'sfx') {
                    icon = '🔊';
                    text = `SFX (${event.file}) at ${event.time}`;
                    textColor = 'text-red-300';
                }

                item.innerHTML = `
                    <span class="text-xl mr-3">${icon}</span>
                    <span class="flex-grow ${textColor}">${text}</span>
                    <button class="ml-4 px-3 py-1 bg-red-700 text-white text-sm rounded-md hover:bg-red-800 transition-colors duration-200" onclick="removeEditEvent(${index})">Remove</button>
                `;
                listContainer.appendChild(item);
            });
        }

        // Remove an editing event
        function removeEditEvent(index) {
            editEvents.splice(index, 1);
            updateEditEventsList();
            showMessage("Event removed.", 'info');
        }

        // Generate Long Form (Conceptual)
        document.getElementById('generateLongFormBtn').addEventListener('click', () => {
            if (!videoPlayer.src) {
                showMessage("Please upload a video first!", 'error');
                return;
            }
            let output = `--- Conceptual Long Form Video Output ---\n`;
            output += `Original Video: ${videoPlayer.src.split('/').pop()}\n`;
            output += `Total Duration: ${formatTime(videoPlayer.duration)}\n\n`;
            output += `Applied Edits:\n`;
            if (editEvents.length === 0) {
                output += `  No specific edits marked.\n`;
            } else {
                editEvents.forEach(event => {
                    output += `  - [${event.type.toUpperCase()}] at ${event.time}`;
                    if (event.file) output += ` (File: ${event.file})`;
                    output += `\n`;
                });
            }
            output += `\n--- End of Conceptual Output ---\n`;
            console.log(output);
            showMessage("Long form video concept generated! (Check console for details)", 'success');
            // In a real app, this would trigger server-side video rendering or a WebAssembly process.
        });

        // Generate Short Form (Conceptual)
        document.getElementById('generateShortFormBtn').addEventListener('click', () => {
            if (!videoPlayer.src) {
                showMessage("Please upload a video first!", 'error');
                return;
            }
            if (editEvents.filter(e => e.type === 'moment').length === 0) {
                showMessage("Mark at least one 'Funny Moment' to generate a short form!", 'error');
                return;
            }

            // For short form, let's conceptually pick a segment around the first funny moment
            const firstFunnyMoment = editEvents.find(e => e.type === 'moment');
            let startTime = 0;
            let endTime = videoPlayer.duration;

            if (firstFunnyMoment) {
                // Example: Short form is 15 seconds around the funny moment
                const segmentDuration = 15; // seconds
                startTime = Math.max(0, firstFunnyMoment.timestamp - (segmentDuration / 2));
                endTime = Math.min(videoPlayer.duration, firstFunnyMoment.timestamp + (segmentDuration / 2));
            }

            let output = `--- Conceptual Short Form Video Output ---\n`;
            output += `Original Video: ${videoPlayer.src.split('/').pop()}\n`;
            output += `Segment: ${formatTime(startTime)} - ${formatTime(endTime)}\n`;
            output += `Conceptual Duration: ${formatTime(endTime - startTime)}\n\n`;
            output += `Applied Edits within segment:\n`;
            const relevantEvents = editEvents.filter(event => event.timestamp >= startTime && event.timestamp <= endTime);
            if (relevantEvents.length === 0) {
                output += `  No specific edits within this segment.\n`;
            } else {
                relevantEvents.forEach(event => {
                    output += `  - [${event.type.toUpperCase()}] at ${event.time}`;
                    if (event.file) output += ` (File: ${event.file})`;
                    output += `\n`;
                });
            }
            output += `\n--- End of Conceptual Output ---\n`;
            console.log(output);
            showMessage("Short form video concept generated! (Check console for details)", 'success');
            // In a real app, this would trigger server-side video rendering or a WebAssembly process.
        });

        // --- Initial Setup ---
        window.onload = () => {
            switchTab('thumbnail'); // Start with the thumbnail tab active
        };
    </script>
</body>
</html>

