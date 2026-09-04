# MOTION_LIBRARY.md
# Library of visual techniques and motion patterns
# Used by @MOTION when choosing a concept and writing MOTION_SPEC
# This is not a technical stack canon — it is a creative arsenal

> **Principle:** a technique without a concept is decoration. A technique with a concept is an argument.

> **Boundary with Law 26 — read before picking anything from this file.** The law says animation runs on `transform`/`opacity` **in the document flow**, and that is not negotiable: nothing here may animate width, height, margin, padding, `top/left`, or `clip-path` on an element that other content is laid out against. Several techniques below (morph, blur fields, clip-path reveals, size transitions) do exactly that. They are legitimate **only inside a motion island** — an element with reserved space and a fixed box, whose internal animation cannot move a neighbour (`roles/LAYOUT_INVARIANTS.md` §11) — and, for the heavier ones, only on a `statement` surface. Outside an island they are a 🔴 at @QA_VISUAL, and the fact that this file lists them is not a permission. `prefers-reduced-motion` is honoured everywhere, without exception.
> Before choosing any technique — answer: "what does this communicate about the product?"

---

## HOW TO USE

```
Step 1: Define the product's visual metaphor (ROLE_MOTION.md §CONCEPT)
Step 2: Open the corresponding library section
Step 3: Choose one main technique + maximum two supporting ones
Step 4: Record in MOTION_SPEC with justification of why this technique
```

**Rule of three:** no more than three different motion patterns on one page.
More — visual chaos. Fewer — stronger.

---

## PART I: TYPOGRAPHIC TECHNIQUES

*Typography is the most powerful tool. Works without 3D, without WebGL, without libraries.*

---

### T1. Kinetic Heading (Word Reveal)

**What it communicates:** energy, impulse, forward movement. Each word has weight.

**When:** personal brand of developer/designer, product startup with ambitions.

**Technically:** GSAP + SplitType, words fly in from below through overflow:hidden clip.

```javascript
// Soft, confident — for professional brand
gsap.from(words, {
  y: '100%', opacity: 0,
  stagger: 0.04, duration: 0.85,
  ease: 'power4.out'
})

// Sharp, aggressive — for tech/gaming product
gsap.from(words, {
  y: '120%', skewY: 5,
  stagger: 0.03, duration: 0.6,
  ease: 'expo.out'
})

// Soft with rotation — for creative/design studio
gsap.from(words, {
  y: '100%', rotationX: -80,
  transformOrigin: '0% 50% -40px',
  stagger: 0.05, duration: 1.0,
  ease: 'back.out(1.2)'
})
```

**References:** obys.agency, activetheory.net, yacht.co

---

### T2. Morphing Text (Cycling Words)

**What it communicates:** transformation, process, multiplicity. "I do different things".

**When:** multidisciplinary specialists, agencies, products with multiple use cases.

**Pattern:** one word in the heading changes to another through a smooth transition.

```javascript
// Vertical flip — elegant
const words = ['Launch', 'Build', 'Deliver']
let current = 0

function cycle() {
  gsap.to(currentEl, {
    y: -40, opacity: 0, duration: 0.3,
    ease: 'power2.in',
    onComplete: () => {
      current = (current + 1) % words.length
      currentEl.textContent = words[current]
      gsap.fromTo(currentEl,
        { y: 40, opacity: 0 },
        { y: 0, opacity: 1, duration: 0.4, ease: 'power2.out' }
      )
    }
  })
}

setInterval(cycle, 2500)
```

**Transition variants:**
- Vertical flip (up/down) — most common
- Horizontal wipe — cinematic
- Blur transition — technological
- Character scramble — for tech/crypto/cyberpunk

**References:** superhuman.com (old version), framer.com hero

---

### T3. Scramble Text (Code Shuffle)

**What it communicates:** technology, speed, data, AI. "There is a complex system behind this".

**When:** tech products, AI startups, cybersecurity, fintech.

**Pattern:** text "shuffles" through random characters before settling in place.

```javascript
function scrambleText(element, finalText, duration = 1.5) {
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789@#$%'
  let iterations = 0
  const totalIterations = duration * 30 // 30fps

  const interval = setInterval(() => {
    element.textContent = finalText
      .split('')
      .map((char, i) => {
        if (i < Math.floor(iterations / (totalIterations / finalText.length))) {
          return char // already "settled" in place
        }
        return chars[Math.floor(Math.random() * chars.length)]
      })
      .join('')

    if (iterations >= totalIterations) {
      clearInterval(interval)
      element.textContent = finalText
    }
    iterations++
  }, 1000 / 30)
}
```

