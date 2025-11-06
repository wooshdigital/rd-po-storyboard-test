# Implementation Notes

Implement the following feature phase:

Phase: Phase 3: Core Calculator Logic
Description: Implement basic arithmetic operations and calculation engine

Technical Details:
Build the calculator's core functionality with proper state management for current number, previous number, and selected operation. Implement safe arithmetic operations with error handling for division by zero. Create functions for number input, operator selection, and calculation execution. Handle decimal point logic and number formatting.

Files to focus on: {
  "script.js": "Complete calculator logic with state management, arithmetic operations, input handling, and calculation methods",
  "styles.css": "Minor updates for active states and calculation feedback"
}

Generate working, production-ready code for this phase. Make sure the implementation is complete and functional.

## Claude Response

FILES_START
FILE: script.js
CONTENT_START
class Calculator {
    constructor() {
        this.currentNumber = '0';
        this.previousNumber = '';
        this.operation = null;
        this.waitingForNewNumber = false;
        this.hasDecimal = false;
        
        this.display = document.getElementById('display');
        this.initializeEventListeners();
        this.updateDisplay();
    }

    initializeEventListeners() {
        // Number buttons
        document.querySelectorAll('.number').forEach(button => {
            button.addEventListener('click', () => {
                this.inputNumber(button.textContent);
            });
        });

        // Operator buttons
        document.querySelectorAll('.operator').forEach(button => {
            button.addEventListener('click', () => {
                this.inputOperator(button.textContent);
            });
        });

        // Decimal button
        document.querySelector('.decimal').addEventListener('click', () => {
            this.inputDecimal();
        });

        // Equals button
        document.querySelector('.equals').addEventListener('click', () => {
            this.calculate();
        });

        // Clear button
        document.querySelector('.clear').addEventListener('click', () => {
            this.clear();
        });

        // Delete button (if exists)
        const deleteButton = document.querySelector('.delete');
        if (deleteButton) {
            deleteButton.addEventListener('click', () => {
                this.delete();
            });
        }

        // Keyboard support
        document.addEventListener('keydown', (e) => {
            this.handleKeyboard(e);
        });
    }

    inputNumber(num) {
        if (this.waitingForNewNumber) {
            this.currentNumber = num;
            this.waitingForNewNumber = false;
            this.hasDecimal = false;
        } else {
            this.currentNumber = this.currentNumber === '0' ? num : this.currentNumber + num;
        }
        this.updateDisplay();
    }

    inputOperator(op) {
        // If we have a previous number and current operation, calculate first
        if (this.previousNumber !== '' && this.operation && !this.waitingForNewNumber) {
            this.calculate();
        }

        this.previousNumber = this.currentNumber;
        this.operation = op;
        this.waitingForNewNumber = true;
        this.hasDecimal = false;

        // Add visual feedback for active operation
        this.highlightOperator(op);
    }

    inputDecimal() {
        if (this.waitingForNewNumber) {
            this.currentNumber = '0.';
            this.waitingForNewNumber = false;
            this.hasDecimal = true;
        } else if (!this.hasDecimal) {
            this.currentNumber += '.';
            this.hasDecimal = true;
        }
        this.updateDisplay();
    }

    calculate() {
        if (this.operation && this.previousNumber !== '' && !this.waitingForNewNumber) {
            const prev = parseFloat(this.previousNumber);
            const current = parseFloat(this.currentNumber);
            let result;

            try {
                switch (this.operation) {
                    case '+':
                        result = prev + current;
                        break;
                    case '−':
                    case '-':
                        result = prev - current;
                        break;
                    case '×':
                    case '*':
                        result = prev * current;
                        break;
                    case '÷':
                    case '/':
                        if (current === 0) {
                            throw new Error('Cannot divide by zero');
                        }
                        result = prev / current;
                        break;
                    default:
                        return;
                }

                // Handle floating point precision issues
                result = this.roundResult(result);
                
                // Check for overflow
                if (!isFinite(result)) {
                    throw new Error('Result is too large');
                }

                this.currentNumber = result.toString();
                this.previousNumber = '';
                this.operation = null;
                this.waitingForNewNumber = true;
                this.hasDecimal = this.currentNumber.includes('.');
                
            } catch (error) {
                this.displayError(error.message);
                return;
            }
        }

        this.clearOperatorHighlight();
        this.updateDisplay();
    }

