# Промпты: три фрагмента для принятого концепта

Дата: 29.07.2026

## Статус

Концепт принят: `~/Downloads/ChatGPT Image Jul 29, 2026 at 12_50_00 PM.png` -
винтажный дневник путешественника. Рваные фрагменты на скотче, компас, пунктирный
маршрут, наброски на полях, следы протектора, кольца от чашки, рукописные пометки.
Палитра насыщенная: тёмная умбра, жжёная сиена, охра.

Отвергнуты: `variant-a/b/c.html` (аскетичная линия) и первая акварельная версия.

## Ключевое решение: что в картинке, а что в HTML

**В картинке (генерируется):**
- сама акварель на рваном фрагменте, скотч по углам
- вся графика на полях: компас, шлем, канистра, протектор, стебли, пятна, кольца
- рукописные пометки на полях: "шлем обязателен", "жду тебя", "топлива хватит" и др.
- фактура бумаги, разводы, пятна

**В HTML (набирается шрифтом):**
- `Вика,` и `предлагаю тебе`
- три строки: `В субботу - степь,` / `недалеко от города,` / `два мотоцикла.`
- `Поехали?`
- кнопка `Да, поехали`

Почему так: крупные надписи в картинке размылись бы при масштабировании под разные
экраны, а кнопка обязана быть нажимаемым элементом, не пикселями. Пометки на полях -
декор, их резкость некритична.

**Поэтому в промптах ниже есть место под текст**, и явно сказано не рисовать
крупных надписей.

## Формат

Вертикаль 9:16 на каждый фрагмент - экран телефона целиком. Не 3:2 как раньше:
теперь картинка занимает весь экран, а не полосу сверху.

Фон бумаги входит в картинку (в отличие от прошлой версии, где бумага была кодом).
Это упрощает стык: рваные края и пятна лежат на своей же бумаге, без подгонки.

---

## Фрагмент 1 - дорога → `art-01.webp`

```
Vertical 9:16. A page from a vintage traveller's journal, aged cream paper
filling the entire frame, edge to edge - visible fibre, foxing spots, water
stains, uneven tone, worn corners.

Centred in the UPPER 55% of the page: a torn-edged watercolor painting of an
empty dirt track receding across flat golden steppe toward a distant city
skyline on the horizon under a dramatic evening sky. The painting is a ragged
paper fragment laid on the page, held down by two strips of aged masking tape
at opposite corners, with a soft shadow beneath its deckled edges.

Around the margins, in sepia ink and graphite:
- a detailed compass rose in the lower left, drawn in fine ink
- a dotted route line curving from the upper right down and off the page edge,
  with a small x mark
- sketched grass stems and seed heads in the left and right margins
- a coffee ring stain in the lower right
- small handwritten Cyrillic annotations in a loose script, reading exactly:
  "Астана степь ветер свобода" in the upper left with a thin arrow pointing
  at the painting, and "сюда, за горизонт" in the right margin with an arrow

CRITICAL: leave the LOWER 40% of the page as clear open paper - only faint
texture, stains and a few thin ink flourishes there. No large lettering
anywhere. Do NOT render any large title text. That area is reserved for
typography added later in code.

Palette: rich and warm - burnt sienna, deep ochre, umber shadows, olive drab,
faded indigo in the sky. Saturated, never pastel or washed out.

Mixed media: loose watercolor washes with pigment pooling, fine ink linework
over them, visible graphite construction lines left unerased.

No large text. No title. No headings. No button. No English words. No
watermark. No device frame, no phone bezel, no browser chrome. Shown flat and
straight on.
```

## Фрагмент 2 - мотоциклы → `art-02.webp`

