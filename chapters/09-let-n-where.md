# Пусть будет там, Где...

В этой главе мы узнаем, как сделать наши функции более удобными и читабельными.

## Пусть

В нижеследующих примерах мы вновь будем использовать расширение GHC `MultiWayIf`, не забудьте включить его. Рассмотрим следующую функцию:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  if | timeInS <  40 -> timeInS + 120
     | timeInS >= 40 -> timeInS + 8 + 120
```

Мы считаем время некоторого события, и если исходное время меньше `40` секунд &mdash; результирующее время увеличено на `120` секунд, в противном случае &mdash; ещё на `8` секунд сверх того. Перед нами классический пример &laquo;магических чисел&raquo; (англ. magic numbers), когда смысл конкретных значений скрыт за семью печатями. Что за `40`, и что за `8`? Во избежание этой проблемы можно ввести промежуточные (только для выражений следующих за словом `in` ) выражения с более осмысленными названиями, и тогда код станет более понятным:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let threshold  = 40
      correction = 120
      delta      = 8
  in
  if | timeInS <  threshold -> timeInS + correction
     | timeInS >= threshold -> timeInS + delta + correction
```

Вот, совсем другое дело! Мы избавились от &laquo;магических чисел&raquo;, введя поясняющие выражения `threshold`, `correction` и `delta`. Конструкция `let-in` вводит поясняющие выражения по схеме:

```haskell
let ПРОМЕЖУТОЧНЫЕ_ВЫРАЖЕНИЯ in ВЫРАЖЕНИЯ_КУДА_ПОДСТАВЛЯЮТСЯ_ПРОМЕЖУТОЧНЫЕ_ВЫРАЖЕНИЯ
```

где `ПРОМЕЖУТОЧНЫЕ_ВЫРАЖЕНИЯ` &mdash; промежуточные выражения, объявляемые нами, а `ВЫРАЖЕНИЯ_КУДА_ПОДСТАВЛЯЮТСЯ_ПРОМЕЖУТОЧНЫЕ_ВЫРАЖЕНИЯ` &mdash; выражение, в котором используется выражения из `ПРОМЕЖУТОЧНЫЕ_ВЫРАЖЕНИЯ`. Когда мы написали:

```haskell
let threshold = 40
```

мы объявили: &laquo;Отныне выражение `threshold` равно выражению `40`&raquo;. Выглядит как присваивание, но мы-то уже знаем, что в Haskell присаивания нет. Теперь выражение `threshold` может заменить собою число `40` внутри выражений, следующих за словом `in`:

```haskell
  let threshold = 40
      ...
  in if | timeInS <  threshold -> ...
        | timeInS >= threshold -> ...
```

Эта конструкция легко читается:

```haskell
let    threshold  =      40       ... in ...

пусть  это        будет  этому        в  том
       выражение  равно  выражению       выражении
```

С помощью ключевого слова `let` можно ввести сколько угодно промежуточных выражений, что делает наш код понятнее, а во многих случаях ещё и короче.

Мы ведь можем упростить условную конструкцию, воспользовавшись `otherwise` (`в остальных случаях`):

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let threshold  = 40
      correction = 120
      delta      = 8
  in
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS + delta + correction
```

Важно помнить, что введённое конструкцией `let-in` выражение существует ТОЛЬКО лишь в рамках выражения, следующего за словом `in`, т.е. выражения введённые после слова let рапространяются даже не на всю функцию! Изменим функцию:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let threshold  = 40
      correction = 120
  in
  if | timeInS < threshold -> timeInS + correction
     | otherwise ->
        let delta     = 8 in timeInS
                             + delta
                             + correction

            это              существует лишь в
            выражение        рамках этого выражения
```

Мы ЕЩЁ сократили область видимости промежуточного выражения `delta`, сделав его видимым лишь в выражении `timeInS + delta + correction`.

При желании `let`-выражения можно записывать и в строчку:

```haskell
  ...
  let threshold = 40; correction = 120
  in
  if | timeInS < threshold -> timeInS + correction
     | otherwise ->
        let delta = 8 in timeInS + delta + correction
```

В этом случае мы перечисляем их через точку с запятой. Лично мне такой стиль не нравится, но выбирать вам.

## Где

Существует иной способ введения промежуточных выражений, взгляните:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS +
                    delta +
                    correction
  where
    threshold  = 40
    correction = 120
    delta      = 8
```

Ключевое слово `where` делает примерно то же, что и `let`, но промежуточные выражения задаются в конце функции. Такая конструкция читается подобно научной формуле:

```haskell
  S = V * t,      -- Выражение
где
  -- Всё то, что
  -- используется
  -- в выражении.
  S = расстояние,
  V = скорость,
  t = время.
```

В отличие от `let`, которое может быть использовано для введения сверх-локального выражения (как в последнем примере с `delta`), все `where`-выражения доступны в любой части выражения, ПРЕДШЕСТВУЮЩЕГО ключевому слову `where`. Т.о. расположив слово `where` (где) в самом конце функции можно распространить действие промежуточных выражений на всю функцию.

## Вместе

Мы можем использовать `let-in` и `where` совместно, в рамках одной функции:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let threshold = 40 in
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS + delta + correction
  where
    correction = 120
    delta      = 8
```

Часть промежуточных значений задана вверху, а часть &mdash; внизу. Общая рекомендация: не смешивайте `let-in` и `where` без особой надобности, такой код читается тяжело, избыточно.

Отмечу, что в качестве промежуточных могут выступать и более сложные выражения. Например:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let threshold = 40 in
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS + delta + correction
  where
    -- Это промежуточное выражение зависит от аргумента...
    correction = timeInS * 2
    -- А это - от другого выражения...
    delta      = correction - 4
```

Выражение `correction` равно `timeInS * 2`, то есть теперь оно зависит от значения аргумента функции. А выражение `delta` зависит в свою очередь от `correction`. Причём мы можем менять порядок задания выражений:

```haskell
  ...
  let threshold = 40
  in
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS + delta + correction
  where
    delta      = correction - 4
    correction = timeInS * 2
```

Выражение `delta` теперь задано первым по счёту, но это не имеет никакого значения. Ведь мы всего лишь объявляем равенства, и результат этих объявлений не влияет на конечный результат вычислений. Конечно, порядок объявления равенств не важен и для `let`-выражений:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let delta     = correction - 4
      threshold = 40
  in
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS + delta + correction
  where
    correction = timeInS * 2
```

Мало того, что мы задали `let`-выражения в другом порядке, так мы ещё и использовали в одном из них выражение `correction`! То есть в `let`-выражении использовалось `where`-выражение. А вот проделать обратное, увы, не получится:

```haskell
calculateTime :: Int -> Int
calculateTime timeInS =
  let delta     = correction - 4
      threshold = 40
  in
  if | timeInS < threshold -> timeInS + correction
     | otherwise -> timeInS + delta + correction
  where
    correction = timeInS * 2 * threshold -- Из let??
```

При попытке скомпилировать такой код мы получим ошибку:

```bash
Not in scope: ‘threshold’
```

Таково ограничение: использовать `let`-выражения внутри `where`-выражений невозможно, т.к. `where`-выражения уже не входят в выражение, следующее за словом `in`.

Ну что ж, пора двигаться дальше, ведь внутренности наших функций не ограничены условными конструкциями.

