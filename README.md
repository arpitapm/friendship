# friendship
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Happy Friendship Day</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #1a1a1a;
        }

        #date-button {
            position: absolute;
            top: 30%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 15px 30px;
            font-size: 24px;
            font-family: Arial, sans-serif;
            color: white;
            background-color: #4CAF50;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s, transform 0.1s, opacity 0.5s;
        }

        #date-button:hover {
            background-color: #45a049;
        }

        #date-button:active {
            transform: translate(-50%, -50%) scale(0.98);
        }
    </style>
</head>

<body>
    <button id="date-button">August 4, 2024</button>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.7.1/gsap.min.js"></script>
    <script>
        let scene, camera, renderer, text, particles;
        let animationFrameId;
        let isAnimating = false;

        function init() {
            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setClearColor(0x1a1a1a);
            document.body.appendChild(renderer.domElement);

            const loader = new THREE.FontLoader();
            loader.load('https://threejs.org/examples/fonts/helvetiker_bold.typeface.json', function (font) {
                const textGeometry = new THREE.TextGeometry('Happy Friendship Day', {
                    font: font,
                    size: 0.5,
                    height: 0.2,
                    curveSegments: 12,
                    bevelEnabled: true,
                    bevelThickness: 0.03,
                    bevelSize: 0.02,
                    bevelOffset: 0,
                    bevelSegments: 5
                });
                textGeometry.center();
                const textMaterial = new THREE.MeshPhongMaterial({ color: 0xffffff });
                text = new THREE.Mesh(textGeometry, textMaterial);
                text.position.set(0, 0, -5);
                scene.add(text);
            });

            const light = new THREE.PointLight(0xffffff, 1, 100);
            light.position.set(0, 0, 10);
            scene.add(light);

            particles = new THREE.Group();
            for (let i = 0; i < 1000; i++) {
                const geometry = new THREE.SphereGeometry(0.02, 32, 32);
                const material = new THREE.MeshBasicMaterial({ color: Math.random() * 0xffffff });
                const particle = new THREE.Mesh(geometry, material);
                particle.position.set(
                    Math.random() * 10 - 5,
                    Math.random() * 10 - 5,
                    Math.random() * 10 - 5
                );
                particles.add(particle);
            }
            scene.add(particles);

            camera.position.z = 5;
        }

        function animate() {
            if (!isAnimating) return;

            animationFrameId = requestAnimationFrame(animate);
            particles.rotation.x += 0.001;
            particles.rotation.y += 0.002;
            renderer.render(scene, camera);
        }

        function startAnimation() {
            if (isAnimating) return;

            isAnimating = true;
            gsap.to('#date-button', {
                duration: 0.5, opacity: 0, onComplete: () => {
                    document.getElementById('date-button').style.display = 'none';
                }
            });

            gsap.to(text.rotation, { duration: 2, y: Math.PI * 2, ease: "power1.inOut" });
            gsap.to(text.position, { duration: 3, z: -2, yoyo: true, repeat: 1, ease: "power1.inOut" });
            gsap.to(particles.rotation, { duration: 10, x: Math.PI * 2, y: Math.PI * 2, ease: "none" });

            gsap.to(text.material.color, { duration: 2, r: 1, g: 0, b: 0, repeat: 2, yoyo: true });

            gsap.to(particles.scale, { duration: 2, x: 1.5, y: 1.5, z: 1.5, yoyo: true, repeat: 1 });

            animate();

            // Stop animation after 10 seconds
            setTimeout(() => {
                isAnimating = false;
                cancelAnimationFrame(animationFrameId);
                // Reset positions
                text.rotation.set(0, 0, 0);
                text.position.set(0, 0, -5);
                particles.rotation.set(0, 0, 0);
                particles.scale.set(1, 1, 1);
                text.material.color.setRGB(1, 1, 1);
                renderer.render(scene, camera);

                // Bring back the date button
                document.getElementById('date-button').style.display = 'block';
                gsap.to('#date-button', { duration: 0.5, opacity: 1 });
            }, 5000);
        }

        init();

        document.getElementById('date-button').addEventListener('click', startAnimation);
        window.addEventListener('resize', function () {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>

</html>
