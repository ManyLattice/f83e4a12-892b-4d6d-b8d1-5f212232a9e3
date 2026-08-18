# Приглашение с развилкой: план реализации

> **For agentic workers:** REQUIRED SUB-SKILL: Use
> superpowers:subagent-driven-development (recommended) or
> superpowers:executing-plans to implement this plan task-by-task.
> Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Собрать одностраничное приглашение: экран-развилка из двух
иллюстраций, тап раскрывает выбранную на весь экран и показывает кнопку
в Telegram.

**Architecture:** Один файл `index.html` со встроенными стилями и
скриптом. Разметка - две кнопки-половины плюс наложенный заголовок и
скрытая CTA. Скрипт делает ровно три вещи: подставляет тексты из
`CONFIG`, вешает обработчик выбора, строит ссылку в Telegram. Никакой
сборки, никаких зависимостей.

**Tech Stack:** HTML, CSS (custom properties, flexbox, transitions),
ванильный JS. Хостинг GitHub Pages.

**Spec:** `docs/superpowers/specs/2026-08-18-weekend-invite-design.md`

## Global Constraints

- Язык страницы русский, `<html lang="ru">`
- Один файл `index.html`, стили и скрипт inline, без зависимостей
- Тире в текстах - обычный дефис `-`, никаких `—` и `–`
- Все видимые тексты только из блока `CONFIG`, ни одной строки в разметке
- Telegram: `ManyLattice`, сообщение подставляется в поле ввода и НЕ
  отправляется автоматически
- Ассеты уже готовы: `images/picnic.webp` (196 КБ),
  `images/horses.webp` (136 КБ), оба 1086×1448
- Передумать нельзя: обратного пути из раскрытого состояния нет
- На половинах нет текста - только иллюстрации
- Комментарии в коде на английском
- Коммиты по формату `[Tag] Description`, без Co-Authored-By

## Файловая структура

| Файл | Ответственность |
|---|---|
| `index.html` | Вся страница: разметка, стили, скрипт |
| `images/picnic.webp` | Иллюстрация пикника (готова) |
| `images/horses.webp` | Иллюстрация лошадей (готова) |
| `.gitignore` | Прячет `.orig-art/` и `.DS_Store` (готов) |

Один файл - это осознанно: страница маленькая, а серия
(`ceramic-date`, `moto-date`) уже устроена так же.

---

### Task 1: Каркас развилки

Статичный экран: две половины с иллюстрациями и заголовок поверх.
Ещё без интерактива - только вёрстка и адаптив.

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: `images/picnic.webp`, `images/horses.webp`
- Produces: разметка, на которую опираются задачи 2-4:
  - `.fork` - контейнер обеих половин
  - `button.half` с `data-choice="picnic"` и `data-choice="horses"`
  - `.half__art` - `<img>` внутри половины
  - `.title` - наложенный заголовок
  - CSS-переменные `--dur` (длительность перехода) и `--ease`

- [ ] **Step 1: Создать `index.html` с каркасом**

