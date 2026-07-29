# Промпты для изображений

Дата: 29.07.2026

Пять изображений для страницы-приглашения. Генерируются вручную через веб-сервис
(Gemini / ChatGPT / Midjourney), затем складываются в `images/`.

## Откуда взяты данные о месте

Разделено намеренно: что подтверждено, что нет.

**Подтверждено.** Михаил дал ссылки 2ГИС на две базы, из них разобраны
координаты:

- База 1: 51.265103 N, 71.658421 E - около 19 км к северо-востоку от центра
  Астаны
- База 2: 51.089619 N, 71.126816 E - около 21 км к юго-западу

Сами страницы 2ГИС недоступны (домен заблокирован на уровне сети), Instagram базы
тоже. Спутниковые снимки посмотреть не удалось.

Ландшафт подтверждён независимыми источниками по этой местности:

- В 30-60 минутах езды на юг и запад от Астаны начинается непрерывная открытая
  степь - это про базу 2
- Растительность: ковыль (Stipa), полынь (Artemisia), типчак (Festuca)
- Лесостепь с берёзами и соснами - к северу от Астаны, вперемешку с заброшенной
  пашней - это про базу 1
- За городом открытая безлесная равнина, плоский горизонт

**Не подтверждено и в промптах отсутствует.** Как выглядят постройки на базах,
какие именно мотоциклы, как выглядят трассы. Данных нет, поэтому в кадре построек
нет вообще - вместо домика во второй секции кадр самой дороги. Это и честнее, и
для приглашения работает лучше: зовёт в поездку, а не показывает недвижимость.

**Выбор базы не сделан**, поэтому промпты написаны на общий знаменатель: открытая
равнинная степь, деревья только силуэтами далеко на горизонте. Подходит к любой из
двух.

## Что держит серию вместе

Во всех пяти промптах повторяется один набор параметров - без него картинки
выглядят как пять разных стоков, а не как одна серия. Менять только осознанно и
сразу во всех пяти:

- Ландшафт: открытая равнинная степь, плоский горизонт, деревья только силуэтами
  далеко
- Растительность: ковыль, полынь, типчак - серебристо-золотая, невысокая
- Небо: занимает половину-две трети кадра
- Палитра: выгоревшее золото, полынное серебро, тёплая пыль, бледно-синее небо
- Время: раннее утро с длинными тенями, к финалу закат
- Оптика: 35mm, естественная перспектива
- Плёночность: мягкий контраст, лёгкое зерно, без HDR

Полынь в палитре важна: без её серебристо-серого степь выходит однотонно-жёлтой.

Сезон - лето / ранняя осень, трава выгоревшая. Если поездка сдвинется на май,
палитру менять на зелёную.

## Обязательные запреты

Во всех промптах стоит `No mountains`, `No hills`, `No buildings`.

`No mountains` и `No hills` не перестраховка: на слово "steppe" модели охотно
добавляют горную гряду или сопки на горизонте, вероятно из монгольских и
киргизских фотографий. Под Астаной равнина, любой рельеф на горизонте сразу выдаст
чужое место.

`No buildings` - потому что о постройках нет данных (см. выше).

## Требование к композиции

Изображение занимает весь экран телефона (вертикаль ~9:19.5), поверх нижней части
ложится текст. Поэтому у каждой картинки **низ должен быть пустым** - небо, дорога,
трава, доски, что угодно без деталей. Иначе текст ляжет на важное и станет
нечитаемым.

Доля пустого низа разная и указана в каждом промпте: от 30% (мотоциклы, мало
текста) до 45% (финал, текст плюс кнопка).

Если сервис выдаёт картинку с деталями внизу - перегенерировать, а не пытаться
исправить затемнением. Затемнение спасает читаемость, но убивает фотографию.

В промптах указано 9:16, а не 9:19.5: большинство сервисов такую вытянутую
вертикаль не поддерживают, а из 9:16 нужный кадр обрезается без потерь. Главное -
гнать именно вертикаль; из горизонтального кадра вертикальный экран не вырезать.
В Midjourney это `--ar 9:16`.

