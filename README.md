# AIRcontrol
<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>МОНІТОРИНГ ЯКОСТІ ПОВІТРЯ</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <style>
        .container {
            margin-top: 20px;
        }
        @media (max-width: 768px) {
            .container {
                margin-top: 10px;
            }
        }
        .page-section {
            margin-bottom: 30px;
        }
        .page-section h3 {
            color: #4A90E2;
        }
        .table th, .table td {
            text-align: center;
        }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
    <div class="container">
        <div class="row">
            <div class="col-12 text-center">
                <h1>МОНІТОРИНГ ЯКОСТІ ПОВІТРЯ</h1>
                <p>Дані в реальному часі від сенсорів якості повітря.</p>
            </div>
        </div>

  <div class="row page-section">
            <div class="col-md-6 col-12">
                <h3>Графік якості повітря</h3>
                <canvas id="airQualityChart" class="img-fluid"></canvas>
            </div>
        </div>

   <div class="row page-section">
            <div class="col-md-6 col-12">
                <h3>Поточні дані про якість повітря</h3>
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th>Ідентифікатор сенсора</th>
                            <th>Місце розташування</th>
                            <th>PM2.5</th>
                            <th>PM10</th>
                            <th>NO2</th>
                            <th>CO</th>
                            <th>Час вимірювання</th>
                            <th>Дії</th>
                        </tr>
                    </thead>
                    <tbody id="current-data-table-body">
                      <!-- Поточні дані будуть додаватися сюди -->
                    </tbody>
                </table>
            </div>
        </div>

  <div class="row page-section">
            <div class="col-12">
                <h3>Задання якості повітря</h3>

   <div class="nav-buttons mb-4">
                    <button onclick="showPage('sensorForm')" class="btn btn-primary btn-block">Додати Дані</button>
                    <button onclick="showPage('dataView')" class="btn btn-primary btn-block">Переглянути Дані</button>
                </div>

  <div id="sensorForm" class="page active">
                    <form id="sensor-form">
                        <div class="form-group">
                            <label for="sensor_id">ID Сенсора:</label>
                            <input type="text" id="sensor_id" class="form-control" required>
                        </div>
                        <div class="form-group">
                            <label for="location">Місце розташування:</label>
                            <input type="text" id="location" class="form-control" required>
                        </div>
                        <div class="form-group">
                            <label for="pm25">PM2.5 (μg/m³):</label>
                            <input type="number" id="pm25" class="form-control" step="0.01" required>
                        </div>
                        <div class="form-group">
                            <label for="pm10">PM10 (μg/m³):</label>
                            <input type="number" id="pm10" class="form-control" step="0.01" required>
                        </div>
                        <div class="form-group">
                            <label for="no2">NO₂ (μg/m³):</label>
                            <input type="number" id="no2" class="form-control" step="0.01" required>
                        </div>
                        <div class="form-group">
                            <label for="co">CO (μg/m³):</label>
                            <input type="number" id="co" class="form-control" step="0.01" required>
                        </div>
                        <div class="form-group">
                            <label for="timestamp">Час вимірювання:</label>
                            <input type="datetime-local" id="timestamp" class="form-control" required>
                        </div>
                        <button type="button" class="btn btn-primary btn-block" onclick="saveData()">Зберегти Дані</button>
                    </form>
                </div>

  <div id="dataView" class="page">
                    <h3>Редагування даних</h3>
                    <button class="btn btn-danger" onclick="clearData()">Очистити всі дані</button>
                </div>
            </div>
        </div>

        <!-- Історія даних про якість повітря -->
 <div class="row page-section">
            <div class="col-md-6 col-12">
                <h3>Історія даних про якість повітря</h3>
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th>Ідентифікатор сенсора</th>
                            <th>Місце розташування</th>
                            <th>PM2.5</th>
                            <th>PM10</th>
                            <th>NO2</th>
                            <th>CO</th>
                            <th>Час вимірювання</th>
                        </tr>
                    </thead>
                    <tbody id="history-data-table-body">
                        <!-- Історичні дані будуть додаватися сюди -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.5.3/dist/umd/popper.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>

  <script>
        let airQualityData = {
            labels: [],
            datasets: [
                {
                    label: 'PM2.5 (μg/m³)',
                    borderColor: 'rgba(255, 99, 132, 1)',
                    backgroundColor: 'rgba(255, 99, 132, 0.2)',
                    data: [],
                    fill: false
                },
                {
                    label: 'PM10 (μg/m³)',
                    borderColor: 'rgba(54, 162, 235, 1)',
                    backgroundColor: 'rgba(54, 162, 235, 0.2)',
                    data: [],
                    fill: false
                },
                {
                    label: 'NO₂ (μg/m³)',
                    borderColor: 'rgba(75, 192, 192, 1)',
                    backgroundColor: 'rgba(75, 192, 192, 0.2)',
                    data: [],
                    fill: false
                },
                {
                    label: 'CO (μg/m³)',
                    borderColor: 'rgba(153, 102, 255, 1)',
                    backgroundColor: 'rgba(153, 102, 255, 0.2)',
                    data: [],
                    fill: false
                }
            ]
        };

        const ctx = document.getElementById('airQualityChart').getContext('2d');
        const airQualityChart = new Chart(ctx, {
            type: 'line',
            data: airQualityData,
            options: {
                responsive: true,
                scales: {
                    x: { title: { text: 'Час', display: true } },
                    y: { title: { text: 'Концентрація (μg/m³)', display: true }, beginAtZero: true }
                }
            }
        });

        function saveData() {
            const sensorId = document.getElementById('sensor_id').value;
            const location = document.getElementById('location').value;
            const pm25 = parseFloat(document.getElementById('pm25').value);
            const pm10 = parseFloat(document.getElementById('pm10').value);
            const no2 = parseFloat(document.getElementById('no2').value);
            const co = parseFloat(document.getElementById('co').value);
            const timestamp = document.getElementById('timestamp').value;

            const newData = { sensorId, location, pm25, pm10, no2, co, timestamp };
            addDataToChart(newData);
            addDataToCurrentTable(newData);
            addDataToHistoryTable(newData);

            const storedData = JSON.parse(localStorage.getItem('airQualityData')) || [];
            storedData.push(newData);
            localStorage.setItem('airQualityData', JSON.stringify(storedData));
        }

        function loadStoredData() {
            const storedData = JSON.parse(localStorage.getItem('airQualityData')) || [];
            storedData.forEach(data => {
                addDataToChart(data);
                addDataToCurrentTable(data);
                addDataToHistoryTable(data);
            });
        }

        document.addEventListener('DOMContentLoaded', loadStoredData);

        function addDataToChart(data) {
            airQualityData.labels.push(data.timestamp);
            airQualityData.datasets[0].data.push(data.pm25);
            airQualityData.datasets[1].data.push(data.pm10);
            airQualityData.datasets[2].data.push(data.no2);
            airQualityData.datasets[3].data.push(data.co);
            airQualityChart.update();
        }

        function addDataToCurrentTable(data) {
            const row = `<tr>
                <td>${data.sensorId}</td>
                <td>${data.location}</td>
                <td>${data.pm25}</td>
                <td>${data.pm10}</td>
                <td>${data.no2}</td>
                <td>${data.co}</td>
                <td>${data.timestamp}</td>
                <td><button onclick="deleteRow(this)" class="btn btn-danger btn-sm">Видалити</button></td>
            </tr>`;
            document.getElementById('current-data-table-body').insertAdjacentHTML('beforeend', row);
        }

        function addDataToHistoryTable(data) {
            const row = `<tr>
                <td>${data.sensorId}</td>
                <td>${data.location}</td>
                <td>${data.pm25}</td>
                <td>${data.pm10}</td>
                <td>${data.no2}</td>
                <td>${data.co}</td>
                <td>${data.timestamp}</td>
            </tr>`;
            document.getElementById('history-data-table-body').insertAdjacentHTML('beforeend', row);
        }

        function deleteRow(button) {
            const rowIndex = button.closest('tr').rowIndex - 1;
            button.closest('tr').remove();

            const storedData = JSON.parse(localStorage.getItem('airQualityData'));
            storedData.splice(rowIndex, 1);
            localStorage.setItem('airQualityData', JSON.stringify(storedData));
        }

        function clearData() {
            airQualityData.labels = [];
            airQualityData.datasets.forEach(dataset => dataset.data = []);
            airQualityChart.update();
            document.getElementById('current-data-table-body').innerHTML = '';
            document.getElementById('history-data-table-body').innerHTML = '';
            localStorage.removeItem('airQualityData');
        }
    </script>
    
</body>
</html>