    clear() {
        this.currentNumber = '0';
        this.previousNumber = '';
        this.operation = null;
        this.waitingForNewNumber = false;
        this.hasDecimal = false;
        this.clearOperatorHighlight();
        this.updateDisplay();
    }

    delete() {
        if (this.waitingForNewNumber || this.currentNumber === '0') {
            return;
        }

        if (this.currentNumber.length > 1) {
            const lastChar = this.currentNumber.slice(-1);
            if (lastChar === '.') {
                this.hasDecimal = false;
            }
            this.currentNumber = this.currentNumber.slice(0, -1);
        } else {
            this.currentNumber = '0';
        }
        
        this.updateDisplay();
    }

    roundResult(num) {
        // Round to avoid floating point precision issues
        const precision = 10;
        return Math.round((num + Number.EPSILON) * Math.pow(10, precision)) / Math.pow(10, precision);
    }

    formatDisplay(num) {
        const numStr = num.toString();
        
        // Handle very large or very small numbers with scientific notation
        if (Math.abs(parseFloat(num)) > 999999999999 || (Math.abs(parseFloat(num)) < 0.000001 && parseFloat(num) !== 0)) {
            return parseFloat(num).toExponential(6);
        }

        // Limit decimal places for display
        if (numStr.includes('.')) {
            const parts = numStr.split('.');
            if (parts[1].length > 8) {
                return parseFloat(num).toFixed(8).replace(/\.?0+$/, '');
            }
        }

        return numStr;
    }

    updateDisplay() {
        this.display.textContent = this.formatDisplay(this.currentNumber);
        
        // Adjust font size for long numbers
        if (this.display.textContent.length > 12) {
            this.display.style.fontSize = '1.8rem';
        } else if (this.display.textContent.length > 10) {
            this.display.style.fontSize = '2.2rem';
        } else {
            this.display.style.fontSize = '2.5rem';
        }
    }

    displayError(message) {
        this.display.textContent = 'Error';
        this.display.style.color = '#ff6b6b';
        
        setTimeout(() => {
            this.clear();
            this.display.style.color = '';
        }, 1500);
    }

    highlightOperator(op) {
        this.clearOperatorHighlight();
        const operators = document.querySelectorAll('.operator');
        operators.forEach(button => {
            if (button.textContent === op) {
                button.classList.add('active');
            }
        });
    }

    clearOperatorHighlight() {
        document.querySelectorAll('.operator').forEach(button => {
            button.classList.remove('active');
        });
    }

    handleKeyboard(e) {
        e.preventDefault();
        
        if (e.key >= '0' && e.key <= '9') {
            this.inputNumber(e.key);
        } else if (e.key === '.') {
            this.inputDecimal();
        } else if (e.key === '+') {
            this.inputOperator('+');
        } else if (e.key === '-') {
            this.inputOperator('−');
        } else if (e.key === '*') {
            this.inputOperator('×');
        } else if (e.key === '/') {
            this.inputOperator('÷');
        } else if (e.key === 'Enter' || e.key === '=') {
            this.calculate();
        } else if (e.key === 'Escape' || e.key === 'c' || e.key === 'C') {
            this.clear();
        } else if (e.key === 'Backspace') {
            this.delete();
        }
    }
}

// Initialize calculator when DOM is loaded
document.addEventListener('DOMContentLoaded', () => {
    new Calculator();
});
CONTENT_END
FILE: styles.css
CONTENT_START
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
}

