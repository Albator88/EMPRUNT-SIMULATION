<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Yamaha Prototype 3D Concept</title>
    <style>
        body { margin: 0; background: #050505; display: flex; justify-content: center; align-items: center; height: 100vh; perspective: 1000px; overflow: hidden; font-family: sans-serif; }
        .scene { width: 400px; height: 400px; position: relative; transform-style: preserve-3d; animation: rotate 15s infinite linear; }
        
        @keyframes rotate {
            from { transform: rotateY(0deg) rotateX(-10deg); }
            to { transform: rotateY(360deg) rotateX(-10deg); }
        }

        /* Pièces de la moto */
        .part { position: absolute; transform-style: preserve-3d; }

        /* Corps principal (Châssis) */
        .chassis {
            width: 200px; height: 60px; background: #e0e0e0;
            top: 170px; left: 100px;
            transform: translateZ(0px);
            box-shadow: inset 0 0 20px rgba(0,0,0,0.5);
        }

        /* Roues */
        .wheel {
            width: 120px; height: 120px;
            border: 15px solid #222;
            border-radius: 50%;
            background: radial-gradient(circle, #333 30%, #111 70%);
        }
        .front-wheel { top: 180px; left: 0px; transform: rotateY(90deg); }
        .rear-wheel { top: 180px; left: 280px; transform: rotateY(90deg); }

        /* Éléments de design (Bleu Yamaha) */
        .neon {
            width: 180px; height: 5px; background: #0046ad;
            top: 175px; left: 110px;
            box-shadow: 0 0 15px #00a2ff;
        }

        .label {
            position: absolute; top: 20px; color: white;
            text-align: center; width: 100%; font-weight: bold;
            letter-spacing: 2px; text-transform: uppercase;
        }
    </style>
</head>
<body>
    <div class="label">Yamaha Concept Motoroid - CSS 3D View</div>
    <div class="scene">
        <div class="part chassis"></div>
        <div class="part wheel front-wheel"></div>
        <div class="part wheel rear-wheel"></div>
        <div class="part neon"></div>
        <div class="part" style="width:150px; height:20px; background:#444; top:210px; left:180px; transform: rotateZ(-10deg);"></div>
    </div>
</body>
</html>
