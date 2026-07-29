# Moto Invite Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Собрать одностраничный сайт-приглашение с Apple-style scroll-scrubbing на трёх изображениях, заканчивающийся кнопкой в Telegram с готовым текстом ответа.

**Architecture:** Один файл `index.html` без сборки и зависимостей. Внутри три независимых зоны: `CONFIG` (весь текст), `ScrollEngine` (считает прогресс секции 0→1 и пишет в CSS-переменную `--p`), `init()` (подставляет CONFIG в разметку, собирает Telegram-ссылку, запускает движок). Движок не знает про анимации - что делать с числом решает CSS каждой секции. Скролл работает как ползунок времени: прокрутил секцию на 30% - анимация на 30%, назад - отматывается.

**Tech Stack:** HTML + CSS + vanilla JS (ES2020). Ноль зависимостей, ноль сборки. Хостинг GitHub Pages.

**Spec:** `docs/superpowers/specs/2026-07-29-moto-invite-design.md`

## Global Constraints

- **Слово "свидание" не используется нигде** - ни в тексте страницы, ни в сообщении, ни в именах файлов, переменных, коммитов.
- **Все комментарии в коде на английском.** Идентификаторы на английском. Текст, видимый пользователю - на русском.
- **Ни одного дефиса-тире (— или –) в текстах страницы.** Только обычный дефис-минус (-).
- **Анимируются только `transform` и `opacity`.** Ни `top`, ни `width`, ни `filter`, ни `box-shadow` - они вызывают перерасчёт layout в каждом кадре.
- **`scale` в пределах 1.0-1.15.** Резкие трансформы на инерционном свайпе iOS покажут ступеньки.
- **Один `requestAnimationFrame`-цикл на всю страницу**, не по одному на секцию. Внутри цикла сначала все чтения `getBoundingClientRect()`, потом все записи в CSS-переменные - иначе layout thrashing.
- **`will-change: transform` только на sticky-фото**, не на всём подряд.
- **Ноль зависимостей, ноль сборки, один HTML-файл.** Автотестов нет - проверка ручная (обосновано в спеке: тест-раннер в проекте из одного файла это больше инфраструктуры, чем кода).
- **Никакой аналитики, счётчиков, отслеживания открытий.**
- **Изображения уже готовы** в `images/`: `01-road.jpg`, `02-moto.jpg`, `03-sunset.jpg` (941×1672, JPEG q85, 1.1 МБ суммарно). Перегенерировать или пережимать не нужно.
- **Коммиты по формату** `[Tag] Description` - см. `~/.claude/rules/commits.md`. Без Co-Authored-By. Push только по явной команде Михаила.

## File Structure

| Файл | Ответственность | Статус |
|------|-----------------|--------|
| `index.html` | Вся страница: разметка, стили, скрипт | Создаётся |
| `images/01-road.jpg` | Фон секции 1 | Готов |
| `images/02-moto.jpg` | Фон секции 2 | Готов |
| `images/03-sunset.jpg` | Фон секции 3, og:image | Готов |
| `.gitignore` | `.DS_Store` | Готов |

Внутри `index.html` четыре зоны, разделённые комментариями-баннерами:

```
<head>    мета для мобильного, og-теги, preload первого фото
<style>   CSS-переменные → базовые стили → секции → reduced-motion
<body>    3 секции разметки
<script>  CONFIG → ScrollEngine → init()
```

Один файл выбран сознательно: разделение на три файла усложнило бы хостинг и потребовало HTTP-сервера для локального просмотра (ES-модули не работают через `file://`), а выигрыша при таком объёме не даёт.

## Порядок задач

Задачи идут так, чтобы после каждой страницу можно было открыть и увидеть результат:

1. Скелет: разметка, стили, три секции - статичная страница без анимаций
2. `CONFIG` + `init()` - тексты подставляются, кнопка работает
3. `ScrollEngine` - появляется скраббинг
4. Reduced-motion и деградация без JS
5. Ручная проверка на телефоне

---

