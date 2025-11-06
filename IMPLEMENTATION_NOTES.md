# Implementation Notes

Implement the following feature phase:

Phase: Phase 5: Keyboard Support & Accessibility
Description: Add full keyboard support and accessibility features for enhanced usability

Technical Details:
Implement keyboard event listeners for all calculator functions (numbers 0-9, operators +, -, *, /, Enter for equals, Escape/C for clear, Backspace for delete). Add proper ARIA labels, keyboard focus management, and screen reader support. Ensure proper tab navigation and visual focus indicators.

Files to focus on: {
  "script.js": "Enhanced with comprehensive keyboard event handling and accessibility features",
  "index.html": "Updated with ARIA labels and accessibility attributes",
  "styles.css": "Added focus styles and accessibility improvements"
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
    <title>Calculator</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="calculator" role="application" aria-label="Calculator">
        <div class="display-container">
            <div id="display" 
                 class="display" 
                 role="textbox" 
                 aria-readonly="true" 
                 aria-label="Calculator display"
                 aria-live="polite"
                 tabindex="0">0</div>
        </div>
        <div class="buttons" role="grid" aria-label="Calculator buttons">
            <button class="btn clear" 
                    data-key="clear" 
                    aria-label="Clear all"
                    data-keyboard="Escape,c,C">C</button>
            <button class="btn operator" 
                    data-key="/" 
                    aria-label="Divide"
                    data-keyboard="/">/</button>
            <button class="btn operator" 
                    data-key="*" 
                    aria-label="Multiply"
                    data-keyboard="*,x,X">×</button>
            <button class="btn delete" 
                    data-key="delete" 
                    aria-label="Delete last digit"
                    data-keyboard="Backspace">⌫</button>
            
            <button class="btn number" 
                    data-key="7" 
                    aria-label="Seven"
                    data-keyboard="7">7</button>
            <button class="btn number" 
                    data-key="8" 
                    aria-label="Eight"
                    data-keyboard="8">8</button>
            <button class="btn number" 
                    data-key="9" 
                    aria-label="Nine"
                    data-keyboard="9">9</button>
            <button class="btn operator" 
                    data-key="-" 
                    aria-label="Subtract"
                    data-keyboard="-">-</button>
            
            <button class="btn number" 
                    data-key="4" 
                    aria-label="Four"
                    data-keyboard="4">4</button>
            <button class="btn number" 
                    data-key="5" 
                    aria-label="Five"
                    data-keyboard="5">5</button>
            <button class="btn number" 
                    data-key="6" 
                    aria-label="Six"
                    data-keyboard="6">6</button>
            <button class="btn operator" 
                    data-key="+" 
                    aria-label="Add"
                    data-keyboard="+">+</button>
            
            <button class="btn number" 
                    data-key="1" 
                    aria-label="One"
                    data-keyboard="1">1</button>
            <button class="btn number" 
                    data-key="2" 
                    aria-label="Two"
                    data-keyboard="2">2</button>
            <button class="btn number" 
                    data-key="3" 
                    aria-label="Three"
                    data-keyboard="3">3</button>
            <button class="btn equals" 
                    data-key="=" 
                    aria-label="Equals"
                    data-keyboard="Enter,="
                    rowspan="2">=</button>
            
            <button class="btn number zero" 
                    data-key="0" 
                    aria-label="Zero"
                    data-keyboard="0">0</button>
            <button class="btn decimal" 
                    data-key="." 
                    aria-label="Decimal point"
                    data-keyboard=".,">.</button>
        </div>
    </div>
    
    <!-- Screen reader only instructions -->
    <div class="sr-only" role="region" aria-label="Calculator instructions">
        <h2>Calculator Instructions</h2>
        <p>Use number keys 0-9 to enter numbers. Use +, -, *, / for operations. Press Enter or = to calculate. Press Escape or C to clear. Press Backspace to delete last digit.</p>
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
    font-family: 'Arial', sans-serif;
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
    padding: 20px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    max-width: 300px;
    width: 100%;
}

