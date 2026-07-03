# Calculator-MG
CALCULATOR Mg
<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<title>Tape Calculator</title>
<style>
  :root{
    --body-green: #26362f;
    --body-green-dark: #1b2822;
    --panel: #33463d;
    --paper: #f3ecd9;
    --paper-line: #d8cdae;
    --ink: #2a2622;
    --amber: #ffb400;
    --amber-dim: #7a5a12;
    --key: #3d5148;
    --key-dark: #23322b;
    --key-op: #b5502c;
    --key-op-dark: #7c3419;
    --key-eq: #4f7a5a;
    --key-eq-dark: #33513c;
  }

  *{box-sizing:border-box;}

  html,body{
    height:100%;
    margin:0;
    background:radial-gradient(ellipse at 50% 0%, #3a4a41 0%, #1a2420 70%);
    display:flex;
    align-items:center;
    justify-content:center;
    font-family: 'Courier New', 'Share Tech Mono', monospace;
    padding: 24px;
  }

  .machine{
    width: 340px;
    background: linear-gradient(160deg, var(--body-green) 0%, var(--body-green-dark) 100%);
    border-radius: 22px;
    padding: 18px 18px 22px;
    box-shadow:
      0 30px 60px -20px rgba(0,0,0,0.6),
      inset 0 1px 0 rgba(255,255,255,0.06),
      inset 0 -2px 0 rgba(0,0,0,0.3);
    position: relative;
  }

  .brand{
    text-align:center;
    color: #9db3a5;
    letter-spacing: 4px;
    font-size: 11px;
    margin-bottom: 10px;
    text-transform: uppercase;
    opacity: 0.7;
  }

  /* --- Paper tape --- */
  .tape-window{
    background: #10160f;
    border-radius: 10px;
    padding: 10px 12px 0;
    height: 118px;
    overflow: hidden;
    position: relative;
    box-shadow: inset 0 8px 14px -6px rgba(0,0,0,0.7), inset 0 -2px 4px rgba(0,0,0,0.5);
  }

  .tape-window::before{
    content:"";
    position:absolute;
    top:0; left:0; right:0; height:22px;
    background: linear-gradient(#10160f 20%, transparent);
    z-index: 2;
    pointer-events:none;
  }

  .tape-scroll{
    display:flex;
    flex-direction: column-reverse;
    justify-content: flex-start;
    height:100%;
    overflow:hidden;
  }

  .tape{
    background: var(--paper);
    color: var(--ink);
    padding: 8px 10px 14px;
    font-size: 13px;
    line-height: 1.55;
    box-shadow: 0 2px 6px rgba(0,0,0,0.35);
    clip-path: polygon(
      0% 0%, 100% 0%, 100% 100%,
      92% 94%, 84% 100%, 76% 94%, 68% 100%, 60% 94%, 52% 100%,
      44% 94%, 36% 100%, 28% 94%, 20% 100%, 12% 94%, 4% 100%, 0% 94%
    );
  }

  .tape-entry{
    display:flex;
    justify-content: space-between;
    gap: 10px;
    white-space: nowrap;
  }
  .tape-entry .op{ color:#8a7d5f; }
  .tape-entry .res{ font-weight:bold; }
  .tape-empty{ color:#a89b76; font-style:italic; font-size:12px; }

  /* --- Display --- */
  .display-panel{
    margin-top: 12px;
    background: #10160f;
    border-radius: 10px;
    padding: 14px 16px;
    box-shadow: inset 0 3px 8px rgba(0,0,0,0.6);
  }

  .display{
    text-align: right;
    font-size: 40px;
    color: var(--amber);
    text-shadow: 0 0 8px rgba(255,180,0,0.65), 0 0 18px rgba(255,180,0,0.25);
    font-variant-numeric: tabular-nums;
    letter-spacing: 1px;
    min-height: 48px;
    overflow-x: auto;
    white-space: nowrap;
    scrollbar-width: none;
  }
  .display::-webkit-scrollbar{ display:none; }

  .pending-op{
    text-align:right;
    color: var(--amber-dim);
    font-size: 12px;
    height: 14px;
    letter-spacing: 2px;
  }

  /* --- Keypad --- */
  .keypad{
    margin-top: 16px;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 9px;
  }

  button{
    border: none;
    font-family: inherit;
    font-size: 17px;
    height: 52px;
    border-radius: 10px;
    color: #f3f1e8;
    background: linear-gradient(180deg, var(--key) 0%, var(--key-dark) 100%);
    box-shadow:
      0 3px 0 var(--key-dark),
      0 5px 8px rgba(0,0,0,0.4);
    cursor: pointer;
    transition: transform 0.05s ease, box-shadow 0.05s ease;
    -webkit-tap-highlight-color: transparent;
  }
  button:active, button.pressed{
    transform: translateY(3px);
    box-shadow: 0 0 0 var(--key-dark), inset 0 2px 4px rgba(0,0,0,0.4);
  }

  button.op{
    background: linear-gradient(180deg, var(--key-op) 0%, var(--key-op-dark) 100%);
    box-shadow: 0 3px 0 var(--key-op-dark), 0 5px 8px rgba(0,0,0,0.4);
  }
  button.op:active, button.op.pressed{
    box-shadow: 0 0 0 var(--key-op-dark), inset 0 2px 4px rgba(0,0,0,0.4);
  }

  button.eq{
    background: linear-gradient(180deg, var(--key-eq) 0%, var(--key-eq-dark) 100%);
    box-shadow: 0 3px 0 var(--key-eq-dark), 0 5px 8px rgba(0,0,0,0.4);
    grid-column: span 2;
  }
  button.eq:active, button.eq.pressed{
    box-shadow: 0 0 0 var(--key-eq-dark), inset 0 2px 4px rgba(0,0,0,0.4);
  }

  button.zero{ grid-column: span 2; }

  .foot{
    text-align:center;
    color:#6f8578;
    font-size: 10px;
    letter-spacing: 3px;
    margin-top: 14px;
    text-transform: uppercase;
    opacity:0.6;
  }

  @media (prefers-reduced-motion: reduce){
    button{ transition: none; }
  }
</style>
</head>
<body>

<div class="machine">
  <div class="brand">Model 7 &middot; Adding Machine</div>

  <div class="tape-window">
    <div class="tape-scroll" id="tapeScroll">
      <div class="tape" id="tape">
        <div class="tape-empty">— tape khaali hai, calculation shuru karein —</div>
      </div>
    </div>
  </div>

  <div class="display-panel">
    <div class="pending-op" id="pendingOp">&nbsp;</div>
    <div class="display" id="display">0</div>
  </div>

  <div class="keypad" id="keypad">
    <button data-action="clear">C</button>
    <button data-action="backspace">⌫</button>
    <button data-action="percent">%</button>
    <button class="op" data-action="op" data-op="÷">÷</button>

    <button data-num="7">7</button>
    <button data-num="8">8</button>
    <button data-num="9">9</button>
    <button class="op" data-action="op" data-op="×">×</button>

    <button data-num="4">4</button>
    <button data-num="5">5</button>
    <button data-num="6">6</button>
    <button class="op" data-action="op" data-op="−">−</button>

    <button data-num="1">1</button>
    <button data-num="2">2</button>
    <button data-num="3">3</button>
    <button class="op" data-action="op" data-op="+">+</button>

    <button class="zero" data-num="0">0</button>
    <button data-action="decimal">.</button>
    <button class="eq" data-action="equals">=</button>
  </div>

  <div class="foot">Paper Tape History</div>
</div>

<script>
(function(){
  const displayEl = document.getElementById('display');
  const pendingOpEl = document.getElementById('pendingOp');
  const tapeEl = document.getElementById('tape');
  const keypad = document.getElementById('keypad');

  let current = "0";
  let previous = null;
  let operator = null;
  let waitingForNewValue = false;
  let tapeHasEntries = false;

  function formatNumber(numStr){
    if (numStr === "Error") return numStr;
    const n = parseFloat(numStr);
    if (Number.isNaN(n)) return "0";
    // trim floating point noise
    const rounded = Math.round((n + Number.EPSILON) * 1e10) / 1e10;
    return rounded.toString();
  }

  function updateDisplay(){
    displayEl.textContent = current;
    pendingOpEl.textContent = operator ? (formatNumber(previous) + " " + operator) : "\u00A0";
  }

  function inputDigit(d){
    if (waitingForNewValue || current === "0"){
      current = d;
      waitingForNewValue = false;
    } else {
      if (current.replace('-','').replace('.','').length >= 12) return;
      current += d;
    }
    updateDisplay();
  }

  function inputDecimal(){
    if (waitingForNewValue){
      current = "0.";
      waitingForNewValue = false;
      updateDisplay();
      return;
    }
    if (!current.includes(".")) current += ".";
    updateDisplay();
  }

  function clearAll(){
    current = "0";
    previous = null;
    operator = null;
    waitingForNewValue = false;
    updateDisplay();
  }

  function backspace(){
    if (waitingForNewValue) return;
    current = current.length > 1 ? current.slice(0, -1) : "0";
    updateDisplay();
  }

  function percent(){
    current = formatNumber((parseFloat(current) / 100).toString());
    updateDisplay();
  }

  function compute(a, b, op){
    a = parseFloat(a); b = parseFloat(b);
    switch(op){
      case "+": return a + b;
      case "−": return a - b;
      case "×": return a * b;
      case "÷": return b === 0 ? NaN : a / b;
      default: return b;
    }
  }

  function chooseOperator(op){
    if (operator && !waitingForNewValue){
      const result = compute(previous, current, operator);
      current = Number.isNaN(result) ? "Error" : formatNumber(result.toString());
    }
    previous = current;
    operator = op;
    waitingForNewValue = true;
    updateDisplay();
  }

  function printToTape(a, op, b, result){
    if (!tapeHasEntries){
      tapeEl.innerHTML = "";
      tapeHasEntries = true;
    }
    const entry = document.createElement('div');
    entry.className = 'tape-entry';
    entry.innerHTML =
      '<span class="op">' + formatNumber(a) + ' ' + op + ' ' + formatNumber(b) + ' =</span>' +
      '<span class="res">' + result + '</span>';
    tapeEl.appendChild(entry);
    tapeEl.scrollTop = tapeEl.scrollHeight;
  }

  function equals(){
    if (operator === null || previous === null) return;
    if (current === "Error"){ clearAll(); return; }
    const result = compute(previous, current, operator);
    const resultStr = Number.isNaN(result) ? "Error" : formatNumber(result.toString());
    printToTape(previous, operator, current, resultStr);
    current = resultStr;
    operator = null;
    previous = null;
    waitingForNewValue = true;
    updateDisplay();
  }

  keypad.addEventListener('click', (e) => {
    const btn = e.target.closest('button');
    if (!btn) return;
    btn.classList.add('pressed');
    setTimeout(() => btn.classList.remove('pressed'), 90);

    if (btn.dataset.num !== undefined) inputDigit(btn.dataset.num);
    else if (btn.dataset.action === 'decimal') inputDecimal();
    else if (btn.dataset.action === 'clear') clearAll();
    else if (btn.dataset.action === 'backspace') backspace();
    else if (btn.dataset.action === 'percent') percent();
    else if (btn.dataset.action === 'op') chooseOperator(btn.dataset.op);
    else if (btn.dataset.action === 'equals') equals();
  });

  window.addEventListener('keydown', (e) => {
    if (e.key >= '0' && e.key <= '9') inputDigit(e.key);
    else if (e.key === '.') inputDecimal();
    else if (e.key === '+') chooseOperator('+');
    else if (e.key === '-') chooseOperator('−');
    else if (e.key === '*') chooseOperator('×');
    else if (e.key === '/') { e.preventDefault(); chooseOperator('÷'); }
    else if (e.key === 'Enter' || e.key === '=') equals();
    else if (e.key === 'Backspace') backspace();
    else if (e.key === 'Escape') clearAll();
    else if (e.key === '%') percent();
  });

  updateDisplay();
})();
</script>

</body>
</html>
