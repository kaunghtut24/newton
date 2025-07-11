# Running the 3D Newton’s Laws Simulation

This guide provides step-by-step instructions to set up and run the 3D Newton’s Laws simulation, which displays a red ball on a green plane with a grid, controlled by keyboard inputs (WASD/arrow keys) and a mass slider. It uses Three.js version r160.1 (`three.min.js`) and `npx http-server` for local hosting.

## Prerequisites
- **Node.js**: Required for `npx http-server`. Download from [nodejs.org](https://nodejs.org) (LTS version, e.g., v20).
- **Project Directory**: Use `C:/Users/an_hu/Documents/` (or any preferred folder).
- **Browser**: Chrome or another modern browser.
- **Files**: You’ll need `newton_law.html` and `three.min.js` (r160.1) in `C:/Users/an_hu/Documents/`.

## Step 1: Install Node.js
1. Download and install Node.js LTS from [nodejs.org](https://nodejs.org/en/download).
2. Open a terminal (Command Prompt, PowerShell, or VS Code terminal) and verify:
   ```bash
   node -v
   npm -v
   ```
   - Expected output: Version numbers (e.g., `v20.x.x` for Node.js, `10.x.x` for npm).
3. If not installed, run the installer and retry.

## Step 2: Download `three.min.js` (r160.1)
Your simulation works with Three.js r160.1 (`three.min.js`). Previously downloaded files (`shim.min.js` from r77.1, `three.min.js` from r124, and r178 files like `three.module.min.js`) are incompatible. Follow these steps:

1. **Download `three.min.js`**:
   - Visit: [https://cdn.jsdelivr.net/npm/three@0.160.1/build/three.min.js](https://cdn.jsdelivr.net/npm/three@0.160.1/build/three.min.js)
   - Right-click, select **Save As**, and save as `three.min.js` in `C:/Users/an_hu/Documents/`.
2. **Verify the File**:
   - Open `three.min.js` in a text editor (e.g., Notepad).
   - Ensure it starts with `!function` (JavaScript code) and is ~600-700 KB.
   - If it’s HTML or a login page, try again or use the alternative.
3. **Alternative Source**:
   - Go to [Three.js GitHub releases](https://github.com/mrdoob/three.js/releases/tag/r160).
   - Download the ZIP, extract, and copy `build/three.min.js` to `C:/Users/an_hu/Documents/`.
4. **Remove Incorrect Files**:
   - Delete any `three.min.js` from r124, `shim.min.js` (r77.1), or r178 files (e.g., `three.module.min.js`) in `C:/Users/an_hu/Documents/`.
   - Only keep `three.min.js` (r160.1).

## Step 3: Save the Simulation HTML
1. Create a file named `newton_law.html` in `C:/Users/an_hu/Documents/`.
2. Copy and paste the following complete code into `newton_law.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>3D Newton’s Laws Simulation</title>
  <link rel="icon" type="image/x-icon" href="./favicon.ico" />
  <style>
    body {
      margin: 0;
      overflow: hidden;
      font-family: Arial, sans-serif;
    }
    #info {
      position: absolute;
      top: 10px;
      left: 10px;
      color: white;
      background-color: rgba(0, 0, 0, 0.8);
      padding: 12px;
      border-radius: 8px;
      font-size: 14px;
      max-width: 320px;
      line-height: 1.5;
      z-index: 200;
      transition: opacity 0.3s;
    }
    #info.hidden {
      opacity: 0;
      pointer-events: none;
    }
    #controls {
      position: absolute;
      top: 180px;
      left: 10px;
      color: white;
      background-color: rgba(0, 0, 0, 0.8);
      padding: 12px;
      border-radius: 8px;
      z-index: 200;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }
    #controls label {
      font-size: 16px;
      display: flex;
      align-items: center;
      gap: 10px;
    }
    #massSlider {
      width: 200px;
      -webkit-appearance: none;
      appearance: none;
      height: 8px;
      background: #ddd;
      border-radius: 4px;
      outline: none;
      cursor: pointer;
    }
    #massSlider::-webkit-slider-thumb {
      -webkit-appearance: none;
      appearance: none;
      width: 20px;
      height: 20px;
      background: #007bff;
      border-radius: 50%;
      border: 2px solid white;
    }
    #massSlider::-moz-range-thumb {
      width: 20px;
      height: 20px;
      background: #007bff;
      border-radius: 50%;
      border: 2px solid white;
    }
    #massSlider:hover::-webkit-slider-thumb,
    #massSlider:hover::-moz-range-thumb {
      background: #0056b3;
    }
    #resetButton, #toggleInfoButton {
      padding: 8px 12px;
      background: #007bff;
      border: none;
      border-radius: 4px;
      color: white;
      font-size: 14px;
      cursor: pointer;
      transition: background 0.2s;
    }
    #resetButton:hover, #toggleInfoButton:hover {
      background: #0056b3;
    }
    #error {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      color: red;
      background-color: rgba(255, 255, 255, 0.9);
      padding: 20px;
      border-radius: 5px;
      display: none;
      max-width: 80%;
      text-align: center;
      z-index: 300;
    }
    canvas {
      display: block;
    }
    @media (max-width: 600px) {
      #info, #controls {
        left: 5px;
        max-width: 90%;
      }
      #massSlider {
        width: 150px;
      }
    }
  </style>
</head>
<body>
  <div id="info"></div>
  <div id="controls">
    <label for="massSlider">Mass (kg): <span id="massValue">10</span></label>
    <input type="range" id="massSlider" min="1" max="20" step="0.1" value="10" />
    <button id="resetButton">Reset Simulation</button>
    <button id="toggleInfoButton">Hide Info</button>
  </div>
  <div id="error">Error: Failed to load Three.js. Ensure 'three.min.js' (version r160.1) is in C:/Users/an_hu/Documents/ and you're running a local server (e.g., 'npx http-server').</div>

  <script src="./three.min.js"></script>
  <script>
    if (typeof THREE === 'undefined') {
      document.getElementById('error').style.display = 'block';
      throw new Error('Three.js failed to load. Ensure three.min.js (version r160.1) is in C:/Users/an_hu/Documents/ and the server is running.');
    }

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    document.body.appendChild(renderer.domElement);

    camera.position.set(0, 5, 10);
    camera.lookAt(0, 0, 0);

    const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
    scene.add(ambientLight);
    const pointLight = new THREE.PointLight(0xffffff, 1, 100);
    pointLight.position.set(5, 5, 5);
    pointLight.castShadow = true;
    scene.add(pointLight);

    const sphereRadius = 1;
    const geometry = new THREE.SphereGeometry(sphereRadius, 32, 32);
    const material = new THREE.MeshStandardMaterial({ color: 0xff0000, roughness: 0.5 });
    const ball = new THREE.Mesh(geometry, material);
    ball.castShadow = true;
    ball.receiveShadow = true;
    scene.add(ball);

    const planeGeometry = new THREE.PlaneGeometry(20, 20);
    const planeMaterial = new THREE.MeshStandardMaterial({ color: 0x00ff00, side: THREE.DoubleSide, roughness: 0.8 });
    const plane = new THREE.Mesh(planeGeometry, planeMaterial);
    plane.rotation.x = -Math.PI / 2;
    plane.receiveShadow = true;
    scene.add(plane);

    const gridHelper = new THREE.GridHelper(20, 20, 0x888888, 0x888888);
    scene.add(gridHelper);

    let mass = parseFloat(document.getElementById('massSlider').value);
    const gravity = new THREE.Vector3(0, -9.81, 0);
    let velocity = new THREE.Vector3(0, 0, 0);
    let appliedForce = new THREE.Vector3(0, 0, 0);
    const forceMagnitudeX = 50;
    const forceMagnitudeY = 100;
    const frictionCoefficient = 0.1;

    let prevTime = performance.now();
    const maxDt = 0.033;

    const massSlider = document.getElementById('massSlider');
    const massValueDisplay = document.getElementById('massValue');
    const infoDiv = document.getElementById('info');
    const resetButton = document.getElementById('resetButton');
    const toggleInfoButton = document.getElementById('toggleInfoButton');

    massSlider.addEventListener('input', function (e) {
      mass = Math.max(0.1, parseFloat(e.target.value));
      massValueDisplay.textContent = mass.toFixed(1);
    });

    resetButton.addEventListener('click', function () {
      ball.position.set(0, sphereRadius, 0);
      velocity.set(0, 0, 0);
      appliedForce.set(0, 0, 0);
      mass = 10;
      massSlider.value = 10;
      massValueDisplay.textContent = '10';
    });

    toggleInfoButton.addEventListener('click', function () {
      infoDiv.classList.toggle('hidden');
      toggleInfoButton.textContent = infoDiv.classList.contains('hidden') ? 'Show Info' : 'Hide Info';
    });

    window.addEventListener('keydown', function (event) {
      switch (event.key) {
        case 'ArrowLeft':
        case 'a':
          appliedForce.x = -forceMagnitudeX;
          break;
        case 'ArrowRight':
        case 'd':
          appliedForce.x = forceMagnitudeX;
          break;
        case 'ArrowUp':
        case 'w':
          appliedForce.y = forceMagnitudeY;
          break;
        case 'ArrowDown':
        case 's':
          appliedForce.y = -forceMagnitudeY / 2;
          break;
      }
    });

    window.addEventListener('keyup', function (event) {
      switch (event.key) {
        case 'ArrowLeft':
        case 'a':
        case 'ArrowRight':
        case 'd':
          appliedForce.x = 0;
          break;
        case 'ArrowUp':
        case 'w':
        case 'ArrowDown':
        case 's':
          appliedForce.y = 0;
          break;
      }
    });

    window.addEventListener('resize', function () {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    function updateInfo() {
      const weightForce = gravity.clone().multiplyScalar(mass);
      const netForce = appliedForce.clone().add(weightForce);
      const acceleration = netForce.clone().divideScalar(mass);

      infoDiv.innerHTML = `
        Mass: ${mass.toFixed(2)} kg<br>
        Applied Force: (${appliedForce.x.toFixed(2)}, ${appliedForce.y.toFixed(2)}, 0) N<br>
        Weight Force: (${weightForce.x.toFixed(2)}, ${weightForce.y.toFixed(2)}, 0) N<br>
        Net Force: (${netForce.x.toFixed(2)}, ${netForce.y.toFixed(2)}, 0) N<br>
        Acceleration: (${acceleration.x.toFixed(2)}, ${acceleration.y.toFixed(2)}, 0) m/s²<br>
        Velocity: (${velocity.x.toFixed(2)}, ${velocity.y.toFixed(2)}, 0) m/s<br>
        Position: (${ball.position.x.toFixed(2)}, ${ball.position.y.toFixed(2)}, 0) m
      `;
    }

    function animate() {
      requestAnimationFrame(animate);

      const now = performance.now();
      let dt = Math.min(maxDt, (now - prevTime) / 1000);
      prevTime = now;

      const weightForce = gravity.clone().multiplyScalar(mass);
      const netForce = appliedForce.clone().add(weightForce);

      let friction = new THREE.Vector3(0, 0, 0);
      if (ball.position.y <= sphereRadius) {
        const normalForce = -weightForce.y;
        friction.x = -velocity.x * frictionCoefficient * normalForce;
        netForce.add(friction);
      }

      const acceleration = netForce.clone().divideScalar(mass);

      velocity.add(acceleration.clone().multiplyScalar(dt));
      ball.position.add(velocity.clone().multiplyScalar(dt));

      if (ball.position.y < sphereRadius) {
        ball.position.y = sphereRadius;
        velocity.y *= -0.7;
        velocity.x *= 0.9;
      }

      updateInfo();
      renderer.render(scene, camera);
    }

    animate();
  </script>
</body>
</html>
```

## Step 4: Install and Run http-server
1. **Install http-server** (if not already installed):
   - In a terminal, run:
     ```bash
     npm install -g http-server
     ```
   - This enables `npx http-server`.
2. **Start the Server**:
   - Navigate to your project directory:
     ```bash
     cd C:/Users/an_hu/Documents
     ```
   - Run:
     ```bash
     npx http-server
     ```
   - Output will show:
     ```
     Available on:
       http://127.0.0.1:8080
       http://192.168.1.7:8080
       ...
     Hit CTRL-C to stop the server
     ```
   - Note: Since `three.min.js` is local, CORS isn’t needed. For future CDN use, run:
     ```bash
     npx http-server --cors
     ```

## Step 5: Run the Simulation
1. Open Chrome and go to: [http://127.0.0.1:8080/newton_law.html](http://127.0.0.1:8080/newton_law.html)
2. **Expected Behavior**:
   - A red ball on a green plane with a grid.
   - **Info Panel** (top-left): Displays physics data (mass, forces, velocity, position).
   - **Controls Panel** (below, no overlap):
     - **Mass Slider**: Adjust mass (1–20 kg).
     - **Reset Button**: Resets ball position, velocity, and mass to 10 kg.
     - **Toggle Info Button**: Hides/shows the info panel.
   - **Controls**:
     - **Arrow keys** or **WASD**:
       - Left/A, Right/D: ±50 N horizontal force.
       - Up/W: +100 N vertical force (jump).
       - Down/S: -50 N vertical force.
3. **Verify UI**:
   - Slider and controls don’t overlap the info panel.
   - Text is readable (white on dark background).
   - Buttons and slider are styled (blue, with hover effects).

## Troubleshooting
- **Error: “Failed to load Three.js”**:
  - Ensure `three.min.js` (r160.1) is in `C:/Users/an_hu/Documents/`.
  - Check file name is exactly `three.min.js` (case-sensitive).
  - Open in Notepad to confirm it’s JavaScript (starts with `!function`), not HTML.
- **Blank Canvas or Errors**:
  - Open Chrome DevTools (F12):
    - **Console Tab**: Check for errors (e.g., `THREE.MeshStandardMaterial is not defined`).
    - **Network Tab**: Confirm `three.min.js` loads with status 200.
  - If errors occur, redownload `three.min.js` from r160.1.
- **Server Issues**:
  - Ensure you’re in `C:/Users/an_hu/Documents/` when running `npx http-server`.
  - If `npx` fails, reinstall: `npm install -g http-server`.
- **Deprecation Warning** (`DEP0066`):
  - Harmless; update `http-server` to suppress:
    ```bash
    npm install -g http-server
    ```

## Optional: Add Favicon
- To avoid a `/favicon.ico` 404 error (harmless):
  - Create a favicon at [favicon.io](https://favicon.io).
  - Save as `favicon.ico` in `C:/Users/an_hu/Documents/`.

## Notes
- **Why r160.1?**:
  - Confirmed to work with your simulation (supports `MeshStandardMaterial`, shadows, etc.).
  - Matches your non-module `<script src>` setup.
- **Why Not r178 Files?**:
  - Files in `C:/Users/an_hu/Downloads/three.js-r178/three.js-r178/build` (e.g., `three.module.min.js`) are ES modules, requiring `<script type="module">` and code changes, adding complexity.
  - r178 focuses on WebGPU, not needed for your WebGL-based project.
- **Why Not r124 or r77.1?**:
  - `shim.min.js` (r77.1) and `three.min.js` (r124) are outdated and lack required features.
- **Using r178 (Advanced)**:
  - To use `three.module.min.js` (r178):
    1. Copy from `C:/Users/an_hu/Downloads/three.js-r178/three.js-r178/build` to `C:/Users/an_hu/Documents/`.
    2. Modify `newton_law.html` to use `<script type="module">` and `import * as THREE`.
    3. Test for API changes (contact for a guide if needed).
- **Further Enhancements**:
  - Add mouse-based camera controls (`THREE.OrbitControls`).
  - Visualize forces with arrows.
  - Contact for help with these features.

Enjoy your simulation! For issues, share:
- DevTools Console errors.
- Network tab screenshot (showing `three.min.js` status).
- Confirmation of files in `C:/Users/an_hu/Documents/`.