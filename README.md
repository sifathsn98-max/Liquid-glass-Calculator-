
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Liquid Glass Calculator</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Arial, sans-serif;
}

body {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background:
      radial-gradient(circle at 20% 20%, #353d75, transparent 40%),
      radial-gradient(circle at 80% 80%, #56305f, transparent 40%),
      #080a14;
    overflow: hidden;
}

.calculator {
    width: 340px;
    padding: 22px;
    border-radius: 35px;
    background: rgba(255,255,255,0.09);
    backdrop-filter: blur(35px);
    -webkit-backdrop-filter: blur(35px);
    border: 1px solid rgba(255,255,255,0.22);
    box-shadow:
      0 20px 60px rgba(0,0,0,0.5),
      inset 0 1px 1px rgba(255,255,255,0.3);
}

.display {
    height: 145px;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    align-items: flex-end;
    padding: 15px 5px;
    color: white;
    overflow: hidden;
}

#history {
    color: rgba(255,255,255,0.5);
    font-size: 18px;
    min-height: 28px;
}

#result {
    font-size: 52px;
    font-weight: 300;
    max-width: 100%;
    overflow-x: auto;
}

.buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 11px;
}

button {
    height: 65px;
    border-radius: 50%;
    border: 1px solid rgba(255,255,255,0.18);
    background: rgba(255,255,255,0.11);
    color: white;
    font-size: 23px;
    cursor: pointer;
    backdrop-filter: blur(15px);
    -webkit-backdrop-filter: blur(15px);
    box-shadow: inset 0 1px 2px rgba(255,255,255,0.12);
    transition: 0.15s;
}

button:active {
    transform: scale(0.88);
    background: rgba(255,255,255,0.3);
}

.operator {
    background: rgba(80,150,255,0.32);
}

.special {
    background: rgba(255,255,255,0.24);
    color: #fff;
}

.equal {
    background: linear-gradient(145deg,#52a8ff,#7764ff);
    box-shadow: 0 0 20px rgba(82,168,255,0.3);
}

.zero {
    grid-column: span 2;
    border-radius: 40px;
}

button:hover {
    border-color: rgba(255,255,255,0.5);
}
</style>
</head>

<body>

<div class="calculator">

    <div class="display">
        <div id="history"></div>
        <div id="result">0</div>
    </div>

    <div class="buttons">
        <button class="special" onclick="clearAll()">AC</button>
        <button class="special" onclick="backspace()">⌫</button>
        <button class="special" onclick="percent()">%</button>
        <button class="operator" onclick="operator('/')">÷</button>

        <button onclick="number('7')">7</button>
        <button onclick="number('8')">8</button>
        <button onclick="number('9')">9</button>
        <button class="operator" onclick="operator('*')">×</button>

        <button onclick="number('4')">4</button>
        <button onclick="number('5')">5</button>
        <button onclick="number('6')">6</button>
        <button class="operator" onclick="operator('-')">−</button>

        <button onclick="number('1')">1</button>
        <button onclick="number('2')">2</button>
        <button onclick="number('3')">3</button>
        <button class="operator" onclick="operator('+')">+</button>

        <button class="zero" onclick="number('0')">0</button>
        <button onclick="decimal()">.</button>
        <button class="equal" onclick="calculate()">=</button>
    </div>

</div>

<script>
let current = '0';
let previous = null;
let op = null;
let reset = false;

const result = document.getElementById('result');
const history = document.getElementById('history');

function update() {
    result.textContent = current;
}

function number(n) {
    if (reset || current === 'Error') {
        current = n;
        reset = false;
    } else {
        current = current === '0' ? n : current + n;
    }
    update();
}

function decimal() {
    if (reset) {
        current = '0';
        reset = false;
    }
    if (!current.includes('.')) {
        current += '.';
    }
    update();
}

function operator(o) {
    if (op && !reset) calculate();

    previous = parseFloat(current);
    op = o;
    history.textContent = current + ' ' + o;
    reset = true;
}

function calculate() {
    if (op === null || previous === null) return;

    const a = previous;
    const b = parseFloat(current);
    let answer;

    switch (op) {
        case '+': answer = a + b; break;
        case '-': answer = a - b; break;
        case '*': answer = a * b; break;
        case '/': answer = b === 0 ? NaN : a / b; break;
    }

    history.textContent = a + ' ' + op + ' ' + b + ' =';

    current = Number.isFinite(answer)
        ? String(Number(answer.toPrecision(12)))
        : 'Error';

    op = null;
    previous = null;
    reset = true;
    update();
}

function clearAll() {
    current = '0';
    previous = null;
    op = null;
    reset = false;
    history.textContent = '';
    update();
}

function backspace() {
    if (reset || current === 'Error') {
        current = '0';
        reset = false;
    } else {
        current = current.length > 1
            ? current.slice(0, -1)
            : '0';
    }
    update();
}

function percent() {
    const value = parseFloat(current);
    current = String(value / 100);
    update();
}

// Keyboard support
document.addEventListener('keydown', e => {
    if (/^[0-9]$/.test(e.key)) number(e.key);
    else if (e.key === '.') decimal();
    else if (['+', '-', '*', '/'].includes(e.key)) operator(e.key);
    else if (e.key === 'Enter' || e.key === '=') calculate();
    else if (e.key === 'Backspace') backspace();
    else if (e.key === 'Escape') clearAll();
});
</script>

</body>
</html>