### Task 1: Скелет страницы - разметка и стили без анимаций

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: изображения `images/01-road.jpg`, `images/02-moto.jpg`, `images/03-sunset.jpg`
- Produces: DOM-структура, на которую опираются Task 2 и Task 3:
  - `section.section` × 3, с классами `section--road`, `section--moto`, `section--final`
  - внутри каждой: `.section__sticky > .section__photo` (фон) и `.section__content` (текст)
  - элементы с `data-config` для Task 2: `data-config="name"`, `data-config="place"`, `data-config="when"`
  - `a.cta` (кнопка) и `a.fallback` (мелкая ссылка под ней) в третьей секции

**Почему сначала статика.** После этой задачи страница уже читаема и её можно открыть на телефоне. Если что-то не так с композицией или читаемостью текста поверх фото - это видно до того, как написана строка JS.

- [ ] **Step 1: Создать `index.html` с head и мета**

Создать файл `index.html`:

```html
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Вика, поехали</title>

<!-- Open Graph: preview when the link is shared in Telegram.
     Absolute URL is required - Telegram ignores relative paths.
     Replace the host after the GitHub Pages URL is known. -->
<meta property="og:type" content="website">
<meta property="og:title" content="Вика, поехали">
<meta property="og:description" content="Степь, мотоциклы, выходные">
<meta property="og:image" content="https://REPLACE-ME.github.io/moto-date/images/03-sunset.jpg">

<link rel="preload" as="image" href="images/01-road.jpg">
</head>
<body>
</body>
</html>
```

`viewport-fit=cover` нужен, чтобы фото уходило под скруглённые углы и вырез iPhone.

`og:image` - закат, а не первое фото: в маленьком превью он читается лучше, чем колея.

- [ ] **Step 2: Добавить разметку трёх секций**

Вставить внутрь `<body>`:

```html
<main>

<!-- ===== Section 1: the track ===== -->
<section class="section section--road">
  <div class="section__sticky">
    <img class="section__photo" src="images/01-road.jpg" alt="">
    <div class="section__scrim"></div>
    <div class="section__content">
      <h1 class="title" data-config="name">Вика,</h1>
      <p class="hint">пролистай</p>
    </div>
  </div>
</section>

<!-- ===== Section 2: the bikes ===== -->
<section class="section section--moto">
  <div class="section__sticky">
    <img class="section__photo" src="images/02-moto.jpg" alt="" loading="lazy">
    <div class="section__scrim"></div>
    <div class="section__content">
      <ul class="facts">
        <li class="fact" style="--i:0"><span data-config="place">Степь в часе от города</span></li>
        <li class="fact" style="--i:1">Два мотоцикла, каждый на своём</li>
        <li class="fact" style="--i:2">Научу, страшно не будет</li>
        <li class="fact" style="--i:3"><span data-config="when">выходные</span></li>
        <li class="fact" style="--i:4">Куртка и закрытая обувь, остальное дам</li>
      </ul>
    </div>
  </div>
</section>

<!-- ===== Section 3: the ask ===== -->
<section class="section section--final">
  <div class="section__sticky">
    <img class="section__photo" src="images/03-sunset.jpg" alt="" loading="lazy">
    <div class="section__scrim"></div>
    <div class="section__content">
      <p class="ask">Поехали?</p>
      <a class="cta" href="https://t.me/" target="_blank" rel="noopener">Да, поехали</a>
      <a class="fallback" href="https://t.me/" target="_blank" rel="noopener">или просто напиши мне</a>
    </div>
  </div>
</section>

</main>
```

Два момента, которые тут неочевидны:

`alt=""` на всех трёх фото - намеренно. Это декоративные фоны, весь смысл несёт текст поверх. Непустой `alt` заставил бы скринридер читать описание картинки перед каждым блоком текста.

`--i` на пунктах списка - индекс для каскада в CSS (Task 3). Значения проставлены в разметке, чтобы CSS мог посчитать задержку каждого пункта без JS.

