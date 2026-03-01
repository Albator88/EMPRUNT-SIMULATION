<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Moto Futuriste Concept – Modèle 3D interactif</title>
  <style>
    body { margin: 0; overflow: hidden; background: #0a0a15; font-family: Arial, sans-serif; color: #e0e0ff; }
    #container { position: absolute; inset: 0; }
    #legend {
      position: absolute;
      bottom: 20px;
      left: 20px;
      background: rgba(0,0,0,0.65);
      padding: 15px 20px;
      border-radius: 10px;
      max-width: 380px;
      backdrop-filter: blur(4px);
      border: 1px solid #444488;
    }
    h2 { margin: 0 0 12px 0; color: #ff4d4d; }
    table { border-collapse: collapse; width: 100%; }
    td { padding: 6px 10px; border-bottom: 1px solid #333366; }
    td:first-child { color: #aaccff; font-weight: bold; width: 55%; }
    #info {
      position: absolute;
      top: 15px;
      left: 15px;
      background: rgba(0,0,0,0.5);
      padding: 10px 15px;
      border-radius: 8px;
      font-size: 0.9em;
    }
  </style>
</head>
<body>

<div id="container"></div>

<div id="info">
  Souris : tourner la vue • Molette : zoom • Clic droit + glisser : déplacer
</div>

<div id="legend">
  <h2>Dimensions estimées – Moto Concept "OB-08"</h2>
  <table>
    <tr><td>Longueur totale</td><td>~2350 mm</td></tr>
    <tr><td>Largeur (au guidon)</td><td>~820 mm</td></tr>
    <tr><td>Hauteur (miroir inclus)</td><td>~1150 mm</td></tr>
    <tr><td>Hauteur de selle approx.</td><td>~720–780 mm</td></tr>
    <tr><td>Empattement</td><td>~1650–1750 mm</td></tr>
    <tr><td>Diamètre roues (ext.)</td><td>~720 mm (26–28")</td></tr>
    <tr><td>Épaisseur bande lumineuse roue</td><td>~140–160 mm</td></tr>
    <tr><td>Longueur aileron arrière</td><td>~650 mm</td></tr>
    <tr><td>Poids estimé (concept)</td><td>~140–180 kg</td></tr>
  </table>
  <p style="margin:10px 0 0;font-size:0.85em;color:#9999cc;">
    Dimensions approximatives d'après l'image fournie (concept non-officiel – proportions visuelles).
  </p>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.168.0/build/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.168.0/examples/js/controls/OrbitControls.min.js"></script>

<script>
// ======================== SCÈNE 3D ========================
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x0a0a15);

const camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(4.5, 2.8, 5.5);

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
renderer.shadowMap.enabled = true;
document.getElementById('container').appendChild(renderer.domElement);

// Contrôles
const controls = new THREE.OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.07;
controls.rotateSpeed = 0.5;
controls.minDistance = 3;
controls.maxDistance = 12;

// Lumière
const ambientLight = new THREE.AmbientLight(0x404070, 1.2);
scene.add(ambientLight);

const dirLight = new THREE.DirectionalLight(0xffffff, 1.1);
dirLight.position.set(8, 12, 7);
dirLight.castShadow = true;
dirLight.shadow.mapSize.width = 2048;
dirLight.shadow.mapSize.height = 2048;
scene.add(dirLight);

const backLight = new THREE.PointLight(0x4488ff, 2, 20);
backLight.position.set(-3, 4, -4);
scene.add(backLight);

// Sol réfléchissant
const planeGeo = new THREE.PlaneGeometry(40, 40);
const planeMat = new THREE.MeshStandardMaterial({ color: 0x111122, roughness: 0.3, metalness: 0.7 });
const plane = new THREE.Mesh(planeGeo, planeMat);
plane.rotation.x = -Math.PI / 2;
plane.position.y = -0.01;
plane.receiveShadow = true;
scene.add(plane);

// ======================== MODÈLE SIMPLIFIÉ DE LA MOTO ========================
const group = new THREE.Group();
scene.add(group);

// Corps principal (rouge métallique brillant)
const bodyGeo = new THREE.BoxGeometry(2.1, 0.9, 0.55);
const bodyMat = new THREE.MeshPhongMaterial({
  color: 0xc8102e,
  shininess: 90,
  specular: 0xffffff
});
const body = new THREE.Mesh(bodyGeo, bodyMat);
body.position.y = 0.7;
body.castShadow = true;
group.add(body);

// Carénage avant / nez pointu
const noseGeo = new THREE.ConeGeometry(0.4, 1.2, 4, 1);
const nose = new THREE.Mesh(noseGeo, bodyMat);
nose.position.set(-1.35, 0.75, 0);
nose.rotation.z = Math.PI / 2;
group.add(nose);

// Aileron arrière
const finGeo = new THREE.BoxGeometry(0.15, 0.9, 1.4);
const fin = new THREE.Mesh(finGeo, new THREE.MeshPhongMaterial({color: 0xaaaaaa, shininess: 60}));
fin.position.set(0.9, 1.35, 0);
fin.rotation.y = Math.PI / 12;
group.add(fin);

// Selle / siège flottant
const seatGeo = new THREE.BoxGeometry(0.9, 0.18, 0.6);
const seatMat = new THREE.MeshPhongMaterial({color: 0x111133, shininess: 30});
const seat = new THREE.Mesh(seatGeo, seatMat);
seat.position.set(-0.1, 1.05, 0);
group.add(seat);

// Roues glow transparentes
function createWheel(posX, glowColor = 0x00aaff) {
  const wheelGroup = new THREE.Group();
  wheelGroup.position.x = posX;

  // Anneau extérieur glow
  const rimGeo = new THREE.TorusGeometry(0.72, 0.14, 16, 60);
  const rimMat = new THREE.MeshBasicMaterial({
    color: glowColor,
    transparent: true,
    opacity: 0.85,
    blending: THREE.AdditiveBlending
  });
  const rim = new THREE.Mesh(rimGeo, rimMat);
  rim.rotation.y = Math.PI / 2;
  wheelGroup.add(rim);

  // Anneau intérieur sombre
  const innerGeo = new THREE.TorusGeometry(0.58, 0.08, 12, 50);
  const inner = new THREE.Mesh(innerGeo, new THREE.MeshBasicMaterial({color: 0x000022}));
  inner.rotation.y = Math.PI / 2;
  wheelGroup.add(inner);

  return wheelGroup;
}

const frontWheel = createWheel(-0.95);
frontWheel.position.y = 0.72;
group.add(frontWheel);

const rearWheel = createWheel(0.95);
rearWheel.position.y = 0.72;
group.add(rearWheel);

// Animation glow pulsé
let time = 0;
function animateGlow() {
  time += 0.015;
  const intensity = 0.78 + Math.sin(time * 1.4) * 0.22;
  frontWheel.children[0].material.opacity = intensity;
  rearWheel.children[0].material.opacity = intensity;
}

// ======================== RENDU & ANIMATION ========================
function animate() {
  requestAnimationFrame(animate);
  controls.update();
  animateGlow();
  renderer.render(scene, camera);
}
animate();

// Redimensionnement
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});
</script>
</body>
</html>
        const labels = [];
        const dataA = [];
        const dataB = [];
        for (let step = 5000; step <= P; step += 2500) {
            labels.push(step);
            dataA.push(calculateRA(P, LA, C, step));
            dataB.push(calculateRA(P, LB, C, step));
        }
        chart.data.labels = labels;
        chart.data.datasets[0].data = dataA;
        chart.data.datasets[1].data = dataB;
        chart.update();
    }

    const ctx = document.getElementById('compChart').getContext('2d');
    chart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: [],
            datasets: [
                { label: 'Loyer A', data: [], borderColor: '#3182ce', tension: 0.3, pointRadius: 0 },
                { label: 'Loyer B', data: [], borderColor: '#ed8936', tension: 0.3, pointRadius: 0 }
            ]
        },
        options: {
            responsive: true,
            scales: {
                y: { title: { display: true, text: 'Rentabilité sur Apport (%)' } },
                x: { title: { display: true, text: 'Montant de l\'apport (€)' } }
            }
        }
    });

    update();
</script>

</body>
</html>
