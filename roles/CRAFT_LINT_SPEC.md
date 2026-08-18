# CRAFT_LINT_SPEC — машинный пол вкуса и вёрстки

**Статус:** 🟢 канон · слой между `LAYOUT_INVARIANTS` (геометрия) и `VISUAL_CRAFT`/`INTERFACE_CRAFT` (вкус)
**Дата:** 2026-07-20 · **Роли:** @QA_VISUAL (владелец) · @DEV (исполняет фиксы) · @FRONTEND (правила линта)
**Тезис:** каждое правило вкуса, которое сводится к счёту/грепу/метру, становится **блокирующей проверкой**. Что не сводится — уходит в `QA_VISUAL_AESTHETE_SENSOR.md` как каталог преступлений с обязательным вердиктом.

> Принцип: **пол держит машина, потолок — глаз.** Геометрия (`V1–V12`) уже машинная. Этот файл добавляет `V15–V18` — новые *измеримые* векторы, которые закрывают частые «глазные» баги, и сводит X1–X12 / I1–I12 к проверкам там, где это возможно.

---

## 0. Как читать таблицы

| Колонка | Значение |
|---|---|
| **Проверка** | конкретный греп / eslint-правило / Playwright-метр |
| **Тип** | 🟩 machine (детерминированно) · 🟨 hybrid (метр + порог, но нужен зонный контекст) · 🟥 eye (только `AESTHETE_SENSOR`) |
| **Гейт** | 🔴 blocking (роняет CI) · 🟡 advisory (в отчёт, не блок) |

---

## 1. Новые измеримые векторы V15–V18 (закрывают частые баги)

> Эти четыре — прямой ответ на «двухстрочная кнопка ×2», «ряд кнопок разной высоты», «текст на hover становится нечитаемым», «огромные шрифты в меню». Раньше это ловилось глазом (и пропускалось). Теперь — числом.

### V15 — State Contrast (текст читаем во ВСЕХ состояниях)

**Баг, который убивает:** синяя кнопка → на hover текст уходит в тёмно-синий/фиолетовый → нечитаемо.

```ts
// measures.ts
/** Минимальный контраст текст↔фон по всем состояниям. WCAG: ≥4.5 обычный, ≥3 крупный. */
export async function stateContrast(page, selector,
  states = ['rest','hover','active','focus-visible','disabled']): Promise<{state:string,ratio:number}> {
  let worst = {state:'rest', ratio:99};
  for (const s of states) {
    await applyState(page, selector, s);           // hover/focus/[data-state]/:active эмуляция
    const { color, bg } = await page.$eval(selector, el => {
      const cs = getComputedStyle(el);
      return { color: cs.color, bg: effectiveBg(el) }; // effectiveBg поднимается по предкам до непрозрачного
    });
    const ratio = wcagContrast(color, bg);
    if (ratio < worst.ratio) worst = { state:s, ratio };
  }
  return worst;
}
```

| Проверка | Тип | Гейт | Порог |
|---|---|---|---|
| `stateContrast(button).ratio ≥ 4.5` (обычный текст) / `≥ 3` (крупный ≥18.66px bold) во всех состояниях | 🟩 | 🔴 | disabled может 🟡 (но ≥3 всё равно) |

**Правило для @DEV:** hover меняет **фон/тень**, но НЕ уводит текст в цвет, близкий к фону. `color` на hover либо не меняется, либо меняется на такой же контрастный. Синяя кнопка → текст остаётся белым.

---

### V16 — Control-Row Equal-Height (ряд кнопок/чипов ровный)

**Баг, который убивает:** в ряду одна кнопка однострочная, другая двухстрочная → высота ×2 → «лесенка».

```ts
/** Разброс высот в группе контролов + детект переноса строки. */
export async function controlRowDelta(page, groupSelector): Promise<{delta:number, wrapped:number}> {
  const items = await page.$$eval(`${groupSelector} > *`, els => els.map(el => {
    const r = el.getBoundingClientRect();
    const lh = parseFloat(getComputedStyle(el).lineHeight) || r.height;
    return { h: Math.round(r.height), lines: Math.round(r.height / lh) };
  }));
  const heights = items.map(i => i.h);
  return { delta: heights.length<2 ? 0 : Math.max(...heights)-Math.min(...heights),
           wrapped: items.filter(i => i.lines > 1).length };
}
```

| Проверка | Тип | Гейт | Порог |
|---|---|---|---|
| `controlRowDelta('[data-btn-group]').delta == 0` | 🟩 | 🔴 | строго |
| `.wrapped == 0` (в ряду кнопок нет переносов) ИЛИ группа `align-items:stretch` и все равны | 🟩 | 🔴 | — |