.display-container {
    margin-bottom: 20px;
}

.display {
    background: #34495e;
    color: #ecf0f1;
    font-size: 2.5rem;
    text-align: right;
    padding: 20px;
    border-radius: 10px;
    min-height: 80px;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    word-wrap: break-word;
    overflow-wrap: break-word;
    border: 2px solid transparent;
    position: relative;
}

.display:focus {
    outline: none;
    border-color: #3498db;
    box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.3);
}

.display::before {
    content: '';
    position: absolute;
    top: 50%;
    right: 15px;
    transform: translateY(-50%);
    width: 2px;
    height: 24px;
    background: #3498db;
    animation: blink 1s infinite;
    opacity: 0;
}

.display:focus::before {
    opacity: 1;
}

@keyframes blink {
    0%, 50% { opacity: 1; }
    51%, 100% { opacity: 0; }
}

.buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 15px;
}

.btn {
    background: #3498db;
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 1.5rem;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 60px;
    position: relative;
    border: 2px solid transparent;
}

.btn:hover {
    background: #2980b9;
    transform: translateY(-2px);
}

.btn:active {
    transform: translateY(0);
}

.btn:focus {
    outline: none;
    border-color: #ffffff;
    box-shadow: 0 0 0 3px rgba(255, 255, 255, 0.4);
}

.btn:focus:not(:focus-visible) {
    border-color: transparent;
    box-shadow: none;
}

.btn:focus-visible {
    border-color: #ffffff;
    box-shadow: 0 0 0 3px rgba(255, 255, 255, 0.4);
}

.btn.operator {
    background: #e67e22;
}

.btn.operator:hover {
    background: #d35400;
}

.btn.equals {
    background: #27ae60;
    grid-row: span 2;
}

.btn.equals:hover {
    background: #229954;
}

.btn.clear {
    background: #e74c3c;
}

.btn.clear:hover {
    background: #c0392b;
}

.btn.delete {
    background: #9b59b6;
}

.btn.delete:hover {
    background: #8e44ad;
}

.btn.zero {
    grid-column: span 2;
}

.btn.number:hover {
    background: #2980b9;
}

.btn.decimal {
    background: #34495e;
}

.btn.decimal:hover {
    background: #2c3e50;
}

/* Active state for keyboard interactions */
.btn.active {
    background: #1abc9c !important;
    transform: scale(0.95);
}

/* High contrast mode support */
@media (prefers-contrast: high) {
    .calculator {
        border: 3px solid #ffffff;
    }
    
    .display {
        border: 2px solid #ffffff;
    }
    
    .btn {
        border: 2px solid #ffffff;
    }
    
    .btn:focus {
        border-width: 4px;
    }
}

/* Reduced motion support */
@media (prefers-reduced-motion: reduce) {
    .btn {
        transition: none;
    }
    
    .btn:hover {
        transform: none;
    }
    
    .btn:active {
        transform: none;
    }
    
    .display::before {
        animation: none;
    }
}

/* Screen reader only content */
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}

