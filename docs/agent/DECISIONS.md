# Decisions

## 2026-10-18: Single static page, served by nginx

- The whole app is one `index.html`. three.js r172 loads from jsDelivr through an import map, and there is no build step.
- The Docker image is `nginx:1.27-alpine` with the page and `nginx.conf` copied in. The container runs read-only with tmpfs for nginx's runtime dirs, and deploying is a rebuild.
- nginx sends `Permissions-Policy: accelerometer=(self), gyroscope=(self)` because phone tilt control needs DeviceMotion on a secure origin. `Cache-Control: no-cache` makes deploys show up straight away.
- Physics runs on the CPU (shape matching at 240 Hz) so the WebGL 2 fallback behaves the same as WebGPU. Only rendering uses the GPU.
