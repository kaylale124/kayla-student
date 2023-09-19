---
title: JS Calculator
comments: true
hide: true
layout: default
description: A common way to become familiar with a language is to build a calculator.  This calculator shows off button with actions.
permalink: /techtalk/home_style
courses: { compsci: {week: 4} }
type: hacks
---

<!-- 
Hack 0: Right justify result
Hack 1: Test conditions on small, big, and decimal numbers, report on findings. Fix issues.
Hack 2: Add the common math operation that is missing from calculator
Hack 3: Implement 1 number operation (ie SQRT) 
-->

<!-- 
HTML implementation of the calculator. 
-->


<!-- 
    Style and Action are aligned with HRML class definitions
    style.css contains majority of style definition (number, operation, clear, and equals)
    - The div calculator-container sets 4 elements to a row
    Background is credited to Vanta JS and is implemented at bottom of this page
-->
<style>
  .calculator-output {
    /* calulator output 
      top bar shows the results of the calculator;
      result to take up the entirety of the first row;
      span defines 4 columns and 1 row
    */
    grid-column: span 4;
    grid-row: span 1;
  
    border-radius: 10px;
    padding: 0.25em;
    font-size: 20px;
    border: 5px solid black;
  
    display: flex;
    align-items: center;
  }

  .calculator-container{
    display: grid;
    grid-template-columns: repeat(4, 1fr); /* 4 columns */
    grid-template-rows: repeat(5, 1fr); /* 5 rows*/
    grid-gap: 10px;
  }

  
</style>

<!-- Add a container for the animation -->
<div id="animation">
  <div class="calculator-container">
      <!--result-->
      <div class="calculator-output" id="output">0</div>
      <!--row 1-->
      <div class="calculator-clear" style="grid-column: 4/ span 1; grid-row: 2/span 1" ><button type="button">A/C</button></div>
      <!--row 2-->
      <div class="calculator-number" style="grid-column: 1/ span 1; grid-row: 3/span 1" id="button-1" data-value="1"><button type="button">1</button></div>
      <div class="calculator-number" style="grid-column: 2/ span 1; grid-row: 3/span 1" id="button-2" data-value="2"><button type="button">2</button></div>
      <div class="calculator-number" style="grid-column: 3/ span 1; grid-row: 3/span 1" id="button-3" data-value="3"><button type="button">3</button></div>
      <div class="calculator-operation" style="grid-column: 4/ span 1; grid-row: 3/span 1"><button type="button">+</button></div>
      <!--row 3-->
      <div class="calculator-number" style="grid-column: 1/ span 1; grid-row: 4/span 1" id="button-4" data-value="4"><button type="button">4</button></div>
      <div class="calculator-number" style="grid-column: 2/ span 1; grid-row: 4/span 1" id="button-5" data-value="5"><button type="button">5</button></div>
      <div class="calculator-number" style="grid-column: 3/ span 1; grid-row: 4/span 1" id="button-6" data-value="6"><button type="button">6</button></div>
      <div class="calculator-operation" style="grid-column: 4/ span 1; grid-row: 4/span 1"><button type="button">-</button></div>
      <!--row 4-->
      <div class="calculator-number" style="grid-column: 1/ span 1; grid-row: 5/span 1" id="button-7" data-value="7"><button type="button">7</button></div>
      <div class="calculator-number" style="grid-column: 2/ span 1; grid-row: 5/span 1" id="button-8" data-value="8"><button type="button">8</button></div>
      <div class="calculator-number" style="grid-column: 3/ span 1; grid-row: 5/span 1" id="button-9" data-value="9"><button type="button">9</button></div>
      <div class="calculator-operation" style="grid-column: 4/ span 1; grid-row: 5/span 1"><button type="button">*</button></div>
      <!--row 5-->
      <div class="calculator-number" style="grid-column: 2/ span 1; grid-row: 6/span 1" id="button-0" data-value="0"><button type="button">0</button></div>
      <div class="calculator-number" style="grid-column: 1/ span 1; grid-row: 6/span 1"><button type="button">.</button></div>
      <div class="calculator-equals" style="grid-column: 3/ span 1; grid-row: 6/span 1"><button type="button">=</button></div>
      <div class="calculator-operation" style="grid-column: 4/ span 1; grid-row: 6/span 1"><button type="button">/</button></div>
  </div>
</div>