```
Vertical 9:16. A page from the same vintage traveller's journal - identical
aged cream paper, same fibre, same foxing and stains, edge to edge.

Centred in the UPPER 60% of the page: a torn-edged watercolor and ink drawing
of two enduro motorcycles standing side by side on dry steppe ground, seen
three-quarter from the front. One machine warm orange, the other off-white.
Knobby tyres, dusty bodywork, rendered with visible graphite construction
lines and fine ink detailing on the engines and spokes. The drawing sits on a
ragged paper fragment overlapping the page, taped at one corner.

Around the margins, in sepia ink and graphite:
- a detailed sketch of a motocross helmet in the upper left
- a jerry can sketched in the right margin
- a tyre tread pattern rubbing in the lower left
- sketched grass tufts in the lower right
- coffee ring stains
- small handwritten Cyrillic annotations in loose script with thin arrows,
  reading exactly: "шлем обязателен" and "проверить давление" on the left,
  "топлива хватит" and "не забыть воду" on the right

CRITICAL: leave the LOWER 30% of the page as clear open paper - only faint
texture and stains. No large lettering anywhere. Do NOT render the three
lines of body text. That area is reserved for typography added later in code.

Palette: identical to the previous page - burnt sienna, deep ochre, umber,
olive drab, warm orange on the one machine. Saturated and warm.

Mixed media: watercolor washes, ink linework, unerased graphite. Same hand,
same journal, same author as the previous page.

No large text. No headings. No button. No English words. No watermark. No
device frame. Shown flat and straight on.
```

## Фрагмент 3 - закат → `art-03.webp`

```
Vertical 9:16. A page from the same vintage traveller's journal - identical
aged cream paper, same fibre, same foxing and stains, edge to edge.

Centred in the UPPER 55% of the page: a torn-edged watercolor of a blazing
sunset over flat open steppe - deep amber, orange and rose sky bleeding
wet-on-wet with dramatic cloud banks, the sun sitting on a dead-level horizon,
dark grassland silhouette below. Richer and more saturated than the other two
paintings. A ragged paper fragment taped at the upper right corner, deckled
edges casting a soft shadow.

Around the margins, in sepia ink and graphite:
- tall sketched grass stems and seed heads rising in the lower left and lower
  right corners, drawn in fine ink
- a small sketched sun with rays in the upper right
- a thumbprint smudge in the lower right
- two small hand-drawn hearts, understated
- coffee ring stains
- small handwritten Cyrillic annotations in loose script with thin arrows,
  reading exactly: "закат здесь особенный" in the upper left, "лето в степи"
  in the upper right, "это будет хороший день" in the lower left, and
  "жду тебя" in the lower right

CRITICAL: leave the LOWER 40% of the page as clear open paper - only faint
texture, stains, and the sketched grass in the corners. No large lettering.
Do NOT render any large title text and do NOT draw a button. Both are added
later in code.

Palette: the warmest of the three - deep amber, burnt orange, rose, crimson in
the sky, deep umber and sepia in the foreground. Rich and saturated.

Mixed media: watercolor bleeding wet-on-wet, ink linework, unerased graphite.
Same hand and same journal as the other two pages.

No large text. No headings. No button. No English words. No watermark. No
device frame. Shown flat and straight on.
```

---

## Требования к результату

**Одна серия.** Три страницы должны выглядеть из одного дневника: та же бумага, те же
пятна, та же рука. Генерировать подряд, в один сеанс. Если сервис принимает референс -
приложить принятый концепт.

**Свободный низ обязателен.** 30-40% страницы снизу - чистая бумага. Туда ляжет текст
HTML-ом. Если генератор нарисовал там крупные надписи - перегенерировать, замазать не
получится.

**Никаких крупных надписей.** Только мелкие рукописные пометки на полях. Если
появились большие буквы - брак.

**Вертикаль 9:16.** Не 3:2. Каждый фрагмент - целый экран.

## Куда положить

```
images/
├── art-01.webp   дорога
├── art-02.webp   мотоциклы
└── art-03.webp   закат
```

PNG тоже подойдёт - конвертацию и оптимизацию сделает Claude. Прозрачность здесь НЕ
нужна: бумага входит в картинку.

Старые `art-01-road.webp`, `art-02-moto.webp`, `art-03-sunset.webp` останутся в папке
до момента, когда новая вёрстка заработает.

## Что дальше

1. Михаил генерирует три фрагмента
2. Claude оптимизирует, собирает вёрстку заново: картинка на весь экран, текст в
   свободной нижней зоне
3. Движок скраббинга, `CONFIG`, Telegram-ссылка переезжают как есть - они отлажены
4. `variant-a/b/c.html` удаляются