**Правило для @DEV (в `LAYOUT_INVARIANTS §12.3` дополнением):** кнопки в CLUSTER имеют **фиксированный `min-height`** и `white-space:nowrap` (или `line-clamp:1`). Если лейбл длинный — он усекается или кнопка шире, но **не выше**. Двухстрочные кнопки в одном ряду с однострочными запрещены. Если перенос неизбежен по дизайну — весь ряд `align-items:stretch`, тогда все тянутся до высшей.

---

### V17 — Chrome Type-Scale Ceiling (шрифт в «мебели» под потолком)

**Баг, который убивает:** меню/нав/табы огромными шрифтами «в ущерб интерфейсу».

```ts
/** Реальный font-size элементов по зонам против потолка зоны. */
export async function typeScaleCeiling(page, zoneCaps): Promise<Array<{zone,el,size,cap}>> {
  const viol = [];
  for (const [zone, cap] of Object.entries(zoneCaps)) {
    const sizes = await page.$$eval(`[data-zone="${zone}"] :is(a,button,span,li,td)`,
      els => els.map(el => Math.round(parseFloat(getComputedStyle(el).fontSize))));
    sizes.forEach(s => { if (s > cap) viol.push({zone, size:s, cap}); });
  }
  return viol;
}
```

| Зона | Потолок font-size | Тип | Гейт |
|---|---|---|---|
| `nav` / header-меню | **16px** | 🟩 | 🔴 |
| mega-menu / dropdown item | **15px** | 🟩 | 🔴 |
| segmented / tab | **15px** | 🟩 | 🔴 |
| table cell | **15px** (заголовок 13) | 🟩 | 🔴 |
| chip / badge | **14px** | 🟩 | 🔴 |
| display / hero / section h1–h2 | **нет потолка** (это контент, а не chrome) | 🟥 | — |

**Разрешение конфликта «44px tap target vs огромный шрифт»:** touch-target 44×44 достигается **паддингом**, а не раздуванием шрифта. Крупный тап-таргет ≠ крупный текст. Это снимает твою претензию про «геометрия задаёт меню с огромными шрифтами».

---

### V18 — Primitive Source (кнопка = единственный источник)

**Баг, который убивает:** кнопки разного стиля, потому что кто-то нарисовал `<button className="...">` вручную.

| Проверка | Тип | Гейт |
|---|---|---|
| eslint: `<button>` в JSX запрещён вне `ui/Button.tsx` (allowlist) → бери `<Button>` | 🟩 | 🔴 |
| grep: нет inline `style=` c `background`/`padding`/`border-radius` на интерактиве | 🟩 | 🔴 |
| grep: нет `class*="btn"` определений вне `theme/*.css` (все варианты — токенами) | 🟩 | 🔴 |
| primitive-conformance (QA_VISUAL): класс примитива на рендере = класс из паспорта | 🟨 | 🔴 |

Правило-вставка в роли — §4.

---

## 1b. Canvas legibility — вектор V19 (Toy-Graph Detector)

> Для node-graph продуктов (конструктор пайплайнов, agent canvas). Канон — `CANVAS_CRAFT_CANON.md`; его §9 детектор G1–G10 становится **вектором V19**. Часть машинна, часть — глаз. Эталон читаемого узла: `pipeline_constructor_readable.html`.

| # | Проверка | Тип | Гейт |
|---|---|---|---|
| V19.1 | каждый узел несёт **глиф + тинт категории** (не только текст) — `[data-node] .glyph` присутствует, категорий 5–7 | 🟩 | 🔴 (G1) |
| V19.2 | каждый узел выносит **≥1 значимый параметр в шапку** (`.node-sub` непустой) — узел без сабтайтла = 🔴 | 🟩 | 🔴 (G1) |
| V19.3 | порты **типизированы и подписаны** (`data-port-type` + видимое имя); нелегальная цель гаснет при drag, не toast после | 🟨 | 🔴 (G2/G3) |
| V19.4 | есть **auto-layout / «Выровнять»** в один keypress; flow-направление единое (L→R) | 🟩 | 🔴 (G4) |
| V19.5 | **минимапа + поиск по холсту** при >20 узлов | 🟨 | 🔴 (G5) |
| V19.6 | hover узла → подсветка подграфа (upstream+downstream), остальное dim до ~30% | 🟨 | 🟡 (G6) |
| V19.7 | узел — **не форма** (нет `input`/`select` внутри тела узла; конфиг только в инспекторе) | 🟩 | 🔴 (G7) |
| V19.8 | **run overlay** на том же графе, не отдельный экран лога | 🟥 | 🔴 (G8) |
| V19.9 | клик по узлу после прогона → его фактические **вход/выход** | 🟥 | 🔴 (G9) |
| V19.10 | петля визуально отлична + видимое условие выхода + видимый cap итераций | 🟨 | 🔴 (G10) |