**References:** pika.art, runway.ml, vercel.com/ai

---

### T4. Split Screen Typography

**What it communicates:** contrast, choice, "before and after", dichotomy.

**When:** products solving a specific pain point, b2b with a clear value prop.

**Pattern:** the screen splits into two zones with different backgrounds. The heading crosses the boundary.

```css
.split-hero {
  display: grid;
  grid-template-columns: 1fr 1fr;
  height: 100vh;
}

.split-left  { background: #0D1B2A; }
.split-right { background: #F0F4F8; }

/* Heading clips differently in each zone */
.title-dark  { color: #F0F4F8; clip-path: inset(0 50% 0 0); }
.title-light { color: #0D1B2A; clip-path: inset(0 0 0 50%); }
```

**References:** epicgames.com, some Apple product pages

---

### T5. Staggered Line Reveal

**What it communicates:** methodical approach, systematic thinking, architectural mindset.

**When:** consulting, developers, agencies with a process-driven approach.

**Pattern:** heading lines appear one after another with a clear rhythm.

```javascript
// Each line — a separate clip container
gsap.from('.line-inner', {
  y: '105%',
  stagger: { amount: 0.5, from: 'start' },
  duration: 0.75,
  ease: 'power3.out'
})
```

**Stagger rhythm by intent:**
- 0.08–0.12s — fast, dynamic
- 0.15–0.2s — confident, methodical  
- 0.25–0.35s — slow, ceremonial

---

### T6. Headline Scale Entrance

**What it communicates:** scale, dominance, confidence.

**When:** when the product/person wants to occupy all the space.

**Pattern:** the heading starts huge and shrinks to its final size.

```javascript
gsap.from('.hero-title', {
  scale: 1.4,
  opacity: 0,
  duration: 1.2,
  ease: 'expo.out',
  transformOrigin: 'left center'
})
```

---

## PART II: BACKGROUND TECHNIQUES

*The background creates atmosphere. Must never compete with content.*

---

### B1. Animated Grid (Living Grid)

**What it communicates:** architecture, system, structure, engineering.

**When:** developers, SaaS products, tech startups.

```css
.grid-bg::before {
  content: '';
  position: absolute;
  inset: -100%;
  background-image:
    linear-gradient(rgba(45,125,210,0.05) 1px, transparent 1px),
    linear-gradient(90deg, rgba(45,125,210,0.05) 1px, transparent 1px);
  background-size: 60px 60px;
  animation: grid-drift 25s linear infinite;
}

/* Grid variants by character */
/* Large grid 80×80 — spacious, airy */
/* Fine grid 32×32 — dense, technical */
/* Diagonal 45deg — dynamic, unconventional */

@keyframes grid-drift {
  to { transform: translate(60px, 60px); }
}
```

**Variations:**
- Diagonal drift — most organic
- Opacity pulse — living breath
- Perspective 3D — depth, tunnel

---

### B2. Gradient Orbs (Atmospheric Gradients)

**What it communicates:** modernity, premium feel, fluid. Not specific — atmospheric.

**When:** design studio, creative agency, products where aesthetics matter.

```css
.orb-bg {
  background:
    radial-gradient(ellipse 60% 50% at 20% 40%,
      rgba(120, 80, 255, 0.25) 0%, transparent 60%),
    radial-gradient(ellipse 50% 60% at 80% 60%,
      rgba(45, 125, 210, 0.15) 0%, transparent 55%),
    radial-gradient(ellipse 40% 40% at 50% 100%,
      rgba(255, 100, 50, 0.1) 0%, transparent 50%),
    #0D1B2A;
  animation: orb-drift 20s ease-in-out infinite alternate;
}

@keyframes orb-drift {
  0%   { filter: hue-rotate(0deg); }
  100% { filter: hue-rotate(30deg); }
}
```

**Orb rule:** maximum three, non-competing colours, opacity < 30%.

---

### B3. Noise/Grain Texture

**What it communicates:** organic feel, tactility, handcrafted quality, premium.

**When:** luxury brands, creative professionals, fashion/lifestyle.

```css
/* SVG noise filter — no images needed */
.noise-bg::after {
  content: '';
  position: fixed;
  inset: 0;
  opacity: 0.04;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  pointer-events: none;
}
```

---

### B4. Dot Matrix / Particle Field

**What it communicates:** data, connections, network, possibilities.

