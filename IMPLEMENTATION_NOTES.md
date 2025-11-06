# Implementation Notes

Implement the following feature phase:

Phase: Phase 6: Advanced Features & Polish
Description: Add advanced calculator features like backspace, memory functions, and enhanced error handling

Technical Details:
Implement backspace/delete functionality, add memory operations (MC, MR, M+, M-), implement calculation history, and add advanced error handling with user-friendly error messages. Include input validation and edge case management for robust operation.

Files to focus on: {
  "script.js": "Complete feature set with memory functions, backspace, history, and comprehensive error handling",
  "index.html": "Additional buttons for memory and advanced functions",
  "styles.css": "Styling for new features and error states"
}

Generate working, production-ready code for this phase. Make sure the implementation is complete and functional.

## Claude Response

FILES_START
FILE: index.html
CONTENT_START
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Calculator</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="calculator-container">
        <div class="calculator">
            <!-- Display Section -->
            <div class="display-section">
                <div class="memory-indicator" id="memoryIndicator">M</div>
                <div class="display" id="display">0</div>
                <div class="expression" id="expression"></div>
            </div>
            
            <!-- History Section -->
            <div class="history-section" id="historySection">
                <div class="history-header">
                    <span>History</span>
                    <button class="history-clear" id="historyClear">Clear</button>
                </div>
                <div class="history-list" id="historyList"></div>
            </div>
            
            <!-- Error Message -->
            <div class="error-message" id="errorMessage"></div>
            
            <!-- Memory Functions Row -->
            <div class="button-row memory-row">
                <button class="btn memory-btn" id="memoryClear">MC</button>
                <button class="btn memory-btn" id="memoryRecall">MR</button>
                <button class="btn memory-btn" id="memoryAdd">M+</button>
                <button class="btn memory-btn" id="memorySubtract">M-</button>
                <button class="btn memory-btn" id="memoryStore">MS</button>
            </div>
            
            <!-- Function Row -->
            <div class="button-row">
                <button class="btn function-btn" id="clear">C</button>
                <button class="btn function-btn" id="clearEntry">CE</button>
                <button class="btn function-btn" id="backspace">⌫</button>
                <button class="btn operator-btn" id="divide">÷</button>
            </div>
            
            <!-- Number Rows -->
            <div class="button-row">
                <button class="btn number-btn" id="seven">7</button>
                <button class="btn number-btn" id="eight">8</button>
                <button class="btn number-btn" id="nine">9</button>
                <button class="btn operator-btn" id="multiply">×</button>
            </div>
            
            <div class="button-row">
                <button class="btn number-btn" id="four">4</button>
                <button class="btn number-btn" id="five">5</button>
                <button class="btn number-btn" id="six">6</button>
                <button class="btn operator-btn" id="subtract">-</button>
            </div>
            
            <div class="button-row">
                <button class="btn number-btn" id="one">1</button>
                <button class="btn number-btn" id="two">2</button>
                <button class="btn number-btn" id="three">3</button>
                <button class="btn operator-btn" id="add">+</button>
            </div>
            
            <div class="button-row">
                <button class="btn function-btn" id="toggleSign">±</button>
                <button class="btn number-btn" id="zero">0</button>
                <button class="btn function-btn" id="decimal">.</button>
                <button class="btn equals-btn" id="equals">=</button>
            </div>
            
            <!-- Advanced Functions Row -->
            <div class="button-row advanced-row">
                <button class="btn advanced-btn" id="sqrt">√</button>
                <button class="btn advanced-btn" id="square">x²</button>
                <button class="btn advanced-btn" id="percent">%</button>
                <button class="btn advanced-btn" id="reciprocal">1/x</button>
            </div>
            
            <!-- History Toggle -->
            <button class="history-toggle" id="historyToggle">History</button>
        </div>
    </div>
    
    <script src="script.js"></script>
</body>
</html>
CONTENT_END
FILE: script.js
CONTENT_START
class AdvancedCalculator {
    constructor() {
        this.display = document.getElementById('display');
        this.expression = document.getElementById('expression');
        this.errorMessage = document.getElementById('errorMessage');
        this.memoryIndicator = document.getElementById('memoryIndicator');
        this.historySection = document.getElementById('historySection');
        this.historyList = document.getElementById('historyList');
        this.historyToggle = document.getElementById('historyToggle');
        
        this.currentValue = '0';
        this.previousValue = '';
        this.operator = '';
        this.waitingForOperand = false;
        this.shouldResetDisplay = false;
        this.memory = 0;
        this.history = [];
        this.isHistoryVisible = false;
        
        this.initializeEventListeners();
        this.loadHistory();
        this.updateMemoryIndicator();
    }
    