`data-config="place"` на первом пункте: если название базы известно, оно заменит строку целиком ("Экобаза «Название»" вместо "Степь в часе от города"). Если `CONFIG.place` пустой - остаётся дефолтный текст, и пункт всё равно осмыслен. Это тот же механизм, что и с `if (value)` в Task 2.

Тексты - черновые. Михаил перепишет под свою интонацию; в Task 2 они переезжают в `CONFIG`.

- [ ] **Step 3: Добавить `<style>` с переменными и базовыми стилями**

Вставить перед `</head>`:

```html
<style>
/* ===== Variables ===== */
:root {
  --ink: #fff;
  --scrim: rgba(20, 14, 10, 0.55);
  --accent: #e8843c;
  --font: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
}

/* ===== Base ===== */
* { margin: 0; padding: 0; box-sizing: border-box; }

html { scroll-behavior: auto; }

body {
  background: #14100c;
  color: var(--ink);
  font-family: var(--font);
  -webkit-font-smoothing: antialiased;
  overflow-x: hidden;
}
</style>
```

`overflow-x: hidden` на body - страховка: `scale` на фото может дать горизонтальную полосу прокрутки на узких экранах.

Фон `#14100c` под цвет тёмной части фотографий - если фото не успело загрузиться, экран не будет белым.

- [ ] **Step 4: Добавить стили секций**

Дописать в `<style>`:

```css
/* ===== Sections ===== */
.section {
  position: relative;
  height: 200vh;      /* double height gives the scroll room to scrub */
}

.section__sticky {
  position: sticky;
  top: 0;
  height: 100vh;
  overflow: hidden;
}

.section__photo {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  will-change: transform;   /* only on the photo, not on everything */
}

/* Dark gradient so text stays readable over the lower part of the photo */
.section__scrim {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    to bottom,
    transparent 30%,
    var(--scrim) 65%,
    rgba(20, 14, 10, 0.82) 100%
  );
}

.section__content {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  padding: 0 28px calc(48px + env(safe-area-inset-bottom));
}
```

`env(safe-area-inset-bottom)` поднимает текст над домашней полоской iPhone. Без него нижняя строка окажется под ней.

Градиент начинается с 30% - выше текста нет, и незачем затемнять небо.

- [ ] **Step 5: Добавить типографику**

Дописать в `<style>`:

```css
/* ===== Typography ===== */
.title {
  font-size: clamp(2.6rem, 13vw, 4.5rem);
  font-weight: 700;
  letter-spacing: -0.02em;
  line-height: 1.05;
}

.hint {
  margin-top: 14px;
  font-size: 0.95rem;
  opacity: 0.7;
}

.facts {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.fact {
  font-size: clamp(1.15rem, 5vw, 1.6rem);
  font-weight: 500;
  line-height: 1.3;
  text-shadow: 0 1px 12px rgba(0, 0, 0, 0.4);
}

.ask {
  font-size: clamp(2rem, 9vw, 3rem);
  font-weight: 700;
  margin-bottom: 28px;
}

.cta {
  display: block;
  padding: 18px 24px;
  background: var(--accent);
  color: #fff;
  font-size: 1.15rem;
  font-weight: 600;
  text-align: center;
  text-decoration: none;
  border-radius: 16px;
}

.fallback {
  display: block;
  margin-top: 18px;
  color: var(--ink);
  font-size: 0.9rem;
  text-align: center;
  text-decoration: underline;
  opacity: 0.65;
}
```

`clamp()` на всех размерах - на узком iPhone SE и на широком Pro Max текст должен читаться одинаково, без ручных медиа-запросов.

`text-shadow` на пунктах списка - фото под ними неоднородное (трава, грунт), тени хватает, чтобы текст не терялся на светлых участках.

- [ ] **Step 6: Открыть в браузере и проверить композицию**

```bash
open index.html
```

Затем в браузере включить мобильный вид: Safari → Develop → Enter Responsive Design Mode, или Chrome → DevTools → Toggle device toolbar → iPhone 14 Pro.

