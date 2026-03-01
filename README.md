<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Moto Prototype Yamaha - Modèle 3D simple</title>
  <style>
    body { margin: 0; overflow: hidden; background: #000; }
    canvas { display: block; }
    #info {
      position: absolute;
      top: 15px; left: 15px;
      color: #0f0; font-family: Arial, sans-serif;
      background: rgba(0,0,0,0.6);
      padding: 10px 15px;
      border-radius: 8px;
      pointer-events: none;
    }
  </style>
</head>
<body>
  <div id="info">
    Moto Prototype Yamaha Concept<br>
    Utilise la molette pour zoom • Clic gauche + glisser pour tourner
  </div>

  <script src="https://cdn.jsdelivr.net/npm/three@0.168.0/build/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.168.0/examples/js/controls/OrbitControls.min.js"></script>

  <script>
    // Scène de base
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x0a0a1f);

    const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.set(0, 4, 12);

    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    document.body.appendChild(renderer.domElement);

    // Contrôles orbit
    const controls = new THREE.OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;
    controls.minDistance = 6;
    controls.maxDistance = 35;
    controls.maxPolarAngle = Math.PI / 2.1;

    // Lumière
    const ambientLight = new THREE.AmbientLight(0x404060, 1.2);
    scene.add(ambientLight);

    const dirLight = new THREE.DirectionalLight(0xffffff, 1.1);
    dirLight.position.set(10, 15, 8);
    dirLight.castShadow = true;
    dirLight.shadow.mapSize.width = 2048;
    dirLight.shadow.mapSize.height = 2048;
    scene.add(dirLight);

    const backLight = new THREE.PointLight(0x88aaff, 1.5, 50);
    backLight.position.set(0, 5, -8);
    scene.add(backLight);

    // Sol
    const ground = new THREE.Mesh(
      new THREE.PlaneGeometry(100, 100),
      new THREE.MeshStandardMaterial({ color: 0x111122, roughness: 0.9 })
    );
    ground.rotation.x = -Math.PI / 2;
    ground.position.y = -0.01;
    ground.receiveShadow = true;
    scene.add(ground);

    // Groupe moto
    const moto = new THREE.Group();
    scene.add(moto);

    // Matériaux Yamaha-style (bleu/noir mat + accents)
    const blackMat   = new THREE.MeshStandardMaterial({ color: 0x0a0a0a, metalness: 0.2, roughness: 0.4 });
    const darkBlue   = new THREE.MeshStandardMaterial({ color: 0x001144, metalness: 0.6, roughness: 0.25 });
    const blueGlow   = new THREE.MeshStandardMaterial({ color: 0x2266ff, emissive: 0x1133aa, emissiveIntensity: 0.7 });
    const silverMat  = new THREE.MeshStandardMaterial({ color: 0xcccccc, metalness: 0.85, roughness: 0.15 });
    const redAccent  = new THREE.MeshStandardMaterial({ color: 0xaa0000, metalness: 0.4, roughness: 0.3 });

    // Cadre principal (style exosquelette futuriste)
    const frameGeo = new THREE.BoxGeometry(1.2, 0.4, 5.5);
    const frame = new THREE.Mesh(frameGeo, darkBlue);
    frame.position.y = 1.1;
    frame.castShadow = true;
    moto.add(frame);

    // Arceau / arche centrale (style MOTOROiD)
    const archGeo = new THREE.TorusGeometry(1.8, 0.18, 16, 60, Math.PI * 1.1);
    const arch = new THREE.Mesh(archGeo, silverMat);
    arch.rotation.z = Math.PI / 2;
    arch.rotation.y = Math.PI / 2;
    arch.position.set(0, 1.8, -0.5);
    moto.add(arch);

    // Roue avant
    const wheelGeo = new THREE.CylinderGeometry(1.1, 1.1, 0.4, 32);
    const wheelFront = new THREE.Mesh(wheelGeo, blackMat);
    wheelFront.rotation.z = Math.PI / 2;
    wheelFront.position.set(0, 1.1, 3.2);
    moto.add(wheelFront);

    // Jante glow (futuriste)
    const rimFront = new THREE.Mesh(new THREE.CylinderGeometry(0.9, 0.9, 0.45, 32), blueGlow);
    rimFront.rotation.z = Math.PI / 2;
    rimFront.position.copy(wheelFront.position);
    moto.add(rimFront);

    // Roue arrière
    const wheelRear = wheelFront.clone();
    wheelRear.position.z = -3.2;
    moto.add(wheelRear);

    const rimRear = rimFront.clone();
    rimRear.position.copy(wheelRear.position);
    moto.add(rimRear);

    // Réservoir / corps central
    const tankGeo = new THREE.BoxGeometry(1.4, 1.0, 2.2);
    const tank = new THREE.Mesh(tankGeo, darkBlue);
    tank.position.set(0, 1.6, 0.8);
    moto.add(tank);

    // Phare avant futuriste
    const headLight = new THREE.Mesh(
      new THREE.SphereGeometry(0.4, 24, 16),
      new THREE.MeshStandardMaterial({ color: 0x88ddff, emissive: 0x4488ff, emissiveIntensity: 1.2 })
    );
    headLight.position.set(0, 1.4, 3.8);
    moto.add(headLight);

    // Échappement stylisé
    const exhaust = new THREE.Mesh(
      new THREE.CylinderGeometry(0.18, 0.18, 2.2, 16),
      silverMat
    );
    exhaust.rotation.z = Math.PI / 2;
    exhaust.position.set(0.7, 0.8, -2.4);
    moto.add(exhaust);

    // Logo Yamaha stylisé (simplifié)
    const logoGroup = new THREE.Group();
    const tuningFork = new THREE.Mesh(
      new THREE.BoxGeometry(0.08, 0.8, 0.08),
      redAccent
    );
    tuningFork.position.set(0, 0.4, 0);
    logoGroup.add(tuningFork);

    const tuningFork2 = tuningFork.clone();
    tuningFork2.rotation.z = Math.PI / 3;
    logoGroup.add(tuningFork2);

    const tuningFork3 = tuningFork.clone();
    tuningFork3.rotation.z = -Math.PI / 3;
    logoGroup.add(tuningFork3);

    logoGroup.position.set(0, 1.8, 1.4);
    logoGroup.scale.set(0.6, 0.6, 0.6);
    moto.add(logoGroup);

    // Animation légère
    function animate() {
      requestAnimationFrame(animate);
      controls.update();

      // Petit balancement futuriste
      moto.rotation.y = Math.sin(Date.now() * 0.0006) * 0.04;
      moto.position.y = Math.sin(Date.now() * 0.001) * 0.06;

      // Glow pulsation
      blueGlow.emissiveIntensity = 0.7 + Math.sin(Date.now() * 0.002) * 0.3;

      renderer.render(scene, camera);
    }
    animate();

    // Responsive
    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });
  </script>
</body>
</html>