**Вердикт V19:** любой из G7/G8/G9/G10 → 🔴; 3+ хита суммарно → 🔴 (продукт — редактор диаграмм, притворяющийся пультом).

---

## 1c. Page pacing — вектор V20 (ритм страницы / «экраны»)

> Page-level ось, отдельная от A/V1 (ритм внутри секции). Лечит ощущение «сайт как чердак: блоки навалены неровно, всё в кучу».

| # | Проверка | Тип | Гейт |
|---|---|---|---|
| V20.1 | один section-spacing токен — distinct `padding-block` у top-level `<section>` ≤ 2 | 🟩 | 🔴 (P4) |
| V20.2 | секции не сталкиваются — `pairwiseIntersection` top-level секций == 0px² (ловит наезжающую CTA/панель) | 🟩 | 🔴 (P5) |
| V20.3 | нет застрявшего «Загрузка…»/skeleton на осевшей странице (settle 3s) | 🟨 | 🔴 (P2) |
| V20.4 | секция-плейсхолдер («скоро появятся») **скрыта на prod**, не рендерится полноразмерно | 🟨 | 🔴 (P2) |
| V20.5 | >10 top-level секций на marketing-странице → флаг «pacing review» | 🟩 | 🟡 (P1) |
| V20.6 | hero держит первый viewport; peek следующей секции ≤ ~15vh как намеренный scroll-hint | 🟨 | 🟡 (P3) |

---

## 2. X1–X12 (cheapness, `VISUAL_CRAFT §9`) → проверки

| # | Правило | Проверка | Тип | Гейт |
|---|---|---|---|---|
| X1 | ≤1 метод разделения на поверхность | AST: у элемента не более одного из {border, boxShadow, background-tint} как разделителя | 🟨 | 🟡 |
| X2 | Тёплые/тонированные тени, не `rgba(0,0,0)` | grep: `box-shadow` не содержит `rgba(0,0,0` и `#000` | 🟩 | 🔴 |
| X3 | Насыщенный цвет на большой площади запрещён | метр: площадь элементов с chroma>C и area>40vw → 0 | 🟨 | 🔴 |
| X4 | Один accent hue family | токен-чек: `--accent-*` из одной hue-семьи; счётчик активных hue ≤ 2 | 🟩 | 🔴 |
| X5 | Декоративный градиент без причины | grep-счётчик `linear-gradient` на секцию ≤ бюджета (hero ≤3, секция ≤2); `.btn--primary` без градиента | 🟩 | 🔴 |
| X6 | Модульная типо-шкала, ≤6 размеров/экран | метр: `distinct(font-size)` на маршрут ≤ 6 | 🟩 | 🟡 |
| X7 | Оптическое выравнивание | — | 🟥 | — |
| X8 | Один источник света в тенях | AST: направление всех `box-shadow` согласовано (Δangle ≤ 15°) | 🟨 | 🟡 |
| X9 | Нет чистых серых (`#888` и т.п.) | grep: серые с примесью бренда; запрет «мёртвых» hex из чёрного списка | 🟩 | 🔴 |
| X10 | Радиусы концентричны (вложенный = внешний − padding) | метр: nested radius ≈ outer − pad (±2px) | 🟨 | 🟡 |
| X11 | Bold ≤ ~30% текста | метр: доля weight≥700 по площади текста ≤ 0.3 | 🟩 | 🟡 |
| X12 | Не ряд из 3–4 одинаковых карточек как главный аргумент | 🟥 (композиция) | 🟥 | — |

---

## 3. I1–I12 (stiffness, `INTERFACE_CRAFT §7`) → проверки

> Операционка (`/admin`, консоли). Большинство — про поведение, часть измерима.

| # | Правило | Проверка | Тип | Гейт |
|---|---|---|---|---|
| I1 | Есть клавиатурный путь | a11y: все действия достижимы с клавиатуры; `tabindex` корректен | 🟨 | 🔴 |
| I2 | Bulk-select где есть списки | наличие select-all + bulk-bar в списковых экранах | 🟨 | 🟡 |
| I3 | Не всё через модалку | метр: доля действий, открывающих modal, ≤ порога; create/edit → Drawer | 🟨 | 🟡 |
| I4 | Фильтры переживают reload | тест: применил фильтр → reload → фильтр жив (URL/сторедж) | 🟩 | 🔴 |
| I5 | Нет «are you sure?» на неразрушающем | grep: confirm только на destructive | 🟨 | 🟡 |
| I6–I12 | inline-edit, оптимистик, undo, пусто-состояния, скорость мысли | 🟥 (в основном поведение) | 🟥 | — |

