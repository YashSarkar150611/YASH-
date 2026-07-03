<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>YASH - Watch Customizer</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            max-width: 1200px;
            width: 100%;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            padding: 40px;
        }

        @media (max-width: 900px) {
            .container {
                grid-template-columns: 1fr;
                gap: 20px;
                padding: 20px;
            }
        }

        h1 {
            color: #667eea;
            margin-bottom: 30px;
            text-align: center;
            grid-column: 1 / -1;
            font-size: 2.5em;
        }

        .viewer-section {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        #canvas {
            width: 100%;
            height: 400px;
            border: 2px solid #ddd;
            border-radius: 15px;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            cursor: grab;
            touch-action: none;
        }

        #canvas:active {
            cursor: grabbing;
        }

        .canvas-info {
            background: #f0f4ff;
            padding: 15px;
            border-radius: 10px;
            font-size: 0.9em;
            color: #666;
            text-align: center;
        }

        .customizer-section {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .control-group {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 15px;
            border-left: 4px solid #667eea;
        }

        .control-group h3 {
            color: #667eea;
            margin-bottom: 15px;
            font-size: 1.1em;
        }

        .color-picker {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            margin-bottom: 10px;
        }

        .color-btn {
            width: 100%;
            aspect-ratio: 1;
            border: 3px solid transparent;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 0.7em;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
        }

        .color-btn:hover {
            transform: scale(1.1);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        .color-btn.active {
            border-color: #333;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
            transform: scale(1.15);
        }

        .button-group {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        button {
            flex: 1;
            min-width: 120px;
            padding: 12px 20px;
            border: none;
            border-radius: 10px;
            font-size: 1em;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .btn-primary {
            background: #667eea;
            color: white;
        }

        .btn-primary:hover {
            background: #5568d3;
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
        }

        .btn-secondary {
            background: #f0f0f0;
            color: #333;
        }

        .btn-secondary:hover {
            background: #e0e0e0;
            transform: translateY(-2px);
        }

        .btn-ar {
            background: #ff6b6b;
            color: white;
            grid-column: 1 / -1;
            padding: 15px;
            font-size: 1.1em;
        }

        .btn-ar:hover {
            background: #ff5252;
            box-shadow: 0 10px 25px rgba(255, 107, 107, 0.3);
        }

        .info-box {
            background: #fff3cd;
            border-left: 4px solid #ffc107;
            padding: 15px;
            border-radius: 10px;
            color: #856404;
            font-size: 0.9em;
        }

        .info-box strong {
            color: #333;
        }

        /* AR Mode */
        #arCanvas {
            width: 100%;
            height: 100vh;
            display: block;
        }

        .ar-controls {
            position: fixed;
            top: 20px;
            left: 20px;
            background: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 20px;
            border-radius: 15px;
            z-index: 1000;
            max-width: 300px;
        }

        .ar-controls h2 {
            margin-bottom: 15px;
            font-size: 1.2em;
        }

        .ar-controls p {
            margin-bottom: 10px;
            font-size: 0.9em;
            line-height: 1.4;
        }

        .ar-close-btn {
            position: fixed;
            bottom: 30px;
            right: 30px;
            background: #ff6b6b;
            color: white;
            border: none;
            padding: 15px 30px;
            border-radius: 50px;
            font-size: 1em;
            font-weight: bold;
            cursor: pointer;
            z-index: 1001;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            transition: all 0.3s ease;
        }

        .ar-close-btn:hover {
            transform: scale(1.1);
        }

        .preloader {
            text-align: center;
            padding: 20px;
            color: #666;
        }

        .spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #667eea;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            margin: 10px auto;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .preset-buttons {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
        }

        .preset-buttons button {
            background: #f0f0f0;
            color: #333;
            padding: 12px;
            font-size: 0.9em;
        }

        .preset-buttons button:hover {
            background: #667eea;
            color: white;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🕐 YASH Watch Customizer</h1>

        <!-- Left Side: 3D Viewer -->
        <div class="viewer-section">
            <canvas id="canvas"></canvas>
            <div class="canvas-info">
                <strong>💡 Tip:</strong> Drag to rotate • Scroll to zoom
            </div>
        </div>

        <!-- Right Side: Customizer Controls -->
        <div class="customizer-section">
            <!-- Dial Color -->
            <div class="control-group">
                <h3>📋 Dial Color</h3>
                <div class="color-picker" id="dialColors"></div>
            </div>

            <!-- Strap Color -->
            <div class="control-group">
                <h3>⌚ Strap Color</h3>
                <div class="color-picker" id="strapColors"></div>
            </div>

            <!-- Frame Color -->
            <div class="control-group">
                <h3>🔰 Frame Color</h3>
                <div class="color-picker" id="frameColors"></div>
            </div>

            <!-- Presets -->
            <div class="control-group">
                <h3>⚡ Quick Presets</h3>
                <div class="preset-buttons">
                    <button class="btn-secondary" onclick="applyPreset('classic')">Classic</button>
                    <button class="btn-secondary" onclick="applyPreset('sport')">Sport</button>
                    <button class="btn-secondary" onclick="applyPreset('luxury')">Luxury</button>
                    <button class="btn-secondary" onclick="applyPreset('neon')">Neon</button>
                </div>
            </div>

            <!-- Action Buttons -->
            <div class="button-group">
                <button class="btn-secondary" onclick="resetWatch()">↻ Reset</button>
                <button class="btn-secondary" onclick="downloadImage()">⬇️ Download</button>
            </div>

            <!-- AR Try-On -->
            <button class="btn-ar" onclick="startAR()">📱 Try AR on Your Wrist!</button>

            <!-- Info -->
            <div class="info-box">
                <strong>🎉 Festival Tip:</strong> Use AR mode on your phone to show friends your custom watch on your wrist!
            </div>
        </div>
    </div>

    <!-- Scripts -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@babylonjs/core@6.0.0/babylon.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@mediapipe/selfie_segmentation@0.1/selfie_segmentation.js"></script>

    <script>
        // ==================== WATCH STATE ====================
        const watchState = {
            dialColor: '#000000',
            strapColor: '#1a1a1a',
            frameColor: '#c0c0c0'
        };

        const colorPalette = {
            blacks: [
                { name: 'Black', hex: '#000000' },
                { name: 'Charcoal', hex: '#36454f' },
                { name: 'Dark Grey', hex: '#2f4f4f' },
            ],
            whites: [
                { name: 'White', hex: '#ffffff' },
                { name: 'Cream', hex: '#fffdd0' },
                { name: 'Off-White', hex: '#f5f5f5' },
            ],
            blues: [
                { name: 'Navy', hex: '#000080' },
                { name: 'Blue', hex: '#0066ff' },
                { name: 'Sky Blue', hex: '#87ceeb' },
            ],
            reds: [
                { name: 'Red', hex: '#ff0000' },
                { name: 'Crimson', hex: '#dc143c' },
                { name: 'Maroon', hex: '#800000' },
            ],
            metals: [
                { name: 'Silver', hex: '#c0c0c0' },
                { name: 'Gold', hex: '#ffd700' },
                { name: 'Rose Gold', hex: '#b76e79' },
            ]
        };

        // ==================== THREE.JS SETUP ====================
        let scene, camera, renderer, watchGroup, dialMesh, strapMesh, frameMesh, handsMesh;
        let isDragging = false, previousMousePosition = { x: 0, y: 0 };

        function initThreeJS() {
            const canvas = document.getElementById('canvas');
            
            // Scene setup
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0xf5f7fa);
            scene.fog = new THREE.Fog(0xf5f7fa, 100, 500);

            // Camera
            camera = new THREE.PerspectiveCamera(
                75,
                canvas.clientWidth / canvas.clientHeight,
                0.1,
                1000
            );
            camera.position.set(0, 0, 2);

            // Renderer
            renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
            renderer.setSize(canvas.clientWidth, canvas.clientHeight);
            renderer.setPixelRatio(window.devicePixelRatio);
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFShadowShadowMap;

            // Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            scene.add(ambientLight);

            const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
            directionalLight.position.set(5, 5, 5);
            directionalLight.castShadow = true;
            directionalLight.shadow.mapSize.width = 2048;
            directionalLight.shadow.mapSize.height = 2048;
            scene.add(directionalLight);

            // Watch Group
            watchGroup = new THREE.Group();
            scene.add(watchGroup);

            // Create watch components
            createWatchComponents();

            // Mouse controls
            canvas.addEventListener('mousedown', onMouseDown);
            canvas.addEventListener('mousemove', onMouseMove);
            canvas.addEventListener('mouseup', onMouseUp);
            canvas.addEventListener('wheel', onMouseWheel, false);
            canvas.addEventListener('touchstart', onTouchStart);
            canvas.addEventListener('touchmove', onTouchMove);
            canvas.addEventListener('touchend', onTouchEnd);

            // Window resize
            window.addEventListener('resize', onWindowResize);

            // Start animation loop
            animate();
        }

        function createWatchComponents() {
            // Frame (outer case)
            const frameGeometry = new THREE.CylinderGeometry(1, 1, 0.2, 64);
            const frameMaterial = new THREE.MeshStandardMaterial({
                color: watchState.frameColor,
                metalness: 0.9,
                roughness: 0.1
            });
            frameMesh = new THREE.Mesh(frameGeometry, frameMaterial);
            frameMesh.castShadow = true;
            watchGroup.add(frameMesh);

            // Dial (face)
            const dialGeometry = new THREE.CylinderGeometry(0.95, 0.95, 0.15, 64);
            const dialMaterial = new THREE.MeshStandardMaterial({
                color: watchState.dialColor,
                metalness: 0.3,
                roughness: 0.7
            });
            dialMesh = new THREE.Mesh(dialGeometry, dialMaterial);
            dialMesh.position.z = 0.1;
            dialMesh.castShadow = true;
            watchGroup.add(dialMesh);

            // Strap (left)
            const strapGeometry = new THREE.BoxGeometry(0.3, 2, 0.15);
            const strapMaterial = new THREE.MeshStandardMaterial({
                color: watchState.strapColor,
                metalness: 0.2,
                roughness: 0.8
            });
            strapMesh = new THREE.Mesh(strapGeometry, strapMaterial);
            strapMesh.position.set(-0.65, 0, 0);
            strapMesh.castShadow = true;
            watchGroup.add(strapMesh);

            // Strap (right)
            const strapRight = new THREE.Mesh(strapGeometry, strapMaterial);
            strapRight.position.set(0.65, 0, 0);
            strapRight.castShadow = true;
            watchGroup.add(strapRight);

            // Clock hands
            createClockHands();

            // Markers on dial
            for (let i = 0; i < 12; i++) {
                const angle = (i / 12) * Math.PI * 2;
                const x = Math.sin(angle) * 0.85;
                const y = Math.cos(angle) * 0.85;

                const markerGeometry = new THREE.BoxGeometry(0.05, 0.1, 0.05);
                const markerMaterial = new THREE.MeshStandardMaterial({ color: 0xffffff });
                const marker = new THREE.Mesh(markerGeometry, markerMaterial);
                marker.position.set(x, y, 0.15);
                watchGroup.add(marker);
            }
        }

        function createClockHands() {
            // Hour hand
            const hourHandGeometry = new THREE.BoxGeometry(0.08, 0.4, 0.05);
            const handMaterial = new THREE.MeshStandardMaterial({ color: 0xffffff });
            const hourHand = new THREE.Mesh(hourHandGeometry, handMaterial);
            hourHand.position.z = 0.15;
            hourHand.userData.type = 'hour';
            watchGroup.add(hourHand);

            // Minute hand
            const minuteHandGeometry = new THREE.BoxGeometry(0.06, 0.55, 0.05);
            const minuteHand = new THREE.Mesh(minuteHandGeometry, handMaterial);
            minuteHand.position.z = 0.16;
            minuteHand.userData.type = 'minute';
            watchGroup.add(minuteHand);

            // Second hand
            const secondHandGeometry = new THREE.BoxGeometry(0.03, 0.6, 0.05);
            const secondMaterial = new THREE.MeshStandardMaterial({ color: 0xff6b6b });
            const secondHand = new THREE.Mesh(secondHandGeometry, secondMaterial);
            secondHand.position.z = 0.17;
            secondHand.userData.type = 'second';
            watchGroup.add(secondHand);
        }

        function updateClockHands() {
            const now = new Date();
            const hours = now.getHours() % 12;
            const minutes = now.getMinutes();
            const seconds = now.getSeconds();

            watchGroup.children.forEach(child => {
                if (child.userData.type === 'hour') {
                    child.rotation.z = -(hours + minutes / 60) * (Math.PI / 6);
                } else if (child.userData.type === 'minute') {
                    child.rotation.z = -(minutes + seconds / 60) * (Math.PI / 30);
                } else if (child.userData.type === 'second') {
                    child.rotation.z = -seconds * (Math.PI / 30);
                }
            });
        }

        function updateWatchColors() {
            dialMesh.material.color.setHex(watchState.dialColor);
            strapMesh.material.color.setHex(watchState.strapColor);
            frameMesh.material.color.setHex(watchState.frameColor);
            watchGroup.children.forEach(child => {
                if (child.geometry && child.geometry instanceof THREE.BoxGeometry) {
                    if (child.position.x !== 0) {
                        child.material.color.setHex(watchState.strapColor);
                    }
                }
            });
        }

        function animate() {
            requestAnimationFrame(animate);
            updateClockHands();
            renderer.render(scene, camera);
        }

        // ==================== MOUSE CONTROLS ====================
        function onMouseDown(e) {
            isDragging = true;
            previousMousePosition = { x: e.clientX, y: e.clientY };
        }

        function onMouseMove(e) {
            if (!isDragging) return;
            const deltaX = e.clientX - previousMousePosition.x;
            const deltaY = e.clientY - previousMousePosition.y;
            watchGroup.rotation.y += deltaX * 0.005;
            watchGroup.rotation.x += deltaY * 0.005;
            previousMousePosition = { x: e.clientX, y: e.clientY };
        }

        function onMouseUp() {
            isDragging = false;
        }

        function onMouseWheel(e) {
            e.preventDefault();
            camera.position.z += e.deltaY * 0.001;
            camera.position.z = Math.max(1, Math.min(5, camera.position.z));
        }

        function onTouchStart(e) {
            isDragging = true;
            previousMousePosition = { x: e.touches[0].clientX, y: e.touches[0].clientY };
        }

        function onTouchMove(e) {
            if (!isDragging) return;
            const deltaX = e.touches[0].clientX - previousMousePosition.x;
            const deltaY = e.touches[0].clientY - previousMousePosition.y;
            watchGroup.rotation.y += deltaX * 0.005;
            watchGroup.rotation.x += deltaY * 0.005;
            previousMousePosition = { x: e.touches[0].clientX, y: e.touches[0].clientY };
        }

        function onTouchEnd() {
            isDragging = false;
        }

        function onWindowResize() {
            const canvas = document.getElementById('canvas');
            const width = canvas.clientWidth;
            const height = canvas.clientHeight;
            camera.aspect = width / height;
            camera.updateProjectionMatrix();
            renderer.setSize(width, height);
        }

        // ==================== COLOR PICKER ====================
        function createColorPickers() {
            // Dial colors
            const dialContainer = document.getElementById('dialColors');
            [...colorPalette.blacks, ...colorPalette.whites, ...colorPalette.blues].forEach(color => {
                const btn = createColorButton(color, 'dial');
                dialContainer.appendChild(btn);
            });

            // Strap colors
            const strapContainer = document.getElementById('strapColors');
            [...colorPalette.blacks, ...colorPalette.blues, ...colorPalette.reds].forEach(color => {
                const btn = createColorButton(color, 'strap');
                strapContainer.appendChild(btn);
            });

            // Frame colors
            const frameContainer = document.getElementById('frameColors');
            [...colorPalette.metals, ...colorPalette.whites, ...colorPalette.blacks].forEach(color => {
                const btn = createColorButton(color, 'frame');
                frameContainer.appendChild(btn);
            });
        }

        function createColorButton(color, type) {
            const btn = document.createElement('button');
            btn.className = 'color-btn';
            btn.style.backgroundColor = color.hex;
            btn.textContent = color.name;
            btn.onclick = () => selectColor(color.hex, type);
            return btn;
        }

        function selectColor(hex, type) {
            if (type === 'dial') {
                watchState.dialColor = hex;
            } else if (type === 'strap') {
                watchState.strapColor = hex;
            } else if (type === 'frame') {
                watchState.frameColor = hex;
            }
            updateWatchColors();
            updateActiveButtons();
        }

        function updateActiveButtons() {
            document.querySelectorAll('.color-btn').forEach(btn => {
                btn.classList.remove('active');
                if (btn.style.backgroundColor === watchState.dialColor ||
                    btn.style.backgroundColor === watchState.strapColor ||
                    btn.style.backgroundColor === watchState.frameColor) {
                    btn.classList.add('active');
                }
            });
        }

        // ==================== PRESETS ====================
        const presets = {
            classic: { dialColor: '#000000', strapColor: '#1a1a1a', frameColor: '#c0c0c0' },
            sport: { dialColor: '#0066ff', strapColor: '#000000', frameColor: '#333333' },
            luxury: { dialColor: '#ffffff', strapColor: '#8b0000', frameColor: '#ffd700' },
            neon: { dialColor: '#00ff00', strapColor: '#ff00ff', frameColor: '#00ffff' }
        };

        function applyPreset(name) {
            const preset = presets[name];
            watchState.dialColor = preset.dialColor;
            watchState.strapColor = preset.strapColor;
            watchState.frameColor = preset.frameColor;
            updateWatchColors();
            updateActiveButtons();
        }

        // ==================== DOWNLOAD ====================
        function downloadImage() {
            const canvas = document.getElementById('canvas');
            const link = document.createElement('a');
            link.href = renderer.domElement.toDataURL('image/png');
            link.download = yash-custom-watch-${Date.now()}.png;
            link.click();
        }

        // ==================== RESET ====================
        function resetWatch() {
            watchState.dialColor = '#000000';
            watchState.strapColor = '#1a1a1a';
            watchState.frameColor = '#c0c0c0';
            updateWatchColors();
            updateActiveButtons();
            watchGroup.rotation.set(0, 0, 0);
        }

        // ==================== AR MODE ====================
        let arEngine = null;

        async function startAR() {
            try {
                // Hide main container
                document.querySelector('.container').style.display = 'none';

                // Create AR canvas
                const arCanvas = document.createElement('canvas');
                arCanvas.id = 'arCanvas';
                document.body.appendChild(arCanvas);

                // Create Babylon.js AR scene
                const engine = new BABYLON.Engine(arCanvas, true);
                const scene = new BABYLON.Scene(engine);

                // Add lights
                const light = new BABYLON.PointLight('light', new BABYLON.Vector3(0, 1, 1), scene);
                light.intensity = 0.8;

                // Get camera permission
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } });
                const video = document.createElement('video');
                video.srcObject = stream;
                video.play();

                // Create video texture
                const videoTexture = new BABYLON.DynamicTexture('video', 1024);
                videoTexture.video = video;

                // Create background
                const skybox = BABYLON.MeshBuilder.CreateBox('skybox', { size: 1000 }, scene);
                const skyboxMaterial = new BABYLON.StandardMaterial('skybox', scene);
                skyboxMaterial.emissiveTexture = videoTexture;
                skyboxMaterial.backFaceCulling = false;
                skybox.material = skyboxMaterial;

                // Create 3D watch model (simple cylinder for demo)
                const watchMesh = BABYLON.MeshBuilder.CreateCylinder('watch', { diameter: 0.5, height: 0.1, tessellation: 64 }, scene);
                const watchMaterial = new BABYLON.StandardMaterial('watchMat', scene);
                watchMaterial.diffuse = new BABYLON.Color3.FromHexString(watchState.dialColor);
                watchMesh.material = watchMaterial;
                watchMesh.position = new BABYLON.Vector3(0, 0, 0.5);

                // Rotation animation
                engine.runRenderLoop(() => {
                    watchMesh.rotation.z += 0.005;
                    watchMesh.rotation.x = Math.sin(Date.now() * 0.001) * 0.3;
                    scene.render();
                });

                // Close button
                const closeBtn = document.createElement('button');
                closeBtn.className = 'ar-close-btn';
                closeBtn.textContent = '✕ Close AR';
                closeBtn.onclick = () => {
                    stream.getTracks().forEach(track => track.stop());
                    arCanvas.remove();
                    closeBtn.remove();
                    document.querySelector('.container').style.display = 'grid';
                    engine.dispose();
                };
                document.body.appendChild(closeBtn);

                // Info
                const info = document.createElement('div');
                info.className = 'ar-controls';
                info.innerHTML = `
                    <h2>AR Try-On</h2>
                    <p>📱 Point your camera at your wrist</p>
                    <p>✋ Move your hand to reposition</p>
                    <p>The watch should appear on your arm!</p>
                `;
                document.body.appendChild(info);

            } catch (error) {
                console.error('AR Error:', error);
                alert('Camera access needed for AR!\nPlease allow camera permission.');
            }
        }

        // ==================== INITIALIZE ====================
        window.addEventListener('DOMContentLoaded', () => {
            initThreeJS();
            createColorPickers();
            updateWatchColors();
        });
    </script>
</body>
</html>