    initializeEventListeners() {
        // Number buttons
        document.querySelectorAll('.number-btn').forEach(button => {
            button.addEventListener('click', () => this.inputNumber(button.textContent));
        });
        
        // Operator buttons
        document.getElementById('add').addEventListener('click', () => this.inputOperator('+'));
        document.getElementById('subtract').addEventListener('click', () => this.inputOperator('-'));
        document.getElementById('multiply').addEventListener('click', () => this.inputOperator('×'));
        document.getElementById('divide').addEventListener('click', () => this.inputOperator('÷'));
        
        // Function buttons
        document.getElementById('equals').addEventListener('click', () => this.calculate());
        document.getElementById('clear').addEventListener('click', () => this.clear());
        document.getElementById('clearEntry').addEventListener('click', () => this.clearEntry());
        document.getElementById('backspace').addEventListener('click', () => this.backspace());
        document.getElementById('decimal').addEventListener('click', () => this.inputDecimal());
        document.getElementById('toggleSign').addEventListener('click', () => this.toggleSign());
        
        // Advanced functions
        document.getElementById('sqrt').addEventListener('click', () => this.sqrt());
        document.getElementById('square').addEventListener('click', () => this.square());
        document.getElementById('percent').addEventListener('click', () => this.percent());
        document.getElementById('reciprocal').addEventListener('click', () => this.reciprocal());
        
        // Memory functions
        document.getElementById('memoryClear').addEventListener('click', () => this.memoryClear());
        document.getElementById('memoryRecall').addEventListener('click', () => this.memoryRecall());
        document.getElementById('memoryAdd').addEventListener('click', () => this.memoryAdd());
        document.getElementById('memorySubtract').addEventListener('click', () => this.memorySubtract());
        document.getElementById('memoryStore').addEventListener('click', () => this.memoryStore());
        
        // History functions
        document.getElementById('historyToggle').addEventListener('click', () => this.toggleHistory());
        document.getElementById('historyClear').addEventListener('click', () => this.clearHistory());
        
        // Keyboard support
        document.addEventListener('keydown', (e) => this.handleKeyboard(e));
        
        // Click outside to close error
        document.addEventListener('click', (e) => {
            if (!e.target.closest('.error-message')) {
                this.hideError();
            }
        });
    }
    
    inputNumber(num) {
        this.hideError();
        
        if (this.waitingForOperand || this.shouldResetDisplay) {
            this.currentValue = num;
            this.waitingForOperand = false;
            this.shouldResetDisplay = false;
        } else {
            if (this.currentValue === '0') {
                this.currentValue = num;
            } else if (this.currentValue.length < 15) { // Limit input length
                this.currentValue += num;
            }
        }
        
        this.updateDisplay();
    }
    
    inputOperator(nextOperator) {
        this.hideError();
        const inputValue = parseFloat(this.currentValue);
        
        if (this.previousValue === '') {
            this.previousValue = inputValue;
        } else if (this.operator) {
            const currentValue = this.previousValue || 0;
            const newValue = this.performCalculation(currentValue, inputValue, this.operator);
            
            if (newValue === null) return; // Error occurred
            
            this.currentValue = String(newValue);
            this.previousValue = newValue;
        }
        
        this.waitingForOperand = true;
        this.operator = nextOperator;
        this.updateExpression();
        this.updateDisplay();
    }
    
    calculate() {
        this.hideError();
        const inputValue = parseFloat(this.currentValue);
        
        if (this.previousValue !== '' && this.operator) {
            const currentValue = this.previousValue || 0;
            const result = this.performCalculation(currentValue, inputValue, this.operator);
            
            if (result === null) return; // Error occurred
            
            const calculation = `${this.formatNumber(currentValue)} ${this.operator} ${this.formatNumber(inputValue)} = ${this.formatNumber(result)}`;
            this.addToHistory(calculation);
            
            this.currentValue = String(result);
            this.previousValue = '';
            this.operator = '';
            this.waitingForOperand = true;
            this.shouldResetDisplay = true;
        }
        
        this.updateDisplay();
        this.updateExpression();
    }
    