<!-- JavaScript (JS) implementation of the calculator. -->
<script>
// initialize important variables to manage calculations
var firstNumber = null;
var operator = null;
var nextReady = true;
// build objects containing key elements
const output = document.getElementById("output");
const numbers = document.querySelectorAll(".calculator-number");
const operations = document.querySelectorAll(".calculator-operation");
const clear = document.querySelectorAll(".calculator-clear");
const equals = document.querySelectorAll(".calculator-equals");

// Number buttons listener
numbers.forEach(button => {
  button.addEventListener("click", function() {
    number(button.textContent);
  });
});

// Number action
function number (value) { // function to input numbers into the calculator
    if (value != ".") {
        if (nextReady == true) { // nextReady is used to tell the computer when the user is going to input a completely new number
            output.innerHTML = value;
            if (value != "0") { // if statement to ensure that there are no multiple leading zeroes
                nextReady = false;
            }
        } else {
            output.innerHTML = output.innerHTML + value; // concatenation is used to add the numbers to the end of the input
        }
    } else { // special case for adding a decimal; can't have two decimals
        if (output.innerHTML.indexOf(".") == -1) {
            output.innerHTML = output.innerHTML + value;
            nextReady = false;
        }
    }
}

// Operation buttons listener
operations.forEach(button => {
  button.addEventListener("click", function() {
    operation(button.textContent);
  });
});

// Operator action
function operation (choice) { // function to input operations into the calculator
    if (firstNumber == null) { // once the operation is chosen, the displayed number is stored into the variable firstNumber
        firstNumber = parseInt(output.innerHTML);
        nextReady = true;
        operator = choice;
        return; // exits function
    }
    // occurs if there is already a number stored in the calculator
    firstNumber = calculate(firstNumber, parseFloat(output.innerHTML)); 
    operator = choice;
    output.innerHTML = firstNumber.toString();
    nextReady = true;
}


// Keyboard input listener
document.addEventListener("keydown", function(event) {
  const keyValue = event.key;
  if (/[0-9]/.test(keyValue)) {
    number(keyValue); // Handle number keys 0-9
  } else if (keyValue === "+") {
    operation("+");
  } else if (keyValue === "-") {
    operation("-");
  } else if (keyValue === "*") {
    operation("*");
  } else if (keyValue === "/") {
    operation("/");
  } else if (keyValue === "=" || keyValue === "Enter") {
    equal();
  } else if (keyValue === "Escape") {
    clearCalc();
  } else if (keyValue === "Backspace"){

  }
});


// Calculator
function calculate (first, second) { // function to calculate the result of the equation
    let result = 0;
    switch (operator) {
        case "+":
            result = first + second;
            break;
        case "-":
            result = first - second;
            break;
        case "*":
            result = first * second;
            break;
        case "/":
            result = first / second;
            break;
        default: 
            break;
    }
    return result;
}

// Equals button listener
equals.forEach(button => {
  button.addEventListener("click", function() {
    equal();
  });
});

// Equal action
function equal () { // function used when the equals button is clicked; calculates equation and displays it
    firstNumber = calculate(firstNumber, parseFloat(output.innerHTML));
    output.innerHTML = firstNumber.toString();
    nextReady = true;
}

// Clear button listener
clear.forEach(button => {
  button.addEventListener("click", function() {
    clearCalc();
  });
});

// A/C action
function clearCalc () { // clears calculator
    firstNumber = null;
    output.innerHTML = "0";
    nextReady = true;
}
</script>

<!-- 
Vanta animations just for fun, load JS onto the page
-->
<script src="/teacher/assets/js/three.r119.min.js"></script>
<script src="/teacher/assets/js/vanta.halo.min.js"></script>
<script src="/teacher/assets/js/vanta.birds.min.js"></script>
<script src="/teacher/assets/js/vanta.net.min.js"></script>
<script src="/teacher/assets/js/vanta.rings.min.js"></script>

<script>
// setup vanta scripts as functions
var vantaInstances = {
  halo: VANTA.HALO,
  birds: VANTA.BIRDS,
  net: VANTA.NET,
  rings: VANTA.RINGS
};

// obtain a random vanta function
var vantaInstance = vantaInstances[Object.keys(vantaInstances)[Math.floor(Math.random() * Object.keys(vantaInstances).length)]];

// run the animation
vantaInstance({
  el: "#animation",
  mouseControls: true,
  touchControls: true,
  gyroControls: false
});
</script>