**When:** data products, networking platforms, AI/ML startups.

```javascript
// Canvas particle field — lightweight version without libraries
class ParticleField {
  constructor(canvas, count = 60) {
    this.ctx = canvas.getContext('2d')
    this.particles = Array.from({ length: count }, () => ({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      vx: (Math.random() - 0.5) * 0.3,
      vy: (Math.random() - 0.5) * 0.3,
      r: Math.random() * 2 + 1
    }))
  }

  draw() {
    const { ctx } = this
    ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height)

    this.particles.forEach(p => {
      p.x += p.vx; p.y += p.vy

      // Wrap edges
      if (p.x < 0) p.x = ctx.canvas.width
      if (p.x > ctx.canvas.width) p.x = 0
      if (p.y < 0) p.y = ctx.canvas.height
      if (p.y > ctx.canvas.height) p.y = 0

      ctx.beginPath()
      ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2)
      ctx.fillStyle = 'rgba(45, 125, 210, 0.4)'
      ctx.fill()
    })

    // Connect nearby particles
    this.particles.forEach((a, i) => {
      this.particles.slice(i + 1).forEach(b => {
        const dist = Math.hypot(a.x - b.x, a.y - b.y)
        if (dist < 120) {
          ctx.strokeStyle = `rgba(45,125,210,${0.15 * (1 - dist/120)})`
          ctx.lineWidth = 0.5
          ctx.beginPath()
          ctx.moveTo(a.x, a.y)
          ctx.lineTo(b.x, b.y)
          ctx.stroke()
        }
      })
    })

    requestAnimationFrame(() => this.draw())
  }
}
```

---

### B5. Aurora / Chromatic Gradient

**What it communicates:** fluid, modern, tech-poetry. Between technology and art.

**When:** AI products, creative tools, modern SaaS.

```css
.aurora {
  background:
    linear-gradient(135deg,
      #667eea 0%,
      #764ba2 25%,
      #f093fb 50%,
      #4facfe 75%,
      #00f2fe 100%
    );
  background-size: 400% 400%;
  animation: aurora 12s ease infinite;
}

@keyframes aurora {
  0%   { background-position: 0% 50%; }
  50%  { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

/* Dark overlay on top so text remains readable */
.aurora::before {
  content: '';
  position: absolute;
  inset: 0;
  background: rgba(10, 10, 20, 0.7);
}
```

---

### B6. Code Rain (Terminal Background)

**What it communicates:** developer, system, code-first thinking.

**When:** developer tools, developer personal brand, open source projects.

```javascript
// Matrix but elegant — lines of real code
const codeSnippets = [
  'const product = await build(idea)',
  'git commit -m "ship it"',
  'docker compose up -d',
  '→ deployed to production',
  'npm run typecheck ✓',
  'pg_stat: 0 slow queries',
]

// Render as a barely-visible waterfall
// opacity: 0.04-0.06, font: IBM Plex Mono 11px
// Speed: very slow, different for each line
```

---

## PART III: INTERACTIVE TECHNIQUES

*Interaction creates the feeling of a living product.*

---

### I1. Magnetic Elements

**What it communicates:** attention to detail, tactility, premium interaction.

**When:** any site with buttons — adds the feel of "made with love".

```javascript
function magnetic(el, strength = 0.35) {
  el.addEventListener('mousemove', (e) => {
    const rect = el.getBoundingClientRect()
    const dx = e.clientX - (rect.left + rect.width / 2)
    const dy = e.clientY - (rect.top + rect.height / 2)

    gsap.to(el, {
      x: dx * strength,
      y: dy * strength,
      duration: 0.4,
      ease: 'power2.out'
    })
  })

  el.addEventListener('mouseleave', () => {
    gsap.to(el, {
      x: 0, y: 0,
      duration: 0.7,
      ease: 'elastic.out(1, 0.5)'
    })
  })
}

// Apply to: CTA buttons, social icons, logo
// Do NOT apply to: navbar items, form elements, table rows
```

---

### I2. Custom Cursor

**What it communicates:** non-standard approach, attention to detail, premium experience.

**When:** creative portfolio, design studio, luxury brands.

**Cursor variants:**

