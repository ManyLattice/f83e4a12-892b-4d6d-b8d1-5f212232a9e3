# Промпт для богатого концепта

Дата: 29.07.2026

## Зачем

Первые три варианта (`variant-a/b/c.html`) отвергнуты все. Причина, скорее всего, не в
композиции - её крутили по-разному, - а в том, что вся линия была слишком аскетичной:
светлая бумага, один акцентный цвет, минимум деталей, много пустого места. Аскеза
задавалась текстовым тоном ("скупо и спокойно") и по инерции перешла на визуал, где
обернулась пустотой.

Новый промпт идёт в обратную сторону: плотность, слои, детали, насыщенность.

## Что осталось неизменным

Тексты приняты, их не переписываем:

```
Экран 1:  Вика,
          предлагаю тебе

Экран 2:  В субботу - степь,
          недалеко от города,
          два мотоцикла.

Экран 3:  Поехали?
          [Да, поехали]
```

## Как читать результат

Смотреть на плотность и характер, не на пиксельную точность. Текст на генерации может
выйти искажённым - это нормально, надписи ставятся HTML-ом при вёрстке.

Если результат понравится - вёрстка собирается заново по нему как по референсу.

---

## Промпт

```
Tall vertical image, 1:2 aspect ratio, high resolution. A finished, richly
detailed mobile website design: one page shown as three phone screens stacked
in a single vertical column, top to bottom, as one continuous scroll. Each
screen a 9:19.5 portrait rectangle, small even gaps between them.

This is a personal invitation page - a man inviting a woman to ride enduro
motorcycles across the summer steppe outside Astana, Kazakhstan. Warm, intimate,
handcrafted. It should feel like a page torn from someone's travel journal, not
like a corporate landing page.

VISUAL DENSITY - this is the most important instruction:
Every screen must feel layered and full, never sparse. Build depth with
overlapping elements: torn paper edges, layered washes, pencil underdrawing
showing through paint, ink linework over watercolor, scattered small marks.
Fill the composition. Empty space should feel like considered breathing room
between rich elements, never like unused canvas. If a screen looks calm and
minimal, it is wrong.

STYLE: mixed-media collage on aged paper. Combine within one coherent look:
- loose watercolor washes with visible pigment pooling and hard edges where
  paint dried
- fine ink linework and cross-hatching over the washes
- visible graphite pencil construction lines, not erased
- torn and layered paper fragments with visible deckled edges and slight
  shadows where they overlap
- small hand-drawn marginalia: arrows, dotted route lines, tiny sketched
  details, coffee-ring stains, a thumbprint smudge
- masking-tape corners holding photographs down

PAPER: warm aged cream with real presence - visible fibre, foxing spots,
subtle water stains at the edges, a slight uneven tone across the sheet. The
paper is a character, not a backdrop.

PALETTE - warm and saturated, far richer than a pale beige page:
burnt sienna, deep ochre, dusty terracotta, olive drab, faded indigo,
warm sepia ink, and one hot accent of orange-red for the button. Deep shadows
in umber. Nothing washed out or pastel.

SCREEN 1 - the opening:
A torn-edged watercolor of an empty dirt track receding into flat golden
steppe, taped down at two corners with masking tape. Around and beneath it:
pencil marginalia, a hand-drawn compass rose, a dotted route line curving off
the edge of the paper, a few sketched grass stems in ink. Large elegant serif
Cyrillic reading exactly "Вика," set low and confident, with a smaller
handwritten-style line beneath reading exactly "предлагаю тебе". A thin
hand-drawn ink rule under the text.

SCREEN 2 - the substance:
A layered watercolor and ink drawing of two enduro motorcycles standing side
by side on dry steppe ground - one warm orange, one off-white - rendered with
visible pencil construction lines and ink detailing on the machines. The
drawing sits on a torn paper fragment overlapping the page. Around it: small
sketched details in the margins - a helmet, a tyre tread pattern, a fuel can,
handwritten annotations. Three lines of Cyrillic text, each with a small
hand-drawn tick or dash marker, reading exactly:
"В субботу - степь,"
"недалеко от города,"
"два мотоцикла."

SCREEN 3 - the ask:
A rich saturated watercolor sunset over flat steppe, deep amber and rose sky
bleeding wet-on-wet, dark grassland silhouette below, torn edges, taped down.
Scattered ink marks and a few sketched grass stems in the foreground. Large
serif Cyrillic reading exactly "Поехали?" and below it one prominent
hand-drawn-feeling rounded button in hot orange-red with a slightly rough
inked outline, its centered label in cream reading exactly "Да, поехали".

TYPOGRAPHY: an elegant high-contrast serif for the two headings, a clean
readable sans for the body lines, and a touch of handwritten script for the
marginalia only. All Cyrillic text must be rendered as actual crisp legible
Russian words, correctly spelled, not squiggles or fake lettering. The letters
themselves stay sharp and printed - they are NOT painted or watercolored.

CONSISTENCY: the same aged paper, the same palette, the same hand runs through
all three screens so it reads as one page and one author.

Shown perfectly flat and straight on. No device frames, no phone bezels, no
status bars, no hands, no perspective, no drop shadows under the screens
themselves, no browser chrome, no surrounding mockup background, no
annotations from a designer. No watermark. No English text anywhere.
```

## Если хочется ещё плотнее

Добавить в конец промпта:

```
Maximalist density: every square centimetre of the paper has something on it -
a mark, a stain, a scrap, a line of pencil. Think of a naturalist's field
notebook after a long expedition, filled edge to edge.
```

## Если вышло слишком шумно и текст тонет

Убрать из промпта строки про marginalia и coffee-ring stains, оставить рваные края,
слои и наклейки. Плотность упадёт на треть, читаемость вырастет.
