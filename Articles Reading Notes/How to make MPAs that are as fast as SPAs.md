---
tags: web, spa, mpa
---
1. Serve pre-rendered, static HTML
2. Inline everything, including css and javascript
3. Use mostly platform-native JavaScript, as little of it as possible
4. Minify and gzip all things
5. Lean heavily on service workers

#Hugo-static-site-generator

Servers respond to HTTP requests in 14kb chunks, each HTTP request adds a bit of latency to the rendering process. 

Use ES modules to create tiny bundles for JS

## Lean heavily on [[Service Workers|service workers]]

Cache HTML pages as the user browses. 