Проверить глазами:
- Три экрана листаются, фото занимают весь экран
- Текст читается поверх фото на всех трёх - не теряется на светлых участках
- Нижняя строка не прижата к самому краю
- Нет горизонтальной прокрутки
- Кнопка не уехала за пределы экрана

Если текст где-то не читается - усилить `--scrim` (поднять альфу) или добавить `text-shadow`. Композицию правим здесь, до анимаций.

- [ ] **Step 7: Коммит**

```bash
git add index.html
git commit -m "[UI] Add static page skeleton with three sections"
```

---

### Task 2: CONFIG и init() - тексты и Telegram-ссылка

**Files:**
- Modify: `index.html` (добавить `<script>` перед `</body>`)

**Interfaces:**
- Consumes: DOM из Task 1 - элементы с `data-config`, `a.cta`, `a.fallback`
- Produces:
  - `const CONFIG` - объект с полями `name`, `place`, `when`, `telegram`, `reply`
  - `function buildTelegramUrl(username, text)` → `string` - вызывается дважды из `init()`: с текстом для кнопки, с пустой строкой для фолбэк-ссылки
  - `function init()` - вызывается на `DOMContentLoaded`

**Почему до движка.** Кнопка - единственное, что обязано работать. Если скраббинг не заведётся, а кнопка работает - страница выполняет свою задачу. Наоборот - нет.

- [ ] **Step 1: Добавить CONFIG**

Вставить перед `</body>`:

```html
<script>
// ============================================================
// CONFIG - the only place to edit when the content changes
// ============================================================
const CONFIG = {
  name: "Вика",
  place: "",              // base name - fill in before sending
  when: "выходные",
  telegram: "REPLACE_ME", // real Telegram username, must be public
  reply: "Я согласна! Поехали 🏍️"
};
</script>
```

Два поля намеренно оставлены заглушками, и это не недоделка:

`telegram: "REPLACE_ME"` - настоящий username Михаила неизвестен. Ссылка `t.me/?text=` работает **только** если username публичный: он должен быть установлен в настройках Telegram и не скрыт настройками приватности. Проверить перед отправкой.

`place: ""` - название базы на момент написания неизвестно. Пустая строка, а не `«...»`: пустое значение просто не отобразится, а `«...»` она увидит как недоделку.

- [ ] **Step 2: Добавить сборку Telegram-ссылки**

Дописать в `<script>`:

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
  const base = `https://t.me/${username}`;
  return text ? `${base}?text=${encodeURIComponent(text)}` : base;
}
```

`encodeURIComponent` обязателен - без него эмодзи и пробелы сломают ссылку.

Функция возвращает ссылку без `?text=`, если текст пустой - это же используется для фолбэк-ссылки под кнопкой.

- [ ] **Step 3: Добавить init()**

Дописать в `<script>`:

```js
// ============================================================
// init - inject CONFIG into markup, wire up the links
// ============================================================
function init() {
  // Text substitution: every [data-config] element gets its value.
  // Empty values leave the markup default in place.
  document.querySelectorAll("[data-config]").forEach((el) => {
    const value = CONFIG[el.dataset.config];
    if (value) el.textContent = value;
  });

  const cta = document.querySelector(".cta");
  const fallback = document.querySelector(".fallback");

  if (cta) cta.href = buildTelegramUrl(CONFIG.telegram, CONFIG.reply);
  if (fallback) fallback.href = buildTelegramUrl(CONFIG.telegram, "");
}

document.addEventListener("DOMContentLoaded", init);
```

`if (value)` - не косметика: если поле в `CONFIG` пустое, в разметке остаётся дефолтный текст из Task 1. Именно поэтому в разметке стоят осмысленные значения, а не пустые плейсхолдеры - страница читаема даже если JS не выполнился.

- [ ] **Step 4: Проверить подстановку и ссылку**

Открыть `index.html`, в консоли браузера:

```js
document.querySelector(".title").textContent
// Expected: "Вика"

