# Salary_Calculator.io












<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>מחשבון שכר מתקדם</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f7f6;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 700px;
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            margin: auto;
        }
        h1, h2, h3 { color: #333; text-align: center; }
        .section-title {
            background-color: #e0e0e0;
            padding: 10px;
            border-radius: 5px;
            margin-top: 25px;
            font-weight: bold;
            margin-bottom: 15px;
        }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        input[type="number"], input[type="time"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            box-sizing: border-box;
        }
        .grid-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }
        
        /* עיצוב הטאבים */
        .tabs {
            display: flex;
            margin-top: 20px;
            border-bottom: 2px solid #4CAF50;
        }
        .tab-btn {
            flex: 1;
            padding: 12px;
            background-color: #f1f1f1;
            border: none;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            border-radius: 5px 5px 0 0;
            margin-left: 5px;
            transition: background-color 0.3s;
        }
        .tab-btn:last-child { margin-left: 0; }
        .tab-btn:hover { background-color: #ddd; }
        .tab-btn.active {
            background-color: #4CAF50;
            color: white;
        }
        .tab-content {
            display: none;
            padding: 20px;
            border: 1px solid #ccc;
            border-top: none;
            background-color: #fafafa;
            border-radius: 0 0 5px 5px;
        }
        .tab-content.active {
            display: block;
        }

        /* עיצוב טבלת שעות מתקדמת */
        table { width: 100%; border-collapse: collapse; margin-bottom: 15px; }
        th, td { padding: 10px; text-align: right; border-bottom: 1px solid #ddd; }
        .btn-small {
            background-color: #2196F3; color: white; border: none; padding: 8px 15px; border-radius: 3px; cursor: pointer;
        }
        .btn-small:hover { background-color: #0b7dda; }

        button.calc-btn {
            width: 100%; padding: 15px; background-color: #4CAF50; color: white; border: none; border-radius: 5px; font-size: 18px; cursor: pointer; margin-top: 20px;
        }
        button.calc-btn:hover { background-color: #45a049; }

        .result-box {
            margin-top: 30px; padding: 20px; background-color: #e9f5e9; border: 1px solid #c3e6c3; border-radius: 5px; display: none;
        }
        .result-item { display: flex; justify-content: space-between; margin-bottom: 10px; font-size: 16px; border-bottom: 1px dashed #ccc; padding-bottom: 5px;}
        .total-net { font-size: 24px; font-weight: bold; color: #2e7d32; border-top: 2px solid #333; padding-top: 15px; margin-top: 15px; border-bottom: none;}
        .deduction { color: #d32f2f; }
        .addition { color: #388e3c; }
    </style>
</head>
<body>

<div class="container">
    <h1>מחשבון שכר</h1>
    
    <div class="section-title">נתוני בסיס וניכויי חובה</div>
    <div class="grid-2">
        <div class="form-group">
            <label for="hourly-wage">שכר שעתי (₪):</label>
            <input type="number" id="hourly-wage" value="0" min="0">
        </div>
        <div class="form-group">
            <label for="credit-points">נקודות זיכוי (מס הכנסה):</label>
            <input type="number" id="credit-points" value="2.25" step="0.25" min="0">
        </div>
    </div>

    <div class="tabs">
        <button class="tab-btn active" id="btn-manual" onclick="switchTab('manual')">הזנת שעות (רגיל)</button>
        <button class="tab-btn" id="btn-advanced" onclick="switchTab('advanced')">הזנה יומית (מתקדם)</button>
    </div>

    <div id="tab-manual" class="tab-content active">
        <div class="form-group">
            <label for="total-hours">סה"כ שעות עבודה בחודש:</label>
            <input type="number" id="total-hours" placeholder="לדוגמה: 180" value="0">
        </div>
    </div>

    <div id="tab-advanced" class="tab-content">
        <p style="font-size: 14px; color: #555; margin-top: 0;">הזן שעות כניסה ויציאה לכל יום. המערכת תחשב את סך השעות אוטומטית.</p>
        <table id="time-table">
            <thead>
                <tr>
                    <th>כניסה</th>
                    <th>יציאה</th>
                    <th>סה"כ שעות</th>
                </tr>
            </thead>
            <tbody id="time-body">
                </tbody>
        </table>
        <button class="btn-small" onclick="addTimeRow()">+ הוסף יום עבודה</button>
        <div style="margin-top: 15px; font-weight: bold; font-size: 18px;">
            סה"כ שעות מהטבלה: <span id="calculated-hours" style="color: #2196F3;">0.00</span>
        </div>
    </div>

    <div class="section-title">היעדרויות ותוספות (בתשלום)</div>
    <div class="grid-2">
        <div class="form-group">
            <label for="vacation-days">ימי חופשה (בתשלום):</label>
            <input type="number" id="vacation-days" value="0" min="0">
        </div>
        <div class="form-group">
            <label for="sick-days">ימי מחלה (בתשלום):</label>
            <input type="number" id="sick-days" value="0" min="0">
        </div>
    </div>
    <div class="form-group">
        <label for="travel-expenses">סכום נסיעות (₪):</label>
        <input type="number" id="travel-expenses" value="0" min="0">
    </div>

    <div class="section-title">ניכויי רשות</div>
    <div class="form-group">
        <label for="deduction-other">שונות / ניכויים אחרים (₪):</label>
        <input type="number" id="deduction-other" value="0" min="0">
    </div>

    <button class="calc-btn" onclick="calculateSalary()">חשב שכר נטו</button>

    <div class="result-box" id="result-box">
        <h3>תלוש שכר (סיכום)</h3>
        
        <div class="result-item"><span>שכר בסיס (עבודה בפועל):</span> <span id="res-base" class="addition">0 ₪</span></div>
        <div class="result-item"><span>דמי חופשה:</span> <span id="res-vacation" class="addition">0 ₪</span></div>
        <div class="result-item"><span>דמי מחלה:</span> <span id="res-sick" class="addition">0 ₪</span></div>
        <div class="result-item"><span>החזר נסיעות:</span> <span id="res-travel" class="addition">0 ₪</span></div>
        
        <div class="result-item" style="font-weight:bold; background:#fff; padding:5px;"><span>סה"כ שכר ברוטו:</span> <span id="res-gross">0 ₪</span></div>
        
        <h4 style="margin-bottom: 5px; color:#555;">ניכויי חובה</h4>
        <div class="result-item deduction"><span>מס הכנסה:</span> <span id="res-tax">-0 ₪</span></div>
        <div class="result-item deduction"><span>ביטוח לאומי:</span> <span id="res-btl">-0 ₪</span></div>
        <div class="result-item deduction"><span>ביטוח בריאות:</span> <span id="res-health">-0 ₪</span></div>
        
        <h4 style="margin-bottom: 5px; color:#555;">ניכויי רשות</h4>
        <div class="result-item deduction"><span>שונות:</span> <span id="res-other">-0 ₪</span></div>
        
        <div class="result-item total-net"><span>שכר נטו לתשלום:</span> <span id="res-net">0 ₪</span></div>
    </div>
</div>

<script>
    // --- לוגיקת טאבים ---
    let activeTabMode = 'manual';

    function switchTab(tabName) {
        activeTabMode = tabName;
        document.getElementById('tab-manual').classList.remove('active');
        document.getElementById('tab-advanced').classList.remove('active');
        document.getElementById('btn-manual').classList.remove('active');
        document.getElementById('btn-advanced').classList.remove('active');

        document.getElementById('tab-' + tabName).classList.add('active');
        document.getElementById('btn-' + tabName).classList.add('active');
    }

    // --- לוגיקת חישוב מתקדם (טבלה) ---
    let autoCalculatedHours = 0;

    function addTimeRow() {
        const tbody = document.getElementById('time-body');
        const tr = document.createElement('tr');
        tr.innerHTML = `
            <td><input type="time" class="time-start" onchange="updateAdvancedHours()"></td>
            <td><input type="time" class="time-end" onchange="updateAdvancedHours()"></td>
            <td class="row-total">0.00</td>
        `;
        tbody.appendChild(tr);
    }

    function updateAdvancedHours() {
        autoCalculatedHours = 0;
        const rows = document.querySelectorAll('#time-body tr');
        
        rows.forEach(row => {
            const start = row.querySelector('.time-start').value;
            const end = row.querySelector('.time-end').value;
            let rowTotal = 0;

            if (start && end) {
                const startTime = new Date(`1970-01-01T${start}:00`);
                const endTime = new Date(`1970-01-01T${end}:00`);
                let diff = (endTime - startTime) / (1000 * 60 * 60); 
                if (diff < 0) diff += 24; 
                rowTotal = diff;
                autoCalculatedHours += rowTotal;
            }
            row.querySelector('.row-total').innerText = rowTotal.toFixed(2);
        });

        document.getElementById('calculated-hours').innerText = autoCalculatedHours.toFixed(2);
    }

    // --- לוגיקת חישוב שכר ראשית ---
    function calculateSalary() {
        const hourlyWage = parseFloat(document.getElementById('hourly-wage').value) || 0;
        
        let totalHours = 0;
        if (activeTabMode === 'manual') {
            totalHours = parseFloat(document.getElementById('total-hours').value) || 0;
        } else {
            totalHours = autoCalculatedHours;
        }

        const vacationDays = parseFloat(document.getElementById('vacation-days').value) || 0;
        const sickDays = parseFloat(document.getElementById('sick-days').value) || 0;
        const travelExpenses = parseFloat(document.getElementById('travel-expenses').value) || 0;
        const creditPoints = parseFloat(document.getElementById('credit-points').value) || 0;

        // ניכויי רשות 
        const dedOther = parseFloat(document.getElementById('deduction-other').value) || 0;

        const hoursPerDay = 8; 
        
        const baseSalary = hourlyWage * totalHours;
        const vacationPay = vacationDays * hoursPerDay * hourlyWage;
        const sickPay = sickDays * hoursPerDay * hourlyWage; 
        
        const grossSalary = baseSalary + vacationPay + sickPay + travelExpenses;

        const reducedBracketLimit = 7522;
        let btlTax = 0;
        let healthTax = 0;

        if (grossSalary <= reducedBracketLimit) {
            btlTax = grossSalary * 0.004;
            healthTax = grossSalary * 0.031;
        } else {
            btlTax = (reducedBracketLimit * 0.004) + ((grossSalary - reducedBracketLimit) * 0.0295);
            healthTax = (reducedBracketLimit * 0.031) + ((grossSalary - reducedBracketLimit) * 0.05);
        }

        let incomeTax = 0;
        if (grossSalary > 0) incomeTax += Math.min(grossSalary, 7010) * 0.10;
        if (grossSalary > 7010) incomeTax += Math.min(grossSalary - 7010, 3050) * 0.14;
        if (grossSalary > 10060) incomeTax += Math.min(grossSalary - 10060, 6090) * 0.20;
        if (grossSalary > 16150) incomeTax += (grossSalary - 16150) * 0.31; 

        const creditPointValue = 242;
        const taxDiscount = creditPoints * creditPointValue;
        incomeTax = Math.max(0, incomeTax - taxDiscount); 

        // הפחתת כל ניכויי החובה וניכויי הרשות מסך הנטו
        const netSalary = grossSalary - incomeTax - btlTax - healthTax - dedOther;

        // עדכון תצוגה
        document.getElementById('res-base').innerText = baseSalary.toFixed(2) + ' ₪';
        document.getElementById('res-vacation').innerText = vacationPay.toFixed(2) + ' ₪';
        document.getElementById('res-sick').innerText = sickPay.toFixed(2) + ' ₪';
        document.getElementById('res-travel').innerText = travelExpenses.toFixed(2) + ' ₪';
        document.getElementById('res-gross').innerText = grossSalary.toFixed(2) + ' ₪';
        
        document.getElementById('res-tax').innerText = '-' + incomeTax.toFixed(2) + ' ₪';
        document.getElementById('res-btl').innerText = '-' + btlTax.toFixed(2) + ' ₪';
        document.getElementById('res-health').innerText = '-' + healthTax.toFixed(2) + ' ₪';
        
        document.getElementById('res-other').innerText = '-' + dedOther.toFixed(2) + ' ₪';
        
        document.getElementById('res-net').innerText = netSalary.toFixed(2) + ' ₪';

        document.getElementById('result-box').style.display = 'block';
    }

    window.onload = function() {
        addTimeRow();
    };
</script>

</body>
</html>