```javascript
// Variant A: Trailing dot — quiet, elegant
class TrailingCursor {
  constructor(size = 12, color = 'rgba(45,125,210,0.8)') {
    this.dot = this.createDot(size, color)
    this.pos = { x: 0, y: 0 }
    this.target = { x: 0, y: 0 }
    document.addEventListener('mousemove', (e) => {
      this.target = { x: e.clientX, y: e.clientY }
    })
    this.animate()
  }

  createDot(size, color) {
    const el = document.createElement('div')
    el.style.cssText = `
      width: ${size}px; height: ${size}px;
      background: ${color}; border-radius: 50%;
      position: fixed; pointer-events: none; z-index: 9999;
      transform: translate(-50%, -50%);
      transition: transform 0.1s;
      mix-blend-mode: difference;
    `
    document.body.appendChild(el)
    return el
  }

  animate() {
    this.pos.x += (this.target.x - this.pos.x) * 0.12
    this.pos.y += (this.target.y - this.pos.y) * 0.12
    this.dot.style.left = this.pos.x + 'px'
    this.dot.style.top  = this.pos.y + 'px'
    requestAnimationFrame(() => this.animate())
  }
}

// Variant B: Expanding ring — appears on hover over elements
// Variant C: Text cursor — shows word on hover ("View", "Open")
// Variant D: Blend mode difference — inverts colour under cursor
```

---

### I3. Parallax Layers

**What it communicates:** depth, space, cinematic quality.

**When:** portfolio, storytelling pages, landings where atmosphere matters.

```javascript
// Simple parallax without libraries
function initParallax() {
  const layers = document.querySelectorAll('[data-parallax]')

  document.addEventListener('mousemove', (e) => {
    const cx = window.innerWidth / 2
    const cy = window.innerHeight / 2
    const dx = (e.clientX - cx) / cx
    const dy = (e.clientY - cy) / cy

    layers.forEach(layer => {
      const depth = parseFloat(layer.dataset.parallax) // 0.1 - 0.5
      gsap.to(layer, {
        x: dx * depth * 40,
        y: dy * depth * 30,
        duration: 0.8,
        ease: 'power2.out'
      })
    })
  })
}

// HTML: <div data-parallax="0.3">...</div>
// depth: 0.1 — barely noticeable; 0.5 — expressive
```

**Parallax rule:** mobile — disable completely (prefers-reduced-motion + touch check).

---

### I4. Hover Distortion (Image Ripple)

**What it communicates:** interactivity, fluid feel, living system.

**When:** portfolio cases, product previews, creative agency.

```javascript
// CSS filter distortion on hover — no WebGL
const card = document.querySelector('.project-card')

card.addEventListener('mouseenter', () => {
  gsap.to(card.querySelector('img'), {
    filter: 'url(#distort)',
    duration: 0.3
  })
})

card.addEventListener('mouseleave', () => {
  gsap.to(card.querySelector('img'), {
    filter: 'none',
    duration: 0.5,
    ease: 'power2.out'
  })
})
```

---

### I5. Scroll Velocity Effects

**What it communicates:** dynamics, speed, live response.

**When:** portfolio, storytelling pages — enhances the sense of motion while scrolling.

```javascript
// Elements "stretch" on fast scroll
let lastScrollY = 0
let ticking = false

function updateOnScroll() {
  const scrollY = window.scrollY
  const velocity = scrollY - lastScrollY
  lastScrollY = scrollY

  document.querySelectorAll('[data-scroll-skew]').forEach(el => {
    const intensity = parseFloat(el.dataset.scrollSkew) || 1
    gsap.to(el, {
      skewY: velocity * 0.04 * intensity,
      duration: 0.5,
      ease: 'power3.out'
    })
  })

  ticking = false
}

window.addEventListener('scroll', () => {
  if (!ticking) {
    requestAnimationFrame(updateOnScroll)
    ticking = true
  }
})
```

---

## PART IV: SCROLL-DRIVEN NARRATIVE

*Scroll as a directorial tool — every scroll = a frame of the story.*

---

### S1. Horizontal Scroll Section

**What it communicates:** process, stages, journey. "Come with me".

**When:** process timeline, work stages, product history.

```javascript
// GSAP ScrollTrigger horizontal scroll
gsap.to('.horizontal-track', {
  xPercent: -100 * (sections.length - 1),
  ease: 'none',
  scrollTrigger: {
    trigger: '.horizontal-container',
    pin: true,
    scrub: 1,
    snap: 1 / (sections.length - 1),
    end: () => '+=' + document.querySelector('.horizontal-container').offsetWidth
  }
})
```

---

### S2. Scale + Opacity Scrub

**What it communicates:** zoom in/out, focus, attention.

**When:** product demos, feature highlights.

