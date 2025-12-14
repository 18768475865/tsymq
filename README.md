# tsymq<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>粉色3D圣诞树</title>
    <script src="https://cdn.jsdelivr.net/npm/three@0.132.2/build/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.132.2/examples/js/controls/OrbitControls.js"></script>
    <style>
        body { margin: 0; overflow: hidden; background: #000; font-family: "Monotype Corsiva", Arial, sans-serif; }
        #info { position: absolute; top: 20px; left: 20px; color: #fff; font-size: 24px; }
    </style>
</head>
<body>
    <div id="info">
        <div>Merry Christmas</div>
        <div style="font-size: 18px; margin-top: 5px;">章文希</div>
    </div>
    <script>
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        const controls = new OrbitControls(camera, renderer.domElement);
        camera.position.set(0, 6, 13);
        controls.enableDamping = true;

        const TREE_HEIGHT = 12.0;
        const particleCount = 50000;
        const treeGeometry = new THREE.BufferGeometry();
        const positions = new Float32Array(particleCount * 3);
        const colors = new Float32Array(particleCount * 3);

        for (let i = 0; i < particleCount; i++) {
            const u = Math.random();
            const h = Math.pow(u, 1.6);
            const y = TREE_HEIGHT * h + 0.2;
            const baseR = Math.pow(1 - h, 1.1) * 3.2;
            const branchWave = Math.max(0.0, Math.sin((h * 5.8 + 0.15) * Math.PI * 2));
            const branchFactor = 1.0 + 0.65 * branchWave;
            const r = baseR * branchFactor * Math.random() * 0.23 + baseR * 0.85;
            const angle = u * 9 * Math.PI * 2 + Math.random() * 0.44 - 0.22;
            const x = Math.cos(angle) * r;
            const z = Math.sin(angle) * r;

            const midBoost = Math.max(0.15, 1.0 - Math.abs(h - 0.55) * 1.5);
            const baseG = 155 + Math.floor(90 * midBoost);
            const baseB = 185 + Math.floor(70 * midBoost);
            let rColor = 1.0;
            let gColor = Math.min(1.0, (baseG + Math.floor(Math.random() * 35 - 15)) / 255);
            let bColor = Math.min(1.0, (baseB + Math.floor(Math.random() * 50 - 15)) / 255);

            if (Math.random() < 0.08) {
                rColor = 1.0;
                gColor = Math.min(1.0, (255 - Math.floor(Math.random() * 35)) / 255);
                bColor = Math.min(1.0, (230 + Math.floor(Math.random() * 25)) / 255);
            }

            positions[i * 3] = x;
            positions[i * 3 + 1] = y;
            positions[i * 3 + 2] = z;
            colors[i * 3] = rColor;
            colors[i * 3 + 1] = gColor;
            colors[i * 3 + 2] = bColor;
        }

        treeGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
        treeGeometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));
        const treeMaterial = new THREE.PointsMaterial({ size: 0.1, vertexColors: true, transparent: true, opacity: 0.8 });
        const tree = new THREE.Points(treeGeometry, treeMaterial);
        scene.add(tree);

        // 地面光环
        const groundGeometry = new THREE.BufferGeometry();
        const groundPositions = new Float32Array(4000 * 3);
        const groundColors = new Float32Array(4000 * 3);
        const rings = [4.6, 6.0, 7.4, 8.8, 10.2, 11.4];
        for (let i = 0; i < 4000; i++) {
            const ring = rings[Math.floor(Math.random() * rings.length)];
            const r = ring + Math.random() * 0.6 - 0.3;
            const theta = Math.random() * Math.PI * 2;
            const x = Math.cos(theta) * r;
            const z = Math.sin(theta) * r;
            const y = -0.25;
            let c = Math.random() < 0.15 ? Math.floor(Math.random() * 20) + 235 : Math.floor(Math.random() * 45) + 190;
            c = Math.min(255, c) / 255;
            groundPositions[i * 3] = x;
            groundPositions[i * 3 + 1] = y;
            groundPositions[i * 3 + 2] = z;
            groundColors[i * 3] = c;
            groundColors[i * 3 + 1] = c;
            groundColors[i * 3 + 2] = 1.0;
        }
        groundGeometry.setAttribute('position', new THREE.BufferAttribute(groundPositions, 3));
        groundGeometry.setAttribute('color', new THREE.BufferAttribute(groundColors, 3));
        const groundMaterial = new THREE.PointsMaterial({ size: 0.1, vertexColors: true, transparent: true, opacity: 0.7 });
        const ground = new THREE.Points(groundGeometry, groundMaterial);
        scene.add(ground);

        // 空中星星
        const starGeometry = new THREE.BufferGeometry();
        const starPositions = new Float32Array(1200 * 3);
        const starColors = new Float32Array(1200 * 3);
        for (let i = 0; i < 1200; i++) {
            const x = Math.random() * 36 - 18;
            const z = Math.random() * 36 - 18;
            const y = Math.random() * 15 + 3;
            const base = Math.floor(Math.random() * 40) + 215;
            const color = base / 255;
            starPositions[i * 3] = x;
            starPositions[i * 3 + 1] = y;
            starPositions[i * 3 + 2] = z;
            starColors[i * 3] = color;
            starColors[i * 3 + 1] = color;
            starColors[i * 3 + 2] = 1.0;
        }
        starGeometry.setAttribute('position', new THREE.BufferAttribute(starPositions, 3));
        starGeometry.setAttribute('color', new THREE.BufferAttribute(starColors, 3));
        const starMaterial = new THREE.PointsMaterial({ size: 0.15, vertexColors: true, transparent: true, opacity: 0.8 });
        const stars = new THREE.Points(starGeometry, starMaterial);
        scene.add(stars);

        // 树顶爱心
        const heartGeometry = new THREE.BufferGeometry();
        const heartPositions = new Float32Array(1000 * 3);
        const heartColors = new Float32Array(1000 * 3);
        const scale = 0.9;
        const topY = TREE_HEIGHT + 0.05;
        let heartCount = 0;
        while (heartCount < 1000) {
            const x = Math.random() * 2.6 - 1.3;
            const y = Math.random() * 2.8 - 1.4;
            const f = Math.pow(x * x + y * y - 1.0, 3) - x * x * Math.pow(y, 3);
            if (f <= 0.0) {
                const wx = x * scale * 0.8;
                const wy = topY + (y + 1.0) * scale * 0.5;
                const wz = Math.random() * 0.36 - 0.18;
                const dist = Math.hypot(x, y);
                const factor = Math.max(0.35, 1.15 - 0.5 * dist);
                const r = 1.0;
                let g = (130 * factor + 80);
                let b = (190 * factor + 70);
                g = Math.max(120, Math.min(255, g)) / 255;
                b = Math.max(120, Math.min(255, b)) / 255;
                heartPositions[heartCount * 3] = wx;
                heartPositions[heartCount * 3 + 1] = wy;
                heartPositions[heartCount * 3 + 2] = wz;
                heartColors[heartCount * 3] = r;
                heartColors[heartCount * 3 + 1] = g;
                heartColors[heartCount * 3 + 2] = b;
                heartCount++;
            }
        }
        heartGeometry.setAttribute('position', new THREE.BufferAttribute(heartPositions, 3));
        heartGeometry.setAttribute('color', new THREE.BufferAttribute(heartColors, 3));
        const heartMaterial = new THREE.PointsMaterial({ size: 0.2, vertexColors: true, transparent: true, opacity: 0.9 });
        const heart = new THREE.Points(heartGeometry, heartMaterial);
        scene.add(heart);

        // 动画循环
        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            tree.rotation.y += 0.0045;
            ground.rotation.y += 0.0045;
            stars.rotation.y += 0.0045;
            renderer.render(scene, camera);
        }
        animate();

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
