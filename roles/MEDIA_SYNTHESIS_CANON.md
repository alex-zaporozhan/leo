# MEDIA_SYNTHESIS_CANON — the knowledge layer for @MEDIA_ENGINEER

> **Pairs with** `roles/ROLE_MEDIA_ENGINEER.md` the way `roles/SEO_CANON.md` pairs with `roles/ROLE_SEO.md` and `roles/RAG_CANON.md` with `roles/ROLE_AI_ENGINEER.md`: the **role is the process**, this canon is the **knowledge**.
> **Principle (inherited from VISUAL_CONCEPT_PROTOCOL):** taste that lives in files is cheap and portable; taste that lives in the orchestrator model is expensive and dies on a model swap. This file is a **recipe book**, not inspiration — every aesthetic choice is a **controlled token** that maps to a prompt fragment. Composer assembles from here; it does not invent from its head.
> **Version:** 1.0 · 2026-07-22

---

## 0. How to use this canon

An asset's prompt is **assembled**, never hand-written (role §P1). This canon supplies the controlled vocabularies for four of the layers:

```
FINAL PROMPT =
  SUBJECT        (unique scene — from the manifest row)
+ CINE_SPINE     (the project's DoP contract — §8, fixed for the whole series)
+ WORLD          (surface/material module — role §W1)
+ CLASS_NOTE     (LIGHT / PHOTO — role §P4)
+ CORE           (register + real-camera + no-text law — role §P2)
+ NEGATIVE       (axis-tuned — §11)
```

**Rules that never bend here:**
- **Colour is described in words, never in hex/codes** (role §P2). Exact brand colour lives in CSS (role §P3). This canon controls *perceived* colour (grade, temperature, palette relationships); code controls *exact* colour.
- **Declare the REGISTER first** (mirrors `.cursorrules` Law 33). `statement-trust` / institutional → restraint is the craft: gentle movement, motivated light, muted grade. `statement-bold` / campaign → scale, anamorphic, committed gesture. The register sets the *default position of every dial below*.
- **One choice per axis, and the axes must agree** (§1 coherence law). A shot is not a pile of adjectives; it is a stack of decisions that share one logic.
- **Gear names are style anchors, not guarantees.** Naming a camera/lens/stock nudges a *look*; it does not deliver literal optics. Use **one** camera-look and **one** lens-look per series (§5).

---

## 1. The Visual DNA of a shot — the eight axes (coherence spine)

Every still or frame is a stack of eight decisions. **A coherent set fixes axes 4–8 once (the CINE_SPINE) and varies only 1–3 per asset.** Three random frames placed side by side must read as one photographer / one film.

| # | Axis | Controls | Canon §|
|---|------|----------|--------|
| 1 | **Subject class** | portrait / still-life / architecture / environment / product / character | §2 |
| 2 | **Medium & render** | photoreal / stylized-3D / 2D-illustration / abstract | §2 |
| 3 | **Format & framing** | aspect, shot size, angle, composition | §3.5, §4.2 |
| 4 | **Optics** | focal length, aperture/DoF, distortion, lens character | §3.1–3.2 |
| 5 | **Light** | direction, quality, ratio, colour temperature, time of day | §3.3 |
| 6 | **Colour** | palette relationship, grade, contrast, saturation | §6 |
| 7 | **Camera & motion** (video) | move, speed, easing, lens-in-motion | §4 |
| 8 | **Texture & finish** | grain, sharpness, atmosphere, halation | §3.6 |

**Coherence law:** if two frames disagree on light direction, colour temperature, lens compression or grain, they are two different shoots — a 🔴 series-coherence failure (role §W1, §Q2). Fixing axes 4–8 in the CINE_SPINE is how you prevent it structurally.

---

## 2. Image types — taxonomy, default axes, failure mode

Pick the type first; it sets sensible defaults for the other axes.