.calculator {
    background: #2c3e50;
    border-radius: 20px;
    padding: 25px;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
    max-width: 350px;
    width: 100%;
}

.display {
    background: #1a252f;
    border-radius: 10px;
    padding: 20px;
    margin-bottom: 20px;
    text-align: right;
    font-size: 2.5rem;
    color: #ecf0f1;
    font-weight: 300;
    min-height: 80px;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    overflow: hidden;
    word-wrap: break-word;
    transition: all 0.2s ease;
}

.buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 15px;
}

button {
    height: 70px;
    border: none;
    border-radius: 15px;
    font-size: 1.4rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease;
    outline: none;
    position: relative;
    overflow: hidden;
}

button:before {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 0;
    height: 0;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.3);
    transition: width 0.6s, height 0.6s, top 0.6s, left 0.6s;
    transform: translate(-50%, -50%);
    z-index: 0;
}

button:active:before {
    width: 300px;
    height: 300px;
    top: 50%;
    left: 50%;
}

button span {
    position: relative;
    z-index: 1;
}

/* Number buttons */
.number {
    background: #34495e;
    color: #ecf0f1;
}

.number:hover {
    background: #4a6273;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.number:active {
    transform: translateY(0);
    background: #2c3e50;
}

/* Operator buttons */
.operator {
    background: #e67e22;
    color: white;
}

.operator:hover {
    background: #f39c12;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.operator:active,
.operator.active {
    transform: translateY(0);
    background: #d35400;
    box-shadow: inset 0 3px 10px rgba(0, 0, 0, 0.3);
}

.operator.active {
    background: #c0392b;
    transform: scale(0.95);
}

/* Equals button */
.equals {
    background: #27ae60;
    color: white;
    grid-column: span 2;
}

.equals:hover {
    background: #2ecc71;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.equals:active {
    transform: translateY(0);
    background: #229954;
}

/* Clear button */
.clear {
    background: #e74c3c;
    color: white;
    grid-column: span 2;
}

.clear:hover {
    background: #c0392b;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.clear:active {
    transform: translateY(0);
    background: #a93226;
}

/* Decimal button */
.decimal {
    background: #34495e;
    color: #ecf0f1;
}

.decimal:hover {
    background: #4a6273;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.decimal:active {
    transform: translateY(0);
    background: #2c3e50;
}

/* Zero button spans two columns */
.zero {
    grid-column: span 2;
}

/* Responsive design */
@media (max-width: 480px) {
    .calculator {
        padding: 20px;
        max-width: 300px;
    }
    
    .display {
        font-size: 2rem;
        min-height: 60px;
        padding: 15px;
    }
    
    button {
        height: 60px;
        font-size: 1.2rem;
    }
    
    .buttons {
        gap: 10px;
    }
}

/* Error state styling */
.display.error {
    color: #e74c3c;
    animation: shake 0.5s ease-in-out;
}

@keyframes shake {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(-5px); }
    75% { transform: translateX(5px); }
}

/* Loading animation for calculations */
.display.calculating {
    opacity: 0.7;
}

.display.calculating:after {
    content: '';
    position: absolute;
    top: 50%;
    right: 20px;
    width: 20px;
    height: 20px;
    border: 2px solid #ecf0f1;
    border-top: 2px solid transparent;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

/* Focus styles for accessibility */
button:focus {
    outline: 2px solid #3498db;
    outline-offset: 2px;
}

/* High contrast mode support */
@media (prefers-contrast: high) {
    .calculator {
        background: #000;
        border: 2px solid #fff;
    }
    
    .display {
        background: #000;
        color: #fff;
        border: 1px solid #fff;
    }
    
    .number {
        background: #333;
        color: #fff;
        border: 1px solid #fff;
    }
    
    .operator {
        background: #666;
        color: #fff;
        border: 1px solid #fff;
    }
}

/* Reduced motion support */
@media (prefers-reduced-motion: reduce) {
    