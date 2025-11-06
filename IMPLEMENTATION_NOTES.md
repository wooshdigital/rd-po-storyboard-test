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
    <div class="calculator">
        <div class="display-section">
            <div class="memory-indicator" id="memoryIndicator">M</div>
            <div class="display" id="display">0</div>
            <div class="error-message" id="errorMessage"></div>
        </div>
        
        <div class="history-panel" id="historyPanel">
            <div class="history-header">
                <span>History</span>
                <button class="clear-history" id="clearHistory">Clear</button>
            </div>
            <div class="history-list" id="historyList"></div>
        </div>
        
        <div class="buttons">
            <!-- Memory and utility row -->
            <button class="btn memory" data-action="mc">MC</button>
            <button class="btn memory" data-action="mr">MR</button>
            <button class="btn memory" data-action="m-plus">M+</button>
            <button class="btn memory" data-action="m-minus">M-</button>
            <button class="btn utility" data-action="backspace">⌫</button>
            
            <!-- First row -->
            <button class="btn utility" data-action="clear">C</button>
            <button class="btn utility" data-action="clear-entry">CE</button>
            <button class="btn utility" data-action="toggle-sign">±</button>
            <button class="btn operator" data-action="divide">÷</button>
            <button class="btn utility" data-action="toggle-history">📋</button>
            
            <!-- Second row -->
            <button class="btn number" data-number="7">7</button>
            <button class="btn number" data-number="8">8</button>
            <button class="btn number" data-number="9">9</button>
            <button class="btn operator" data-action="multiply">×</button>
            <button class="btn scientific" data-action="square">x²</button>
            
            <!-- Third row -->
            <button class="btn number" data-number="4">4</button>
            <button class="btn number" data-number="5">5</button>
            <button class="btn number" data-number="6">6</button>
            <button class="btn operator" data-action="subtract">-</button>
            <button class="btn scientific" data-action="sqrt">√</button>
            
            <!-- Fourth row -->
            <button class="btn number" data-number="1">1</button>
            <button class="btn number" data-number="2">2</button>
            <button class="btn number" data-number="3">3</button>
            <button class="btn operator" data-action="add">+</button>
            <button class="btn scientific" data-action="percent">%</button>
            
            <!-- Fifth row -->
            <button class="btn number zero" data-number="0">0</button>
            <button class="btn number" data-action="decimal">.</button>
            <button class="btn equals" data-action="equals">=</button>
            <button class="btn scientific" data-action="reciprocal">1/x</button>
        </div>
    </div>
    
    <script src="script.js"></script>
</body>
</html>
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
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.calculator {
    background: #2c3e50;
    border-radius: 20px;
    padding: 25px;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
    max-width: 500px;
    width: 100%;
    position: relative;
}

.display-section {
    background: #34495e;
    border-radius: 15px;
    padding: 20px;
    margin-bottom: 20px;
    position: relative;
}

.memory-indicator {
    position: absolute;
    top: 10px;
    left: 15px;
    color: #e74c3c;
    font-size: 12px;
    font-weight: bold;
    opacity: 0;
    transition: opacity 0.3s ease;
}

.memory-indicator.active {
    opacity: 1;
}

.display {
    color: #ecf0f1;
    font-size: 2.5em;
    font-weight: 300;
    text-align: right;
    min-height: 60px;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    word-break: break-all;
    overflow: hidden;
}

.error-message {
    color: #e74c3c;
    font-size: 14px;
    text-align: center;
    margin-top: 10px;
    min-height: 20px;
    opacity: 0;
    transition: opacity 0.3s ease;
}

.error-message.show {
    opacity: 1;
}

.history-panel {
    position: absolute;
    top: 25px;
    right: 25px;
    width: 300px;
    background: #34495e;
    border-radius: 15px;
    padding: 15px;
    max-height: 400px;
    transform: translateX(320px);
    transition: transform 0.3s ease;
    z-index: 1000;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.history-panel.active {
    transform: translateX(0);
}

.history-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 15px;
    color: #ecf0f1;
    font-weight: 600;
}

.clear-history {
    background: #e74c3c;
    color: white;
    border: none;
    padding: 5px 10px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 12px;
    transition: background-color 0.3s ease;
}

.clear-history:hover {
    background: #c0392b;
}

.history-list {
    max-height: 320px;
    overflow-y: auto;
}

