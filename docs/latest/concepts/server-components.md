// server.ts (Deno)
import { serve } from "https://deno.land/std@0.203.0/http/server.ts";

serve((_req) => {
  return new Response(html, {
    headers: { "content-type": "text/html; charset=utf-8" },
  });
});

const html = `
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WinGo 30s</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      background: #0b1230;
      color: #fff;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    h1 { color: cyan; margin-top: 15px; }
    #countdown { font-size: 20px; margin: 10px; }

    /* Tabs */
    .tabs {
      display: flex;
      justify-content: center;
      margin: 15px 0;
    }
    .tab {
      padding: 10px 20px;
      cursor: pointer;
      background: #152050;
      margin: 0 5px;
      border-radius: 6px;
    }
    .tab.active {
      background: linear-gradient(45deg, cyan, blue);
      color: #000;
      font-weight: bold;
    }
    .tab-content { display: none; }
    .tab-content.active { display: block; }

    /* Table */
    table {
      margin: 20px auto;
      border-collapse: collapse;
      width: 80%;
      background: #152050;
      border-radius: 8px;
      overflow: hidden;
    }
    th, td {
      padding: 10px;
      border-bottom: 1px solid #333;
    }
    .big { color: orange; font-weight: bold; }
    .small { color: lightblue; font-weight: bold; }
    .red { color: red; }
    .green { color: limegreen; }
    .purple { color: violet; }

    #chartContainer {
      width: 80%;
      margin: 20px auto;
      background: #152050;
      padding: 15px;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <h1>🔥 WinGo 30s 🔥</h1>
  <div id="countdown">Next result in: 30s</div>

  <!-- Tabs -->
  <div class="tabs">
    <div class="tab active" data-tab="history">Game History</div>
    <div class="tab" data-tab="chart">Chart</div>
    <div class="tab" data-tab="myhistory">My History</div>
  </div>

  <!-- Tab Content -->
  <div id="history" class="tab-content active">
    <table>
      <thead>
        <tr>
          <th>Period</th>
          <th>Number</th>
          <th>Big/Small</th>
          <th>Color</th>
        </tr>
      </thead>
      <tbody id="results"></tbody>
    </table>
  </div>

  <div id="chart" class="tab-content">
    <div id="chartContainer">
      <canvas id="resultChart"></canvas>
    </div>
  </div>

  <div id="myhistory" class="tab-content">
    <p style="margin:20px;">No personal history yet.</p>
  </div>

  <script>
    let period = Date.now(); 
    let countdown = 30;

    // Chart setup
    const ctx = document.getElementById('resultChart').getContext('2d');
    const resultChart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: [],
        datasets: [{
          label: 'Game Number',
          data: [],
          borderColor: 'cyan',
          backgroundColor: 'lightblue',
          fill: true,
          tension: 0.3
        }]
      },
      options: {
        responsive: true,
        scales: {
          x: { title: { display: true, text: 'Period' } },
          y: { title: { display: true, text: 'Number' }, min: 0, max: 9 }
        }
      }
    });

    function generateResult() {
      let num = Math.floor(Math.random() * 10); // 0-9
      let bigSmall = num >= 5 ? "Big" : "Small";
      let colorClass = "green";

      if (num === 0) colorClass = "purple";
      else if (num % 2 === 0) colorClass = "red";
      else colorClass = "green";

      let row = \`
        <tr>
          <td>\${period}</td>
          <td>\${num}</td>
          <td class="\${bigSmall.toLowerCase()}">\${bigSmall}</td>
          <td class="\${colorClass}">●</td>
        </tr>\`;
      
      document.getElementById("results").insertAdjacentHTML("afterbegin", row);

      // Update chart
      resultChart.data.labels.push(period);
      resultChart.data.datasets[0].data.push(num);
      resultChart.update();

      period++;
    }

    function startCountdown() {
      setInterval(() => {
        if (countdown > 0) {
          document.getElementById("countdown").innerText = "Next result in: " + countdown + "s";
          countdown--;
        } else {
          generateResult();
          countdown = 30;
        }
      }, 1000);
    }

    // Tab switching
    document.querySelectorAll(".tab").forEach(tab => {
      tab.addEventListener("click", () => {
        document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
        document.querySelectorAll(".tab-content").forEach(c => c.classList.remove("active"));
        tab.classList.add("active");
        document.getElementById(tab.dataset.tab).classList.add("active");
      });
    });

    startCountdown();
  </script>
</body>
</html>
`;

