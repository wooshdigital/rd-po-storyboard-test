# Implementation Notes

Implement the following feature phase:

Phase: Phase 8: Performance & Browser Compatibility
Description: Optimize performance, ensure cross-browser compatibility, and add progressive enhancements

Technical Details:
Optimize JavaScript performance with efficient event handling and minimal DOM manipulation. Add CSS vendor prefixes for maximum browser support. Implement feature detection and graceful degradation. Minimize and optimize code for faster loading. Add proper caching headers and optimize for Core Web Vitals.

Files to focus on: {
  ".htaccess": "Caching headers and performance optimizations for web servers",
  "script.js": "Optimized code with efficient algorithms and cross-browser compatibility",
  "styles.css": "Browser-compatible CSS with vendor prefixes and performance optimizations",
  "manifest.json": "Web app manifest for PWA capabilities"
}

Generate working, production-ready code for this phase. Make sure the implementation is complete and functional.

## Claude Response

FILES_START
FILE: .htaccess
CONTENT_START
# Enable compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>

# Enable browser caching
<IfModule mod_expires.c>
    ExpiresActive on
    
    # CSS and JavaScript
    ExpiresByType text/css "access plus 1 month"
    ExpiresByType application/javascript "access plus 1 month"
    ExpiresByType application/x-javascript "access plus 1 month"
    
    # Images
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
    ExpiresByType image/webp "access plus 1 year"
    
    # HTML
    ExpiresByType text/html "access plus 1 hour"
    
    # Fonts
    ExpiresByType font/woff "access plus 1 year"
    ExpiresByType font/woff2 "access plus 1 year"
    ExpiresByType application/font-woff "access plus 1 year"
    ExpiresByType application/font-woff2 "access plus 1 year"
</IfModule>

# Cache-Control headers
<IfModule mod_headers.c>
    # CSS and JavaScript
    <FilesMatch "\.(css|js)$">
        Header set Cache-Control "public, max-age=2592000"
    </FilesMatch>
    
    # Images
    <FilesMatch "\.(png|jpg|jpeg|gif|svg|webp|ico)$">
        Header set Cache-Control "public, max-age=31536000"
    </FilesMatch>
    
    # HTML
    <FilesMatch "\.html$">
        Header set Cache-Control "public, max-age=3600"
    </FilesMatch>
    
    # Security headers
    Header always set X-Content-Type-Options nosniff
    Header always set X-Frame-Options DENY
    Header always set X-XSS-Protection "1; mode=block"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
</IfModule>

# Enable ETags
FileETag MTime Size