document.querySelector(".cta").href
// Expected: "https://t.me/REPLACE_ME?text=%D0%AF%20%D1%81%D0%BE%D0%B3%D0%BB%D0%B0%D1%81%D0%BD%D0%B0!%20%D0%9F%D0%BE%D0%B5%D1%85%D0%B0%D0%BB%D0%B8%20%F0%9F%8F%8D%EF%B8%8F"

document.querySelector(".fallback").href
// Expected: "https://t.me/REPLACE_ME"  (no ?text=)
```

Главное здесь - что эмодзи в `?text=` закодировался (`%F0%9F%8F%8D`), а не попал в ссылку как есть.

- [ ] **Step 5: Коммит**

```bash
git add index.html
git commit -m "[Logic] Add CONFIG and Telegram link builder"
```

---

### Task 3: ScrollEngine - скраббинг

**Files:**
- Modify: `index.html` (дописать в `<script>`, дописать в `<style>`)

**Interfaces:**
- Consumes: `section.section` из Task 1, `init()` из Task 2
- Produces:
  - `function sectionProgress(rect, viewportHeight)` → `number` 0..1
  - `const ScrollEngine` с методом `start()`
  - CSS-переменная `--p` на каждой `.section`, читаемая из CSS

**Почему движок ничего не знает про анимации.** Он пишет только число в `--p`. Что с ним делать решает CSS каждой секции. Это значит: подкрутить анимацию можно правкой одной CSS-строки, не влезая в JS.

- [ ] **Step 1: Добавить функцию прогресса**

Дописать в `<script>` перед `init()`:

```js
// ============================================================
// ScrollEngine - turns scroll position into a 0..1 progress
// value per section, written to the --p custom property.
// Knows nothing about what the animations look like.
// ============================================================

/**
 * How far a section has scrolled through the viewport.
 *
 * 0 - the section's top edge just reached the top of the screen
 * 1 - the section's bottom edge reached the bottom of the screen
 *
 * The section is 200vh tall with a 100vh sticky child, so the
 * scrubbing distance is exactly one viewport height.
 */
function sectionProgress(rect, viewportHeight) {
  const distance = rect.height - viewportHeight;
  if (distance <= 0) return 0;
  const scrolled = -rect.top;
  return Math.min(1, Math.max(0, scrolled / distance));
}
```

Математика тут стоит объяснения. Секция высотой `200vh`, sticky-ребёнок `100vh`. Пока секция проезжает мимо, `rect.top` уходит от 0 до `-100vh`. Значит путь скраббинга - это `rect.height - viewportHeight`, то есть ровно один экран. `Math.min/max` зажимают результат: до входа секции и после выхода прогресс не должен вылезать за 0..1.

`if (distance <= 0) return 0` - защита от деления на ноль. Сработает, если кто-то поменяет высоту секции на `100vh`.

- [ ] **Step 2: Добавить движок с rAF-циклом**

Дописать в `<script>`:

```js
const ScrollEngine = {
  sections: [],
  smoothed: [],

  start() {
    this.sections = Array.from(document.querySelectorAll(".section"));
    this.smoothed = this.sections.map(() => 0);
    this.tick();
  },

  tick() {
    const vh = window.innerHeight;

    // Phase 1: read everything. Never interleave reads and writes -
    // that forces a layout recalculation per section (layout thrashing),
    // which is immediately visible on a phone.
    const targets = this.sections.map((section) => {
      const rect = section.getBoundingClientRect();
      // Off-screen sections keep their last value - no point computing.
      if (rect.bottom < 0 || rect.top > vh) return null;
      return sectionProgress(rect, vh);
    });

    // Phase 2: write everything.
    targets.forEach((target, i) => {
      if (target === null) return;
      // lerp: the value chases its target instead of snapping to it.
      // Without this the scrubbing looks jerky.
      this.smoothed[i] += (target - this.smoothed[i]) * 0.1;
      this.sections[i].style.setProperty("--p", this.smoothed[i].toFixed(4));
    });

    requestAnimationFrame(() => this.tick());
  }
};
```

Три вещи, которые здесь сделаны намеренно:

**Один цикл на всю страницу**, не по одному на секцию - иначе три независимых rAF конкурируют за кадр.

**Чтение и запись разделены на фазы.** Если перемешать `getBoundingClientRect()` с `style.setProperty()`, браузер пересчитает layout по разу на секцию. На телефоне это заметно сразу.

**`toFixed(4)`** - обрезает бесконечный хвост от lerp. Без него в CSS-переменную улетает 17 знаков и мы дёргаем стили на изменениях, которых глаз не видит.

- [ ] **Step 3: Запустить движок из init()**

В `init()`, после блока со ссылками, дописать:

```js
  ScrollEngine.start();