```javascript
// Element appears and disappears as user scrolls
gsap.fromTo('.feature-visual', 
  { scale: 0.8, opacity: 0 },
  {
    scale: 1, opacity: 1,
    ease: 'none',
    scrollTrigger: {
      trigger: '.feature-section',
      start: 'top 80%',
      end: 'center center',
      scrub: true
    }
  }
)
```

---

### S3. Sticky Hero Transform

**What it communicates:** transformation, product evolution.

**When:** products with a strong value proposition — show "before" and "after".

```javascript
// Hero stays in place, content changes around it
ScrollTrigger.create({
  trigger: '.hero-sticky',
  pin: true,
  start: 'top top',
  end: '+=200%',
  onUpdate: (self) => {
    const progress = self.progress
    // Fade out first text, fade in second
    gsap.set('.text-v1', { opacity: 1 - progress * 2 })
    gsap.set('.text-v2', { opacity: Math.max(0, progress * 2 - 1) })
  }
})
```

---

### S4. Letter Spacing Scrub

**What it communicates:** unfolding, expansion, exhale.

**When:** philosophical headings, mission statements, brand moments.

```javascript
gsap.from('.expanding-title', {
  letterSpacing: '-0.05em',
  scrollTrigger: {
    trigger: '.expanding-title',
    start: 'top 80%',
    end: 'top 20%',
    scrub: true
  }
})
// By the end: letter-spacing: 0.15em — the word "opens up"
```

---

## PART V: 3D AND WEBGL TECHNIQUES

*Only when there is a conceptual reason. WebGL for its own sake — anti-pattern.*

---

### W1. When WebGL Is Justified

**Three questions before WebGL:**
1. Does the 3D scene express the product's essence — or is it decoration?
2. Can the same effect be achieved via CSS/Canvas?
3. Is the performance cost worth the visual result?

**WebGL is justified:**
- The product itself is 3D/spatial (architecture, environmental design)
- Interactive product demo in 3D
- Gaming/entertainment context
- Bruno Simon: his 3D world IS his product (he teaches Three.js)

**WebGL is NOT justified:**
- Abstract sphere "because it looks technical"
- Particle system "because it's beautiful"
- Any 3D element that can be replaced by CSS without loss of meaning

---

### W2. Three.js — Geometric Objects

**What it communicates:** depends on form. Sphere — completion, perfection. Torus — cycle, infinity. Octahedron — precision, crystal. Cube — structure, block.

```javascript
// React Three Fiber — the right approach for React
import { Canvas, useFrame } from '@react-three/fiber'
import { MeshDistortMaterial, Environment } from '@react-three/drei'

function FloatingSphere() {
  const meshRef = useRef()

  useFrame((state) => {
    meshRef.current.rotation.y = state.clock.elapsedTime * 0.2
    meshRef.current.position.y = Math.sin(state.clock.elapsedTime * 0.5) * 0.1
  })

  return (
    <mesh ref={meshRef}>
      <sphereGeometry args={[1, 64, 64]} />
      <MeshDistortMaterial
        color="#2D7DD2"
        attach="material"
        distort={0.3}
        speed={2}
        roughness={0.1}
        metalness={0.8}
      />
    </mesh>
  )
}
```

---

### W3. Shader Material (Liquid / Fluid)

**What it communicates:** fluid, adaptive, living — a product that changes.

**When:** AI products, adaptive systems, creative tools.

```glsl
/* Fragment shader for liquid effect */
uniform float uTime;
uniform vec2 uMouse;
varying vec2 vUv;

void main() {
  vec2 uv = vUv;

  /* Distort UV by time and mouse position */
  float distortX = sin(uv.y * 10.0 + uTime * 0.5) * 0.02;
  float distortY = cos(uv.x * 10.0 + uTime * 0.3) * 0.02;

  /* Mouse influence */
  vec2 mouseInfluence = (uMouse - 0.5) * 0.05;
  uv += vec2(distortX, distortY) + mouseInfluence;

  /* Color */
  vec3 color1 = vec3(0.18, 0.49, 0.82); /* --accent */
  vec3 color2 = vec3(0.05, 0.11, 0.17); /* --bg */
  vec3 color = mix(color2, color1, uv.y + sin(uTime * 0.2) * 0.1);

  gl_FragColor = vec4(color, 1.0);
}
```

---

### W4. Image Distortion on Hover

**What it communicates:** interactivity, fluid feel, "this product is alive".

**When:** portfolio cases, product previews.