# Remove server signature
ServerTokens Prod
CONTENT_END
FILE: script.js
CONTENT_START
// Feature detection and polyfills
(function() {
    'use strict';
    
    // Check for modern browser features
    const hasModernFeatures = {
        intersectionObserver: 'IntersectionObserver' in window,
        requestAnimationFrame: 'requestAnimationFrame' in window,
        classList: 'classList' in document.documentElement,
        addEventListener: 'addEventListener' in window,
        querySelector: 'querySelector' in document,
        localStorage: (function() {
            try {
                localStorage.setItem('test', 'test');
                localStorage.removeItem('test');
                return true;
            } catch(e) {
                return false;
            }
        })()
    };
    
    // Polyfill for classList (IE9 support)
    if (!hasModernFeatures.classList) {
        /*! @source http://purl.eligrey.com/github/classList.js/blob/master/classList.js */
        if ("document" in self) {
            if (!("classList" in document.createElement("_"))) {
                (function (view) {
                    "use strict";
                    if (!('Element' in view)) return;
                    var classListProp = "classList",
                        protoProp = "prototype",
                        elemCtrProto = view.Element[protoProp],
                        objCtr = Object,
                        strTrim = String[protoProp].trim || function () {
                            return this.replace(/^\s+|\s+$/g, "");
                        },
                        arrIndexOf = Array[protoProp].indexOf || function (item) {
                            var i = 0, len = this.length;
                            for (; i < len; i++) {
                                if (i in this && this[i] === item) {
                                    return i;
                                }
                            }
                            return -1;
                        },
                        DOMTokenList = function (el) {
                            this.el = el;
                            var classes = el.className.replace(/^\s+|\s+$/g, "").split(/\s+/);
                            for (var i = 0, len = classes.length; i < len; i++) {
                                this.push(classes[i]);
                            }
                            this._updateClassName = function () {
                                el.className = this.toString();
                            };
                        },
                        tokenListProto = DOMTokenList[protoProp] = [],
                        tokenListGetter = function () {
                            return new DOMTokenList(this);
                        };
                    tokenListProto.item = function (i) {
                        return this[i] || null;
                    };
                    tokenListProto.contains = function (token) {
                        token += "";
                        return arrIndexOf.call(this, token) !== -1;
                    };
                    tokenListProto.add = function () {
                        var tokens = arguments,
                            i = 0,
                            l = tokens.length,
                            token,
                            updated = false;
                        do {
                            token = tokens[i] + "";
                            if (arrIndexOf.call(this, token) === -1) {
                                this.push(token);
                                updated = true;
                            }
                        }
                        while (++i < l);
                        if (updated) {
                            this._updateClassName();
                        }
                    };
                    tokenListProto.remove = function () {
                        var tokens = arguments,
                            i = 0,
                            l = tokens.length,
                            token,
                            updated = false,
                            index;
                        do {
                            token = tokens[i] + "";
                            index = arrIndexOf.call(this, token);
                            while (index !== -1) {
                                this.splice(index, 1);
                                updated = true;
                                index = arrIndexOf.call(this, token);
                            }
                        }
                        while (++i < l);
                        if (updated) {
                            this._updateClassName();
                        }
                    };
                    tokenListProto.toggle = function (token, force) {
                        token += "";
                        var result = this.contains(token),
                            method = result ?
                            force !== true && "remove" :
                            force !== false && "add";
                        if (method) {
                            this[method](token);
                        }
                        if (force === true || force === false) {
                            return force;
                        } else {
                            return !result;
                        }
                    };
                    tokenListProto.toString = function () {
                        return this.join(" ");
                    };
                    if (objCtr.defineProperty) {
                        var defineProperty = function (object, name, definition) {
                            if (definition.get || definition.set) {
                                objCtr.defineProperty(object, name, definition);
                            } else {
                                object[name] = definition.value;
                            }
                        };
                        try {
                            defineProperty(elemCtrProto, classListProp, {
                                get: tokenListGetter,
                                enumerable: true,
                                configurable: true
                            });
                        } catch (ex) {
                            if (ex.number === -0x7FF5EC54) {
                                defineProperty(elemCtrProto, classListProp, {
                                    value: tokenListGetter,
                                    enumerable: true,
                                    configurable: true
                                });
                            }
                        }
                    } else if (objCtr[protoProp].__defineGetter__) {
                        elemCtrProto.__defineGetter__(classListProp, tokenListGetter);
                    }
                }(self));
            }
        }
    }
    
    // Polyfill for requestAnimationFrame
    if (!hasModernFeatures.requestAnimationFrame) {
        window.requestAnimationFrame = window.requestAnimationFrame ||
            window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame ||
            function(callback) {
                return window.setTimeout(callback, 1000 / 60);
            };
        
        window.cancelAnimationFrame = window.cancelAnimationFrame ||
            window.webkitCancelAnimationFrame ||
            window.mozCancelAnimationFrame ||
            function(id) {
                window.clearTimeout(id);
            };
    }
    
    // Optimized DOM utilities
    const DOMUtils = {
        // Cached selectors for better performance
        cache: new Map(),
        
        // Efficient element selection with caching
        select(selector, context = document, useCache = true) {
            const key = selector + (context === document ? '' : context.outerHTML);
            if (useCache && this.cache.has(key)) {
                return this.cache.get(key);
            }
            const element = context.querySelector(selector);
            if (useCache && element) {
                this.cache.set(key, element);
            }
            return element;
        },
        
        selectAll(selector, context = document) {
            return Array.from(context.querySelectorAll(selector));
        },
        
        // Efficient class manipulation
        addClass(element, className) {
            if (element && className) {
                element.classList.add(className);
            }
        },
        
        removeClass(element, className) {
            if (element && className) {
                element.classList.remove(className);
            }
        },
        
        toggleClass(element, className) {
            if (element && className) {
                element.classList.toggle(className);
            }
        },
        
        hasClass(element, className) {
            return element && element.classList.contains(className);
        }
    };
    
    // Optimized event handling
    const EventManager = {
        listeners: new WeakMap(),
        
        // Efficient event delegation
        delegate(parent, selector, eventType, handler) {
            const delegatedHandler = (e) => {
                const target = e.target.closest(selector);
                if (target && parent.contains(target)) {
                    handler.call(target, e);
                }
            };
            
            parent.addEventListener(eventType, delegatedHandler);
            
            if (!this.listeners.has(parent)) {
                this.listeners.set(parent, []);
            }
            this.listeners.get(parent).push({
                eventType,
                handler: delegatedHandler,
                originalHandler: handler
            });
        },
        
        // Throttled event handler
        throttle(func, limit) {
            let inThrottle;
            return function() {
                const args = arguments;
                const context = this;
                if (!inThrottle) {
                    func.apply(context, args);
                    inThrottle = true;
                    setTimeout(() => inThrottle = false, limit);
                }
            };
        },
        
        // Debounced event handler
        debounce(func, wait, immediate) {
            let timeout;
            return function() {
                const context = this;
                const args = arguments;
                const later = function() {
                    timeout = null;
                    if (!immediate) func.apply(context, args);
                };
                const callNow = immediate && !timeout;
                clearTimeout(timeout);
                timeout = setTimeout(later, wait);
                if (callNow) func.apply(context, args);
            };
        }
    };
    
    // Performance monitoring
    const PerformanceMonitor = {
        // Core Web Vitals measurement
        measureCLS() {
            if ('LayoutShift' in window) {
                let clsValue = 0;
                let clsEntries = [];
                let sessionValue = 0;
                let sessionEntries = [];
                
                const observer = new PerformanceObserver((entryList) => {
                    for (const entry of entryList.getEntries()) {
                        if (!entry.hadRecentInput) {
                            const firstSessionEntry = sessionEntries[0];
                            const lastSessionEntry = sessionEntries[sessionEntries.length - 1];
                            
                            if (sessionValue && entry.startTime - lastSessionEntry.startTime < 1000 && 
                                entry.startTime - firstSessionEntry.startTime < 5000) {
                                sessionValue += entry.value;
                                sessionEntries.push(entry);
                            } else {
                                sessionValue = entry.value;
                                sessionEntries = [entry];
                            }
                            
                            if (sessionValue > clsValue) {
                                clsValue = sessionValue;
                                clsEntries = [...sessionEntries];
                            }
                        }
                    }
                });
                
                try {
                    observer.observe({type: 'layout-shift', buffered: true});
                } catch (e) {
                    console.warn('LayoutShift observer not supported');
                }
                
                return clsValue;
            }
        },
        
        measureLCP() {
            if ('LargestContentfulPaint' in window) {
                const observer = new PerformanceObserver((entryList) => {
                    const entries = entryList.getEntries();
                    const lastEntry = entries[entries.length - 1];
                    console.log('LCP:', lastEntry.startTime);
                });
                
                try {
                    observer.observe({type: 'largest-contentful-paint', buffered: true});
                } catch (e) {
                    console.warn('LCP observer not supported');
                }
            }
        },
        
        measureFID() {
            if ('PerformanceEventTiming' in window) {
                const observer = new PerformanceObserver((entryList) => {
                    for (const entry of entryList.getEntries()) {
                        const delay = entry.processingStart - entry.startTime;
                        console.log('FID:', delay);
                    }
                });
                
                try {
                    observer.observe({type: 'first-input', buffered: true});
                } catch (e) {
                    console.warn('FID observer not supported');
                }
            }
        }
    };
    
    // Intersection Observer for efficient scroll animations
    const ScrollAnimations = {
        observer: null,
        
        init() {
            if (hasModernFeatures.intersectionObserver) {
                this.observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => {
                        if (entry.isIntersecting) {
                            DOMUtils.addClass(entry.target, 'animate-in');
                            this.observer.unobserve(entry.target);
                        }
                    });
                }, {
                    threshold: 0.1,
                    rootMargin: '0px 0px -50px 0px'
                });
                
                // Observe elements with data-animate attribute
                DOMUtils.selectAll('[data-animate]').forEach(el => {
                    this.observer.observe(el);
                });
            } else {
                // Fallback for browsers without Intersection Observer
                this.fallbackScrollHandler();
            }
        },
        
        fallbackScrollHandler() {
            const animateElements = D