```

Итоговый `init()`:

```js
function init() {
  document.querySelectorAll("[data-config]").forEach((el) => {
    const value = CONFIG[el.dataset.config];
    if (value) el.textContent = value;
  });

  const cta = document.querySelector(".cta");
  const fallback = document.querySelector(".fallback");

  if (cta) cta.href = buildTelegramUrl(CONFIG.telegram, CONFIG.reply);
  if (fallback) fallback.href = buildTelegramUrl(CONFIG.telegram, "");

  ScrollEngine.start();
}
```

- [ ] **Step 4: Проверить, что `--p` меняется**

Открыть страницу, в консоли:

```js
getComputedStyle(document.querySelector(".section--road")).getPropertyValue("--p")
```

Прокрутить немного и вызвать снова. Значение должно расти от 0 к 1. Прокрутить назад - убывать.

Если всегда 0 - движок не запустился, смотреть ошибки в консоли. Если сразу 1 - перепутан знак в `scrolled`.

- [ ] **Step 5: Привязать анимации в CSS**

Дописать в `<style>`:

```css
/* ===== Scrubbed animations =====
   Each section decides what to do with its own --p.
   The engine only supplies the number. */

/* Section 1: photo pushes in, text lifts away and fades */
.section--road .section__photo {
  transform: scale(calc(1 + var(--p, 0) * 0.1));
}
.section--road .section__content {
  transform: translateY(calc(var(--p, 0) * -60px));
  opacity: calc(1 - var(--p, 0) * 1.6);
}

/* Section 2: photo pulls back and opens up, facts cascade in.
   Each fact starts 0.06 later than the one before it (--i). */
.section--moto .section__photo {
  transform: scale(calc(1.15 - var(--p, 0) * 0.15));
}
.section--moto .fact {
  --start: calc(var(--i) * 0.06);
  --local: calc((var(--p, 0) - var(--start)) * 3.5);
  opacity: clamp(0, var(--local), 1);
  transform: translateY(calc((1 - clamp(0, var(--local), 1)) * 24px));
}

