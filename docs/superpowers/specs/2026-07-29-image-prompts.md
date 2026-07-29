# Промпты для изображений

Дата: 29.07.2026

Пять изображений для страницы-приглашения. Генерируются вручную через веб-сервис
(Gemini / ChatGPT / Midjourney), затем складываются в `images/`.

## Ландшафт

Место - окрестности Астаны: ковыльная степь, плоский горизонт, редкие берёзовые
колки, огромное небо. Хвойного леса и гор там нет, поэтому во всех промптах стоит
`No conifers` и `No mountains`.

`No mountains` не перестраховка: на слово "steppe" модели охотно добавляют горную
гряду на горизонте - вероятно, из монгольских и киргизских фотографий. Под Астаной
горизонт плоский, гряда сразу выдаст чужое место.

## Что держит серию вместе

Во всех пяти промптах повторяется один набор параметров - без него картинки
выглядят как пять разных стоков, а не как одна серия. Менять только осознанно и
сразу во всех пяти:

- Время: раннее утро с длинными тенями, к финалу переходит в закат
- Ландшафт: открытая степь, плоский горизонт, небо занимает много кадра
- Палитра: выгоревшая золотистая трава, серебристый ковыль, бледно-синее небо,
  тёплая пыль. Зелени мало и она приглушённая
- Оптика: 35mm, естественная перспектива
- Плёночность: мягкий контраст, лёгкое зерно, без HDR

Туман из промптов убран: в сухой степи он редкость и прочитается как чужой кадр.
Оставлена лёгкая дымка у горизонта - она там бывает и хорошо работает под текст.

## Требование к композиции

Изображение занимает весь экран телефона (вертикаль ~9:19.5), поверх нижней части
ложится текст. Поэтому у каждой картинки **низ должен быть пустым** - небо,
дорога, трава, доски, что угодно без деталей. Иначе текст ляжет на важное и
станет нечитаемым.

В промптах указано 9:16, а не 9:19.5: большинство сервисов такую вытянутую
вертикаль не поддерживают, а из 9:16 нужный кадр обрезается без потерь. Главное -
гнать именно вертикаль; из горизонтального кадра вертикальный экран не вырезать.
В Midjourney это `--ar 9:16`.

Доля пустого низа разная и указана в каждом промпте: от 30% (мотоциклы, мало
текста) до 45% (финал, текст плюс кнопка).

Если сервис выдаёт картинку с деталями внизу - перегенерировать, а не пытаться
исправить затемнением. Затемнение спасает читаемость, но убивает фотографию.

---

## 1. Hero → `images/01-hero.jpg`

```
Vertical 9:16 photograph. Empty dirt track running straight through open
steppe grassland toward a flat horizon, seen from ground level. Early
morning, low sun from the side, long shadows across the track. Silvery
feather grass, sun-bleached golden steppe, pale blue sky filling the upper
half of the frame. A few distant birch trees far off on the horizon.

The bottom 40% of the frame is the dirt track and grass only — no objects,
no detail, empty and soft, so text can be placed over it.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No vehicles. No mountains. No text.
No watermark.
```

## 2. Куда, экобаза → `images/02-place.jpg`

```
Vertical 9:16 photograph. Small wooden cabin with a low pitched roof standing
at the edge of a birch grove where the trees meet open steppe, seen from a
distance. Early morning, warm sunlight on the timber wall, pale haze along
the flat horizon behind.

Same palette: sun-bleached golden grass, silvery feather grass, muted green
birch leaves, pale blue sky. The bottom 35% is open steppe grass — empty, no
detail, room for text.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No conifers. No mountains. No text.
No watermark.
```

## 3. Мотоциклы → `images/03-moto.jpg`

```
Vertical 9:16 photograph. Two adventure motorcycles parked side by side on a
dirt track in open steppe, angled away from the camera as if about to leave.
Dusty tanks, knobby tyres. Early morning, warm sunlight raking across the
machines from the left, long soft shadows on the dirt. Flat grassland horizon
and wide pale sky behind them.

Same palette: sun-bleached golden grass, warm dust, pale blue sky. The bottom
30% is bare dirt track — empty, no detail, room for text.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people, no riders. No mountains. No text. No watermark.
```

## 4. Когда и что взять → `images/04-when.jpg`

```
Vertical 9:16 photograph. Riding gear laid out on weathered wooden planks
seen from directly above: two helmets, gloves, a folded jacket, a thermos.
Soft diffused morning daylight, warm tone, gentle shadows. A few dry grass
stems and light dust on the planks.

Same palette: warm golden tones, dusty neutrals, cool grey shadows.
Composition sits in the upper 60% of the frame; the bottom 40% is bare
wooden planks — empty, even, no detail, room for a list of text.

Shot on 35mm from above, soft film contrast, fine grain, muted saturation.
No HDR. No people. No text. No labels. No watermark.
```

Этот кадр почти не зависит от ландшафта - съёмка сверху на доски, степь в кадр не
попадает. Сухие стебли и пыль добавлены, чтобы он не выпадал из серии по фактуре.

## 5. Финал → `images/05-final.jpg`

```
Vertical 9:16 photograph. Wide view across open steppe at sunset, flat
horizon stretching edge to edge, enormous sky filling the upper two thirds
of the frame, layers of warm cloud lit from below.

Palette shifts warmer than the previous frames: deep amber and rose sky,
silhouetted dark grassland, cool blue haze along the horizon. The bottom 45%
is open steppe in shadow, soft and featureless — empty, room for text and a
button.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No mountains. No text. No watermark.
```

---

## После генерации

Файлы можно скидывать в `images/` с любыми именами - переименование и пережатие
до 1600px делает скрипт на этапе реализации (см. основной спек, раздел
"Производительность").
