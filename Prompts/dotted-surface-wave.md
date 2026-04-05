# Dotted Surface Wave Effect

## Prompt

> Add a 3D particle wave animation underneath the hero heading text on all sub-pages (not the homepage). The effect uses Three.js to render gold-tinted dots that undulate in a sine wave pattern, creating a premium ambient visual below the page title.

## Tech Stack Constraints

- Single HTML file with inline CSS and JS — no React, no npm, no build tools
- Three.js loaded via CDN: `https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js`
- Tailwind CSS via CDN with custom config
- Dark luxury theme: background `#0a0a0a`, accent gold `#C5A55A`
- No `next-themes`, no `shadcn` — adapted from React component to vanilla JS

## Effect Details

1. **Gold-tinted dots** arranged in a 3D grid (40x25 on desktop, 25x15 on mobile)
2. **Sine wave animation** — dots undulate with two overlapping sine waves for organic movement
3. **Edge fade** — top and bottom edges fade via CSS mask gradient so it blends into the dark background
4. **Subtle dark gradient backdrop** — `via-lux-surface/60` gradient behind the wave for contrast
5. **Performance optimized** — reduced particle count on mobile, IntersectionObserver pauses when off-screen, pixel ratio capped at 2x
6. **Reduced motion** — renders one static frame for users who prefer reduced motion

## Where to Add

- Add to the hero section of every page **except** `index.html` (homepage)
- Placed between the hero heading content and the next section
- Container: `h-[200px] md:h-[300px]` with `-mt-8` to tuck under the heading

## Dependencies

Add Three.js CDN after the Tailwind CDN in `<head>`:

```html
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
```

## Hero Section Modification

Change the hero content div padding from `py-24 md:py-32` to `pt-24 md:pt-32 pb-4` so the wave has room and isn't clipped by `overflow-hidden`.

## HTML to Add (inside hero `<section>`, after the content `</div>`)

```html
<!-- Dotted Surface Wave -->
<div class="relative w-full h-[200px] md:h-[300px] -mt-8">
  <div class="absolute inset-0 bg-gradient-to-b from-transparent via-lux-surface/60 to-transparent"></div>
  <div id="dotted-surface" class="absolute inset-0 pointer-events-none"
       style="mask-image: linear-gradient(to bottom, transparent 0%, black 15%, black 85%, transparent 100%);
              -webkit-mask-image: linear-gradient(to bottom, transparent 0%, black 15%, black 85%, transparent 100%);"></div>
</div>
```

## JavaScript (add before "Reveal on scroll" in the `<script>` block)

```javascript
// ===== DOTTED SURFACE WAVE (Three.js) =====
(function() {
  const container = document.getElementById('dotted-surface');
  if (!container || typeof THREE === 'undefined') return;

  const isMobile = window.innerWidth < 768;
  const SEPARATION = isMobile ? 120 : 100;
  const AMOUNTX = isMobile ? 25 : 40;
  const AMOUNTY = isMobile ? 15 : 25;

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(
    50, container.offsetWidth / container.offsetHeight, 1, 10000
  );
  camera.position.set(0, 350, 900);
  camera.lookAt(0, 0, 0);

  const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.setSize(container.offsetWidth, container.offsetHeight);
  renderer.setClearColor(0x000000, 0);
  container.appendChild(renderer.domElement);

  const positions = [];
  const colors = [];
  for (let ix = 0; ix < AMOUNTX; ix++) {
    for (let iy = 0; iy < AMOUNTY; iy++) {
      positions.push(
        ix * SEPARATION - (AMOUNTX * SEPARATION) / 2,
        0,
        iy * SEPARATION - (AMOUNTY * SEPARATION) / 2
      );
      colors.push(0.95, 0.82, 0.45); // Bright gold
    }
  }

  const geometry = new THREE.BufferGeometry();
  geometry.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
  geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));

  const material = new THREE.PointsMaterial({
    size: isMobile ? 7 : 10,
    vertexColors: true,
    transparent: true,
    opacity: 0.85,
    sizeAttenuation: true,
  });
  scene.add(new THREE.Points(geometry, material));

  let count = 0, animationId;
  function animate() {
    animationId = requestAnimationFrame(animate);
    const posArr = geometry.attributes.position.array;
    let i = 0;
    for (let ix = 0; ix < AMOUNTX; ix++) {
      for (let iy = 0; iy < AMOUNTY; iy++) {
        posArr[i * 3 + 1] =
          Math.sin((ix + count) * 0.3) * 40 +
          Math.sin((iy + count) * 0.5) * 40;
        i++;
      }
    }
    geometry.attributes.position.needsUpdate = true;
    renderer.render(scene, camera);
    count += 0.06;
  }

  // Only animate when visible
  const surfaceObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) animate();
      else cancelAnimationFrame(animationId);
    });
  }, { threshold: 0.1 });
  surfaceObserver.observe(container);

  // Resize handler
  window.addEventListener('resize', () => {
    camera.aspect = container.offsetWidth / container.offsetHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(container.offsetWidth, container.offsetHeight);
  });

  // Respect reduced motion
  if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    const posArr = geometry.attributes.position.array;
    let i = 0;
    for (let ix = 0; ix < AMOUNTX; ix++) {
      for (let iy = 0; iy < AMOUNTY; iy++) {
        posArr[i * 3 + 1] = Math.sin(ix * 0.3) * 40 + Math.sin(iy * 0.5) * 40;
        i++;
      }
    }
    geometry.attributes.position.needsUpdate = true;
    renderer.render(scene, camera);
  }
})();
```

## Tuning Parameters

| Parameter | Value | Effect |
|-----------|-------|--------|
| Dot color | `(0.95, 0.82, 0.45)` | Bright gold — visible on dark bg |
| Dot size | 10px desktop / 7px mobile | Large enough to see clearly |
| Dot opacity | 0.85 | High visibility |
| Wave amplitude | 40px | Height of sine wave peaks |
| Wave speed | 0.06 per frame | Slow, ambient movement |
| Separation | 100px desktop / 120px mobile | Space between dots |
| Grid size | 40x25 desktop / 25x15 mobile | Particle count |
| Camera Y | 350 | Looking down at the wave |
| Camera Z | 900 | Distance from the grid |
| Container height | 200px mobile / 300px desktop | Visible area |

## Original Source

Adapted from a React + Three.js `DottedSurface` component that used:
- `next-themes` for dark/light mode detection (removed — we're always dark)
- `React.useEffect` + `useRef` (converted to IIFE with vanilla DOM)
- `THREE.Fog` (removed — not needed with transparent background)
- Full viewport sizing (changed to contained element sizing)
