# Frontend Performance Profiler

This is a single-page web application designed to analyze the frontend performance of a webpage. It provides key metrics by rendering the content in a sandboxed `<iframe>` and using browser-native performance APIs.

## Features

* **Analyze Pasted Content:** Directly paste your HTML and CSS into text areas for an immediate analysis.
* **Analyze Live URL:** Enter a public URL, and the tool will attempt to fetch its HTML and CSS for analysis.
* **Core Metrics:**
    * **DOM Size:** Reports the total number of elements in the DOM.
    * **Approx. LCP:** Measures the approximate Largest Contentful Paint time.
    * **Network Calls:** Counts the number of resources (images, etc.) loaded within the sandbox.
* **Detailed Stats:**
    * **Image Stats:** Lists loaded images with their approximate size (transfer size) and load time.
    * **Approx. Unused CSS:** Scans the provided CSS and identifies selectors that do not match any elements in the rendered DOM.

## How It Works

The tool operates in two main modes:

1.  **Pasted Content Mode:**
    * You provide the HTML and CSS.
    * The "Analyze Pasted Content" button constructs a complete HTML document using a `data:` URL.
    * This `data:` URL is loaded into a sandboxed `<iframe>`.
    * A script is injected into the iframe to observe performance and collect data.

2.  **Fetch from URL Mode:**
    * You provide a URL (e.g., `https://example.com`).
    * The "Fetch & Analyze" button uses a public CORS proxy (`api.allorigins.win`) to fetch the raw HTML of the target URL.
    * It parses the HTML to find all inline `<style>` tags and linked `<link rel="stylesheet">` stylesheets.
    * It then fetches all linked CSS files (also via the proxy).
    * The fetched HTML and combined CSS are populated into the text areas.
    * Finally, it programmatically triggers the "Analyze Pasted Content" logic to run the analysis.

### Metrics Collection

All metrics are gathered from within the sandboxed `<iframe>`:

* **LCP:** A `PerformanceObserver` is used to listen for the `largest-contentful-paint` entry.
* **DOM Size:** Calculated using `document.getElementsByTagName('*').length`.
* **Network Calls & Image Stats:** Collected using `performance.getEntriesByType('resource')` to find all loaded resources and filter for images.
* **Unused CSS:** After the iframe loads, the main page accesses its `contentDocument` to check `styleSheets`. It iterates through all CSS rules and uses `iDoc.querySelector(selector)` to check if a selector finds a match.
* **Communication:** The injected script in the iframe posts the collected data (LCP, DOM size, etc.) back to the parent window using `window.parent.postMessage()`.

## Tech Stack

* **HTML5 / CSS3 / JavaScript (ES Modules)**
* **Tailwind CSS:** For all styling.
* **Browser APIs:**
    * `PerformanceObserver`
    * `performance.getEntriesByType('resource')`
    * `DOMParser`
    * `window.postMessage()`
* **CORS Proxy:** Uses `allorigins.win` to bypass Cross-Origin-Resource-Sharing (CORS) restrictions for fetching content from remote URLs.

## Demo
https://swapnilgopale.github.io/Frontend-Performance-Analyzer/