/* Section 3: photo drifts in, text then button */
.section--final .section__photo {
  transform: scale(calc(1 + var(--p, 0) * 0.08));
}
.section--final .ask {
  opacity: clamp(0, calc(var(--p, 0) * 3), 1);
  transform: translateY(calc((1 - clamp(0, calc(var(--p, 0) * 3), 1)) * 20px));
}
.section--final .cta,
.section--final .fallback {
  --btn: clamp(0, calc((var(--p, 0) - 0.18) * 3), 1);
  opacity: var(--btn);
  transform: scale(calc(0.9 + var(--btn) * 0.1));
}
```

Что здесь важно:

**`var(--p, 0)` с дефолтом везде.** Если JS не выполнился, `--p` не существует и без дефолта весь `calc()` станет невалидным - элементы получат сломанные стили. С дефолтом 0 они просто окажутся в начальном состоянии. Task 4 доводит этот сценарий до читаемого вида.

**`--local` в каскаде пунктов** - локальный прогресс каждого пункта. Множитель `3.5` заставляет пункт проявиться за ~28% хода секции, а не тянуться весь скролл. `clamp(0, ..., 1)` зажимает: до своего старта пункт прозрачен, после - полностью виден.

**Кнопка стартует с `- 0.18`** - на 18% позже текста, чтобы взгляд успел дойти до неё.

**Все `scale` в пределах 1.0-1.15**, как требует спек: на инерционном свайпе iOS более резкие трансформы покажут ступеньки.

- [ ] **Step 6: Проверить скраббинг**

Открыть в мобильном виде браузера, медленно прокрутить сверху вниз:
- Фото на первой секции приближается, текст уезжает вверх и растворяется
- Пункты на второй выкатываются один за другим, не хором
- На третьей текст появляется раньше кнопки
- **Прокрутить назад** - всё отматывается. Это главная проверка: если анимации не отматываются, это не скраббинг, а одноразовое появление.

Быстро свайпнуть до конца и обратно - не должно быть рывков и застрявших элементов.

- [ ] **Step 7: Коммит**

```bash
git add index.html
git commit -m "[Logic] Add scroll scrubbing engine and CSS animations"
```

---

### Task 4: Reduced-motion и деградация без JS

**Files:**
- Modify: `index.html` (дописать в `<style>`, поправить `init()`)

**Interfaces:**
- Consumes: `ScrollEngine.start()` из Task 3, CSS-анимации из Task 3
- Produces: ничего нового, меняет поведение существующего

**Почему это не украшение.** От скролл-скраббинга человеку с чувствительностью к движению может стать физически плохо. Если у неё в настройках iOS включено "уменьшение движения", страница обязана работать без анимаций - и остаться полностью читаемой.

- [ ] **Step 1: Не запускать движок при reduced-motion**

В `init()` заменить строку `ScrollEngine.start();` на:

```js
  // Respect the OS "reduce motion" setting: scrubbing can make people
  // with motion sensitivity physically unwell. CSS puts everything in
  // its final state via the matching media query.
  const reduceMotion = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
  if (!reduceMotion) ScrollEngine.start();
```

Движок не просто перестаёт анимировать - rAF-цикл вообще не запускается. Ноль работы в кадре.

- [ ] **Step 2: Добавить медиа-запрос reduced-motion**

Дописать в конец `<style>`:

```css
/* ===== Reduced motion =====
   No scrubbing: sections become a plain vertical scroll,
   every element pinned to its final state. */
@media (prefers-reduced-motion: reduce) {
  .section {
    height: auto;
  }
  .section__sticky {
    position: relative;
    height: 100vh;
  }
  .section__photo,
  .section__content,
  .fact,
  .ask,
  .cta,
  .fallback {
    transform: none !important;
    opacity: 1 !important;
  }
}
```

`height: auto` на секции убирает двойную высоту - без скраббинга лишний экран пустого скролла не нужен.

`position: relative` вместо `sticky` - секции просто идут одна за другой.

`!important` здесь оправдан: он должен перебить `calc()`-трансформы из Task 3, которые сидят на тех же элементах и в норме выигрывают по специфичности.

- [ ] **Step 3: Проверить reduced-motion**

В Chrome DevTools: `Cmd+Shift+P` → "Emulate CSS prefers-reduced-motion: reduce" → перезагрузить страницу.

Проверить:
- Анимаций нет, всё видно сразу
- Три экрана листаются один за другим, без пустых промежутков
- Текст читается на всех трёх
- Кнопка видна и нажимается

- [ ] **Step 4: Проверить страницу без JS**

В Chrome DevTools: `Cmd+Shift+P` → "Disable JavaScript" → перезагрузить.

Проверить:
- Фото видны
- Текст виден - дефолтные значения из разметки Task 1 ("Вика,", пункты, "Поехали?")
- Кнопка видна

Кнопка будет вести на `https://t.me/` без username - это ожидаемо, `init()` не выполнился. Страница остаётся читаемой, и это главное. Сценарий редкий, но цена страховки нулевая.

Если элементы невидимы - значит где-то потерялся дефолт `var(--p, 0)` в Task 3, найти и добавить.

- [ ] **Step 5: Коммит**

```bash
git add index.html
git commit -m "[UI] Add reduced-motion support and no-JS degradation"
```

---

