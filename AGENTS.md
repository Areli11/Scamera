# AGENTS.md - Scamera Development Guide

Scamera is a web-based AR app for scanning football team badges using device camera to view 3D models, videos, trivia, and statistics. Static web app using vanilla HTML, CSS, JavaScript with Three.js and MindAR.

## Technology Stack
- **HTML5/CSS3** - Markup with Bootstrap 5.3
- **JavaScript (ES6+)** - Client-side logic
- **Three.js (v0.160.0)** - 3D model rendering
- **MindAR (v1.2.5)** - AR image tracking
- **A-Frame (v1.5.0)** - VR/AR framework

## Build & Development Commands

### Running the Application
No build step required - static web app.
```bash
# Python
python -m http.server 8000

# Node.js
npx serve

# VS Code: Install "Live Server" extension, right-click index.html → "Open with Live Server"
```

### Testing
No automated tests. Manual testing covers: camera/scanner, 3D model loading, all modal interactions, responsive design.

### Linting
No linter configured. Optional: `npm install -g eslint && eslint script.js`

### Browser Requirements
- Chrome 80+ or Firefox 75+
- Camera access required for AR
- HTTPS required (except localhost)

## Code Style Guidelines

### General Principles
1. Keep it simple - vanilla JS project
2. Modularity - group related functionality
3. Consistency - follow existing patterns

### JavaScript Conventions

**Variable Naming:**
```javascript
let clock = new THREE.Clock();     // camelCase
let initialized = false;
const TOTAL_PREGUNTAS = 3;         // UPPER_SNAKE_CASE for constants
let _internalState = null;         // _prefix for private by convention
```

**Functions:**
```javascript
function init3D() { }                           // Function declarations
document.getElementById("btn").addEventListener("click", () => { }); // Arrow callbacks
```

**Minimize globals** - Use namespace if needed:
```javascript
const Scamera = { animations: {}, clock: new THREE.Clock() };
```

### HTML Guidelines
- Semantic HTML5 (`<nav>`, `<main>`, `<section>`)
- Include `lang="es"` attribute
- Use Bootstrap utilities before custom CSS
```html
<button type="button" class="btn btn-green" data-bs-toggle="modal" data-bs-target="#videoModal">Vídeo</button>
```

### CSS Guidelines
```css
/* Order: CSS Variables → Base → Components → Modifiers → Media queries */
:root { --primary-color: #4a7c2c; }
body { background-color: #fff; }
.btn-green { background-color: var(--primary-color); }
.btn-green:hover { background-color: #33551e; }
@media (max-width: 768px) { }
```
- Use BEM-like naming: `.block__element--modifier`
- Avoid inline styles, `!important`, magic numbers

### Imports (ES Modules via CDN)
```javascript
import * as THREE from 'three';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
```

### Error Handling
```javascript
loader.load('./models/tucanCol.glb', function (gltf) {
    try { scene.add(gltf.scene); }
    catch (error) { console.error("Error loading model:", error); }
});
if (!mixer) return;
```

### Console Logging
```javascript
console.log("Script cargado");           // General info
console.error("Error loading model:", error);  // Errors
```

### Event Handling
```javascript
document.querySelectorAll(".opcion").forEach(boton => {
    boton.addEventListener("click", function () { });
});

infoModal.addEventListener('shown.bs.modal', function () {
    if (!initialized) init3D();
});
```

### Three.js Patterns
```javascript
let scene, camera, renderer, mixer, model;

function init3D() {
    scene = new THREE.Scene();
    scene.background = new THREE.Color(0x3a3a3a);
    camera = new THREE.PerspectiveCamera(45, 350 / 430, 0.1, 1000);
    renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(350, 430);
    container.appendChild(renderer.domElement);
}

function animate() {
    requestAnimationFrame(animate);
    if (mixer) mixer.update(clock.getDelta());
    renderer.render(scene, camera);
}
```

## File Structure
```
Scamera/
├── index.html          # Main HTML
├── script.js           # Main JavaScript
├── style.css           # Custom styles
├── AGENTS.md           # This file
├── Images/             # Assets
├── models/             # 3D GLB models
├── escudos/            # Badge images
└── targets/            # MindAR targets
```

## Common Tasks

### Adding a New Modal
1. Add HTML in `index.html`
2. Add CSS in `style.css` if needed
3. Add event listeners in `script.js`
4. Use Bootstrap: `modal fade`, `modal-dialog`, `modal-content`

### Adding a New 3D Model
1. Place `.glb` in `models/`
2. Update `loader.load()` path
3. Adjust `model.scale.set()` and `model.position.set()`
4. Update animation name in button handler

### Adding Trivia Questions
1. Add question HTML in `triviaModal`
2. Increment `totalPreguntas` in `script.js`

## Best Practices
1. Test on mobile - AR works best there
2. Use HTTPS - camera requires secure context
3. Optimize assets - keep 3D models under 5MB
4. Lazy load - only initialize 3D when modal opens
5. Handle errors - check for null values
6. Use meaningful names