| Type | Use for | Default optics/light | Known failure mode (guard in NEGATIVE) |
|------|---------|----------------------|----------------------------------------|
| **Photoreal editorial** | people/context, hero, professions | 35–50mm, motivated daylight, f/2.8–4 | stock-cheese, uncanny faces, over-shallow |
| **Studio product still-life** | documents, devices, objects | 90–100mm macro, single soft key + rim, f/5.6–8 | CGI/clay toy, plastic, isolated-on-white |
| **Architecture / interior** | spaces, campus, environments | 24mm, deep focus f/8–11, cool daylight | keystone distortion, dead/empty, real building logos |
| **Environment / landscape** | wide establishing | 24–35mm, golden/blue hour, deep focus | postcard cliché, HDR crush |
| **Portrait** | one person, authority | 85mm, soft key (Rembrandt/loop), f/1.8–2.8 | identity of a real/known person → §Q3 legal |
| **Stylized 3D** | mascots, explainer characters | soft global illumination, warm | clay toy, dead eyes, uncanny valley |
| **2D illustration / editorial** | concepts, blog, abstract ideas | flat/vector or textured, limited palette | generic clip-art, AI-slop gradients |
| **Animated character** | brand character, sprite | consistent turnaround via references | inconsistent face across frames → §R3 refs |
| **Abstract / texture** | backgrounds under text | soft light, low detail, one accent | rainbow flood, busy, fights the text |
| **Diagram / technical** | flows, UI, charts | — | **not a generated image** — this is code/SVG (role §W3) |

**Register modulation:** for `statement-trust`, prefer photoreal-editorial, product-still and architecture; treat stylized-3D and heavy illustration as accents, not the spine. Stylized worlds fit playful/consumer registers.

---

## 3. Photography craft — controlled vocabularies

### 3.1 Focal length (full-frame equivalent) → effect → prompt phrase

| Focal | Effect | Prompt phrase |
|-------|--------|---------------|
| 14–24mm | ultrawide, establishing, dramatic space, visible distortion | `shot on a 24mm wide-angle lens, expansive establishing framing` |
| 35mm | reportage/environmental, natural storytelling | `35mm lens, natural environmental framing` |
| 50mm | "normal", neutral human perspective | `50mm lens, natural undistorted perspective` |
| 85mm | classic portrait, flattering compression, isolation | `85mm portrait lens, gentle compression, subject cleanly separated from the background` |
| 100mm macro | product detail, texture | `100mm macro lens, crisp close product detail` |
| 135mm+ | strong tele compression, deep isolation, creamy bokeh | `135mm telephoto, strong compression, background compressed into soft bokeh` |

### 3.2 Aperture / depth of field

| Aperture | DoF | Prompt phrase |
|----------|-----|---------------|
| f/1.4–2 | very shallow, creamy bokeh | `shallow depth of field, creamy background bokeh, only the subject in focus` |
| f/2.8–4 | isolation with some context | `moderate shallow focus, subject sharp, background gently soft` |
| f/5.6–8 | balanced, most in focus | `balanced focus, subject and immediate surroundings sharp` |
| f/11–16 | deep focus (architecture/landscape) | `deep focus, sharp from foreground to background` |

Guard: everything shallow reads as amateur "AI portrait mode" — match DoF to type (architecture is deep, product is medium, portrait is shallow).

### 3.3 Lighting — setups, quality, ratio, temperature

**Setups (direction + shape):**

| Setup | Look | Prompt phrase |
|-------|------|---------------|
| Three-point | clean, controlled | `three-point lighting, soft key, gentle fill, subtle rim` |
| Rembrandt | classic, dimensional | `Rembrandt lighting, key at forty-five degrees, small triangle of light on the shadow cheek` |
| Loop | natural portrait | `soft loop lighting from the upper left` |
| Butterfly / Paramount | beauty, symmetric | `butterfly lighting from slightly above, soft shadow under the nose` |
| Split | dramatic, editorial | `split lighting, one side of the face in shadow` |
| Rim / kicker | separation, premium | `a soft rim light along one edge for separation` |
| Window / north light | soft daylight, editorial | `soft directional daylight from a large window on the left` |
| Motivated practicals | realistic, cinematic | `light motivated by visible practical sources in the scene` |

