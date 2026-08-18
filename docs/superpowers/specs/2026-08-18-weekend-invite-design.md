# Приглашение на свидание: развилка выходных

Дата: 18.08.2026

## Что это

Одностраничное приглашение для Вики. Страница предлагает два варианта
свидания на ближайшие выходные - пикник или конная прогулка - и даёт
ответить в Telegram.

Третье приглашение в серии (после `ceramic-date` и `moto-date`). Ссылок
на предыдущие нет, страница самостоятельная.

## Задача

Вика знает, что готовятся такие активности - это не сюрприз. Выбор
места и финальное решение остаются за постановщиком; страница нужна,
чтобы получить подтверждение и понять, к какому варианту она склоняется.

Выбор на странице - это её пожелание, а не финальное решение. Погода на
выходных может быть дождливая, и последнее слово всё равно за
постановщиком.

## Механика

Один экран, без скролла. Два состояния.

### Состояние 1: развилка

- Иллюстрация-развилка на весь экран: пара стоит спиной, держась за
  руки, перед ней дорога расходится надвое
- Слева вдоль левой дороги виден плед с корзиной у пруда, справа вдоль
  правой - две лошади в степи. Что ждёт на каждом пути, читается из
  самой картинки, без единого слова
- Поверх, сверху по центру - заголовок «Вика, эти выходные»
- Экран поделён невидимой вертикальной границей: тап слева выбирает
  пикник, справа - лошадей

### Состояние 2: выбрано

- Развилка сменяется крупной сценой выбранной активности
- Заголовок меняется на подтверждающий
- Снизу появляется кнопка «Написать»
- Кнопка открывает Telegram с заготовленным текстом

### Передумать нельзя

Обратного пути в интерфейсе нет. Захочет посмотреть второй вариант -
обновит страницу. Это осознанное решение: кнопка «назад» превратила бы
выбор в перебор опций, а он должен быть импульсом.

## Как стороны читаются как выбор

Текста-подсказки на странице нет, поэтому кликабельность передаётся
только поведением:

- Стороны подсвечиваются по очереди: левая поднимается, гаснет,
  поднимается правая. Обе никогда не горят одновременно - это и делает
  из подсветки выбор, а не мерцание фона
- Наведение курсором осветляет сторону, к которой тянешься
- Тач: сторона на мгновение проседает под пальцем

Подсветка исчезает навсегда после первого взаимодействия - касания или
наведения курсора: она сказала то, что должна была, и дальше только
мешала бы смотреть на картину.

Важно: подсветка НЕ спрятана за `@media (hover: hover)`. Многие
мобильные браузеры рапортуют поддержку hover, и подсказка пропадала
именно на тех устройствах, где она единственный сигнал о том, что
стороны можно выбирать.

Зума сцены нет намеренно. Он масштабировал картинку за пределы экрана,
и в нижней точке цикла её края возвращались вровень, показывая полоску
фона по бокам. Подсветка сторон решает ту же задачу - показать, что
выбор есть - и при этом прямо указывает, где именно он.

Пара в центре - нейтральная зона: она принадлежит обоим путям сразу.

Стороны размечены как `<button>`, а не `<div>`, чтобы работали с
клавиатуры и в screen reader.

## Переход

1. Развилка гаснет
2. На её месте проявляется сцена выбранной активности
3. Заголовок меняется
4. После того как сцена проявилась - появляется кнопка

Длительность ~600-700 мс, плавное замедление. Кнопка появляется с
задержкой, а не одновременно со сценой: движение должно читаться как
одно действие.

Обе сцены загружаются заранее, чтобы смена была мгновенной, а не
показывала пустой экран, пока грузится картинка.

При `prefers-reduced-motion` анимации выключаются, состояния меняются
мгновенно.

## Адаптив

Основной сценарий - мобильный: она откроет ссылку из Telegram.

Развилка и обе сцены показываются на весь экран в любой ориентации.
Граница выбора всегда вертикальная по центру: пикник слева, лошади
справа - так же, как на самой иллюстрации.