/* Mobile accessibility improvements */
@media (max-width: 480px) {
    .btn {
        min-height: 70px;
        font-size: 1.3rem;
    }
    
    .display {
        font-size: 2rem;
        padding: 15px;
    }
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
    body {
        background: linear-gradient(135deg, #2c3e50 0%, #34495e 100%);
    }
}
CONTENT_END
FILE: script.js
CONTENT_START
class Calculator {
    constructor() {
        this.display = document.getElementById('display');
        this.buttons = document.querySelectorAll('.btn');
        this.currentInput = '0';
        this.previousInput = '';
        this.operator = '';
        this.waitingForOperand = false;
        this.lastKeyPressed = null;
        
        this.init();
        this.setupKeyboardSupport();
        this.setupAccessibilityFeatures();
    }
    
    init() {
        this.buttons.forEach(button => {
            button.addEventListener('click', (e) => {
                this.handleButtonClick(e.target);
                // Maintain focus for keyboard users
                if (this.lastKeyPressed) {
                    button.blur();
                }
            });
        });
        
        // Make display focusable and announce changes
        this.display.setAttribute('tabindex', '0');
        this.updateDisplay();
    }
    
    setupKeyboardSupport() {
        // Global keyboard event listener
        document.addEventListener('keydown', (e) => {
            this.handleKeydown(e);
        });
        
        document.addEventListener('keyup', (e) => {
            this.handleKeyup(e);
        });
        
        // Create keyboard mapping
        this.keyboardMap = new Map();
        
        // Numbers
        for (let i = 0; i <= 9; i++) {
            this.keyboardMap.set(i.toString(), i.toString());
            this.keyboardMap.set(`Numpad${i}`, i.toString());
        }
        
        // Operators
        this.keyboardMap.set('+', '+');
        this.keyboardMap.set('NumpadAdd', '+');
        this.keyboardMap.set('-', '-');
        this.keyboardMap.set('NumpadSubtract', '-');
        this.keyboardMap.set('*', '*');
        this.keyboardMap.set('NumpadMultiply', '*');
        this.keyboardMap.set('x', '*');
        this.keyboardMap.set('X', '*');
        this.keyboardMap.set('/', '/');
        this.keyboardMap.set('NumpadDivide', '/');
        
        // Special keys
        this.keyboardMap.set('Enter', '=');
        this.keyboardMap.set('NumpadEnter', '=');
        this.keyboardMap.set('=', '=');
        this.keyboardMap.set('Escape', 'clear');
        this.keyboardMap.set('c', 'clear');
        this.keyboardMap.set('C', 'clear');
        this.keyboardMap.set('Backspace', 'delete');
        this.keyboardMap.set('Delete', 'delete');
        this.keyboardMap.set('.', '.');
        this.keyboardMap.set('NumpadDecimal', '.');
        this.keyboardMap.set(',', '.');
    }
    
    setupAccessibilityFeatures() {
        // Set up ARIA live region for announcements
        this.display.setAttribute('aria-live', 'polite');
        this.display.setAttribute('aria-atomic', 'true');
        
        // Focus management
        this.setupFocusManagement();
        
        // Screen reader announcements
        this.setupScreenReaderSupport();
    }
    
    setupFocusManagement() {
        // Allow display to receive focus
        this.display.addEventListener('keydown', (e) => {
            // Allow navigation away from display with Tab
            if (e.key === 'Tab') {
                return;
            }
            // Handle other keys as if they were pressed globally
            this.handleKeydown(e);
        });
        
        // Manage button focus
        this.buttons.forEach((button, index) => {
            button.addEventListener('keydown', (e) => {
                this.handleButtonNavigation(e, button, index);
            });
        });
    }
    
    setupScreenReaderSupport() {
        // Create a live region for detailed announcements
        this.announcementRegion = document.createElement('div');
        this.announcementRegion.setAttribute('aria-live', 'assertive');
        this.announcementRegion.setAttribute('aria-atomic', 'true');
        this.announcementRegion.className = 'sr-only';
        document.body.appendChild(this.announcementRegion);
    }
    
    handleButtonNavigation(e, currentButton, currentIndex) {
        const buttonsArray = Array.from(this.buttons);
        let newIndex = currentIndex;
        
        switch (e.key) {
            case 'ArrowRight':
                newIndex = (currentIndex + 1) % buttonsArray.length;
                break;
            case 'ArrowLeft':
                newIndex = (currentIndex - 1 + buttonsArray.length) % buttonsArray.length;
                break;
            case 'ArrowDown':
                newIndex = Math.min(currentIndex + 4, buttonsArray.length - 1);
                break;
            case 'ArrowUp':
                newIndex = Math.max(currentIndex - 4, 0);
                break;
            default:
                return; // Don't prevent default for other keys
        }
        
        e.preventDefault();
        buttonsArray[newIndex].focus();
    }
    
    handleKeydown(e) {
        this.lastKeyPressed = e.key;
        
        // Prevent default for calculator keys
        if (