.history-item {
    background: #2c3e50;
    padding: 10px;
    margin-bottom: 8px;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.history-item:hover {
    background: #1a252f;
}

.history-calculation {
    color: #bdc3c7;
    font-size: 14px;
}

.history-result {
    color: #3498db;
    font-size: 16px;
    font-weight: 600;
}

.buttons {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 15px;
}

.btn {
    height: 60px;
    border: none;
    border-radius: 12px;
    font-size: 1.2em;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    position: relative;
    overflow: hidden;
}

.btn:before {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 0;
    height: 0;
    background: rgba(255, 255, 255, 0.3);
    border-radius: 50%;
    transform: translate(-50%, -50%);
    transition: width 0.6s, height 0.6s;
}

.btn:active:before {
    width: 300px;
    height: 300px;
}

.btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.btn:active {
    transform: translateY(0);
}

.number {
    background: #3498db;
    color: white;
}

.number:hover {
    background: #2980b9;
}

.operator {
    background: #e67e22;
    color: white;
}

.operator:hover {
    background: #d35400;
}

.operator.active {
    background: #f39c12;
    transform: scale(0.95);
}

.equals {
    background: #27ae60;
    color: white;
    grid-column: span 2;
}

.equals:hover {
    background: #229954;
}

.utility {
    background: #95a5a6;
    color: white;
}

.utility:hover {
    background: #7f8c8d;
}

.memory {
    background: #9b59b6;
    color: white;
    font-size: 0.9em;
}

.memory:hover {
    background: #8e44ad;
}

.memory:disabled {
    background: #6c7b7f;
    cursor: not-allowed;
}

.scientific {
    background: #e74c3c;
    color: white;
    font-size: 1em;
}

.scientific:hover {
    background: #c0392b;
}

.zero {
    grid-column: span 2;
}

/* Error state styling */
.calculator.error .display {
    color: #e74c3c;
    animation: shake 0.5s ease-in-out;
}

@keyframes shake {
    0%, 100% { transform: translateX(0); }
    10%, 30%, 50%, 70%, 90% { transform: translateX(-5px); }
    20%, 40%, 60%, 80% { transform: translateX(5px); }
}

/* Responsive design */
@media (max-width: 600px) {
    .calculator {
        padding: 15px;
        margin: 10px;
    }
    
    .display {
        font-size: 2em;
        min-height: 50px;
    }
    
    .btn {
        height: 50px;
        font-size: 1em;
    }
    
    .buttons {
        gap: 10px;
    }
    
    .history-panel {
        width: 280px;
        transform: translateX(300px);
    }
    
    .history-panel.active {
        transform: translateX(-20px);
    }
}

@media (max-width: 480px) {
    .buttons {
        grid-template-columns: repeat(4, 1fr);
    }
    
    .memory,
    .scientific {
        display: none;
    }
    
    .zero {
        grid-column: span 2;
    }
    
    .equals {
        grid-column: span 2;
    }
}

/* Accessibility improvements */
.btn:focus {
    outline: 3px solid #3498db;
    outline-offset: 2px;
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
    .calculator {
        background: #1a1a1a;
    }
    
    .display-section {
        background: #2d2d2d;
    }
    
    .display {
        color: #ffffff;
    }
}
CONTENT_END
FILE: script.js
CONTENT_START
class AdvancedCalculator {
    constructor() {
        this.display = document.getElementById('display');
        this.errorMessage = document.getElementById('errorMessage');
        this.memoryIndicator = document.getElementById('memoryIndicator');
        this.historyPanel = document.getElementById('historyPanel');
        this.historyList = document.getElementById('historyList');
        this.clearHistoryBtn = document.getElementById('clearHistory');
        
        // Calculator state
        this.currentValue = '0';
        this.previousValue = null;
        this.operator = null;
        this.waitingForNewValue = false;
        this.lastCalculation = null;
        
        // Memory and history
        this.memory = 0;
        this.history = JSON.parse(localStorage.getItem('calculatorHistory')) || [];
        this.maxHistoryItems = 50;
        
        // Error handling
        this.isError = false;
        this.errorTimeout = null;
        
        this.init();
    }
    
    init() {
        this.bindEvents();
        this.loadHistory();
        this.updateMemoryIndicator();
        this.updateDisplay();
    }
    
    bindEvents() {
        // Button events
        document.querySelectorAll('.btn').forEach(button => {
            button.addEventListener('click', this.handleButtonClick.bind(this));
        });
        
        // Keyboard events
        document.addEventListener('keydown', this.handleKeyboard.bind(this));
        
        // History events
        this.clearHistoryBtn.addEventListener('click', this.clearHistory.bind(this));
        
        // Prevent context menu on calculator
        document.querySelector('.calculator').addEventListener('contextmenu', (e) => {
            e.preventDefault();
        });
    }
    
    handleButtonClick(e) {
        const button = e.target;
        const action = button.dataset.action;
        const number = button.dataset.number;
        
        this.clearError();
        
        if (number !== undefined) {
            this.inputNumber(number);
        } else if (action) {
            this.handleAction(action);
        }
        
        // Add visual feedback
        button.style.transform = 'scale(0.95)';
        setTimeout(() => {
            button.style.transform = '';
        }, 150);
    }
    
    handleKeyboard(e) {
        e.preventDefault();
        this.clearError();
        
        const key = e.key;
        
        // Numbers
        if (/[0-9]/.test(key)) {
            this.inputNumber(key);
        }
        // Operators
        else if (key === '+') this.handleAction('add');
        else if (key === '-') this.handleAction('subtract');
        else if (key === '*') this.handleAction('multiply');
        else if (key === '/') this.handleAction('divide');
        else if (key === '=' || key === 'Enter') this.handleAction('equals');
        else if (key === '.') this.handleAction('decimal');
        else if (key === 'Escape') this.handleAction('clear');
        else if (key === 'Delete') this.handleAction('clear-entry');
        else if (key === 'Backspace') this.handleAction('backspace');
        else if (key === '%') this.handleAction('percent');
        // Memory operations
        else if (e.ctrlKey && key === 'm') this.handleAction('m-plus');
        else if (e.ctrlKey && key === 'r') this.handleAction('mr');
        else if (e.ctrlKey && key === 'l') this.handleAction('clear-memory');
    }
    
    handleAction(action) {
        try {
            