**Quality:** `soft` (large source, gentle shadows) vs `hard` (small source, crisp shadows). **Institutional default = soft, motivated.**

**Key-to-fill ratio (contrast of the face/subject):** `1:1` flat/high-key · `2:1` gentle dimensional · `4:1` dramatic · `8:1+` chiaroscuro/low-key. Phrase: `a two-to-one key-to-fill ratio, gently dimensional, shadows open and readable`.

**Colour temperature / time of day:**

| Condition | Feel | Prompt phrase |
|-----------|------|---------------|
| Tungsten ~2700K | warm, intimate, evening | `warm tungsten interior light` |
| Golden hour | warm, hopeful, premium | `warm golden-hour daylight, long soft shadows` |
| Daylight ~5600K | clean, neutral, trustworthy | `neutral daylight, clean white balance` |
| Overcast / softbox sky | even, editorial | `soft overcast daylight, even and shadowless` |
| Blue hour | calm, cool, quiet | `cool blue-hour twilight` |
| Open shade ~7000K+ | cool, contemplative | `cool open-shade light` |

The **key move** for a coherent set: fix ONE temperature system for the whole series (e.g. *warm key upper-left + cool shadows lower-right*) and never break it.

### 3.4 Composition

`rule of thirds` · `centred symmetry` (formal, institutional) · `leading lines` · `negative space` (for text — role §P4) · `frame within a frame` · `foreground occlusion / layered depth` · `flat-lay / top-down` (documents). Phrase the intent, e.g. `centred symmetrical composition with generous negative space on the left for a headline`.

### 3.5 Format & shot size

Aspect per consumption (role §P4/§6). Shot sizes: `extreme wide` · `wide` · `medium` · `medium close-up` · `close-up` · `extreme close-up / macro insert`. Angle: `eye-level` (neutral) · `low-angle` (authority) · `high-angle` (overview) · `top-down` (flat-lay) · `dutch` (unease — avoid for trust). 

### 3.6 Texture & finish

`fine film grain` · `clean digital` · `subtle halation on highlights` (Cinestill look) · `matte, lifted blacks` · `crisp micro-contrast`. Guard against the "AI plastic sheen" with `natural skin texture, no plastic sheen, real material grain`.

### 3.7 Film-stock & body looks (grade anchors — one per series)

| Anchor | Look |
|--------|------|
| Kodak Portra 400 | warm, flattering skin, soft contrast — editorial default |
| Kodak Ektar | saturated, punchy — landscape/product |
| Fuji Pro 400H | pastel, soft greens, airy |
| Cinestill 800T | tungsten night, halation glow |
| Ilford HP5 / Kodak Tri-X | classic B&W grain |
| Hasselblad / Phase One medium format | ultra-clean detail, gentle rendering — premium commercial |
| Leica M | crisp, high micro-contrast, rangefinder character |

---

## 4. Cinematography craft — for video

### 4.1 Camera movement vocabulary

| Move | Feel | Prompt phrase | Register note |
|------|------|---------------|---------------|
| Locked-off / static | formal, stable, premium | `locked-off static camera` | ✅ trust default |
| Slow push-in (dolly-in) | quiet emphasis | `an extremely slow, subtle dolly push-in` | ✅ trust default |
| Pull-out | reveal context | `a slow dolly pull-out revealing the space` | ✅ |
| Lateral tracking / trucking | parallax, motion | `a slow lateral tracking move, gentle parallax` | ✅ |
| Crane / jib | scale, reveal | `a slow crane move rising to reveal` | use sparingly |
| Orbit / arc | product showcase | `a slow arc orbiting the object` | ✅ product |
| Steadicam / gimbal float | smooth flow | `a smooth floating gimbal move` | ⚠️ can feel promo |
| Handheld | energy, realism | `subtle handheld movement` | ⚠️ breaks trust calm |
| Rack focus | shift attention | `a slow rack focus from foreground to subject` | ✅ elegant |
| Dolly zoom (vertigo) | unease/drama | `a dolly-zoom` | 🔴 rarely for institutional |
| Whip pan | transition | `a fast whip pan` | campaign only |