    performCalculation(firstOperand, secondOperand, operator) {
        try {
            let result;
            
            // Validate inputs
            if (!this.isValidNumber(firstOperand) || !this.isValidNumber(secondOperand)) {
                this.showError('Invalid input');
                return null;
            }
            
            switch (operator) {
                case '+':
                    result = firstOperand + secondOperand;
                    break;
                case '-':
                    result = firstOperand - secondOperand;
                    break;
                case '×':
                    result = firstOperand * secondOperand;
                    break;
                case '÷':
                    if (secondOperand === 0) {
                        this.showError('Cannot divide by zero');
                        return null;
                    }
                    result = firstOperand / secondOperand;
                    break;
                default:
                    this.showError('Invalid operator');
                    return null;
            }
            
            // Check for overflow or invalid result
            if (!this.isValidResult(result)) {
                this.showError('Result is too large or invalid');
                return null;
            }
            
            // Round to prevent floating point errors
            return Math.round(result * 1e10) / 1e10;
            
        } catch (error) {
            this.showError('Calculation error');
            return null;
        }
    }
    
    clear() {
        this.currentValue = '0';
        this.previousValue = '';
        this.operator = '';
        this.waitingForOperand = false;
        this.shouldResetDisplay = false;
        this.updateDisplay();
        this.updateExpression();
        this.hideError();
    }
    
    clearEntry() {
        this.currentValue = '0';
        this.updateDisplay();
        this.hideError();
    }
    
    backspace() {
        this.hideError();
        
        if (this.shouldResetDisplay) {
            this.clear();
            return;
        }
        
        if (this.currentValue.length > 1) {
            this.currentValue = this.currentValue.slice(0, -1);
        } else {
            this.currentValue = '0';
        }
        
        this.updateDisplay();
    }
    
    inputDecimal() {
        this.hideError();
        
        if (this.waitingForOperand || this.shouldResetDisplay) {
            this.currentValue = '0.';
            this.waitingForOperand = false;
            this.shouldResetDisplay = false;
        } else if (this.currentValue.indexOf('.') === -1) {
            this.currentValue += '.';
        }
        
        this.updateDisplay();
    }
    
    toggleSign() {
        this.hideError();
        
        if (this.currentValue !== '0') {
            if (this.currentValue.charAt(0) === '-') {
                this.currentValue = this.currentValue.slice(1);
            } else {
                this.currentValue = '-' + this.currentValue;
            }
        }
        
        this.updateDisplay();
    }
    
    // Advanced Functions
    sqrt() {
        const value = parseFloat(this.currentValue);
        
        if (value < 0) {
            this.showError('Cannot calculate square root of negative number');
            return;
        }
        
        const result = Math.sqrt(value);
        if (!this.isValidResult(result)) {
            this.showError('Invalid result');
            return;
        }
        
        const calculation = `√${this.formatNumber(value)} = ${this.formatNumber(result)}`;
        this.addToHistory(calculation);
        
        this.currentValue = String(result);
        this.shouldResetDisplay = true;
        this.updateDisplay();
    }
    
    square() {
        const value = parseFloat(this.currentValue);
        const result = value * value;
        
        if (!this.isValidResult(result)) {
            this.showError('Result is too large');
            return;
        }
        
        const calculation = `${this.formatNumber(value)}² = ${this.formatNumber(result)}`;
        this.addToHistory(calculation);
        
        this.currentValue = String(result);
        this.shouldResetDisplay = true;
        this.updateDisplay();
    }
    
    percent() {
        const value = parseFloat(this.currentValue);
        const result = value / 100;
        
        const calculation = `${this.formatNumber(value)}% = ${this.formatNumber(result)}`;
        this.addToHistory(calculation);
        
        this.currentValue = String(result);
        this.shouldResetDisplay = true;
        this.updateDisplay();
    }
    
    reciprocal() {
        const value = parseFloat(this.currentValue);
        
        if (value === 0) {
            this.showError('Cannot divide by zero');
            return;
        }
        
        const result = 1 / value;
        if (!this.isValidResult(result)) {
            this.showError('Invalid result');
            return;
        }
        
        const calculation = `1/${this.formatNumber(value)} = ${this.formatNumber(result)}`;
        this.addToHistory(calculation);
        
        this.currentValue = String(result);
        this.shouldResetDisplay = true;
        this.updateDisplay();
    }
    
    // Memory Functions
    memoryClear() {
        this.memory = 0;
        this.updateMemoryIndicator();
        this.hideError();
    }
    
    memoryRecall() {
        this.currentValue = String(this.memory);
        this.shouldResetDisplay = true;
        this.updateDisplay();
        this.hideError();
    }
    
    memoryAdd() {
        const value = parseFloat(this.currentValue);
        if (this.isValidNumber(value)) {
            this.memory += value;
            this.updateMemoryIndicator();
            this.addToHistory(`M+ ${this.formatNumber(value)}`);
        }
        this.hideError();
    }
    
    memorySubtract() {
        const value = parseFloat(this.currentValue);
        if (this.isValidNumber(value)) {