---

## 1. Hero → `images/01-hero.jpg`

```
Vertical 9:16 photograph. Empty dirt track running through open flat steppe
toward a dead-flat horizon, seen from ground level. Early morning, low sun
from the side, long shadows across the track. Silvery feather grass and
grey-green wormwood, sun-bleached golden grassland, pale blue sky filling the
upper half of the frame.

The bottom 40% of the frame is the dirt track and low grass only — no objects,
no detail, empty and soft, so text can be placed over it.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No vehicles. No mountains. No hills. No trees.
No buildings. No text. No watermark.
```

## 2. Дорога туда → `images/02-road.jpg`

```
Vertical 9:16 photograph. Twin dirt wheel ruts curving away through open
steppe grassland toward a dead-flat horizon, a thin line of distant trees
silhouetted far away on that horizon. Early morning, warm side light, dust
haze low over the ground.

Same palette: sun-bleached golden grass, silvery feather grass, grey-green
wormwood, pale blue sky. The bottom 35% is bare wheel ruts and low grass —
empty, no detail, room for text.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No mountains. No hills. No buildings.
No conifers. No text. No watermark.
```

## 3. Мотоциклы → `images/03-moto.jpg`

```
Vertical 9:16 photograph. Two dusty enduro motorcycles parked side by side on
a dirt track in open flat steppe, angled away from the camera as if about to
leave. Knobby tyres, scuffed tanks. Early morning, warm sunlight raking across
the machines from the left, long soft shadows on the dirt. Dead-flat grassland
horizon and wide pale sky behind them.

Same palette: sun-bleached golden grass, grey-green wormwood, warm dust, pale
blue sky. The bottom 30% is bare dirt track — empty, no detail, room for text.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people, no riders. No mountains. No hills. No buildings.
No text. No watermark.
```

## 4. Что взять → `images/04-when.jpg`

```
Vertical 9:16 photograph. Riding gear laid out on weathered wooden planks seen
from directly above: two helmets, gloves, a folded jacket, a thermos. Soft
diffused morning daylight, warm tone, gentle shadows. A few dry grass stems
and fine dust scattered on the planks.

Same palette: warm golden tones, dusty neutrals, cool grey shadows.
Composition sits in the upper 60% of the frame; the bottom 40% is bare wooden
planks — empty, even, no detail, room for a list of text.

Shot on 35mm from above, soft film contrast, fine grain, muted saturation.
No HDR. No people. No text. No labels. No watermark.
```

Этот кадр не зависит от ландшафта - съёмка сверху на доски, степь в кадр не
попадает. Сухие стебли и пыль добавлены, чтобы он не выпадал из серии по фактуре.

## 5. Финал → `images/05-final.jpg`

```
Vertical 9:16 photograph. Wide view across open flat steppe at sunset, the
horizon dead level and stretching edge to edge, enormous sky filling the upper
two thirds of the frame, layers of warm cloud lit from below.

Palette shifts warmer than the previous frames: deep amber and rose sky,
silhouetted dark grassland, cool blue haze along the horizon. The bottom 45%
is open steppe in shadow, soft and featureless — empty, room for text and a
button.

Shot on 35mm, natural perspective, soft film contrast, fine grain, muted
saturation. No HDR. No people. No mountains. No hills. No buildings. No text.
No watermark.
```

---

## После генерации

Файлы можно скидывать в `images/` с любыми именами - переименование и пережатие
до 1600px делает скрипт на этапе реализации (см. основной спек, раздел
"Производительность").

## Если появится доступ к фотографиям базы

Скриншоты из Instagram базы или её сайта дадут то, чего здесь нет: цвет и фактуру
построек, реальный свет, вид места с земли. С ними промпты стоит переписать -
особенно вторую секцию, где сейчас дорога вместо домика.
