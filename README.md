# HW4
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Opzioni - Payoff</title>
  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f7f7f7;
      margin: 20px;
    }
    h1 {
      text-align: center;
      color: #333;
    }
    .form-container {
      text-align: center;
      margin-bottom: 20px;
    }
    .form-container input,
    .form-container select {
      margin: 5px;
      padding: 10px;
      font-size: 16px;
      border: 1px solid #ccc;
    }
    .form-container button {
      padding: 10px 20px;
      background-color: #28a745;
      color: white;
      border: none;
      cursor: pointer;
      font-size: 16px;
    }
    .form-container button:hover {
      background-color: #218838;
    }
    canvas {
      display: block;
      margin: 0 auto;
    }
  </style>
</head>
<body>

  <h1>Opzioni e Grafico Payoff</h1>

  <div class="form-container">
    <label for="strike">Strike: </label>
    <input type="number" id="strike" placeholder="Es. 100"><br>
    <label for="scadenza">Scadenza (giorni): </label>
    <input type="number" id="scadenza" placeholder="Es. 30"><br>
    <label for="premio">Premio: </label>
    <input type="number" id="premio" placeholder="Es. 5"><br>
    <label for="prezzo">Prezzo sottostante: </label>
    <input type="number" id="prezzo" placeholder="Es. 105"><br>
    <label for="quantita">Quantità di opzioni: </label>
    <input type="number" id="quantita" placeholder="Es. 1"><br>
    <label for="posizione">Posizione: </label>
    <select id="posizione">
      <option value="long">Long</option>
      <option value="short">Short</option>
    </select><br><br>
    <button onclick="aggiornaGrafico()">Genera Grafico</button>
  </div>

  <canvas id="graficoPayoff" width="600" height="400"></canvas>

  <script>
    function calcolaPayoff(strike, prezzo, premio, quantita, tipo, posizione) {
      let payoff = [];
      let delta = 10;
      let maxPrezzo = prezzo + 50;
      for (let i = prezzo - 50; i <= maxPrezzo; i += delta) {
        let payoffOpzione = 0;
        if (tipo === "call") {
          payoffOpzione = Math.max(0, i - strike) - premio;
        } else if (tipo === "put") {
          payoffOpzione = Math.max(0, strike - i) - premio;
        }
        if (posizione === "short") {
          payoffOpzione = -payoffOpzione;
        }
        payoff.push(payoffOpzione * quantita);
      }
      return payoff;
    }

    function aggiornaGrafico() {
      let strike = parseFloat(document.getElementById("strike").value);
      let scadenza = parseInt(document.getElementById("scadenza").value);
      let premio = parseFloat(document.getElementById("premio").value);
      let prezzo = parseFloat(document.getElementById("prezzo").value);
      let quantita = parseInt(document.getElementById("quantita").value);
      let posizione = document.getElementById("posizione").value;

      let payoffCall = calcolaPayoff(strike, prezzo, premio, quantita, "call", posizione);
      let payoffPut = calcolaPayoff(strike, prezzo, premio, quantita, "put", posizione);

      let ctx = document.getElementById('graficoPayoff').getContext('2d');
      if (window.chart) {
        window.chart.destroy();
      }

      window.chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: Array.from({length: payoffCall.length}, (_, i) => (prezzo - 50) + i * 10),
          datasets: [
            {
              label: 'Call Payoff',
              data: payoffCall,
              borderColor: "green",
              backgroundColor: "rgba(0, 128, 0, 0.2)",
              borderWidth: 2,
            },
            {
              label: 'Put Payoff',
              data: payoffPut,
              borderColor: "red",
              backgroundColor: "rgba(255, 0, 0, 0.2)",
              borderWidth: 2,
            }
          ]
        },
        options: {
          scales: {
            x: {
              title: {
                display: true,
                text: 'Prezzo Sottostante'
              }
            },
            y: {
              title: {
                display: true,
                text: 'Payoff'
              }
            }
          }
        }
      });
    }
  </script>

</body>
</html>