---

## 4. Вставки в роли (готовые к копированию)

### 4.1 В `ROLE_DEV.md` → раздел «Frontend geometry» (после §12.3)

```
□ §12.4 КНОПКА — ТОЛЬКО ПРИМИТИВ. Кнопка рендерится через <Button> (ui/Button.tsx).
  Сырой <button className>, inline-стили фона/паддинга/радиуса на интерактиве,
  локальные .btn-* классы — ЗАПРЕЩЕНЫ (CRAFT_LINT V18). Нет нужного варианта →
  эскалация к @FRONTEND, не рисовать руками.
□ §12.5 РЯД КОНТРОЛОВ РОВНЫЙ. Кнопки/чипы в группе: фиксированный min-height +
  white-space:nowrap (или line-clamp:1). Двухстрочная кнопка в ряду с однострочными
  запрещена. Перенос неизбежен → вся группа align-items:stretch (V16).
□ §12.6 ТЕКСТ ЧИТАЕМ ВО ВСЕХ СОСТОЯНИЯХ. hover/active/focus/disabled меняют фон/тень,
  но не уводят color к цвету фона. Контраст ≥4.5 в каждом состоянии (V15).
□ §12.7 CHROME ПОД ПОТОЛКОМ. Шрифт в nav/menu/tab/table/chip ≤ потолка зоны (V17).
  Tap-target 44px — паддингом, НЕ размером шрифта.
```

### 4.2 В `ROLE_QA_VISUAL.md` → раздел «base meters» (добавить векторы)

```
V15 State Contrast   — stateContrast(button).ratio ≥ 4.5 во всех состояниях
V16 Control-Row      — controlRowDelta('[data-btn-group]').delta == 0 && wrapped == 0
V17 Type Ceiling     — typeScaleCeiling(zoneCaps) == []  (chrome не превышает потолок зоны)
V18 Primitive Source — eslint + primitive-conformance: кнопки из <Button>, класс = паспорт
```
И в PILLARS добавить: **«12. Каталог эстета обязателен — отчёт без заполненного crime-verdict из `QA_VISUAL_AESTHETE_SENSOR.md` неполон.»**

---

## 5. CI-джоб `craft` (скелет)

```yaml
# .ci/craft.yml  — рядом с job:visual, блокирует merge
craft:
  needs: [build]
  steps:
    - run: npm run lint:craft         # eslint: V18 (button primitive), no-inline-style, no magic hex
    - run: npm run grep:craft         # X2/X4/X5/X9: тени, hue-count, gradient-budget, dead-greys
    - run: npx playwright test tests/visual/craft.spec.ts   # V15/V16/V17 измеримо
    - run: node scripts/craft-report.js --fail-on=blocking  # 🔴 → exit 1
```

```ts
// tests/visual/craft.spec.ts (ядро)
for (const route of ROUTES) {
  test(`craft ${route}`, async ({ page }) => {
    await render(page, route, { viewport:[360,768,1280,1920], fixture:'longtext' });
    // V15
    for (const b of await page.$$('[data-btn-group] button, .btn'))
      expect((await stateContrast(page, b)).ratio).toBeGreaterThanOrEqual(4.5);
    // V16
    for (const g of await page.$$('[data-btn-group]')) {
      const r = await controlRowDelta(page, g);
      expect(r.delta).toBe(0); expect(r.wrapped).toBe(0);
    }
    // V17
    expect(await typeScaleCeiling(page, ZONE_CAPS)).toEqual([]);
  });
}
```

**Правило:** блокирующие (🔴) роняют билд — их слабая модель не может «подписать себе». Advisory (🟡) идут в отчёт и на глаз-ревью эстета.

---

## 6. Что сюда НЕ влезло (уходит в глаз)

X7 (оптика), X12 (ряд-как-аргумент), I6–I12 (скорость мысли), композиция, баланс асимметрии, сочетание цветов, «безвкусица» — это `QA_VISUAL_AESTHETE_SENSOR.md`. Там нет метра, но есть **каталог с обязательным вердиктом**: молчание по пункту = пропуск = отчёт неполон.

---

*Версия 1.0 — 2026-07-20 — машинный пол; V15–V18 закрывают частые баги рядов/состояний/шрифтов.*