**Institutional/statement-trust rule:** movement is *barely there*. One slow move per shot, eased in and out, ≤ ~5% of frame travel.

### 4.2 Shot size, angle, lens in motion

Shot sizes and angles as §3.5. Lens choice in motion: `anamorphic` = 2.39:1 widescreen, oval bokeh, horizontal blue flares, overtly cinematic; `spherical` = natural round bokeh. Phrase: `anamorphic widescreen look, subtle horizontal lens flare` OR `clean spherical lens, natural rendering`.

### 4.3 Motion light & atmosphere

The things that *should* move in a calm loop are light and air, not the structure: `dust motes drifting in the sunbeam` · `a soft volumetric god-ray shifting slowly` · `gentle light drift across the surface` · `slow reflections`. Avoid flicker (accessibility) and per-frame relighting jumps.

### 4.4 Speed, easing, frame feel

`extremely slow and steady` · `eased in and out` · `24fps cinematic motion` · `slow-motion` · `timelapse`. For web loops keep it 24fps-feel and gentle.

### 4.5 The seamless loop recipe (institutional hero)

```
first_frame = last_frame (role §R3). Prompt: "Seamless loop. The camera does an
extremely subtle, slow push-in and settles back to the exact starting frame.
CRITICAL: the architecture, the window and its frame stay perfectly still and
unchanged. Only the light, the floating dust motes in the sunbeam, and soft
reflections move. Calm, premium, editorial. No people, no text, no captions."
```

Model routing: `veo-3.1` (final) / `veo-3.1-fast` (draft); alternatives `kling-v3.0-pro`, `seedance-2.0` — probe per §5/role §R2. Audio off for web loops.

---

## 5. Camera & lens reference list (style anchors — pick ONE of each per series)

> Naming gear nudges a *look*. Choose one camera-look and one lens-look and hold them across the whole set for coherence. Do not stack five brand names in one prompt.

### 5.1 Cinema camera looks (video)

| Anchor | What it connotes | Phrase |
|--------|------------------|--------|
| ARRI Alexa 35 / Mini LF | the filmic gold standard — gentle highlight rolloff, natural skin, wide latitude | `shot on an ARRI Alexa, filmic highlight rolloff, natural colour` |
| ARRI Alexa 65 | large-format epic, soft falloff | `large-format Alexa 65 look, soft depth falloff` |
| RED V-Raptor | crisp, high-resolution, punchy | `RED cinema look, crisp high resolution` |
| Sony Venice 2 | rich colour, clean dual-ISO | `Sony Venice look, rich cinematic colour` |
| Blackmagic URSA | indie filmic, affordable character | `Blackmagic filmic look` |
| 35mm film / Kodak Vision3 | organic grain, halation | `shot on 35mm film, organic grain` |
| 65mm / IMAX | epic scale, clarity (Dune/Oppenheimer) | `65mm large-format clarity` |

### 5.2 Lens looks (film & photo)

| Anchor | Character | Phrase |
|--------|-----------|--------|
| ARRI Signature Primes | gentle, warm, soft rolloff | `ARRI Signature Prime rendering, gentle and warm` |
| ARRI/Zeiss Master Primes | clean, sharp, neutral | `Master Prime clarity, neutral and sharp` |
| Cooke S4/S7 ("Cooke Look") | warm, soft, flattering skin | `the Cooke look — warm, gentle, flattering skin` |
| Zeiss Supreme Primes | modern, clean, controlled flare | `Zeiss Supreme rendering, clean modern` |
| Panavision / Cooke Anamorphic | oval bokeh, blue horizontal flares, widescreen | `anamorphic rendering, oval bokeh, subtle blue horizontal flare` |
| Vintage (Canon K35, Lomo, Super Speeds) | flarey, characterful, soft | `vintage lens character, soft flares, gentle falloff` |
| Leica Summilux / Thalia | crisp, high micro-contrast | `Leica rendering, crisp high micro-contrast` |