Кадрирование задаётся через `object-position` отдельно для каждой
иллюстрации, чтобы на узком экране в кадре остались и пара, и оба
объекта по сторонам.

## Тексты

Заголовки на странице - не живой текст, а нарисованная каллиграфия:
кремовые буквы с росчерками на прозрачном фоне. Ни один веб-шрифт с
кириллицей такого не даёт, а именно почерк здесь делает интонацию.

| Место | Текст | Как выводится |
|---|---|---|
| Заголовок вкладки | Вика, эти выходные | `document.title` из `CONFIG` |
| На развилке | Вика, эти выходные | `images/title-fork.webp` |
| После пикника | Значит, пикник | `images/title-picnic.webp` |
| После лошадей | Значит, лошади | `images/title-horses.webp` |
| Кнопка | Написать | живой текст, системный serif |
| В Telegram (пикник) | Пикничок 🧺🥰 | из `CONFIG` |
| В Telegram (лошади) | Лошкадки 🐴🥰 | из `CONFIG` |
| og:description | Выбирай | разметка |

Настоящие слова живут в `<h1 class="title-text">`, визуально скрытом:
он нужен поисковикам, screen reader и случаю без JS. Скрипт держит его
в согласии с картинкой, а сами картинки помечены `aria-hidden` - иначе
надпись прозвучала бы дважды.

Формулировки по-прежнему живут в `CONFIG`, но менять их теперь дороже:
текст придётся перерисовать. Это осознанная плата за почерк.

## Telegram

Кнопка открывает `t.me/ManyLattice` с текстом, подставленным в поле
ввода. Сообщение не отправляется автоматически - нажать send остаётся
её решением.

## Ассеты

Три иллюстрации, все в `images/`. Оригиналы PNG лежат в `.orig-art/`
вне git.

| | Развилка | Пикник | Лошади |
|---|---|---|---|
| Файл | `fork.webp` | `picnic.webp` | `horses.webp` |
| Размер | 173 КБ | 196 КБ | 136 КБ |
| Роль | Первый экран | После выбора | После выбора |

Все три - WebP 1086×1448, вертикальные, в одной живописной манере и
одном вечернем свете.

### Развилка

Пара стоит спиной, держась за руки, внизу по центру; лиц не видно.
Дорога расходится от их ног: левая уходит к пруду и деревьям, где на
траве лежит плед с корзиной, правая - в сухую степь, где стоят две
лошади. Верх кадра - открытое небо под заголовок.

Лиц нет намеренно: они сделали бы это историей нарисованных людей, а
так Вика видит вас двоих.

### Требования к кадру

Изображения показываются на экранах разной формы, поэтому:

1. **Сюжет с запасом по краям.** Телефон срезает по 9% ширины с каждой
   стороны. Плед и лошади должны лежать внутри этого запаса - иначе
   пропадёт именно то, ради чего рисовался кадр.
2. **Верх спокойный.** Там лежит каллиграфия - нужна ровная область без
   мелких деталей.

Первую версию развилки пришлось переснять: объекты стояли вплотную к
краям (плед на 0-12% ширины, лошади на 88-100%), и телефон срезал оба.
В промпте помогла прямая формулировка - внешние 20% с каждой стороны
будут обрезаны, там должна быть только пустая трава.

На широком экране кадр обрезается сверху и снизу, а пара стоит низко,
поэтому для landscape точка фокуса опущена к 82% высоты. Жертвуется
небо, которого в кадре много.

Поверх иллюстраций лежит тёмный оверлей: он держит читаемость белой
каллиграфии независимо от того, что на картинке.

## Технические решения

- Один файл `index.html`, стили и скрипт inline
- Без сборки, без зависимостей - как оба предыдущих проекта
- Хостинг GitHub Pages
- `og:image` - абсолютный URL (Telegram игнорирует относительные пути)
- `theme-color` подобран под палитру, чтобы chrome встроенного браузера
  Telegram не давал светлых полос сверху и снизу
- Без JS страница остаётся в состоянии развилки: обе иллюстрации видны,
  выбор не работает. Деградация мягкая, а не белый экран.

