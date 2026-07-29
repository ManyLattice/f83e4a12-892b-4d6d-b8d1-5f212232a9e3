# Промпты для изображений

Дата: 29.07.2026

Пять изображений для страницы-приглашения. Генерируются вручную через веб-сервис
(Gemini / ChatGPT / Midjourney), затем складываются в `images/`.

## Что держит серию вместе

Во всех пяти промптах повторяется один набор параметров - без него картинки
выглядят как пять разных стоков, а не как одна серия. Менять только осознанно и
сразу во всех пяти:

- Время: позднее утро с дымкой, к финалу переходит в закат
- Погода: ясно, лёгкий туман в низинах
- Палитра: тёплый янтарный свет, глубокая зелень хвои, серо-синие тени
- Оптика: 35mm, естественная перспектива
- Плёночность: мягкий контраст, лёгкое зерно, без HDR

## Требование к композиции

Изображение занимает весь экран телефона (вертикаль ~9:19.5), поверх нижней части
ложится текст. Поэтому у каждой картинки **низ должен быть пустым** - небо,
дорога, туман, доски, что угодно без деталей. Иначе текст ляжет на важное и
станет нечитаемым.

Доля пустого низа разная и указана в каждом промпте: от 30% (мотоциклы, мало
текста) до 45% (финал, текст плюс кнопка).

Если сервис выдаёт картинку с деталями внизу - перегенерировать, а не пытаться
исправить затемнением. Затемнение спасает читаемость, но убивает фотографию.

---

## 1. Hero → `images/01-hero.jpg`

```
Vertical 9:19.5 photograph. Empty forest dirt road curving away between tall
pine trees, seen from ground level. Late morning, low golden sun breaking
through mist between the trunks, visible light rays. Warm amber light, deep
green conifers, grey-blue shadows.

The bottom 40% of the frame is the road surface and mist only — no objects,
no detail, empty and soft, so text can be placed over it.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No vehicles. No text. No watermark.
```

## 2. Куда, экобаза → `images/02-place.jpg`

```
Vertical 9:19.5 photograph. Small wooden cabin with a dark pitched roof
standing among pine trees at the edge of a clearing, seen from a distance.
Late morning, warm sunlight on the timber wall, mist still hanging in the
treeline behind.

Same palette as before: warm amber light, deep green conifers, grey-blue
shadows. The bottom 35% is open grass and light haze — empty, no detail,
room for text.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No text. No watermark.
```

## 3. Мотоциклы → `images/03-moto.jpg`

```
Vertical 9:19.5 photograph. Two adventure motorcycles parked side by side on
a forest dirt road, angled away from the camera as if about to leave. Dusty
tanks, knobby tyres. Late morning, warm sunlight raking across the machines
from the left, long soft shadows on the dirt.

Same palette: warm amber light, deep green conifers, grey-blue shadows. The
bottom 30% is open dirt road in shade — empty, no detail, room for text.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people, no riders. No text. No watermark.
```

## 4. Когда и что взять → `images/04-when.jpg`

```
Vertical 9:19.5 photograph. Riding gear laid out on weathered wooden planks
seen from directly above: two helmets, gloves, a folded jacket, a thermos.
Soft diffused daylight, warm tone, gentle shadows.

Same palette: warm amber light, grey-blue shadows, muted greens. Composition
sits in the upper 60% of the frame; the bottom 40% is bare wooden planks —
empty, even, no detail, room for a list of text.

Shot on 35mm from above, soft film contrast, fine grain, muted saturation.
No HDR. No people. No text. No labels. No watermark.
```

## 5. Финал → `images/05-final.jpg`

```
Vertical 9:19.5 photograph. Wide view over a forest valley from a high
clearing at sunset, layers of pine ridges fading into haze, warm orange sky
above.

Palette shifts warmer than the previous frames: deep amber and rose sky,
silhouetted dark green ridges, cool blue haze in the valley. The bottom 45%
is the misted valley floor, soft and featureless — empty, room for text and
a button.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No text. No watermark.
```

---

## После генерации

Файлы можно скидывать в `images/` с любыми именами - переименование и пережатие
до 1600px делает скрипт на этапе реализации (см. основной спек, раздел
"Производительность").
