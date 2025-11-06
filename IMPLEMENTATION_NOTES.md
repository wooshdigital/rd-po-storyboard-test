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
    AddOutputFilterByType DEFLATE application/json
    AddOutputFilterByType DEFLATE image/svg+xml
</IfModule>

# Enable Gzip compression
<IfModule mod_gzip.c>
    mod_gzip_on Yes
    mod_gzip_dechunk Yes
    mod_gzip_item_include file .(html?|txt|css|js|php|pl)$
    mod_gzip_item_include handler ^cgi-script$
    mod_gzip_item_include mime ^text/.*
    mod_gzip_item_include mime ^application/x-javascript.*
    mod_gzip_item_exclude mime ^image/.*
    mod_gzip_item_exclude rspheader ^Content-Encoding:.*gzip.*
</IfModule>

# Browser Caching
<IfModule mod_expires.c>
    ExpiresActive on
    
    # HTML
    ExpiresByType text/html                     "access plus 1 hour"
    
    # CSS and JavaScript
    ExpiresByType text/css                      "access plus 1 month"
    ExpiresByType application/javascript        "access plus 1 month"
    ExpiresByType application/x-javascript      "access plus 1 month"
    
    # Images
    ExpiresByType image/gif                     "access plus 1 year"
    ExpiresByType image/png                     "access plus 1 year"
    ExpiresByType image/jpeg                    "access plus 1 year"
    ExpiresByType image/jpg                     "access plus 1 year"
    ExpiresByType image/webp                    "access plus 1 year"
    ExpiresByType image/svg+xml                 "access plus 1 year"
    
    # Fonts
    ExpiresByType font/woff2                    "access plus 1 year"
    ExpiresByType font/woff                     "access plus 1 year"
    ExpiresByType application/font-woff2        "access plus 1 year"
    ExpiresByType application/font-woff         "access plus 1 year"
    
    # Favicon
    ExpiresByType image/x-icon                  "access plus 1 year"
</IfModule>

# Cache-Control headers
<IfModule mod_headers.c>
    # HTML files
    <FilesMatch "\.(html|htm)$">
        Header set Cache-Control "max-age=3600, public, must-revalidate"
    </FilesMatch>
    
    # CSS and JS files
    <FilesMatch "\.(css|js)$">
        Header set Cache-Control "max-age=2592000, public"
    </FilesMatch>
    
    # Images
    <FilesMatch "\.(gif|png|jpe?g|webp|svg)$">
        Header set Cache-Control "max-age=31536000, public, immutable"
    </FilesMatch>
    
    # Remove ETag
    Header unset ETag
</IfModule>

# Remove ETag
FileETag None

# Security headers
<IfModule mod_headers.c>
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-XSS-Protection "1; mode=block"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    Header always set Permissions-Policy "camera=(), microphone=(), geolocation=(), payment=()"
</IfModule>

# Enable HTTP/2 Server Push for critical resources
<IfModule mod_http2.c>
    H2PushResource /styles.css
    H2PushResource /script.js