```html
<!DOCTYPE html>
<html lang="ru" class="no-js">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Вика, эти выходные</title>

<!-- Open Graph: preview when the link is shared in Telegram.
     Absolute URL is required - Telegram ignores relative paths.
     Replace the host once the GitHub Pages URL is known. -->
<meta property="og:type" content="website">
<meta property="og:title" content="Вика, эти выходные">
<meta property="og:description" content="Выбирай">
<meta property="og:image" content="https://manylattice.github.io/weekend-date/images/picnic.webp">

<!-- In-app browsers (Telegram, Instagram) paint their chrome over the
     page and tint it with this colour. Sampled between the two palettes
     so neither half shows a bright band above or below it. -->
<meta name="theme-color" content="#6E6A3C">

<link rel="preload" as="image" href="images/picnic.webp">
<link rel="preload" as="image" href="images/horses.webp">

<style>
/* ===== Variables ===== */
:root {
  --dur: 650ms;
  --ease: cubic-bezier(0.22, 0.61, 0.36, 1);
  --ink: #FFFFFF;
  --font-serif: Georgia, "Times New Roman", "Iowan Old Style", serif;
}

/* ===== Base ===== */
* { margin: 0; padding: 0; box-sizing: border-box; }

html, body { height: 100%; }

body {
  background-color: #6E6A3C;
  color: var(--ink);
  font-family: var(--font-serif);
  -webkit-font-smoothing: antialiased;
  overflow: hidden;          /* single screen, never scrolls */
}

/* ===== Fork ===== */
.fork {
  display: flex;
  flex-direction: column;    /* mobile first: halves stack */
  height: 100dvh;
  width: 100%;
}

/* Each half is a real <button>: it must be reachable from a keyboard
   and announced as actionable, which a <div> with a click handler is not. */
.half {
  position: relative;
  flex: 1 1 50%;
  min-height: 0;             /* lets flex shrink the half below content size */
  border: 0;
  padding: 0;
  background: none;
  cursor: pointer;
  overflow: hidden;
  -webkit-tap-highlight-color: transparent;
}

.half__art {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
  /* Per-half focal points: the subjects sit off-centre in both
     illustrations, so each one is anchored separately. Tuned in Task 4. */
  object-position: center;
}

/* Resting dim: both halves sit slightly darkened so that choosing one
   can visibly "light it up", and so white text stays readable on any
   part of either illustration. */
.half::after {
  content: "";
  position: absolute;
  inset: 0;
  background: rgba(28, 24, 16, 0.28);
  pointer-events: none;
}

/* ===== Title ===== */
.title {
  position: fixed;
  top: calc(5vh + env(safe-area-inset-top));
  left: 50%;
  transform: translateX(-50%);
  width: min(100% - 40px, 640px);
  text-align: center;
  font-size: clamp(28px, 7vw, 44px);
  font-weight: 400;
  letter-spacing: 0.01em;
  line-height: 1.2;
  text-shadow: 0 2px 18px rgba(20, 16, 10, 0.55);
  pointer-events: none;      /* never blocks a tap on the halves */
  z-index: 2;
}

/* ===== Desktop: halves sit side by side ===== */
@media (min-width: 760px) and (orientation: landscape) {
  .fork { flex-direction: row; }
}
</style>
</head>
<body>

<h1 class="title"></h1>

<div class="fork">
  <button class="half" type="button" data-choice="picnic">
    <img class="half__art" src="images/picnic.webp" alt="Пикник на траве">
  </button>
  <button class="half" type="button" data-choice="horses">
    <img class="half__art" src="images/horses.webp" alt="Лошадь в степи">
  </button>
</div>

</body>
</html>
```

- [ ] **Step 2: Открыть в браузере и проверить развилку**

```bash
open index.html
```

Проверить глазами:
- Две иллюстрации делят экран пополам по вертикали, скролла нет
- Обе слегка притемнены
- Заголовка пока не видно (текст подставит Task 2) - это ожидаемо
- Сузить окно до узкого/высокого - половины друг под другом
- Расширить в широкое - половины встают рядом

- [ ] **Step 3: Коммит**

```bash
git add index.html
git commit -m "[UI] Add fork screen with two illustrated halves"
```

---

### Task 2: Тексты из CONFIG

Один источник правды для всех надписей. После этой задачи заголовок
виден, а поменять любую формулировку можно в одном месте.

**Files:**
- Modify: `index.html` (добавить `<script>` перед `</body>`)

**Interfaces:**
- Consumes: `.title` из Task 1
- Produces: глобальный `CONFIG` для задач 3-4:
  - `CONFIG.title` - заголовок на развилке
  - `CONFIG.chosen.picnic` / `CONFIG.chosen.horses` - заголовки после выбора
  - `CONFIG.cta` - подпись кнопки
  - `CONFIG.reply.picnic` / `CONFIG.reply.horses` - текст в Telegram
  - `CONFIG.telegram` - username без `@`

- [ ] **Step 1: Добавить скрипт с CONFIG перед `</body>`**

```html
<script>
// The engine is taking over: drop the no-JS safety class.
// If this line never runs, CSS keeps the fork in its visible state.
document.documentElement.classList.remove("no-js");

// ============================================================
// CONFIG - the only place to edit when the wording changes
// ============================================================
const CONFIG = {
  title: "Вика, эти выходные",
  chosen: {
    picnic: "Значит, пикник",
    horses: "Значит, лошади"
  },
  cta: "Написать",
  reply: {
    picnic: "Давай пикник",
    horses: "Давай лошадей"
  },
  telegram: "ManyLattice"
};

const titleEl = document.querySelector(".title");
titleEl.textContent = CONFIG.title;
</script>
```

- [ ] **Step 2: Обновить страницу и проверить заголовок**

```bash
open index.html
```

Ожидается: сверху по центру белым «Вика, эти выходные», читается на
обеих половинах, тап по половине его не задевает.

- [ ] **Step 3: Коммит**

```bash
git add index.html
git commit -m "[UI] Drive all page copy from a single CONFIG block"
```

---

### Task 3: Выбор и раскрытие

Тап раскрывает половину на весь экран, вторая уходит, заголовок
меняется, следом появляется кнопка в Telegram.