### Task 5: Ручная проверка на телефоне и подготовка к отправке

**Files:**
- Modify: `index.html` (подставить настоящие значения)

**Interfaces:**
- Consumes: всё предыдущее
- Produces: страница, готовая к отправке

**Почему это отдельная задача.** Всё до этого проверялось в браузере на десктопе. Скраббинг на инерционном свайпе iOS ведёт себя иначе - это единственное место, где видно ступеньки и рывки, и их нельзя отловить в DevTools.

- [ ] **Step 1: Заполнить настоящие значения в CONFIG**

Спросить у Михаила и подставить:

```js
const CONFIG = {
  name: "Вика",
  place: "<название базы>",
  when: "<дата или «выходные»>",
  telegram: "<настоящий публичный username>",
  reply: "Я согласна! Поехали 🏍️"
};
```

**Перед этим Михаил должен проверить в настройках Telegram, что username установлен и не скрыт настройками приватности.** Без публичного username ссылка `t.me/ник?text=` не откроется вообще - это единственное предусловие, от которого зависит вся страница.

Тексты пунктов в разметке тоже показать Михаилу - они черновые, он перепишет под свою интонацию.

- [ ] **Step 2: Подставить настоящий og:image URL**

В `<head>` заменить `REPLACE-ME` на настоящий адрес GitHub Pages:

```html
<meta property="og:image" content="https://<username>.github.io/moto-date/images/03-sunset.jpg">
```

Обязательно абсолютный URL - относительный Telegram не понимает и превью не покажет.

- [ ] **Step 3: Запустить локальный сервер и открыть на iPhone**

```bash
python3 -m http.server 8000
```

Узнать локальный IP:

```bash
ipconfig getifaddr en0
```

На iPhone в Safari открыть `http://<IP>:8000` - телефон должен быть в той же Wi-Fi сети.

- [ ] **Step 4: Пять проверок на телефоне**

Каждую руками, по порядку:

1. **Пролистать сверху вниз и снизу вверх.** Анимации отматываются, рывков нет. Это главная проверка - если на инерционном свайпе видны ступеньки, уменьшить амплитуду `scale` в CSS (например 1.15 → 1.08).
2. **Повернуть в горизонтальную ориентацию.** Текст не разъезжается, кнопка на экране.
3. **Нажать кнопку.** Открылся Telegram, чат с Михаилом, текст в поле ввода, эмодзи не побился. Текст не отправился сам - это правильно.
4. **Включить "уменьшение движения"** (Настройки → Универсальный доступ → Движение), перезагрузить страницу. Всё читается, анимаций нет.
5. **Проверить фолбэк-ссылку** под кнопкой - открывает тот же чат, но без текста.

- [ ] **Step 5: Коммит**

```bash
git add index.html
git commit -m "[Config] Fill in real CONFIG values and og:image URL"
```

- [ ] **Step 6: Проверить превью в Telegram**

Это делается **после** публикации на GitHub Pages - превью строится по живому URL, локально его не проверить.

После публикации: прислать ссылку самому себе в Telegram и посмотреть, что показывается закат и подпись, а не серый прямоугольник.

Если превью не появилось - Telegram кэширует превью агрессивно. Проверить, что `og:image` открывается в браузере по тому же абсолютному URL.

**Push и публикация - только по явной команде Михаила.** По правилам проекта: коммиты можно, push нет.

---

## Что осталось за рамками плана

Сознательно не делается, обосновано в спеке:

- **Автотестов нет.** Единственная testable-логика - `sectionProgress()`; она написана чистой функцией без DOM-зависимостей, так что её можно будет вынести и покрыть тестами, если проект вырастет. Заводить тест-раннер под одну функцию в проекте из одного HTML-файла - больше инфраструктуры, чем кода.
- **Аналитики, счётчиков, отслеживания открытий нет.** Узнать "она открыла и не нажала" - не та информация, которая поможет.
- **Дополнительных секций нет.** Пригодных изображений три, растягивать их на пять нельзя: повтор фона читается как нехватка материала.