```javascript
// Without Three.js — via SVG displacement filter
// Change scale value on hover
card.addEventListener('mouseenter', () => {
  gsap.to('#displacementScale', {
    attr: { scale: 80 },
    duration: 0.5
  })
})

card.addEventListener('mouseleave', () => {
  gsap.to('#displacementScale', {
    attr: { scale: 0 },
    duration: 0.8,
    ease: 'power2.out'
  })
})
```

---

## PART VI: TRANSITION PATTERNS

*Page and state transitions.*

---

### TR1. Page Transition — Curtain

**What it communicates:** cinematic quality, the scene changes.

```javascript
// Overlay drops covering the page, then lifts revealing the new one
const curtain = document.querySelector('.page-curtain')

async function navigateTo(url) {
  // Close
  await gsap.to(curtain, {
    scaleY: 1, duration: 0.5,
    ease: 'power3.in',
    transformOrigin: 'top'
  })

  // Switch content
  window.location.href = url
}

// On the new page — open
gsap.to(curtain, {
  scaleY: 0, duration: 0.5,
  ease: 'power3.out',
  transformOrigin: 'bottom',
  delay: 0.1
})
```

---

### TR2. Smooth Scroll (Lenis)

**What it communicates:** polish, premium feel. Scroll as physics.

```javascript
import Lenis from 'lenis'

const lenis = new Lenis({
  duration: 1.2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  orientation: 'vertical',
  smoothWheel: true
})

// Sync with GSAP ScrollTrigger
lenis.on('scroll', ScrollTrigger.update)
gsap.ticker.add((time) => { lenis.raf(time * 1000) })
gsap.ticker.lagSmoothing(0)
```

---

## PART VII: PERFORMANCE AND RULES

### Performance Rules

```
GPU-friendly (animate ONLY these):
  ✅ transform: translate / scale / rotate
  ✅ opacity
  ✅ filter (with caution)

CPU-heavy (NEVER animate):
  ❌ width, height, top, left, right, bottom
  ❌ margin, padding
  ❌ background-color (on every frame)
  ❌ box-shadow (repaints)
  ❌ border-radius (ok with scale)
```

### Will-change Protocol

```css
/* Add BEFORE animation */
.will-animate {
  will-change: transform, opacity;
}

/* Remove AFTER animation — otherwise memory leak */
element.addEventListener('animationend', () => {
  element.style.willChange = 'auto'
})
```

### prefers-reduced-motion (mandatory)

```css
@media (prefers-reduced-motion: reduce) {
  /* Remove all background animations */
  .grid-bg::before { animation: none; }
  .aurora { animation: none; }

  /* Simplify transitions */
  * {
    animation-duration: 0.001ms !important;
    transition-duration: 0.001ms !important;
  }
}
```

### Mobile Performance

```javascript
const isMobile = window.innerWidth < 768
const isReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches

// Do not initialise heavy effects on mobile
if (!isMobile && !isReducedMotion) {
  initParticleField()
  initCustomCursor()
  initParallax()
}

// Lightweight effects — on all devices
initKineticTitle()
initGridBackground() // CSS only — OK
```

---

## PART VIII: LIBRARIES AND WHEN TO CHOOSE WHICH

| Task | Library | Why |
|--------|-----------|--------|
| Text kinetics, timeline | GSAP + SplitType | Best physics control |
| React components | Framer Motion | Declarative, works well with React |
| Scroll-driven | GSAP ScrollTrigger | Most accurate scrub |
| Smooth scroll | Lenis | Best scroll physics |
| 3D scenes | React Three Fiber | If React; otherwise vanilla Three.js |
| Canvas animations | Vanilla JS + RAF | No extra dependencies |
| CSS animations | Native CSS | Fastest, GPU-native |
| SVG animations | GSAP MorphSVG | Shape morphing |
| Loading/routing | Barba.js | Page transitions |

### Bundle size (important!)

```
GSAP core:       ~23kb gzip
SplitType:        ~5kb gzip
Framer Motion:   ~45kb gzip
Three.js:       ~150kb gzip (!)
R3F + Drei:     ~200kb gzip (!!)
Lenis:            ~3kb gzip

Rule: if total motion bundle > 100kb — reconsider the approach.
WebGL only if conceptually necessary.
```

---

Reference: `roles/ROLE_MOTION.md` · Awwwards · CSS-Tricks Animation · GSAP docs · Three.js Journey (Bruno Simon) · The Illusion of Life (Disney 12 principles)
Version: 1.0 | 2026-05-22