**Files:**
- Modify: `index.html` (стили состояний + логика выбора)

**Interfaces:**
- Consumes: `CONFIG` (Task 2), `button.half[data-choice]` (Task 1)
- Produces:
  - класс `.is-chosen` на выбранной половине
  - класс `.is-gone` на отвергнутой
  - `body.has-choice` - страница в состоянии «выбрано»
  - `a.cta` - ссылка в Telegram

- [ ] **Step 1: Добавить разметку кнопки перед `<script>`**

```html
<a class="cta" href="#" target="_blank" rel="noopener"></a>
```

- [ ] **Step 2: Добавить стили состояний в конец `<style>`**

```css
/* ===== Chosen / dismissed halves ===== */
.half {
  transition: flex-grow var(--dur) var(--ease),
              flex-basis var(--dur) var(--ease),
              opacity var(--dur) var(--ease);
}

/* Interactive feedback - the illustrations carry no "tap me" label,
   so the affordance has to come from behaviour alone. */
.half:active { transform: scale(0.98); }
.half { transition-property: flex-grow, flex-basis, opacity, transform; }

@media (hover: hover) {
  /* Hovering lifts the dim on one half and deepens it on the other,
     so the pair reads as a choice rather than as a picture. */
  .fork:hover .half::after { background: rgba(28, 24, 16, 0.42); }
  .fork .half:hover::after { background: rgba(28, 24, 16, 0.10); }
}

.half::after { transition: background var(--dur) var(--ease); }

/* The chosen half takes the whole screen; the other collapses away. */
.half.is-chosen { flex-basis: 100%; }
.half.is-chosen::after { background: rgba(28, 24, 16, 0.34); }
.half.is-gone {
  flex-basis: 0%;
  flex-grow: 0;
  opacity: 0;
}

/* Once chosen, nothing on the fork is clickable any more -
   there is no way back by design. */
body.has-choice .half { cursor: default; pointer-events: none; }

/* ===== CTA ===== */
.cta {
  position: fixed;
  left: 50%;
  bottom: calc(8vh + env(safe-area-inset-bottom));
  transform: translate(-50%, 12px);
  z-index: 3;
  display: inline-block;
  padding: 16px 44px;
  border-radius: 999px;
  background: #FFFFFF;
  color: #2C2418;
  font-size: 19px;
  letter-spacing: 0.02em;
  text-decoration: none;
  box-shadow: 0 8px 30px rgba(20, 16, 10, 0.35);
  /* Hidden until a choice is made, and inert while hidden. */
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
  transition: opacity 380ms var(--ease),
              transform 380ms var(--ease),
              visibility 380ms;
}

.cta.is-visible {
  opacity: 1;
  visibility: visible;
  pointer-events: auto;
  transform: translate(-50%, 0);
}

/* ===== Reduced motion ===== */
@media (prefers-reduced-motion: reduce) {
  .half, .half::after, .cta { transition-duration: 1ms; }
  .half:active { transform: none; }
}

/* ===== No JS =====
   Without the script the fork simply stays as it is: both illustrations
   visible, choice inert. A degraded page, never a blank one. */
.no-js .cta { display: none; }
```

- [ ] **Step 3: Добавить логику выбора в конец `<script>`**

```js
// ============================================================
// Telegram link
// ============================================================

/**
 * Builds a t.me link that opens a private chat with the text
 * pre-filled in the input field. The text is NOT sent
 * automatically - pressing send stays her decision.
 */
function buildTelegramUrl(username, text) {
  // Telegram usernames are shown as "@name" everywhere, so a leading "@"
  // is the likeliest thing to end up in CONFIG.telegram. Strip it -
  // t.me links must not contain it.
  const cleanUsername = username.trim().replace(/^@/, "");
  const base = `https://t.me/${cleanUsername}`;
  return text ? `${base}?text=${encodeURIComponent(text)}` : base;
}

// ============================================================
// Choice
// ============================================================
const halves = document.querySelectorAll(".half");
const ctaEl = document.querySelector(".cta");
const reduceMotion = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

// The CTA appears only after the half has finished opening, so the two
// movements read as one gesture instead of competing for attention.
const CTA_DELAY = reduceMotion ? 0 : 700;

let decided = false;

/**
 * Commits to one of the two options. There is deliberately no way back:
 * re-choosing would turn an impulse into browsing a menu. Reloading the
 * page is the only reset.
 */
