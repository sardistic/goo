# goo

Wobble Lab: a soft-body jelly toy in three.js (WebGPU, with WebGL 2 fallback). Live at https://goo.sardistic.com.

- **Objects:** fluted mould, cube, dome, ring, star, ball
- **Surfaces:** clear, layered, glitter, gummy, milky, pearl, mochi
- **Consistencies:** jelly, gummy, bouncy, pudding, slime and dough (slime and dough deform plastically)
- **Worlds:** studio, neon diner, picnic, moon base (0.17 g)
- **Interaction:** drag to pull or fling, tap to poke, twirl, toss, spin or shake the plate. On phones, **Tilt & shake phone** steers gravity with the device's motion sensors (HTTPS only; iOS asks for permission).

## How it works

Everything is in `index.html`. The body is a cloud of particles simulated with region-based shape matching (Müller et al.), stepped at 240 Hz on the CPU. Each overlapping region fits a rotation plus some affine squash and normalizes volume. Plastic consistencies let a region's rest shape creep once strain passes a yield threshold. The render surface is projected onto each shape's signed distance function, then skinned to the regions' best-fit affine frames. Normals come from the cofactor matrix. Materials are TSL node materials.

## Run locally

Any static server works, for example `python -m http.server`, then open http://localhost:8000. Query parameters preselect a setup: `?shape=ring&surface=layered&env=diner&flavor=raspberry&consist=slime`.

## Container

`docker build -t goo .` produces an nginx image that serves the page on port 80.
