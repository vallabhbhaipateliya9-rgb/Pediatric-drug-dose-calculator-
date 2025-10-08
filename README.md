<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pediatric Drug Dose Calculator</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background: linear-gradient(135deg, #89f7fe, #66a6ff);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  .calculator {
    background-color: white;
    padding: 25px 35px;
    border-radius: 15px;
    box-shadow: 0 8px 20px rgba(0,0,0,0.2);
    max-width: 500px;
    width: 100%;
  }
  h2 {
    text-align: center;
    color: #333;
  }
  label {
    font-weight: bold;
  }
  input, select, button {
    width: 100%;
    padding: 10px;
    margin-top: 5px;
    margin-bottom: 15px;
    border-radius: 8px;
    border: 1px solid #ccc;
    font-size: 16px;
  }
  button {
    background-color: #66a6ff;
    color: white;
    font-weight: bold;
    cursor: pointer;
    transition: 0.3s;
  }
  button:hover {
    background-color: #558de8;
  }
  .result {
    background: #f0f8ff;
    padding: 10px;
    border-radius: 8px;
    font-weight: bold;
    text-align: center;
  }
</style>
</head>
<body>

<div class="calculator">
  <h2>Pediatric Drug Dose Calculator</h2>
  
  <label for="age">Child Age:</label>
  <select id="age">
    <option value="">--Select Age--</option>
    <option value="0-1">0 - 1 year</option>
    <option value="1-5">1 - 5 years</option>
    <option value="6-12">6 - 12 years</option>
    <option value="13-18">13 - 18 years</option>
  </select>
  
  <label for="weight">Child Weight (kg):</label>
  <input type="number" id="weight" placeholder="Enter weight in kg">
  
  <label for="drug">Select Drug:</label>
  <select id="drug">
    <option value="">--Select Drug--</option>
    <!-- Antibiotics -->
    <option value="amoxicillin">Amoxicillin</option>
    <option value="ceftriaxone">Ceftriaxone</option>
    <option value="azithromycin">Azithromycin</option>
    <option value="ciprofloxacin">Ciprofloxacin</option>
    <!-- Analgesics / Antipyretics -->
    <option value="paracetamol">Paracetamol</option>
    <option value="ibuprofen">Ibuprofen</option>
    <!-- Antiemetics -->
    <option value="emset">Ondansetron (Emset)</option>
    <!-- Antivirals -->
    <option value="fluvira">Oseltamivir (Fluvira)</option>
    <!-- Others -->
    <option value="pantoprazole">Pantoprazole</option>
  </select>
  
  <label for="formulation">Select Formulation:</label>
  <select id="formulation">
    <option value="">--Select Formulation--</option>
    <option value="tablet">Tablet</option>
    <option value="syrup">Syrup</option>
    <option value="injection">Injection</option>
  </select>
  
  <button onclick="calculateDose()">Calculate Dose</button>
  
  <div class="result" id="result"></div>
</div>

<script>
  // Drug dosing database (mg/kg)
  const drugDatabase = {
    "paracetamol": {min:10, max:15, unit:"mg", maxPerDay:60},
    "ibuprofen": {min:5, max:10, unit:"mg", maxPerDay:40},
    "amoxicillin": {min:20, max:40, unit:"mg", maxPerDay:null},
    "ceftriaxone": {min:50, max:75, unit:"mg", maxPerDay:null},
    "azithromycin": {min:10, max:10, unit:"mg", maxPerDay:null},
    "ciprofloxacin": {min:10, max:20, unit:"mg", maxPerDay:null},
    "pantoprazole": {min:1, max:1, unit:"mg", maxPerDay:null},
    "emset": {min:0.15, max:0.15, unit:"mg", maxPerDay:null},
    "fluvira": {min:3, max:3, unit:"mg", maxPerDay:null}
  };

  // Formulation concentrations for syrups (mg/ml)
  const syrupConcentration = {
    "paracetamol": 125,
    "ibuprofen": 100,
    "amoxicillin": 125,
    "azithromycin": 200,
    "ciprofloxacin": 50,
    "pantoprazole": 4,
    "emset": 4,
    "fluvira": 12
  };

  function calculateDose() {
    const age = document.getElementById('age').value;
    const weight = parseFloat(document.getElementById('weight').value);
    const drug = document.getElementById('drug').value;
    const form = document.getElementById('formulation').value;
    const resultDiv = document.getElementById('result');

    if(!age || !weight || !drug || !form){
      resultDiv.innerHTML = "Please select age, weight, drug, and formulation.";
      return;
    }

    const drugInfo = drugDatabase[drug];
    if(!drugInfo){
      resultDiv.innerHTML = "Drug data not found!";
      return;
    }

    const minDose = weight * drugInfo.min;
    const maxDose = weight * drugInfo.max;

    let doseText = "";
    if(minDose === maxDose){
      doseText = `${minDose.toFixed(2)} ${drugInfo.unit} per dose`;
    } else {
      doseText = `${minDose.toFixed(2)} - ${maxDose.toFixed(2)} ${drugInfo.unit} per dose`;
    }

    // Syrup calculation
    if(form === "syrup" && syrupConcentration[drug]){
      const minMl = minDose / syrupConcentration[drug] * 5; // Convert to 5 ml
      const maxMl = maxDose / syrupConcentration[drug] * 5;
      if(minMl === maxMl){
        doseText += ` (~${minMl.toFixed(1)} ml)`;
      } else {
        doseText += ` (~${minMl.toFixed(1)} - ${maxMl.toFixed(1)} ml)`;
      }
    }

    // Injection: just mention dose in mg/kg
    if(form === "injection"){
      doseText += " (Injectable dose)";
    }

    // Tablet: mention approximate number of tablets if standard tablet strength exists
    if(form === "tablet"){
      doseText += " (Tablet dose)";
    }

    resultDiv.innerHTML = `Recommended Dose: ${doseText}`;
  }
</script>

</body>
</html>