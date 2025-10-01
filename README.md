index.html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SPMN MILL Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
        }
        
        .green-header {
            background-color: #4CAF50;
            color: white;
            padding: 15px;
            text-align: center;
        }
        
        .marquee {
            color: red;
            font-size: 120%;
            font-weight: bold;
            margin-bottom: 20px;
            overflow: hidden;
            white-space: nowrap;
            box-sizing: border-box;
        }
        
        .marquee span {
            display: inline-block;
            padding-left: 100%;
            animation: marquee 15s linear infinite;
        }
        
        @keyframes marquee {
            0% { transform: translate(0, 0); }
            100% { transform: translate(-100%, 0); }
        }
        
        .dashboard {
            display: flex;
            flex-direction: column;
            gap: 20px;
            padding: 20px;
        }
        
        .summary-cards {
            display: flex;
            gap: 20px;
            justify-content: center;
        }
        
        .card {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            padding: 20px;
            width: 250px;
            text-align: center;
        }
        
        .card h3 {
            margin-top: 0;
            color: #333;
        }
        
        .card .value {
            font-size: 36px;
            font-weight: bold;
            color: #4CAF50;
        }
        
        .data-entry {
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
        }
        
        .table-container {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            padding: 20px;
            flex: 1;
            min-width: 300px;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
        }
        
        th, td {
            padding: 8px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        
        th {
            background-color: #f2f2f2;
        }
        
        input {
            width: 100%;
            padding: 5px;
            box-sizing: border-box;
        }
        
        .chart-container {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            padding: 20px;
            height: 300px;
            margin-bottom: 20px;
        }
        
        .charts-row {
            display: flex;
            gap: 20px;
        }
        
        .charts-row .chart-container {
            flex: 1;
        }
        
        .formula {
            margin-top: 10px;
            padding: 10px;
            background-color: #f9f9f9;
            border-left: 4px solid #4CAF50;
        }
        
        .formula h4 {
            margin-top: 0;
        }
        
        .action-buttons {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin-bottom: 20px;
        }
        
        .btn {
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-weight: bold;
        }
        
        .btn-edit {
            background-color: #2196F3;
            color: white;
        }
        
        .btn-save {
            background-color: #4CAF50;
            color: white;
        }
        
        .btn-export {
            background-color: #FF9800;
            color: white;
        }
        
        .btn:hover {
            opacity: 0.9;
        }
        
        .disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            padding: 15px;
            background-color: #4CAF50;
            color: white;
            border-radius: 4px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
            display: none;
            z-index: 1000;
        }
    </style>