### 5.3 Photo bodies (stills)

Medium format (Hasselblad · Phase One · Fuji GFX) → ultra-clean editorial/commercial. Leica M/Q → rangefinder character. Full-frame (Canon R5 · Sony A1 · Nikon Z) → generic pro. Pair with a film-stock grade anchor (§3.7).

---

## 6. Colour palette control

### 6.1 Palette relationships (choose one)

| Scheme | Feel | Phrase |
|--------|------|--------|
| Monochromatic | calm, refined | `a monochromatic palette in cool blues` |
| Analogous | harmonious | `an analogous palette of blues and violets` |
| Complementary | punchy, energetic | `a complementary palette` |
| Warm/cool split | cinematic depth | `warm highlights against cool shadows` |
| Duotone | graphic, brand | `a two-tone duotone treatment` (usually a CSS layer — role §P3) |

### 6.2 Grade language

`filmic, warm highlights and cool shadows` (natural default) · `teal-and-orange` (⚠️ overused — use only deliberately) · `bleach-bypass` (desaturated, silvery, high-contrast) · `matte / lifted blacks` (soft modern) · `pastel / muted editorial` · `high-contrast punchy` · `low-contrast, gentle` · `monochrome`.

### 6.3 Translating a brand palette without hex (LAW A)

The manifest/CINE_SPINE describes brand colour **in words** derived from the tokens: e.g. tokens say the field is a pale lavender-white → prompt says `a cool lavender-white surface`; the accent is indigo→magenta → prompt says `a soft accent drifting from cool blue through indigo to muted magenta`. **Exactness is never in the prompt** — the precise value is applied later in CSS over the clean plate (role §P3). Saturation/contrast are dials: `slightly muted, restrained saturation` for trust; `rich, saturated` for campaign.

---

## 7. Reference library — curated premium sources per scenario

> **Extraction protocol (inherited from `roles/CONCEPT_ANATOMY.md`):** a reference contributes **exactly one** technique, recorded as `SOURCE → EXTRACT → TRANSFER → NOT TAKING`. Never a mood-board dump. ≤2 references per shot. A reference names a *technique to transfer*, not a picture to copy.

### 7.1 Cinematography (DP looks to extract)

| DP | Extract |
|----|---------|
| Roger Deakins | motivated single-source naturalism, deep controlled light, restraint — **the institutional default** |
| Greig Fraser (Dune) | warm large-format softness, atmospheric haze |
| Hoyte van Hoytema | anamorphic + large format, cool palette, practical light, texture |
| Emmanuel Lubezki | natural-only light, wide lenses, magic hour, flow |
| Bradford Young | low-key elegance, rich skin tones, confident underexposure |

### 7.2 Still photography

| Photographer / source | Extract |
|-----------------------|---------|
| Peter Lindbergh | natural B&W, honest skin, restraint |
| Annie Leibovitz | environmental narrative portraiture |
| Platon | bold, tight, high-contrast authority portrait |
| Hélène Binet / Iwan Baan | architectural light-and-shadow / buildings *in use* |
| Todd Hido | moody, atmospheric light |
| Kinfolk · Cereal · Aesop · Apple product | calm editorial minimalism, negative space, premium restraint |

### 7.3 3D / render & motion (for the code/plate track)

Pixar / DreamWorks (appealing stylized character, soft GI) · ILM (photoreal VFX integration) · for **real-time WebGL** (role §W3, owned by @MOTION/@DEV): Active Theory · Lusion · Bruno Simon · Cuberto · Obys · Locomotive.