## Что намеренно не делается

- **Нет отправки выбора на сервер.** Ответ приходит в Telegram живым
  сообщением. Страница остаётся одним файлом без бэкенда.
- **Нет деталей свидания на странице** - ни времени, ни места, ни что
  надеть. Место ещё не выбрано, и выбирает его постановщик.
- **Нет плана Б на случай дождя.** Погода решается вживую, страница про
  настроение и подтверждение.

## Промпты для генерации

Копируются в генератор как есть. Оба кадра вертикальные, сюжет по
центру, верхняя пятая часть спокойная.

### Развилка - `images/fork.webp`

```
A dirt road seen from behind a young couple standing at a fork, holding
hands, seen from the back. They stand in the lower middle of the frame,
close to the viewer, facing away from us toward the two roads ahead.
Their backs and joined hands are clearly visible; their faces are not.
They are small enough that the landscape remains the subject - together
they occupy roughly one fifth of the image height.

The road splits right in front of them. The left road curves away
toward the UPPER LEFT and the right road curves away toward the UPPER
RIGHT - the two paths end far apart on opposite sides of the frame,
never meeting at a single vanishing point.

Left side: a green park landscape - leafy trees, mown grass, flowering
shrubs, and a calm pond reflecting the evening sky. Beside the left
road, at mid-distance, a clearly visible picnic scene: a red-and-white
checkered blanket on the grass with a wicker basket on it, crisp and
catching the warm light.

Right side: dry golden steppe - open grassland, low rolling hills,
scattered brush. Beside the right road, at mid-distance, two clearly
visible horses standing in the grass, their dark silhouettes standing
out sharply against the pale sunlit field.

Warm late-afternoon light, long soft shadows falling toward the viewer,
gentle golden haze in the far distance only. Both sides share the same
light and the same painterly hand.

Vertical composition, 3:4 aspect ratio. The upper middle of the image
is calm open sky, free of sharp detail, so text can sit over it. Keep
generous empty space along the left and right edges of the frame.
No landmark, no building, no signpost at the fork itself.

Painterly illustration, soft edges, oil-painting texture, warm and
romantic mood.
No text, no letters, no watermarks, no faces, no other people.
```

Что оказалось решающим при подборе: элементы по сторонам нужно просить
на `mid-distance`, а не «вдали» - на горизонте они физически выходят
мелкими и не читаются на телефоне. Слова `tiny` и `never the focus`
гасят их до неразличимости.

### Пикник - `images/picnic.webp`

```
A summer picnic laid out on grass, seen from a low three-quarter angle.
A woven basket, fresh fruit, a folded checkered blanket, two glasses.
Warm afternoon sunlight filtering through leaves above, dappled light
on the blanket. Lush warm greens, soft golden highlights, gentle shadows.

Vertical composition, 3:4 aspect ratio. The basket and blanket sit in
the centre of the frame with generous empty space on all four sides.
The top fifth of the image is calm out-of-focus foliage and sky - no
sharp detail, no high contrast there.

Painterly illustration, soft edges, warm and inviting mood.
No text, no letters, no watermarks, no people.
```

### Лошади - `images/horses.webp`

```
A horse standing in open steppe, seen from the side at eye level.
Dry golden grass stretching to a low horizon, dust hanging in warm
light, distant hills. Ochre and amber tones, sunlit haze, long soft
shadows across the grass.

Vertical composition, 3:4 aspect ratio. The horse is centred in the
frame with generous empty space on all four sides. The top fifth of
the image is plain open sky - no sharp detail, no high contrast there.

Painterly illustration, soft edges, warm and unhurried mood.
No text, no letters, no watermarks, no people, no riders.
```

### Если кадр не подошёл

Чаще всего ломается одно из двух:

- **Сюжет уехал от центра** - при раскрытии половины объект срежется.
  Добавить в промпт `subject strictly centred, wide margins on every side`.
- **Верх забит деталями** - заголовок станет нечитаемым. Усилить:
  `the upper 20% must be empty sky with no detail`.
