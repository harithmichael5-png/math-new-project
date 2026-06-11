```
index.html

```
  
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>Simple Integration Calculator</title>  
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/11.8.0/math.js"></script>  
    <style>  
        body {  
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;  
            background-color: #f4f7f6;  
            color: #333;  
            display: flex;  
            justify-content: center;  
            align-items: center;  
            height: 100vh;  
            margin: 0;  
        }  
        .calculator-container {  
            background-color: #ffffff;  
            padding: 30px;  
            border-radius: 12px;  
            box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);  
            width: 100%;  
            max-width: 450px;  
        }  
        h2 {  
            text-align: center;  
            color: #2c3e50;  
            margin-top: 0;  
            margin-bottom: 20px;  
        }  
        .input-group {  
            margin-bottom: 15px;  
        }  
        label {  
            display: block;  
            margin-bottom: 6px;  
            font-weight: 600;  
            color: #555;  
        }  
        input {  
            width: 100%;  
            padding: 10px;  
            border: 1px solid #ccc;  
            border-radius: 6px;  
            box-sizing: border-box;  
            font-size: 16px;  
        }  
        input:focus {  
            border-color: #3498db;  
            outline: none;  
        }  
        .limits-group {  
            display: flex;  
            gap: 15px;  
        }  
        .limits-group .input-group {  
            flex: 1;  
        }  
        button {  
            width: 100%;  
            padding: 12px;  
            background-color: #3498db;  
            color: white;  
            border: none;  
            border-radius: 6px;  
            font-size: 16px;  
            font-weight: bold;  
            cursor: pointer;  
            transition: background-color 0.2s;  
            margin-top: 10px;  
        }  
        button:hover {  
            background-color: #2980b9;  
        }  
        .result-box {  
            margin-top: 20px;  
            padding: 15px;  
            background-color: #eef2f7;  
            border-left: 5px solid #3498db;  
            border-radius: 4px;  
            display: none;  
        }  
        .result-title {  
            font-weight: bold;  
            color: #2c3e50;  
            margin-bottom: 5px;  
        }  
        .result-value {  
            font-size: 20px;  
            color: #27ae60;  
            font-weight: bold;  
        }  
        .error {  
            color: #e74c3c;  
            background-color: #fadbd8;  
            border-left-color: #e74c3c;  
        }  
    </style>  
</head>  
<body>  
  
<div class="calculator-container">  
    <h2>Definite Integral Calculator</h2>  
      
    <div class="input-group">  
        <label for="function-input">Function f(x)</label>  
        <input type="text" id="function-input" placeholder="e.g., x^2 + 3*x - sin(x)" value="x^2">  
    </div>  
      
    <div class="limits-group">  
        <div class="input-group">  
            <label for="lower-limit">Lower Limit (a)</label>  
            <input type="number" id="lower-limit" value="0" step="any">  
        </div>  
        <div class="input-group">  
            <label for="upper-limit">Upper Limit (b)</label>  
            <input type="number" id="upper-limit" value="3" step="any">  
        </div>  
    </div>  
      
    <button onclick="calculateIntegral()">Calculate Area</button>  
      
    <div id="result-box" class="result-box">  
        <div id="result-title" class="result-title">Approximated Integral:</div>  
        <div id="result-value" class="result-value">0</div>  
    </div>  
</div>  
  
<script>  
    function calculateIntegral() {  
        const resultBox = document.getElementById('result-box');  
        const resultValue = document.getElementById('result-value');  
        const resultTitle = document.getElementById('result-title');  
          
        // Grab inputs  
        const exprString = document.getElementById('function-input').value;  
        const a = parseFloat(document.getElementById('lower-limit').value);  
        const b = parseFloat(document.getElementById('upper-limit').value);  
          
        // Validate limits  
        if (isNaN(a) || !isFinite(a) || isNaN(b) || !isFinite(b)) {  
            showError("Please enter valid numeric limits.");  
            return;  
        }  
  
        try {  
            // Compile the expression using math.js  
            const parser = math.compile(exprString);  
              
            // Helper function to evaluate f(x) safely  
            const f = (x) => parser.evaluate({ x: x });  
              
            // Test if the expression evaluates correctly at 'a'  
            if (typeof f(a) !== 'number' || isNaN(f(a))) {  
                throw new Error("Invalid function or math error.");  
            }  
              
            // Numerical Integration using Simpson's 1/3 Rule  
            const n = 1000; // Number of subintervals (must be even)  
            const h = (b - a) / n;  
              
            let sum = f(a) + f(b);  
              
            for (let i = 1; i < n; i++) {  
                const x = a + i * h;  
                if (i % 2 === 0) {  
                    sum += 2 * f(x); // Even intervals  
                } else {  
                    sum += 4 * f(x); // Odd intervals  
                }  
            }  
              
            const integralResult = (h / 3) * sum;  
              
            // Format and display the output safely  
            resultBox.classList.remove('error');  
            resultTitle.innerText = "Approximated Integral Value:";  
            // Fixed to 5 decimal places for clean UI presentation  
            resultValue.innerText = Number(integralResult.toFixed(5)).toString();   
            resultBox.style.display = 'block';  
              
        } catch (error) {  
            showError("Error evaluating function. Check your syntax (e.g., use '*' for multiplication like '3*x').");  
        }  
    }  
  
    function showError(message) {  
        const resultBox = document.getElementById('result-box');  
        const resultValue = document.getElementById('result-value');  
        const resultTitle = document.getElementById('result-title');  
          
        resultBox.classList.add('error');  
        resultTitle.innerText = "Error";  
        resultValue.innerText = message;  
        resultBox.style.display = 'block';  
    }  
</script>  
  
</body>  
</html>  
