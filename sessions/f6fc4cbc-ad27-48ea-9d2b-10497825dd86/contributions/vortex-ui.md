# DELIVERABLE: Complete Weather Dashboard

## FILE 1: index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Open-Meteo Weather Dashboard</title>
    <style>
        /* ===================================================================
           CSS: Vortex-UI Accessibility-First Styling Layer
           Mobile-first, WCAG 2.1 AA compliant, zero dependencies
           =================================================================== */

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --color-bg: #ffffff;
            --color-bg-secondary: #f5f5f5;
            --color-text: #1a1a1a;
            --color-text-secondary: #666666;
            --color-border: #e0e0e0;
            --color-accent: #0066cc;
            --color-accent-hover: #0052a3;
            --color-error: #d32f2f;
            --color-success: #388e3c;
            --color-warning: #f57c00;
            --spacing-xs: 0.5rem;
            --spacing-sm: 1rem;
            --spacing-md: 1.5rem;
            --spacing-lg: 2rem;
            --spacing-xl: 3rem;
            --radius: 8px;
            --shadow-sm: 0 2px 4px rgba(0, 0, 0, 0.08);
            --shadow-md: 0 4px 12px rgba(0, 0, 0, 0.12);
            --transition: all 0.3s ease;
        }

        @media (prefers-color-scheme: dark) {
            :root {
                --color-bg: #121212;
                --color-bg-secondary: #1e1e1e;
                --color-text: #ffffff;
                --color-text-secondary: #b0b0b0;
                --color-border: #333333;
                --color-accent: #66b3ff;
                --color-accent-hover: #4d9ff5;
            }
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
            background-color: var(--color-bg);
            color: var(--color-text);
            line-height: 1.6;
            transition: var(--transition);
        }

        /* Container & Layout */
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: var(--spacing-lg);
        }

        header {
            text-align: center;
            margin-bottom: var(--spacing-xl);
        }

        h1 {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: var(--spacing-sm);
            background: linear-gradient(135deg, var(--color-accent), #00a8ff);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .tagline {
            color: var(--color-text-secondary);
            font-size: 1rem;
        }

        /* Search Section */
        .search-section {
            margin-bottom: var(--spacing-xl);
        }

        .search-wrapper {
            position: relative;
            display: flex;
            gap: var(--spacing-sm);
            flex-wrap: wrap;
        }

        input[type="search"] {
            flex: 1;
            min-width: 200px;
            padding: var(--spacing-sm) var(--spacing-md);
            font-size: 1rem;
            border: 2px solid var(--color-border);
            border-radius: var(--radius);
            background-color: var(--color-bg);
            color: var(--color-text);
            transition: var(--transition);
        }

        input[type="search"]:focus {
            outline: none;
            border-color: var(--color-accent);
            box-shadow: 0 0 0 3px rgba(0, 102, 204, 0.1);
        }

        input[type="search"]::placeholder {
            color: var(--color-text-secondary);
        }

        .search-button {
            padding: var(--spacing-sm) var(--spacing-md);
            font-size: 1rem;
            font-weight: 600;
            background-color: var(--color-accent);
            color: white;
            border: none;
            border-radius: var(--radius);
            cursor: pointer;
            transition: var(--transition);
        }

        .search-button:hover {
            background-color: var(--color-accent-hover);
            transform: translateY(-2px);
            box-shadow: var(--shadow-md);
        }

        .search-button:active {
            transform: translateY(0);
        }

        .search-button:focus {
            outline: none;
            box-shadow: 0 0 0 3px rgba(0, 102, 204, 0.2);
        }

        .search-button[disabled] {
            opacity: 0.6;
            cursor: not-allowed;
        }

        /* Loading Spinner */
        .spinner {
            display: inline-block;
            width: 16px;
            height: 16px;
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-top-color: white;
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
            margin-right: var(--spacing-xs);
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        /* Alert/Toast Messages */
        [aria-live="polite"],
        [aria-live="assertive"] {
            position: relative;
            min-height: 1px;
        }

        .alert {
            padding: var(--spacing-md);
            margin-bottom: var(--spacing-md);
            border-radius: var(--radius);
            border-left: 4px solid;
            display: flex;
            align-items: center;
            gap: var(--spacing-sm);
            animation: slideIn 0.3s ease-out;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(-10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .alert.error {
            background-color: rgba(211, 47, 47, 0.1);
            border-color: var(--color-error);
            color: var(--color-error);
        }

        .alert.success {
            background-color: rgba(56, 142, 60, 0.1);
            border-color: var(--color-success);
            color: var(--color-success);
        }

        .alert.info {
            background-color: rgba(0, 102, 204, 0.1);
            border-color: var(--color-accent);
            color: var(--color-accent);
        }

        .alert-close {
            margin-left: auto;
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: inherit;
            padding: 0;
            width: 32px;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .alert-close:focus {
            outline: 2px solid currentColor;
            outline-offset: 2px;
        }

        /* Current Weather Card */
        .current-weather {
            background: linear-gradient(135deg, var(--color-bg-secondary), var(--color-bg));
            border: 2px solid var(--color-border);
            border-radius: var(--radius);
            padding: var(--spacing-lg);
            margin-bottom: var(--spacing-xl);
            box-shadow: var(--shadow-md);
        }

        .current-weather-location {
            color: var(--color-text-secondary);
            font-size: 0.875rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: var(--spacing-sm);
        }

        .current-weather-main {
            display: flex;
            align-items: flex-start;
            gap: var(--spacing-lg);
            flex-wrap: wrap;
            margin-bottom: var(--spacing-md);
        }

        .weather-icon {
            font-size: 4rem;
            line-height: 1;
            min-width: 80px;
            text-align: center;
        }

        .temperature-section h2 {
            font-size: 3rem;
            font-weight: 700;
            margin-bottom: var(--spacing-xs);
            display: flex;
            align-items: baseline;
            gap: var(--spacing-xs);
        }

        .weather-condition {
            font-size: 1.25rem;
            color: var(--color-text-secondary);
            margin-bottom: var(--spacing-md);
        }

        .weather-details {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: var(--spacing-md);
        }

        .detail-item {
            display: flex;
            flex-direction: column;
            padding: var(--spacing-md);
            background-color: var(--color-bg);
            border-radius: var(--radius);
            border: 1px solid var(--color-border);
        }

        .detail-label {
            font-size: 0.875rem;
            color: var(--color-text-secondary);
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: var(--spacing-xs);
        }

        .detail-value {
            font-size: 1.5rem;
            font-weight: 600;
            color: var(--color-accent);
        }

        /* Forecast Grid */
        .forecast-section h3 {
            font-size: 1.5rem;
            font-weight: 600;
            margin-bottom: var(--spacing-lg);
        }

        .forecast-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            gap: var(--spacing-md);
            margin-bottom: var(--spacing-xl);
        }

        .forecast-card {
            background-color: var(--color-bg-secondary);
            border: 2px solid var(--color-border);
            border-radius: var(--radius);
            padding: var(--spacing-md);
            text-align: center;
            transition: var(--transition);
            display: flex;
            flex-direction: column;
            gap: var(--spacing-sm);
        }

        .forecast-card:hover {
            border-color: var(--color-accent);
            box-shadow: var(--shadow-md);
            transform: translateY(-4px);
        }

        .forecast-date {
            font-size: 0.875rem;
            font-weight: 600;
            color: var(--color-text-secondary);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .forecast-icon {
            font-size: 2.5rem;
            line-height: 1;
        }

        .forecast-temp {
            display: flex;
            justify-content: center;
            gap: var(--spacing-xs);
            font-size: 1rem;
            font-weight: 600;
        }

        .forecast-temp-max {
            color: var(--color-text);
        }

        .forecast-temp-min {
            color: var(--color-text-secondary);
        }

        .forecast-condition {
            font-size: 0.875rem;
            color: var(--color-text-secondary);
            min-height: 2.4em;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .forecast-precipitation {
            font-size: 0.75rem;
            color: var(--color-text-secondary);
            padding-top: var(--spacing-xs);
            border-top: 1px solid var(--color-border);
        }

        /* Empty State */
        .empty-state {
            text-align: center;
            padding: var(--spacing-xl);
            color: var(--color-text-secondary);
        }

        .empty-state-icon {
            font-size: 3rem;
            margin-bottom: var(--spacing-md);
        }

        .empty-state h2 {
            font-size: 1.25rem;
            margin-bottom: var(--spacing-sm);
            color: var(--color-text);
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            .container {
                padding: var(--spacing-md);
            }

            h1 {
                font-size: 2rem;
            }

            .search-wrapper {
                flex-direction: column;
            }

            input[type="search"],
            .search-button {
                width: 100%;
            }

            .current-weather {
                padding: var(--spacing-md);
            }

            .current-weather-main {
                flex-direction: column;
                gap: var(--spacing-md);
            }

            .temperature-section h2 {
                font-size: 2.5rem;
            }

            .weather-icon {
                font-size: 3rem;
                min-width: 60px;
            }

            .forecast-grid {
                grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
                gap: var(--spacing-sm);
            }

            .forecast-card {
                padding: var(--spacing-sm);
            }

            .forecast-icon {
                font-size: 2rem;
            }
        }

        @media (max-width: 480px) {
            h1 {
                font-size: 1.5rem;
            }

            .container {
                padding: var(--spacing-sm);
            }

            .current-weather {
                padding: var(--spacing-sm);
            }

            .weather-details {
                grid-template-columns: repeat(2, 1fr);
            }

            .detail-item {
                padding: var(--spacing-sm);
            }

            .temperature-section h2 {
                font-size: 2rem;
            }

            .weather-icon {
                font-size: 2.5rem;
                min-width: 50px;
            }

            .forecast-grid {
                grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
            }

            .forecast-card {
                padding: var(--spacing-xs);
            }

            .forecast-icon {
                font-size: 1.5rem;
            }

            .forecast-date {
                font-size: 0.75rem;
            }

            .forecast-temp {
                font-size: 0.875rem;
            }
        }

        /* Print Styles */
        @media print {
            .search-section,
            .alert {
                display: none;
            }
        }

        /* Accessibility: Reduced Motion */
        @media (prefers-reduced-motion: reduce) {
            * {
                animation-duration: 0.01ms !important;
                animation-iteration-count: 1 !important;
                transition-duration: 0