function choose(choice) {
  if (decided) return;
  decided = true;

  halves.forEach((half) => {
    const isChosen = half.dataset.choice === choice;
    half.classList.add(isChosen ? "is-chosen" : "is-gone");
    half.setAttribute("aria-hidden", isChosen ? "false" : "true");
  });

  document.body.classList.add("has-choice");
  titleEl.textContent = CONFIG.chosen[choice];

  ctaEl.textContent = CONFIG.cta;
  ctaEl.href = buildTelegramUrl(CONFIG.telegram, CONFIG.reply[choice]);

  window.setTimeout(() => ctaEl.classList.add("is-visible"), CTA_DELAY);
}

halves.forEach((half) => {
  half.addEventListener("click", () => choose(half.dataset.choice));
});
```

- [ ] **Step 4: Проверить оба сценария в браузере**

```bash
open index.html
```

Пройти по списку:
- Тап по пикнику: половина раскрывается на весь экран, лошадь уходит
- Заголовок стал «Значит, пикник»
- Кнопка «Написать» появилась ПОСЛЕ раскрытия, не одновременно
- Повторный тап по фону ничего не делает
- Перезагрузить, выбрать лошадей: заголовок «Значит, лошади»
- Клавиатура: Tab доводит до половины, Enter выбирает
- Кнопка ведёт на `t.me/ManyLattice?text=...`

- [ ] **Step 5: Проверить текст, уходящий в Telegram**

```bash
node -e '
const build = (u, t) => `https://t.me/${u.trim().replace(/^@/, "")}` + (t ? `?text=${encodeURIComponent(t)}` : "");
console.log(build("ManyLattice", "Давай пикник"));
console.log(build("@ManyLattice", "Давай лошадей"));
'
```

Ожидается: обе ссылки без `@` после `t.me/`, текст в percent-encoding.

- [ ] **Step 6: Коммит**

```bash
git add index.html
git commit -m "[Logic] Expand the chosen half and reveal the Telegram button"
```

---

### Task 4: Дыхание половин

На странице нет ни слова «нажми», поэтому кликабельность держится
только на поведении. Тап-отклик и hover уже есть; здесь добавляется
третий сигнал - медленный зум иллюстраций, разнесённый по фазе, чтобы
половины жили не в такт и экран не выглядел мёртвой картинкой.

**Files:**
- Modify: `index.html` (анимация в `<style>`)

**Interfaces:**
- Consumes: `.half__art`, `.is-chosen`, `.is-gone` (задачи 1 и 3)
- Produces: анимацию `breathe`, наружу ничего не отдаёт

- [ ] **Step 1: Добавить анимацию в конец `<style>`**

```css
/* ===== Breathing =====
   The page carries no "tap me" label, so stillness would read as a
   static picture. A slow zoom keeps both halves alive. The two are
   offset by a negative delay so they never pulse in unison - in sync
   they would read as one moving backdrop rather than two choices. */
@keyframes breathe {
  0%   { transform: scale(1.00); }
  50%  { transform: scale(1.045); }
  100% { transform: scale(1.00); }
}

.half__art {
  animation: breathe 9s ease-in-out infinite;
  will-change: transform;
}

/* Negative delay starts this half mid-cycle instead of waiting. */
[data-choice="horses"] .half__art { animation-delay: -4.5s; }

/* Once a choice is made the movement has done its job: freeze it so
   the opening half and the appearing button own the attention. */
.half.is-chosen .half__art,
.half.is-gone .half__art { animation: none; }

@media (prefers-reduced-motion: reduce) {
  .half__art { animation: none; }
}
```

- [ ] **Step 2: Проверить дыхание в браузере**

```bash
open index.html
```

Проверить:
- Обе половины медленно, едва заметно приближаются и отдаляются
- Половины НЕ в такт: в момент, когда одна на пике, другая нет
- Зум не обнажает края - изображение всегда перекрывает половину
- После выбора движение замирает
- При включённом «уменьшить движение» в системе анимации нет

- [ ] **Step 3: Коммит**

```bash
git add index.html
git commit -m "[UI] Breathe the halves so they read as tappable"
```

---

### Task 5: Кадрирование под оба состояния

Сюжет в обеих иллюстрациях смещён от центра. Здесь подбираются точки
фокуса, чтобы корзина и лошадь оставались в кадре и на развилке, и
после раскрытия.

**Files:**
- Modify: `index.html` (правила `object-position`)

**Interfaces:**
- Consumes: `.half__art`, `.is-chosen` (задачи 1 и 3)
- Produces: финальное кадрирование, ничего наружу не отдаёт

- [ ] **Step 1: Задать стартовые точки фокуса**

Добавить в конец `<style>`:

```css
/* ===== Focal points =====
   Both subjects sit off the geometric centre: the picnic scene lives in
   the lower two thirds of its frame, and the horse stands left of centre
   at roughly 45% height. A single object-position cannot serve both the
   narrow half and the full-screen state, so each is anchored per state. */

