# Decisions

## 2026-10-18: Single static page, served by nginx

- The whole app is one `index.html`. three.js r172 loads from jsDelivr through an import map, and there is no build step.
- The Docker image is `nginx:1.27-alpine` with the page and `nginx.conf` copied in. The container runs read-only with tmpfs for nginx's runtime dirs, and deploying is a rebuild.
- nginx sends `Permissions-Policy: accelerometer=(self), gyroscope=(self)` because phone tilt control needs DeviceMotion on a secure origin. `Cache-Control: no-cache` makes deploys show up straight away.
- Physics runs on the CPU (shape matching at 240 Hz) so the WebGL 2 fallback behaves the same as WebGPU. Only rendering uses the GPU.

## 2026-10-18: Stiffness, plasticity and lighting

- Overlapping-region shape matching makes the body much softer than one region's `alpha` suggests. A headless sim measured α 0.03 settling to about 45% of rest height (the "flat/2D" look) with only about 0.3 Hz of sway. α 0.25 stands at about 93% and still sways around 1 Hz. Firmness now maps Bloom 80–360 to α 0.06–0.9, and the readout shows measured height instead of a formula frequency.
- A volume-preserving affine with high `beta` (Squish) lets a body pancake at no cost, so the soft presets keep Squish at 55% or below.
- Plastic flow uses excess strain past yield, and it is gated by a disturbance envelope (grabs, pokes, throws, hard impacts) plus a constant ooze rate for slime only. Without the gate, plastic flow under self-weight runs away and melts the body.
- Shadows come from a PCF-soft shadow map on the key light, using the deformed mesh. Transmissive surfaces set `castShadowNode` so their shadows are tinted by the gel: bright where faces are square to the light, dark at grazing edges. The environment maps carry only soft fill, because a bright sun or ceiling panel in the IBL relights the shadowed areas. Dispersion is off because it shifted amber toward magenta in the WebGL 2 fallback.
- The interior "volume" is a 6-step march of 3D noise along the view ray. The noise is mapped into rest space through the body's rigid best fit (`uBodyRT`, `uBodyC`, `uBodyC0`), so it rides with the jelly.
