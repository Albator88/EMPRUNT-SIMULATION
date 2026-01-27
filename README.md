<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulateur 90k€ - Rentabilité</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #f4f7f9; padding: 20px; color: #2d3748; }
        .container { max-width: 1000px; margin: auto; background: white; padding: 30px; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.08); }
        h1 { text-align: center; color: #1a365d; margin-bottom: 30px; }
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 25px; }
        .input-group { background: #fff; padding: 12px; border: 1px solid #e2e8f0; border-radius: 8px; }
        .input-group.highlight { border-left: 4px solid #3182ce; }
        .input-group.highlight-alt { border-left: 4px solid #ed8936; }
        label { display: block; font-size: 0.8em; font-weight: bold; margin-bottom: 5px; color: #718096; }
        input[type="number"] { width: 100%; border: 1px solid #cbd5e0; padding: 8px; border-radius: 4px; outline: none; }
        .slider-section { background: #edf2f7; padding: 20px; border-radius: 10px; margin-bottom: 25px; text-align: center; }
        input[type="range"] { width: 100%; margin-top: 10px; }
        .results { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 15px; }
        .card { padding: 15px; border-radius: 10px; text-align: center; color: white; }
        .mensualite-display { text-align: center; background: #2d3748; color: white; padding: 10px; border-radius: 10px; margin-bottom: 25px; font-size: 0.9em; }
        .mensualite-display b { font-size: 1.2em; color: #63b3ed; }
        .bg-blue { background: #3182ce; }
        .bg-orange { background: #ed8936; }
        .val { font-size: 1.6em; font-weight: bold; display: block; }
        canvas { background: white; border-radius: 10px; padding: 10px; border: 1px solid #e2e8f0; }
    </style>
</head>
<body>

<div class="container">
    <h1>Simulation Projet 90 000 €</h1>
    
    <div class="grid">
        <div class="input-group">
            <label>Prix Projet (€)</label>
            <input type="number" id="prix" value="90000" oninput="update()">
        </div>
        <div class="input-group highlight">
            <label>Loyer A (Annuel €)</label>
            <input type="number" id="loyerA" value="7800" oninput="update()">
        </div>
        <div class="input-group highlight-alt">
            <label>Loyer B (Annuel €)</label>
            <input type="number" id="loyerB" value="9600" oninput="update()">
        </div>
        <div class="input-group">
            <label>Charges/Taxes (€)</label>
            <input type="number" id="charges" value="1988" oninput="update()">
        </div>
    </div>

    <div class="slider-section">
        <label>MONTANT DE VOTRE APPORT : <span id="apportVal">16000</span> €</label>
        <input type="range" id="apport" min="5000" max="90000" step="500" value="16000" oninput="update()">
    </div>

    <div class="results">
        <div class="card bg-blue">
            <span style="font-size: 0.9em;">Rentabilité Scénario A</span>
            <span class="val" id="resA">0 %</span>
        </div>
        <div class="card bg-orange">
            <span style="font-size: 0.9em;">Rentabilité Scénario B</span>
            <span class="val" id="resB">0 %</span>
        </div>
    </div>

    <div class="mensualite-display">
        Mensualité estimée du crédit (20 ans à 4.5%) : <b id="mensDisplay">0</b> € / mois
    </div>

    <canvas id="compChart"></canvas>
</div>

<script>
    let chart;

    function calculateMensualite(P, A) {
        const borrowed = P - A;
        if (borrowed <= 0) return 0;
        const i = 0.045 / 12; 
        const m = 20 * 12;
        return borrowed * (i / (1 - Math.pow(1 + i, -m)));
    }

    function calculateRA(P, L, C, A) {
        if (A <= 0) return 0;
        const mens = calculateMensualite(P, A);
        const cf_annuel = (L - C) - (mens * 12);
        return (cf_annuel / A) * 100;
    }

    function update() {
        const P = parseFloat(document.getElementById('prix').value) || 0;
        const LA = parseFloat(document.getElementById('loyerA').value) || 0;
        const LB = parseFloat(document.getElementById('loyerB').value) || 0;
        const C = parseFloat(document.getElementById('charges').value) || 0;
        const A = parseFloat(document.getElementById('apport').value) || 1;

        const raA = calculateRA(P, LA, C, A);
        const raB = calculateRA(P, LB, C, A);
        const mens = calculateMensualite(P, A);

        document.getElementById('apportVal').innerText = A.toLocaleString();
        document.getElementById('resA').innerText = raA.toFixed(2) + " %";
        document.getElementById('resB').innerText = raB.toFixed(2) + " %";
        document.getElementById('mensDisplay').innerText = Math.round(mens).toLocaleString();

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