/* Fork: tall narrow crop */
[data-choice="picnic"] .half__art { object-position: 50% 68%; }
[data-choice="horses"] .half__art { object-position: 42% 52%; }

/* Opened: wide crop - pull back toward the middle so the subject
   does not ride the top edge once the frame becomes short and wide. */
.half.is-chosen [data-choice="picnic"] .half__art,
[data-choice="picnic"].is-chosen .half__art { object-position: 50% 60%; }
[data-choice="horses"].is-chosen .half__art { object-position: 45% 48%; }

.half__art { transition: object-position var(--dur) var(--ease); }

@media (prefers-reduced-motion: reduce) {
  .half__art { transition-duration: 1ms; }
}
```

- [ ] **Step 2: Проверить кадр в четырёх ситуациях**

```bash
open index.html
```

Проверить, что сюжет цел:
1. Узкое окно, развилка - видна корзина целиком, видна лошадь целиком
2. Узкое окно, после выбора пикника - корзина в кадре, не срезана снизу
3. Узкое окно, после выбора лошадей - лошадь в кадре, не уехала вверх
4. Широкое окно (десктоп), оба варианта - то же самое

Если сюжет срезается, править проценты в правилах выше: первое число -
горизонталь, второе - вертикаль. Больше процент по вертикали - кадр
смещается ниже по изображению.

- [ ] **Step 3: Коммит**

```bash
git add index.html
git commit -m "[UI] Anchor focal points so subjects survive both crops"
```

---

### Task 6: Финальная проверка и публикация

**Files:**
- Modify: `index.html` (только если проверка что-то найдёт)

**Interfaces:**
- Consumes: всю страницу
- Produces: страницу, готовую к отправке

- [ ] **Step 1: Проверить, что запрещённые тире не просочились**

```bash
grep -n '[—–]' index.html && echo "FOUND DASHES - replace with -" || echo "OK: no em/en dashes"
```

Ожидается: `OK`. Если что-то нашлось - заменить на обычный дефис.

- [ ] **Step 2: Проверить, что тексты не задублированы в разметке**

```bash
grep -nE '>(Вика|Значит|Написать|Давай)' index.html && echo "FOUND HARDCODED COPY" || echo "OK: copy lives in CONFIG only"
```

Ожидается: `OK` - все надписи приходят из `CONFIG`.

- [ ] **Step 3: Проверить вес страницы**

```bash
du -h index.html images/*.webp && echo "--- total ---" && du -ch index.html images/*.webp | tail -1
```

Ожидается: суммарно менее 400 КБ.

- [ ] **Step 4: Проверить деградацию без JS**

Открыть страницу с выключенным JavaScript.

Ожидается: обе иллюстрации видны, заголовка нет, кнопки нет, белого
экрана нет. Страница остаётся картинкой-развилкой.

- [ ] **Step 5: Коммит, если что-то правилось**

```bash
git add index.html
git commit -m "[Fix] Address issues found in the final pass"
```

- [ ] **Step 6: Подготовить публикацию**

GitHub Pages включает Михаил сам. После того как адрес известен -
подставить его в `og:image` вместо заглушки:

```bash
grep -n 'og:image' index.html
```

Заменить `https://manylattice.github.io/weekend-date/` на реальный
адрес. Без этого превью ссылки в Telegram не покажет картинку -
относительные пути там не работают.

```bash
git add index.html
git commit -m "[Config] Point og:image at the published URL"
```

---

## Порядок и зависимости

```
Task 1 (каркас)
  v
Task 2 (тексты)
  v
Task 3 (выбор и раскрытие)
  v
Task 4 (дыхание)           <- нужны классы состояний из Task 3
  v
Task 5 (кадрирование)      <- нужен рабочий переход из Task 3
  v
Task 6 (проверка)
```

Задачи строго последовательны: каждая опирается на разметку предыдущей.

## Чего в плане намеренно нет

- **Автотестов.** Страница - это один экран с одним взаимодействием,
  и проверять тут нечего, кроме того, как это выглядит; браузерный
  тест-раннер ради двух кликов не окупается. Чистая логика в коде одна -
  `buildTelegramUrl`, она проверяется в Task 3 шагом 5.
- **Отправки выбора куда-либо.** Ответ приходит живым сообщением.
- **Кнопки «назад».** Решение спеки, не упущение.