**Golden rule:** ≤1 SaaS/tech reference per concept (mirrors Law 28); do not let "generic startup gradient" become the world.

---

## 8. The single-DP system — building one operator's eye in the prompt

This is the crown of the canon: how 50 assets feel shot by **one** director of photography on **one** day.

### 8.1 The CINE_SPINE (a project's DoP contract)

Fix these once per project, in words, as a reusable fragment (a sibling of the WORLD module). Every asset inherits it; only SUBJECT changes.

```
CINE_SPINE (example — statement-trust institute):
"Cinematography: one director of photography, one shoot. Shot on an ARRI Alexa
with gentle Signature-Prime rendering; a 40mm-equivalent field of view;
motivated soft daylight from the upper left with cool, open shadows falling to
the lower right (a two-to-one ratio, never crushed); a restrained filmic grade —
warm highlights, cool shadows, slightly muted saturation; fine natural grain, no
plastic sheen; calm, symmetrical, generous negative space. The same light,
lens character and grade across every frame."
```

### 8.2 Assembly

```
FINAL = SUBJECT + CINE_SPINE + WORLD + CLASS_NOTE + CORE + NEGATIVE
```

`SUBJECT` carries only axes 1–3 (what/where/framing). `CINE_SPINE` carries axes 4–8 (optics/light/colour/motion/texture) and is **identical** across the series. `WORLD` carries the surface/material. This is why the set coheres.

### 8.3 Worked example (one still, statement-trust)

```
SUBJECT: "A digital tachograph device and a plain driver smart-card resting on
the desk, a real professional workspace, medium close-up, eye-level, centred with
open space on the left."
+ CINE_SPINE (§8.1)
+ WORLD ("civic-real" — a real workspace surface, not a studio marble table)
+ CLASS_NOTE (PHOTO — lower-left falls into gentle shadow for light text later)
+ CORE (institutional register, real camera not 3D, absolutely no text/logos/marks)
+ NEGATIVE (§11, product-still tuned: no CGI/clay/toy, no plastic)
```

### 8.4 Coherence tests (owned by @MEDIA_ENGINEER, mirrored by @QA_VISUAL)

Place three finished frames side by side. They pass only if they share: light **direction** · colour **temperature** · lens **compression/DoF language** · **grade** · **grain**. One outlier → re-roll that asset with the same CINE_SPINE.

---

## 9. Style-axis presets (ready complete looks — pick one and hold it)

| Preset | Full stack (words) | Best for |
|--------|--------------------|----------|
| **Naturalist Institutional** (default) | Deakins-motivated soft daylight · 35–50mm · f/4 balanced · warm-highlight/cool-shadow filmic, muted · fine grain | trust sites, professions, about |
| **Editorial Warm** | golden-hour window light · 85mm · f/2 shallow · Portra warm skin · airy | people, hero, career |
| **Architectural Clean** | cool daylight · 24mm · f/8 deep · neutral grade · crisp · Iwan Baan in-use | spaces, campus, B2B |
| **Product Premium** | single soft key + rim · 100mm macro · f/5.6 · clean, gentle micro-contrast | documents, devices |
| **Cinematic Wide Anamorphic** | Fraser/Hoytema · anamorphic 2.39 · atmospheric haze · large-format softness | campaign hero (bold register) |
| **Stylized Warm 3D** | soft global illumination · appealing character · warm, matte · Pixar-appeal | mascots/explainers (consumer register) |

---

## 10. Video technique presets (ready camera-move recipes)

| Preset | Move + rules | Model |
|--------|--------------|-------|
| **Calm hero loop** | locked-off or ≤5% slow push, seamless first=last, only light/dust/reflections move (§4.5) | veo-3.1 |
| **Product reveal** | slow arc/orbit, single soft key, rack focus to the object | veo-3.1 / kling-v3.0-pro |
| **Establishing drift** | slow lateral dolly, gentle parallax, deep focus | veo-3.1 |
| **Atmosphere breathe** | very slow push, volumetric god-ray drift, dust motes | veo-3.1-fast (draft) |

