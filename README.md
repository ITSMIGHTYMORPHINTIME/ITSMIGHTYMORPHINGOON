<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PS99 Outline Tool</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

body {
    font-family: 'Poppins', sans-serif;
    font-weight: 800;
    color: white;
    text-shadow: 1px 1px 0px #000000, -1px -1px 0px #000000, 1px -1px 0px #000000, -1px 1px 0px #000000;
    background: linear-gradient(135deg, #ffffff 0%, #ffffff 100%);
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 20px;
    position: relative;
}

    body::before {
        content: '';
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-image: url('https://tr.rbxcdn.com/180DAY-63613e0c81a7804328b0f97d13522c1f/420/420/Image/Webp/noFilter');
        background-size: 171px 135px;
        background-position: 0 0;
        background-repeat: repeat;
        filter: brightness(0) saturate(100%) invert(89%) sepia(3%) saturate(191%) hue-rotate(202deg) brightness(95%) contrast(88%);
        opacity: 0.1;
        z-index: -1;
    }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        h1 {
            color: white;
            text-align: center;
            margin-bottom: 30px;
            font-size: 2.5em;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .upload-section {
            background: white;
            border-radius: 15px;
            padding: 40px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            margin-bottom: 30px;
        }

        .upload-area {
            border: 3px dashed #667eea;
            border-radius: 10px;
            padding: 60px 20px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
            background: #f8f9ff;
        }

        .upload-area:hover {
            border-color: #000000;
            background: #000000;
        }

        .upload-area.dragover {
            border-color: #000000;
            background: #000000;
            transform: scale(1.02);
        }

        .upload-icon {
            font-size: 60px;
            margin-bottom: 20px;
        }

        .upload-text {
            font-size: 18px;
            color: #555;
            margin-bottom: 10px;
        }

        .upload-subtext {
            font-size: 14px;
            color: #888;
        }

        #fileInput {
            display: none;
        }

        .button {
            background: linear-gradient(135deg, #000000 0%, #000000 100%);
            color: white;
            border: none;
            padding: 12px 30px;
            border-radius: 8px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 15px;
            transition: transform 0.2s ease;
        }

        .button:hover {
            transform: translateY(-2px);
        }

        .images-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-top: 30px;
        }

        .image-card {
            background: white;
            border-radius: 15px;
            padding: 15px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        .preview-section {
            display: flex;
            gap: 15px;
            margin-bottom: 15px;
        }

        .preview-box {
            flex: 1;
            text-align: center;
        }

        .preview-label {
            font-size: 12px;
            color: #666;
            margin-bottom: 8px;
            font-weight: 600;
        }

        .image-container {
            position: relative;
            width: 100%;
            height: 200px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: repeating-conic-gradient(#f0f0f0 0% 25%, #ffffff 0% 50%) 50% / 20px 20px;
            border-radius: 10px;
            overflow: hidden;
            border: 2px solid #e0e0e0;
        }

        .image-container canvas {
            max-width: 100%;
            max-height: 100%;
            object-fit: contain;
            display: block;
        }
        
        .image-container img {
            max-width: 100%;
            max-height: 100%;
            object-fit: contain;
            display: block;
        }

        .image-actions {
            display: flex;
            gap: 10px;
            margin-top: 15px;
        }

        .download-btn, .remove-btn {
            flex: 1;
            padding: 10px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.2s ease;
        }

        .download-btn {
            background: #667eea;
            color: white;
        }

        .download-btn:hover {
            background: #5568d3;
        }

        .remove-btn {
            background: #ff6b6b;
            color: white;
        }

        .remove-btn:hover {
            background: #ff5252;
        }

        .no-images {
            text-align: center;
            color: white;
            font-size: 18px;
            padding: 40px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>PS99 Outline Tool</h1>
        
        <div class="upload-section">
            <div class="upload-area" id="uploadArea">
                <div class="upload-icon">📁</div>
                <div class="upload-text">Drag & drop images here</div>
                <div class="upload-subtext">or click to browse (supports multiple files)</div>
                <button class="button" onclick="document.getElementById('fileInput').click()">Choose Files</button>
            </div>
            <input type="file" id="fileInput" multiple accept="image/*">
        </div>

        <div id="downloadAllContainer" style="text-align: center; margin-bottom: 20px; display: none;">
            <button class="button" onclick="downloadAll()" style="font-size: 18px; padding: 15px 40px;">
                ⬇ Download All Images
            </button>
        </div>

        <div class="images-grid" id="imagesGrid"></div>
    </div>

    <script>
        const uploadArea = document.getElementById('uploadArea');
        const fileInput = document.getElementById('fileInput');
        const imagesGrid = document.getElementById('imagesGrid');
        let images = [];

        ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
            uploadArea.addEventListener(eventName, preventDefaults, false);
            document.body.addEventListener(eventName, preventDefaults, false);
        });

        function preventDefaults(e) {
            e.preventDefault();
            e.stopPropagation();
        }

        ['dragenter', 'dragover'].forEach(eventName => {
            uploadArea.addEventListener(eventName, () => {
                uploadArea.classList.add('dragover');
            }, false);
        });

        ['dragleave', 'drop'].forEach(eventName => {
            uploadArea.addEventListener(eventName, () => {
                uploadArea.classList.remove('dragover');
            }, false);
        });

        uploadArea.addEventListener('drop', (e) => {
            const dt = e.dataTransfer;
            const files = dt.files;
            handleFiles(files);
        }, false);

        fileInput.addEventListener('change', (e) => {
            handleFiles(e.target.files);
        });

        function handleFiles(files) {
            [...files].forEach(file => {
                if (file.type.startsWith('image/')) {
                    processImage(file);
                }
            });
        }

        function processImage(file) {
            const reader = new FileReader();
            
            reader.onload = (e) => {
                const img = new Image();
                img.onload = () => {
                    // Create original canvas
                    const originalCanvas = document.createElement('canvas');
                    const originalCtx = originalCanvas.getContext('2d');
                    originalCanvas.width = img.width;
                    originalCanvas.height = img.height;
                    originalCtx.drawImage(img, 0, 0);
                    
                    // Create outlined canvas
                    const outlineWidth = 11;
                    const outlinedCanvas = document.createElement('canvas');
                    const ctx = outlinedCanvas.getContext('2d');
                    
                    outlinedCanvas.width = img.width + (outlineWidth * 2);
                    outlinedCanvas.height = img.height + (outlineWidth * 2);
                    
                    // Create a temporary canvas to make a solid silhouette
                    const tempCanvas = document.createElement('canvas');
                    const tempCtx = tempCanvas.getContext('2d');
                    tempCanvas.width = img.width;
                    tempCanvas.height = img.height;
                    tempCtx.drawImage(img, 0, 0);
                    
                    // Get image data and create a solid black silhouette
                    const pixelData = tempCtx.getImageData(0, 0, img.width, img.height);
                    const data = pixelData.data;
                    for (let i = 0; i < data.length; i += 4) {
                        // If pixel has any alpha value, make it solid black
                        if (data[i + 3] > 0) {
                            data[i] = 0;     // R
                            data[i + 1] = 0; // G
                            data[i + 2] = 0; // B
                            data[i + 3] = 255; // A - fully opaque
                        }
                    }
                    tempCtx.putImageData(pixelData, 0, 0);
                    
                    // Create the outline by drawing the solid silhouette multiple times
                    const steps = 110; // More steps = smoother outline
                    
                    for (let i = 0; i < steps; i++) {
                        const angle = (Math.PI * 2 * i) / steps;
                        const x = Math.cos(angle) * outlineWidth + outlineWidth;
                        const y = Math.sin(angle) * outlineWidth + outlineWidth;
                        ctx.drawImage(tempCanvas, x, y);
                    }
                    
                    // No need for source-in since tempCanvas is already solid black
                    
                    // Draw the original image on top
                    ctx.drawImage(img, outlineWidth, outlineWidth);
                    
                    const imgData = {
                        id: Date.now() + Math.random(),
                        originalCanvas: originalCanvas,
                        outlinedCanvas: outlinedCanvas,
                        filename: file.name
                    };
                    
                    images.push(imgData);
                    renderImages();
                };
                img.src = e.target.result;
            };
            
            reader.readAsDataURL(file);
        }

        function renderImages() {
            const downloadAllContainer = document.getElementById('downloadAllContainer');
            
            if (images.length === 0) {
                imagesGrid.innerHTML = '<div class="no-images"></div>';
                downloadAllContainer.style.display = 'none';
                return;
            }

            downloadAllContainer.style.display = 'block';
            imagesGrid.innerHTML = '';
            
            images.forEach(imageData => {
                const card = document.createElement('div');
                card.className = 'image-card';
                
                const previewSection = document.createElement('div');
                previewSection.className = 'preview-section';
                
                // Original preview
                const originalBox = document.createElement('div');
                originalBox.className = 'preview-box';
                const originalLabel = document.createElement('div');
                originalLabel.className = 'preview-label';
                originalLabel.textContent = 'ORIGINAL';
                const originalContainer = document.createElement('div');
                originalContainer.className = 'image-container';
                
                // Clone canvas properly by copying content
                const originalCanvasClone = document.createElement('canvas');
                originalCanvasClone.width = imageData.originalCanvas.width;
                originalCanvasClone.height = imageData.originalCanvas.height;
                const originalCloneCtx = originalCanvasClone.getContext('2d');
                originalCloneCtx.drawImage(imageData.originalCanvas, 0, 0);
                
                originalContainer.appendChild(originalCanvasClone);
                originalBox.appendChild(originalLabel);
                originalBox.appendChild(originalContainer);
                
                // Outlined preview
                const outlinedBox = document.createElement('div');
                outlinedBox.className = 'preview-box';
                const outlinedLabel = document.createElement('div');
                outlinedLabel.className = 'preview-label';
                outlinedLabel.textContent = 'WITH OUTLINE';
                const outlinedContainer = document.createElement('div');
                outlinedContainer.className = 'image-container';
                
                // Clone canvas properly by copying content
                const outlinedCanvasClone = document.createElement('canvas');
                outlinedCanvasClone.width = imageData.outlinedCanvas.width;
                outlinedCanvasClone.height = imageData.outlinedCanvas.height;
                const outlinedCloneCtx = outlinedCanvasClone.getContext('2d');
                outlinedCloneCtx.drawImage(imageData.outlinedCanvas, 0, 0);
                
                outlinedContainer.appendChild(outlinedCanvasClone);
                outlinedBox.appendChild(outlinedLabel);
                outlinedBox.appendChild(outlinedContainer);
                
                previewSection.appendChild(originalBox);
                previewSection.appendChild(outlinedBox);
                
                const actions = document.createElement('div');
                actions.className = 'image-actions';
                
                const downloadBtn = document.createElement('button');
                downloadBtn.className = 'download-btn';
                downloadBtn.textContent = '⬇ Download';
                downloadBtn.onclick = () => downloadImage(imageData);
                
                const removeBtn = document.createElement('button');
                removeBtn.className = 'remove-btn';
                removeBtn.textContent = '🗑 Remove';
                removeBtn.onclick = () => removeImage(imageData.id);
                
                actions.appendChild(downloadBtn);
                actions.appendChild(removeBtn);
                
                card.appendChild(previewSection);
                card.appendChild(actions);
                imagesGrid.appendChild(card);
            });
        }

        function downloadImage(imageData) {
            const link = document.createElement('a');
            link.download = 'outlined_' + imageData.filename;
            link.href = imageData.outlinedCanvas.toDataURL('image/png');
            link.click();
        }

        function removeImage(id) {
            images = images.filter(img => img.id !== id);
            renderImages();
        }

        function downloadAll() {
            images.forEach((imageData, index) => {
                setTimeout(() => {
                    const link = document.createElement('a');
                    link.download = 'outlined_' + imageData.filename;
                    link.href = imageData.outlinedCanvas.toDataURL('image/png');
                    link.click();
                }, index * 100);
            });
        }

        renderImages();
    </script>
</body>
</html>
