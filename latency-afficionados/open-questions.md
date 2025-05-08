- Which java version to use?

  - Java 22+
    - Not LTS, might need to do several upgrades until it is LTS
    - Has new features
  - Java 21
    - Latest LTS (Long term support)
    - Most up-to-date
    - Most features
  - Java 17
    - LTS (Long term support)
    - Good amount of features
  - Older versions
    - Not as rich in features
    - Miss productivity and performance features

- Which node version to use?

  - Node 22
    - Latest LTS
    - Richest in features
  - Node 20
    - LTS
    - Will be out of maintainance in Apr 2026
  - Node 18-
    - Out of maintainance after Apr 2025

- What speeds up rendering time?
  1. Number of requests made in the UI
  - More requests, more time to render
  - Ideally avoid requests that block the ui, render some static content first and incrementally add async content
  2. Image sizes
  - Large images are heavy and can slow rendering time
  - Ideally to have 3 images sizes, 1 for mobile, 1 for tablet and 1 for desktop
  3. Stylesheet content
  - Ideally we should have only the necessary css in the css file downloaded by the page, do not include other pages css or css for entire app.
  4. JS bundle
  - Three shaking libraries avoid downloading unecessary parts of libraries and adding them to the bundle
  - Avoid heavy and replacable libraries like momentjs, Lodash, Jquery
  5. JS/CSS Minification
  - Minifying JS and CSS files for production reduces their bundle size, therefore speeding up page load
  6. Too much content on a page
  - Having too much dynamic content on a page will slow it down as it requires time to async load all the content
  - Avoid image heavy pages, as more images will increase time to render
  7. Lazy loading
  - Specially for vertically long pages, avoid loading all content at once, add lazy loading for elements, images that are not shown in the first render
  8. Pagination
  - If a page is content heavy, avoid loading and rendering everything in a single api call, use pagination to fetch and render few things at a time