</IfModule>
CONTENT_END
FILE: script.js
CONTENT_START
// Feature detection and polyfills
(function() {
    'use strict';

    // Performance optimization: Cache DOM elements
    const DOM_CACHE = new Map();
    
    function getElement(selector) {
        if (!DOM_CACHE.has(selector)) {
            DOM_CACHE.set(selector, document.querySelector(selector));
        }
        return DOM_CACHE.get(selector);
    }

    // Debounce function for performance
    function debounce(func, wait, immediate) {
        let timeout;
        return function executedFunction() {
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

    // Throttle function for scroll/resize events
    function throttle(func, limit) {
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
    }

    // Feature detection
    const FEATURES = {
        addEventListener: !!(window.addEventListener),
        querySelector: !!(document.querySelector),
        classList: !!('classList' in document.createElement('div')),
        requestAnimationFrame: !!(window.requestAnimationFrame || 
                                window.webkitRequestAnimationFrame || 
                                window.mozRequestAnimationFrame),
        localStorage: (function() {
            try {
                return 'localStorage' in window && window.localStorage !== null;
            } catch(e) {
                return false;
            }
        })(),
        intersectionObserver: !!(window.IntersectionObserver),
        webGL: (function() {
            try {
                const canvas = document.createElement('canvas');
                return !!(window.WebGLRenderingContext && 
                         canvas.getContext('webgl'));
            } catch(e) {
                return false;
            }
        })(),
        touchEvents: !!('ontouchstart' in window || 
                       (window.DocumentTouch && document instanceof DocumentTouch)),
        passiveEvents: (function() {
            let supportsPassive = false;
            try {
                const opts = Object.defineProperty({}, 'passive', {
                    get: function() {
                        supportsPassive = true;
                    }
                });
                window.addEventListener('testPassive', null, opts);
                window.removeEventListener('testPassive', null, opts);
            } catch (e) {}
            return supportsPassive;
        })()
    };

    // Cross-browser event handling
    function addEvent(element, event, handler, options) {
        if (FEATURES.addEventListener) {
            const eventOptions = FEATURES.passiveEvents && 
                               (event === 'scroll' || event === 'wheel' || 
                                event === 'touchstart' || event === 'touchmove') 
                               ? { passive: true, ...options } : options;
            element.addEventListener(event, handler, eventOptions);
        } else if (element.attachEvent) {
            element.attachEvent('on' + event, handler);
        } else {
            element['on' + event] = handler;
        }
    }

    // Cross-browser class manipulation
    function addClass(element, className) {
        if (FEATURES.classList) {
            element.classList.add(className);
        } else {
            if (element.className.indexOf(className) === -1) {
                element.className += ' ' + className;
            }
        }
    }

    function removeClass(element, className) {
        if (FEATURES.classList) {
            element.classList.remove(className);
        } else {
            element.className = element.className.replace(
                new RegExp('\\b' + className + '\\b', 'g'), ''
            ).replace(/\s+/g, ' ').trim();
        }
    }

    function hasClass(element, className) {
        if (FEATURES.classList) {
            return element.classList.contains(className);
        } else {
            return element.className.indexOf(className) !== -1;
        }
    }

    // Performance-optimized animation frame
    const requestAnimFrame = (function() {
        return window.requestAnimationFrame ||
               window.webkitRequestAnimationFrame ||
               window.mozRequestAnimationFrame ||
               function(callback) {
                   window.setTimeout(callback, 1000 / 60);
               };
    })();

    // Lazy loading with Intersection Observer fallback
    function initLazyLoading() {
        const images = document.querySelectorAll('img[data-src]');
        
        if (FEATURES.intersectionObserver) {
            const imageObserver = new IntersectionObserver((entries, observer) => {
                entries.forEach(entry => {
                    if (entry.isIntersecting) {
                        const img = entry.target;
                        img.src = img.dataset.src;
                        img.removeAttribute('data-src');
                        addClass(img, 'loaded');
                        observer.unobserve(img);
                    }
                });
            }, {
                rootMargin: '50px 0px'
            });

            images.forEach(img => imageObserver.observe(img));
        } else {
            // Fallback for older browsers
            function loadImagesInViewport() {
                images.forEach(img => {
                    if (img.dataset.src && isInViewport(img)) {
                        img.src = img.dataset.src;
                        img.removeAttribute('data-src');
                        addClass(img, 'loaded');
                    }
                });
            }

            function isInViewport(element) {
                const rect = element.getBoundingClientRect();
                return (
                    rect.top >= 0 &&
                    rect.left >= 0 &&
                    rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
                    rect.right <= (window.innerWidth || document.documentElement.clientWidth)
                );
            }

            const scrollHandler = throttle(loadImagesInViewport, 200);
            addEvent(window, 'scroll', scrollHandler, { passive: true });
            addEvent(window, 'resize', scrollHandler, { passive: true });
            loadImagesInViewport(); // Check on load
        }
    }

    // Performance monitoring
    function measurePerformance() {
        if ('performance' in window) {
            addEvent(window, 'load', function() {
                setTimeout(() => {
                    const perfData = performance.timing;
                    const loadTime = perfData.loadEventEnd - perfData.navigationStart;
                    const domReady = perfData.domContentLoadedEventEnd - perfData.navigationStart;
                    
                    // Log performance metrics (in production, send to analytics)
                    console.log('Page Load Time:', loadTime + 'ms');
                    console.log('DOM Ready Time:', domReady + 'ms');

                    // Measure Core Web Vitals if available
                    if ('PerformanceObserver' in window) {
                        try {
                            new PerformanceObserver((list) => {
                                const entries = list.getEntries();
                                entries.forEach(entry => {
                                    if (entry.entryType === 'largest-contentful-paint') {
                                        console.log('LCP:', entry.startTime);
                                    }
                                    if (entry.entryType === 'first-input') {
                                        console.log('FID:', entry.processingStart - entry.startTime);
                                    }
                                    if (entry.entryType === 'layout-shift') {
                                        if (!entry.hadRecentInput) {
                                            console.log('CLS:', entry.value);
                                        }
                                    }
                                });
                            }).observe({
                                entryTypes: ['largest-contentful-paint', 'first-input', 'layout-shift']
                            });
                        } catch (e) {
                            console.warn('Performance Observer not supported');
                        }
                    }
                }, 0);
            });
        }
    }

    // Enhanced error handling and logging
    function setupErrorHandling() {
        window.onerror = function(message, source, lineno, colno, error) {
            console.error('JavaScript Error:', {
                message: message,
                source: source,
                line: lineno,
                column: colno,
                error: error
            });
            return false; // Don't prevent default browser error handling
        };

        // Handle Promise rejections
        if ('addEventListener' in window) {
            addEvent(window, 'unhandledrejection', function(event) {
                console.error('Unhandled Promise Rejection:', event.reason);
            });
        }
    }

    // Progressive Web App features
    function initServiceWorker() {
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('/sw.js')
                .then(registration => {
                    console.log('SW registered: ', registration);
                })
                .catch(registrationError => {
                    console.log('SW registration failed: ', registrationError);
                });
        }
    }

    // Local storage with error handling
    function safeStorage(key, value) {
        if (!FEATURES.localStorage) return null;
        
        try {
            if (value !== undefined) {
                localStorage.setItem(key, JSON.stringify(value));
                return value;
            } else {
                const item = localStorage.getItem(key);
                return item ? JSON.parse(item) : null;
            }
        } catch (e) {
            console.warn('Storage operation failed:', e);
            return null;
        }
    }

    // Optimized smooth scroll
    function smoothScrollTo(element) {
        if ('scrollBehavior' in document.documentElement.style) {
            element.scrollIntoView({ behavior: 'smooth' });
        } else {
            // Polyfill for older browsers
            const targetPosition = element.offsetTop;
            const startPosition = window.pageYOffset;
            const distance = targetPosition - startPosition;
            const duration = 800;
            let start = null;

            function animation(currentTime) {
                if (start === null) start = currentTime;
                const timeElapsed = currentTime - start;
                const run = ease(timeElapsed, startPosition, distance, duration);
                window.scrollTo(0, run);
                if (timeElapsed < duration) requestAnimFrame(animation);
            }

            function ease(t, b, c, d) {
                t /= d / 2;
                if (t < 1) return c / 2 * t * t + b;
                t--;
                return -c / 2 * (t * (t - 2) - 1) + b;
            }

            requestAnimFrame(animation);
        }
    }

    // Initialize everything when DOM is ready
    function init() {
        // Check if DOM is already loaded
        if (document.readyState === 'loading') {
            addEvent(document, 'DOMContentLoaded', function() {
                initializeApp();
            });
        } else {
            initializeApp();
        }
    }

    function initializeApp() {
        console.log('Initializing application...');
        console.log('Browser features detected:', FEATURES);
        
        // Initialize features based on browser support
        initLazyLoading();
        measurePerformance();
        setupErrorHandling();
        
        // Only init service worker in production
        if (location.protocol === 'https:' || location.hostname === 'localhost') {
            init