# Interstyler

Ответы на вопросы с собеседований по HTML и CSS.  
Простые ответы на сложные (и не очень) вопросы.  
Да-да, это повторение mdn, спецификаций, и статьи, что вы когда-то читали, ничего нового.

## Оглавление
- [Виды HTML-элементов](#виды-html-элементов)
- [Formatting Context](#formatting-context-контекст-форматирования)
- [Stacking Context](#stacking-context-контекст-наложения)
- [Специфичность CSS селекторов](#специфичность-css-селекторов)
- [Схлопывание внешних отступов](#схлопывание-внешних-отступов)
- [Float](#float)
- [Центрирование элемента на экране](#центрирование-элемента-на-экране)
- CSS Методологии
- Техники изоляции стилей
- Viewport
- Пропроцессоры Less, Sass, PostCSS, разница между ними, разница между extend/mixing/entend-only в Sass.
- Анимации / Трансформации
- Способы подключения SVG
- Оптимизация скорости загрузки
- RWD
- A11y (Доступность)
- Регрессионное тестирование
- Табличная / email-верстка
- Микроформаты и OpenGraph
- Retina-support
- Системы сборки

## Вопросы и ответы

### Виды HTML-элементов

Все HTML-теги классифицируются по их логической роли. Об этом пишет [спецификация](https://html.spec.whatwg.org/multipage/dom.html#content-models "ваш любимый whatwg"). Каждый элемент в html попадает в ноль или более категорий, которые объединяют элементы с похожими характеристиками. В спецификации используются следующие широкие категории:
- Metadata content (контент метаданных)
- Flow content (потоковый контент)
- Sectioning content (структурный контент)
- Heading content (заголовочный контент)
- Phrasing content (фразовый контент)
- Embedded content (встроенный контент)
- Interactive content (интерактивный контент)

Существует широко распространённое заблуждение, что в HTML существуют «блочные», «строчные», и даже «строчно-блочные» элементы. Однако это деление – историческая ошибка, о чём подробно можно прочитать [здесь](https://css-live.ru/articles/blochnyx-i-strochnyx-elementov-v-html-bolshe-net.html).  
Визуальная структура оформления, в которой есть «блочные», «строчные» и «внутристрочные» (инлайновые) боксы, относится исключительно к CSS.

### Formatting Context (Контекст Форматирования)

_Я не нашёл чёткого определения контекста форматирования, поэтому если вы можете сформулировать лучше – жду ваших pull requestов_

Контекст форматирования – область, которая была определена для разметки контента определенным образом. Каждый контекст форматирования имеет определенные правила о том, как в нём ведет себя разметка.

Существует несколько контекстов форматирования, основные:

- Инлайновый (Inline)

В инлайновом контексте форматирования боксы (boxes) располагаются горизонтально, один за другим, начиная с верхней части блока, в котором они содержатся. Горизонтальные `margin`'ы, `border`'ы и `padding`'и соблюдаются между этими боксами.  
Прямоугольная область, содержащая блоки, образующие линию, называется линейным боксом (line box).

Ширина линейного бокса определяется содержащим блоком и наличием `float`'ов.

- Блочный (Block)

Это регион страницы, в котором блоки размещаются в привычном для блоков порядке, и в котором плавающие элементы взаимодействуют с другими элементами.

Блочный контекст форматирования может быть создан чем-либо из [этого списка](https://developer.mozilla.org/ru/docs/Web/Guide/CSS/Block_formatting_context#Summary "mdnчик родной").

Правила размещения плавающих элементов и сброса обтекания применяются только к элементам внутри одного и того же блочного контекста форматирования. Плавающие элементы не влияют на размещение содержимого внутри других блочных контекстов форматирования, и отмена обтекания распространяется только на плавающие элементы из _того же самого_ контекста форматирования.  
Схлопывание внешних отступов тоже происходит только между блоками из _одного и того же_ блочного контекста форматирования.

- Табличный (Table)

С точки зрения модели визуального форматирования таблица может вести себя как элемент уровня блока (для элемента `display: table`) или элемента уровня строки (для элемента `display: inline-table`).

В обоих случаях таблица генерирует контейнерный блок основного бокса, называемый __боксом-оберткой таблицы (table wrapper box)__, который содержит сам бокс таблицы и любые боксы заголовков (caption boxes). __Бокс таблицы (table box)__ представляет собой бокс уровня блока, который содержит внутренние боксы таблицы. __Боксы заголовков__ являются основными боксами уровня блока, которые сохраняют свои собственные области содержимого, отступов, полей и границ и отображаются как обычные блочные боксы внутри _бокса-обёртки таблицы_. Размещаются ли _боксы заголовков_ до или после бокса таблицы, определяется свойством `caption-side`.

__Бокс-обёртка таблицы__ имеет уровень блока для `display: table` и уровень строки для `display: inline-table`. _Бокс-обёртка таблицы_ устанавливает контекст форматирования блока, а __бокс таблицы__ устанавливает контекст форматирования _таблицы_. Бокс таблицы (не бокс-обёртка таблицы) используется при выполнении вертикального выравнивания базовой линии для `inline-table`. Ширина бокс-обёртки таблицы – это border-edge ширина бокс таблицы в ней.  
Проценты, указанные в `width` и `height` в таблице относятся к блоку, содержащему бокс-обёртку таблицы, а не саму бокс-обёртку таблицы.

[Подробнее на w3c](https://www.w3.org/TR/CSS22/tables.html#model)

- Флексовый (Flex)

Flex-контайнер устанавливает новый __флексовый контекст форматирования (flex formatting context)__ для своего содержимого. Это то же самое, что и установление контекста форматирования блока, за исключением того, что вместо box layout используется flex layout. Например, float не влияют на flex layout, а `margin`'ы flex контейнера не схлопываются с `margin`'ами его содержимого. Flex-контейнеры образуют _содержащий блок (containing block)_ точно так же, как блочные контейнеры. [CSS21] Свойство `overflow` применяется к flex-контейнерам.

[Узнать больше на w3c](https://www.w3.org/TR/css-flexbox-1/#flex-formatting-context)

- Сеточный (Grid)

Grid-контейнер устанавливает новый __контекст форматирования сетки (grid formatting context)__ для его содержимого. Это то же самое, что и установление контекста форматирования блока, за исключением того, что вместо box layout используется grid layout. Содержимое grid-контейнера выкладывается в сетку, причем _линии сетки (grid lines)_ образуют границы каждого содержащего блока элементов сетки (of each grid item's containing block). Свойство `overflow` применяется к контейнерам сетки.

[Узнать больше на w3c](https://www.w3.org/TR/css-grid-1/#grid-formatting-context)

### Stacking Context (Контекст Наложения)

Контекст наложения – это концепция трехмерного расположения HTML элементов вдоль оси Z.  
Если элемент располагается в самом низу одного контекста наложения, то **никаким** образом **не получится** отобразить его над другим элементом в соседнем контексте наложения, располагающимся выше по иерархии, даже с установленным `z-index` равным миллиону.

Корневой элемент (HTML) всегда содержит корневой контекст наложения. Любой элемент на странице, не участвующий в локальном контексте наложения (сформированном любым из последующих вариантов), участвует в корневом контексте наложения.

Новый (локальный) контекст наложения формируется следующими элементами:
- `position: fixed`,
- `position: absolute` и `position: relative` с `z-index`, у которого значение отличается от `auto`,
- flex-элемент с `z-index` отличным от `auto`, чей родительский элемент имеет свойство `display: flex` или `inline-flex`,
- элементы с `opacity` меньше чем 1,
- элементы с `transform` отличным от `none`,
- элементы с `mix-blend-mode` отличным от `normal`,
- элементы с `filter` отличным от `none`,
- элементы с `isolation` установленным в `isolate`,
- если мы указываем элементу атрибут `will-change` (при этом не обязательно присваивать ему значение),
- элементы с `-webkit-overflow-scrolling` установленным в `touch`.

### Специфичность CSS селекторов

Специфичность – способ определения css-свойств, которые будут применяться к элементу.
Она представляет собой некоторую условную величину, рассчитываемую следующим образом:
1. За каждый идентификатор (`#id`) начисляется 100
2. За каждый класс (`.class`) и псевдокласс (например `:hover`) начисляется 10
3. За каждый селектор тега (`tagname`) и псевдоэлемент (например `::before`) начисляется 1.

Наиболее специфичный селектор применится к элементу.

Следует отметить, что универсальный селектор (`*`),  комбинаторы (`+`, `>`, `~`, '` `')  и отрицающий псевдокласс (`:not()`) __не влияют__ на специфичность. (Однако селекторы, объявленные внутри `:not()`, влияют).  
Стили, обьявленные в элементе (например, `style="font-weight:bold"`), всегда __переопределяют__ любые правила из внешних файлов стилей, их специфичность можно считать наивысшей.  
Когда при объявлении стиля используется модификатор `!important`, это объявление получает __наивысший__ приоритет среди всех прочих объявлений.  
Хотя технически модификатор `!important` не имеет со специфичностью ничего общего, он непосредственно на неё влияет. Поскольку `!important` усложняет отладку, нарушая естественное каскадирование ваших стилей, он не приветствуется и __следует избегать его использования__.  
Если к элементу применимы два взаимоисключающих стиля с модификатором `!important`, то применен будет стиль с большей специфичностью.

### Схлопывание внешних отступов

Свойства блоков `margin-top` и `margin-bottom` иногда комбинируются (схлопываются) в единый отступ, размер которого равен __наибольшему__ из комбинируемых отступов. Это поведение известно, как __схлопывание внешних отступов__.

Схлопывание внешних отступов происходит в трёх случаях:

1. Соседние элементы (siblings)

    Схлопываются отступы соседних элементов  (за исключением случая, когда последнему элементу применено свойство `clear`).

2. Родительский и первый/последний дочерние элементы

    Внешние отступы схлопываются если:

    - отстутствуют границы (`border`), внутренние отступы (`padding`), строчное содержимое или не используется свойство `clear` для отделения `margin-top` блока от `margin-top` блока, являющегося его первым дочерним элементом 
    - отстутствуют границы, внутренние отступы, строчное содержимое, `height`, `min-height` или `max-height` для отделения `margin-bottom` блока от `margin-bottom` его последнего дочернего элемента.  
Схлопнутые отступы заканчиваются за пределами родительского элемента.

3. Пустые блоки

    Если отстутствуют границы, внутренние отступы, строчное содержимое, `height` или `min-height` для отделения `margin-top` этого блока от его `margin-bottom`, то верхние и нижние внешние отступы этого блока схлопываются.

#### Отмена схлопывания margin

Схлопывание `margin` не срабатывает:

- для элементов с абсолютным позиционированием, т. е. таких, у которых `position` установлено как `absolute` или `fixed`;
- для обтекаемых элементов (для них свойство `float` задано как `left` или `right`);
- для элементов со свойством `display` заданым как `inline` или `inline-block`;
- для элементов, у родителя которых свойство `display` задано как `flex` или `inline-flex`;
- для элемента `<html>`.

Схлопывание не действует на дочерние элементы:

- если у родителя значение `overflow` задано как `auto`, `hidden` или `scroll`;
- если у родителя на стороне схлопывания задано свойство `padding`, со значением больше нуля;
- если у родителя на стороне схлопывания задано свойство `border`, со значением больше нуля.

### Float

Свойство `float` прижимает элемент к левому или правому краю родителя, а близлежащий текст обходит элемент с других сторон. Такое поведение текста напоминает поток воды, обтекающий камень, поэтому элементы с `float` называются плавающими, обтекаемыми или поплавками.

У свойства `float` есть три значения: `left`, `right` и `none`.

Обтекаемые элементы _не участвуют в потоке документа_, когда все элементы выстраиваются последовательно друг за другом. Из-за этого обтекаемые элементы _не оказывают_ влияние на высоту блока, внутри которого они располагаются. 

Элемент с `float` со значением `left` или `right` ведёт себя, будто ему добавили свойство `display` со значением `block`.  
Элемент __по ширине сжимается__ до размеров содержимого, если для элемента явно не установлена ширина `width`;

#### Очистка Float

Для отмены обтекания используется несколько вариантов:

1. 100% ширина элементов

    Если обтекаемые элементы будет занимать всю доступную ширину, то остальные элементы, следующие за ними, будут начинаться с новой строки. Для этого суммарная ширина элементов должна равняться __100%__.
    
2. Использоание `overflow`

    Свойство `overflow` управляет отображением содержимого блока, если оно целиком не помещается и выходит за область заданных размеров. Использование `overflow` со значением `auto`, `scroll` или `hidden` отменяет действие `float`.  
    Но иногда возникают ситуации, когда применение этого метода недопустимо. Это происходит в тех случаях, когда элемент выходит за пределы блока, при этом он будет «обрезан».

3. Использование `clear`

    Для отмены действия `float` от вышестоящего элемента применяется свойство `clear` со следующими значениями:

    - `left` — отменяет действие значения `left` у `float`;
    - `right` — отменяет действие значения `right` у `float`;
    - `both` — одновременно отменяет действие значений `left` и right у `float`.
    
    Чтобы отменить действие обтекания, свойство `clear` надо добавлять к элементу, идущему _после_ элемента с `float`. Обычно вводят универсальный класс, к примеру, `clearfix` и вставляют пустой `<div>` с этим классом.
    
 4. Псевдоэлемент `::after`
 
     Частое включение пустого элемента `<div>` со свойством `clear` захламляет код, поэтому используется псевдоэлемент `::after` со следующими стилями:
     ```
     .clearfix::after { 
       content: ""; /* Генерируем пустой элемент */
       clear: both; /* Отменяем обтекание*/
       display: block; /* Блочный элемент */
     }
     ```
    
    Для использования данного метода, необходимо добавить класс `clearfix` внутрь блока, содержащего плавающие элементы.
    
### Центрирование элемента на экране

Речь пойдёт о центрировании именно _на экране_. Существует несколько основных способов:

1. При помощи свойства `position`

    ```css
    .el{
      position: fixed; /* or absolute */
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
    }
    ```
    
    С помощью свойств `top` и `left` элемент сместится таким образом, что по середине экрана окажется верхний левый угол элемента. Свойство `transform: translate` необходимо для того, чтобы это исправить, и сместить элемент обратно на середину своей высоты и ширины.  
    В случае известной ширины и высоты, можно использовать следующие варианты кода:
    ```css
    .el{
      height: 200px;
      width: 200px;
      position: fixed; /* or absolute */
      left: calc(50% - 100px); /* 100px*2 - width элемента */
      top: calc(50% - 100px); /* 100px*2 - height элемента */
    }
    ```
    
    ```css
    .el{
      height: 200px;
      width: 200px;
      position: fixed; /* or absolute */
      left: 50%;
      top: 50%;
      margin: -100px 0 0 -100px;
    }
    ```
    
2. При помощи свойства `display: flex`
    
    Данное свойство применяется к __родителю__ центрируемого элемента. Далее имеются два варианта. 
    Либо __родителю__ также добавить свойства `justify-content: center` и `align-items: center`,  
    либо __элементу__ добавить свойство `margin: auto`.  
    Первый вариант является более предпочтительным.