Duration 6–8s, 24fps feel, audio off, `prefers-reduced-motion` fallback = the static first frame (role/a11y).

---

## 11. Anti-patterns (media craft failures — extends role §Q2)

| # | Pattern | Reaction |
|---|---------|----------|
| 1 | Hex/colour code/model token in the prompt → rendered as text | 🔴 strip to words (§6.3) |
| 2 | Rainbow flood / saturated gradient wedge across the frame | 🔴 one small soft accent only |
| 3 | `product-still` reads as CGI/clay toy | 🔴 add real-camera + material grain; tune negative |
| 4 | Everything shallow "AI portrait mode" | 🟡 match DoF to type (§3.2) |
| 5 | Teal-and-orange by default | 🟡 use a motivated grade, teal-orange only deliberately |
| 6 | Gear-jargon salad (five brands in one prompt) | 🟡 one camera + one lens anchor per series |
| 7 | "Cinematic" as a lone lazy adjective | 🟡 specify the actual move/light/lens/grade |
| 8 | Series incoherence (mixed light direction/temperature/grade) | 🔴 fix the CINE_SPINE, re-roll the outlier |
| 9 | Uncanny faces / real or known likeness | 🔴 soft/out-of-focus faces; §Q3 legal |
| 10 | Baked text / logo / official seal / counterfeit document | 🔴 role §Q3 blocker |
| 11 | Per-frame relight or flicker in a loop | 🔴 only light/air moves; a11y |

---

## 12. Quick-reference vocabulary (the machine-usable core)

```
FOCAL:     14–24 wide · 35 reportage · 50 normal · 85 portrait · 100 macro · 135+ tele
DOF:       f1.4–2 creamy · f2.8–4 isolate · f5.6–8 balanced · f11–16 deep
LIGHT:     three-point · Rembrandt · loop · butterfly · split · rim · window · practicals
QUALITY:   soft (default) | hard ; RATIO 1:1 · 2:1 · 4:1 · 8:1
TEMP:      2700K tungsten · golden hour · 5600K daylight · overcast · blue hour · open shade
MOVE:      locked-off · slow push/pull · lateral track · arc/orbit · crane · rack focus · (whip/dolly-zoom = bold only)
SIZE:      EWS · WS · MS · MCU · CU · ECU/macro
ANGLE:     eye-level · low · high · top-down · (dutch = avoid for trust)
LENS-LOOK: Alexa+Signature (warm filmic) · Master Prime (clean) · Cooke (soft skin) · anamorphic (widescreen/oval bokeh)
GRADE:     warm-highlight/cool-shadow (default) · matte-lifted · bleach-bypass · pastel · high-contrast · mono
STOCK:     Portra (skin) · Ektar (saturated) · Cinestill (night halation) · HP5 (B&W)
FINISH:    fine grain · clean digital · subtle halation · matte blacks · real material grain (no plastic)
```

Assembly reminder: **SUBJECT (axes 1–3) + CINE_SPINE (axes 4–8, fixed) + WORLD + CLASS_NOTE + CORE + NEGATIVE.** Colour in words; exact colour in CSS; one DP across the series.

---

Reference: `roles/ROLE_MEDIA_ENGINEER.md` · `roles/VISUAL_CONCEPT_PROTOCOL.md` · `roles/CONCEPT_ANATOMY.md` (the reference protocol SOURCE→EXTRACT→TRANSFER→NOT TAKING) · `roles/CONCEPT_DNA_LIBRARY.md` · `roles/ROLE_MOTION.md` (motion language + real-time WebGL code) · `roles/MOTION_LIBRARY.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `.cursorrules` (Law 28 concept, Law 33 craft register)
Version: 1.0 | 2026-07-22