</head>
<body>
    <div class="green-header">
        <div class="marquee">
            <span>SPMN MILL</span>
        </div>
    </div>
    
    <div class="dashboard">
        <div class="action-buttons">
            <button class="btn btn-edit" id="editBtn" onclick="toggleEditMode()">Edit</button>
            <button class="btn btn-save disabled" id="saveBtn" onclick="saveData()">Save</button>
            <button class="btn btn-export" onclick="exportToPDF()">Export PDF</button>
        </div>
        
        <div class="summary-cards">
            <div class="card">
                <h3>Average %LF</h3>
                <div class="value" id="avgLF">0.00</div>
            </div>
            <div class="card">
                <h3>Average %Freshness</h3>
                <div class="value" id="avgFreshness">0.00</div>
            </div>
        </div>
        
        <div class="data-entry">
            <div class="table-container">
                <h2>LOOSE FRUIT SPMN</h2>
                <table id="looseFruitTable">
                    <thead>
                        <tr>
                            <th>Date</th>
                            <th>%LF</th>
                            <th>Unit</th>
                            <th>Blokk</th>
                            <th>Estate</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Rows will be generated by JavaScript -->
                    </tbody>
                </table>
            </div>
            
            <div class="table-container">
                <h2>FFB FRESHNESS</h2>
                <table id="ffbFreshnessTable">
                    <thead>
                        <tr>
                            <th>Date</th>
                            <th>Restan</th>
                            <th>Total Unit</th>
                            <th>%Freshness</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Rows will be generated by JavaScript -->
                    </tbody>
                </table>
                <div class="formula">
                    <h4>Formula:</h4>
                    <p>Average: % LF dan % Freshness</p>
                    <p>%freshness = Restan*100/total unit</p>
                </div>
            </div>
        </div>
        
        <div class="charts-row">
            <div class="chart-container">
                <canvas id="lfChart"></canvas>
            </div>
            <div class="chart-container">
                <canvas id="freshnessChart"></canvas>
            </div>
        </div>
    </div>
    
    <div class="notification" id="notification"></div>

    <script>
        let editMode = false;
        
        // Generate tables rows
        function generateTableRows() {
            // Generate rows for LOOSE FRUIT SPMN table
            const looseFruitTable = document.getElementById('looseFruitTable').getElementsByTagName('tbody')[0];
            for (let i = 1; i <= 31; i++) {
                const row = looseFruitTable.insertRow();
                row.innerHTML = `
                    <td><input type="date" id="lf-date-${i}" onchange="updateCharts()" disabled></td>
                    <td><input type="number" id="lf-percent-${i}" step="0.01" onchange="calculateAverages()" disabled></td>
                    <td><input type="number" id="lf-unit-${i}" onchange="updateCharts()" disabled></td>
                    <td><input type="text" id="lf-blokk-${i}" disabled></td>
                    <td><input type="text" id="lf-estate-${i}" disabled></td>
                `;
            }
            
            // Generate rows for FFB FRESHNESS table
            const ffbFreshnessTable = document.getElementById('ffbFreshnessTable').getElementsByTagName('tbody')[0];
            for (let i = 1; i <= 31; i++) {
                const row = ffbFreshnessTable.insertRow();
                row.innerHTML = `
                    <td><input type="date" id="ffb-date-${i}" onchange="updateCharts()" disabled></td>
                    <td><input type="number" id="ffb-restan-${i}" onchange="calculateFreshness(${i})" disabled></td>
                    <td><input type="number" id="ffb-total-${i}" onchange="calculateFreshness(${i})" disabled></td>
                    <td><input type="number" id="ffb-freshness-${i}" step="0.01" readonly disabled></td>
                `;
            }
        }
        
        // Toggle edit mode
        function toggleEditMode() {
            editMode = !editMode;
            const editBtn = document.getElementById('editBtn');
            const saveBtn = document.getElementById('saveBtn');
            
            if (editMode) {
                editBtn.textContent = 'Cancel';
                saveBtn.classList.remove('disabled');
                
                // Enable all inputs
                const inputs = document.querySelectorAll('input');
                inputs.forEach(input => {
                    if (!input.readOnly) {
                        input.disabled = false;
                    }
                });
            } else {
                editBtn.textContent = 'Edit';
                saveBtn.classList.add('disabled');
                
                // Disable all inputs
                const inputs = document.querySelectorAll('input');
                inputs.forEach(input => {
                    input.disabled = true;
                });
                
                // Reload data from localStorage
                loadData();
            }
        }
        
        // Save data to localStorage
        function saveData() {
            const data = {
                looseFruit: [],
                ffbFreshness: []
            };
            
            // Collect data from LOOSE FRUIT table
            for (let i = 1; i <= 31; i++) {
                const date = document.getElementById(`lf-date-${i}`).value;
                const percent = document.getElementById(`lf-percent-${i}`).value;
                const unit = document.getElementById(`lf-unit-${i}`).value;
                const blokk = document.getElementById(`lf-blokk-${i}`).value;
                const estate = document.getElementById(`lf-estate-${i}`).value;
                
                if (date || percent || unit || blokk || estate) {
                    data.looseFruit.push({
                        date, percent, unit, blokk, estate
                    });
                }
            }
            
            // Collect data from FFB FRESHNESS table
            for (let i = 1; i <= 31; i++) {
                const date = document.getElementById(`ffb-date-${i}`).value;
                const restan = document.getElementById(`ffb-restan-${i}`).value;
                const total = document.getElementById(`ffb-total-${i}`).value;
                const freshness = document.getElementById(`ffb-freshness-${i}`).value;
                
                if (date || restan || total || freshness) {
                    data.ffbFreshness.push({
                        date, restan, total, freshness
                    });
                }
            }
            
            // Save to localStorage
            localStorage.setItem('spmnMillData', JSON.stringify(data));
            
            // Show notification
            showNotification('Data saved successfully!');
            
            // Exit edit mode
            toggleEditMode();
        }
        
        // Load data from localStorage
        function loadData() {
            const savedData = localStorage.getItem('spmnMillData');
            if (savedData) {
                const data = JSON.parse(savedData);
                
                // Load data to LOOSE FRUIT table
                data.looseFruit.forEach((item, index) => {
                    if (index < 31) {
                        document.getElementById(`lf-date-${index + 1}`).value = item.date || '';
                        document.getElementById(`lf-percent-${index + 1}`).value = item.percent || '';
                        document.getElementById(`lf-unit-${index + 1}`).value = item.unit || '';
                        document.getElementById(`lf-blokk-${index + 1}`).value = item.blokk || '';
                        document.getElementById(`lf-estate-${index + 1}`).value = item.estate || '';
                    }
                });
                
                // Load data to FFB FRESHNESS table
                data.ffbFreshness.forEach((item, index) => {
                    if (index < 31) {
                        document.getElementById(`ffb-date-${index + 1}`).value = item.date || '';
                        document.getElementById(`ffb-restan-${index + 1}`).value = item.restan || '';
                        document.getElementById(`ffb-total-${index + 1}`).value = item.total || '';
                        document.getElementById(`ffb-freshness-${index + 1}`).value = item.freshness || '';
                    }
                });
                
                // Update calculations and charts
                calculateAverages();
                updateCharts();
            }
        }
        
        // Show notification
        function showNotification(message) {
            const notification = document.getElementById('notification');
            notification.textContent = message;
            notification.style.display = 'block';
            
            setTimeout(() => {
                notification.style.display = 'none';
            }, 3000);
        }
        
        // Export to PDF
        function exportToPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF('p', 'mm', 'a4');
            
            // Add title
            doc.setFontSize(20);
            doc.text('SPMN MILL Dashboard', 105, 15, { align: 'center' });
            
            // Add date
            doc.setFontSize(10);
            doc.text(`Generated on: ${new Date().toLocaleDateString()}`, 105, 25, { align: 'center' });
            
            // Add summary cards
            doc.setFontSize(12);
            doc.text('Summary:', 20, 35);
            doc.text(`Average %LF: ${document.getElementById('avgLF').textContent}`, 20, 45);
            doc.text(`Average %Freshness: ${document.getElementById('avgFreshness').textContent}`, 20, 55);
            
            // Add tables
            doc.text('LOOSE FRUIT SPMN:', 20, 70);
            
            // Add LOOSE FRUIT table
            let yPosition = 80;
            doc.setFontSize(10);
            doc.text('Date', 20, yPosition);
            doc.text('%LF', 50, yPosition);
            doc.text('Unit', 80, yPosition);
            doc.text('Blokk', 110, yPosition);
            doc.text('Estate', 140, yPosition);
            
            yPosition += 10;
            for (let i = 1; i <= 31; i++) {
                const date = document.getElementById(`lf-date-${i}`).value;
                if (date) {
                    doc.text(date, 20, yPosition);
                    doc.text(document.getElementById(`lf-percent-${i}`).value || '', 50, yPosition);
                    doc.text(document.getElementById(`lf-unit-${i}`).value || '', 80, yPosition);
                    doc.text(document.getElementById(`lf-blokk-${i}`).value || '', 110, yPosition);
                    doc.text(document.getElementById(`lf-estate-${i}`).value || '', 140, yPosition);
                    yPosition += 10;
                    
                    if (yPosition > 270) {
                        doc.addPage();
                        yPosition = 20;
                    }
                }
            }
            
            // Add FFB FRESHNESS table
            doc.text('FFB FRESHNESS:', 20, yPosition + 10);
            yPosition += 20;
            
            doc.text('Date', 20, yPosition);
            doc.text('Restan', 50, yPosition);
            doc.text('Total Unit', 80, yPosition);
            doc.text('%Freshness', 120, yPosition);
            
            yPosition += 10;
            for (let i = 1; i <= 31; i++) {
                const date = document.getElementById(`ffb-date-${i}`).value;
                if (date) {
                    doc.text(date, 20, yPosition);
                    doc.text(document.getElementById(`ffb-restan-${i}`).value || '', 50, yPosition);
                    doc.text(document.getElementById(`ffb-total-${i}`).value || '', 80, yPosition);
                    doc.text(document.getElementById(`ffb-freshness-${i}`).value || '', 120, yPosition);
                    yPosition += 10;
                    
                    if (yPosition > 270) {
                        doc.addPage();
                        yPosition = 20;
                    }
                }
            }
            
            // Add charts as images
            html2canvas(document.getElementById('lfChart')).then(canvas => {
                const lfChartImg = canvas.toDataURL('image/png');
                doc.addPage();
                doc.text('%LF Chart', 105, 15, { align: 'center' });
                doc.addImage(lfChartImg, 'PNG', 20, 25, 170, 100);
                
                html2canvas(document.getElementById('freshnessChart')).then(canvas => {
                    const freshnessChartImg = canvas.toDataURL('image/png');
                    doc.addPage();
                    doc.text('%Freshness Chart', 105, 15, { align: 'center' });
                    doc.addImage(freshnessChartImg, 'PNG', 20, 25, 170, 100);
                    
                    // Save the PDF
                    doc.save('SPMN_MILL_Dashboard.pdf');
                    showNotification('PDF exported successfully!');
                });
            });
        }
        
        // Calculate freshness percentage
        function calculateFreshness(row) {
            const restan = parseFloat(document.getElementById(`ffb-restan-${row}`).value) || 0;
            const total = parseFloat(document.getElementById(`ffb-total-${row}`).value) || 0;
            
            if (total > 0) {
                const freshness = (restan * 100) / total;
                document.getElementById(`ffb-freshness-${row}`).value = freshness.toFixed(2);
            } else {
                document.getElementById(`ffb-freshness-${row}`).value = '';
            }
            
            calculateAverages();
            updateCharts();
        }
        
        // Calculate averages
        function calculateAverages() {
            // Calculate average %LF
            let lfSum = 0;
            let lfCount = 0;
            for (let i = 1; i <= 31; i++) {
                const lfValue = parseFloat(document.getElementById(`lf-percent-${i}`).value);
                if (!isNaN(lfValue)) {
                    lfSum += lfValue;
                    lfCount++;
                }
            }
            const avgLF = lfCount > 0 ? lfSum / lfCount : 0;
            document.getElementById('avgLF').textContent = avgLF.toFixed(2);
            
            // Calculate average %Freshness
            let freshnessSum = 0;
            let freshnessCount = 0;
            for (let i = 1; i <= 31; i++) {
                const freshnessValue = parseFloat(document.getElementById(`ffb-freshness-${i}`).value);
                if (!isNaN(freshnessValue)) {
                    freshnessSum += freshnessValue;
                    freshnessCount++;
                }
            }
            const avgFreshness = freshnessCount > 0 ? freshnessSum / freshnessCount : 0;
            document.getElementById('avgFreshness').textContent = avgFreshness.toFixed(2);
            
            updateCharts();
        }
        
        // Initialize charts
        const lfCtx = document.getElementById('lfChart').getContext('2d');
        const lfChart = new Chart(lfCtx, {
            type: 'line',
            data: {
                labels: [],
                datasets: [
                    {
                        label: '%LF',
                        data: [],
                        borderColor: 'rgba(75, 192, 192, 1)',
                        backgroundColor: 'rgba(75, 192, 192, 0.2)',
                        tension: 0.1
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    title: {
                        display: true,
                        text: '%LF Chart'
                    }
                },
                scales: {
                    y: {
                        beginAtZero: true
                    }
                }
            }
        });
        
        const freshnessCtx = document.getElementById('freshnessChart').getContext('2d');
        const freshnessChart = new Chart(freshnessCtx, {
            type: 'line',
            data: {
                labels: [],
                datasets: [
                    {
                        label: '%Freshness',
                        data: [],
                        borderColor: 'rgba(255, 99, 132, 1)',
                        backgroundColor: 'rgba(255, 99, 132, 0.2)',
                        tension: 0.1
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    title: {
                        display: true,
                        text: '%Freshness Chart'
                    }
                },
                scales: {
                    y: {
                        beginAtZero: true
                    }
                }
            }
        });
        
        // Update charts with data
        function updateCharts() {
            // Update LF Chart
            const lfDates = [];
            const lfData = [];
            
            // Collect data from LOOSE FRUIT table
            for (let i = 1; i <= 31; i++) {
                const date = document.getElementById(`lf-date-${i}`).value;
                const lfValue = parseFloat(document.getElementById(`lf-percent-${i}`).value);
                
                if (date && !isNaN(lfValue)) {
                    lfDates.push(date);
                    lfData.push(lfValue);
                }
            }
            
            lfChart.data.labels = lfDates;
            lfChart.data.datasets[0].data = lfData;
            lfChart.update();
            
            // Update Freshness Chart
            const freshnessDates = [];
            const freshnessData = [];
            
            // Collect data from FFB FRESHNESS table
            for (let i = 1; i <= 31; i++) {
                const date = document.getElementById(`ffb-date-${i}`).value;
                const freshnessValue = parseFloat(document.getElementById(`ffb-freshness-${i}`).value);
                
                if (date && !isNaN(freshnessValue)) {
                    freshnessDates.push(date);
                    freshnessData.push(freshnessValue);
                }
            }
            
            freshnessChart.data.labels = freshnessDates;
            freshnessChart.data.datasets[0].data = freshnessData;
            freshnessChart.update();
        }
        
        // Initialize the page
        generateTableRows();
        loadData();
    </script>
</body>
</html>
