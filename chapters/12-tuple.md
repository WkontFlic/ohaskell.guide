# Кортеж

В этой главе мы познакомимся с кортежем и ещё ближе подружимся с сопосовлением по образцу/шаблону (паттерн матчингом) с которым познакомились в главе 8 - Выбор и образцы.

Кортеж (англ. tuple) &mdash; ещё одна стандартная структура данных, которая, в отличие от списка, она может содержать данные разных типов.

Структуры, способные содержать данные разных типов, называют гетерогенными (в переводе с греческого: &laquo;разного рода&raquo;).

Вот как выглядит кортеж:

```haskell
("Haskell", 2010)
```

Круглые скобки и значения, разделённые запятыми. Этот кортеж содержит значение типа `String` и ещё одно, типа `Int`. Вот ещё пример:

```haskell
("Haskell", "2010", "Standard")
```

То есть ничто не мешает нам хранить в кортеже данные одного типа.

## Тип кортежа

Тип списка строк, как вы помните, `[String]`. И не важно, сколько строк мы запихнули в список, одну или миллион &mdash; его тип останется неизменным. С кортежем же дело обстоит абсолютно иначе.

Тип кортежа зависит от количества его элементов. Вот тип кортежа, содержащего две строки:

```haskell
(String, String)
```

Вот ещё пример:

```haskell
(Double, Double, Int)
```

И ещё:

```haskell
(Bool, Double, Int, String)
```

Тип кортежа явно отражает его содержимое. Поэтому если функция применяется к кортежу из 2-х строк, применить её к кортежу из 3-х строк никак не получится, ведь типы этих кортежей различаются:

```haskell
-- Разные типы
(String, String)
(String, String, String)
```

## Действия над кортежами

Со списками можно делать много всего, а вот с кортежами &mdash; не очень. Самые частые действия &mdash; собственно формирование кортежа и извлечение хранящихся в нём данных. Например:

```haskell
makeAlias :: String -> String -> (String, String)
makeAlias host alias = (host, alias) -- значения host alias переданные функции makeAlias помещаются в кортёж (host, alias)
```
или

```haskell
создатьПсевдоним :: String -> String -> (String, String)
создатьПсевдоним хост псевдонимХоста = (хост, псевдонимХоста) -- значения `хост` и `псевдонимХоста` переданные функции создатьПсевдоним помещаются в кортёж (хост, псевдонимХоста)
```

Пожалуй, ничего проще придумать нельзя: на входе 2 аргумента, на выходе &mdash; 2-х элементный кортеж с этими аргументами. Двухэлементный кортеж называют ещё парой (англ. pair), ассоциативным массивом. И хотя кортеж может содержать сколько угодно элементов, на практике именно пары встречаются чаще всего.

Обратите внимание, насколько легко создаётся кортеж. Причина тому &mdash; уже знакомое нам сопоставление по образцу (паттерн матчинг):

```haskell
создатьПсевдоним хост псевдонимХоста = (хост, псевдонимХоста)

                 ____                   ____

                      ==============          ==============
```

Мы просто указываем соответствие между левой и правой сторонами определения: &laquo;Пусть первый элемент пары будет равен аргументу `хост`, а второй &mdash; аргументу `псевдонимХоста`&raquo;. Ничего удобнее и проще и придумать нельзя. И если бы мы хотели получить кортеж из 3-х элементов, это выглядело бы так:

```haskell
создатьПсевдоним :: String -> String -> (String, String, String)
создатьПсевдоним хост псевдонимХоста = (хост, "https://" ++ хост, псевдонимХоста)

                 ____                   ____                ____

                     ==============                              ================
```

Оператор `++` &mdash; это оператор склеивающий 2 строки в одну (конкатенация). Строго говоря, он склеивает 2 списка, но мы-то с вами уже знаем, что тип `String` есть ни что иное, как список `[Char]`. Таким образом, `"https://"` ++ `"www.ya.ru"` даёт нам `"https://www.ya.ru"`.

Извлечение элементов из кортежа также производится через сопоставление с образцом (паттерн матчинг):

```haskell
main :: IO ()
main =
  let (хост, псевдонимХоста) = создатьПсевдоним "173.194.71.106" "www.ya.ru"  -- создаётся локальный кортёж (хост, псевдонимХоста) который заполняется функцией создатьПсевдоним
  in print (хост ++ ", " ++ псевдонимХоста)
```

Функция `makeAlias` даёт нам пару из хоста и псевдонимХоста. Но что это за странная запись возле уже знакомого нам слова `let`? Это промежуточное выражение, но выражение хитрое, образованное через  сопоставление с образцом (паттерн матчинг). Чтобы было понятнее, сначала перепишем функцию без него:

```haskell
main :: IO ()
main =
  let пара  = создатьПсевдоним "173.194.71.106" "www.ya.ru" -- создаём локальный кортеж "пара" 
      хост  = fst пара  -- Берём 1-е значение пары - "173.194.71.106"
      псевдонимХоста = snd пара  -- Берём 2-е значение пары - "www.ya.ru"
  in print (хост ++ ", " ++ псевдонимХоста) -- выводим "173.194.71.106" + ", " + "www.ya.ru"
```

При запуске этой программы получим:

```bash
"173.194.71.106, www.ya.ru"
```

Стандартные функции `fst` и `snd` возвращают 1-й и 2-й элемент кортежа соответственно. Выражение `пара` соответствует кортежу "пара", выражение `хост` &mdash; значению хоста, а `псевдонимХоста` &mdash; значению псевдонимХоста. Но не кажется ли вам такой способ избыточным? Мы в Haskell любим изящные решения, поэтому предпочитаем сосоставление собразцом (паттерн матчинг). Вот как получается вышеприведённый способ:

```haskell
let (хост, псевдонимХоста) = создатьПсевдоним "173.194.71.106" "www.ya.ru"

let (хост, псевдонимХоста) = ("173.194.71.106", "www.ya.ru")

                     данное значение
     это
     хост
                                       а вот это значение
           это
           имя
```

Вот такая простая магия. Функция `создатьПсевдоним` даёт нам пару, и мы достоверно знаем это! А если знаем, нам не нужно вводить промежуточные выражения вроде `пара`. Мы сразу говорим:

```haskell
let (хост, псевдонимХоста) = создатьПсевдоним "173.194.71.106" "www.ya.ru"

                              мы точно знаем, что выражение,
                              вычисленное этой функцией
     это вот
             такая пара
```

Это &laquo;зеркальная&raquo; модель: через сопоставление с образцом (паттерн матчинг0 формируем:

```haskell
-- Формируем правую сторону
-- на основе левой...
хост псевдонимХоста = (хост, псевдонимХоста)

>>>>                   >>>>

     >>>>>>>>>>>>>           >>>>>>>>>>>>>>
```

и через него же извлекаем:

```haskell
-- Формируем левую сторону
-- на основе правой...
(хост, псевдонимХоста) = ("173.194.71.106", "www.ya.ru")

 <<<<                     <<<<<<<<<<<<<<<<

       <<<<<<<<<<<<<<                       <<<<<<<<<<<
```

Вот ещё один пример работы с кортежем через сопоставление с образцом (паттерн матчинг):
Пример для шахмат.

```haskell
переместитьФигуру :: String             -- функция принимает строку
          -> (String, String)           -- функция принимает кортеж из 2-з строк
          -> (String, (String, String)) -- функция выдаёт котреж из строки и вложенный кортеж их 2- строк
переместитьФигуру цвет (откуда, to) = (цвет, (откуда, куда))

main :: IO ()
main = print (цвет ++ ": " ++ откуда ++ "-" ++ куда)
  where
    (цвет, (откуда, куда)) = переместитьФигуру "Белые" ("e2", "e4")
```

И на выходе получаем:

```bash
"Белые: e2-e4"
```

Обратите внимание, объявление функции отформатировано чуток иначе: типы выстроены друг под другом через выравнивание стрелок под двоеточием. Вы часто встретите такой стиль в Haskell-проектах.

Функция `переместитьФигуру` даёт нам кортеж с вложенным кортежем, а раз мы точно знаем вид этого кортежа, сразу указываем `where`-выражение в виде образца:

```haskell
(цвет, (откуда, куда)) = переместитьФигуру "Белые" ("e2", "e4")

 _____                                      _____

         ====                                      ====

               ....                                       ....
```

## Не всё

Мы можем вытаскивать по образцу лишь часть нужной нам информации. Помните универсальный образец `_`? Взгляните:

```haskell
-- Поясняющие псевдонимы ~ типов?
type UUID      = String
type ПолноеИмя = String
type ЭлПочта   = String
type Возраст   = Int
type Пациент   = (UUID, ПолноеИмя, ЭлПочта, Возраст)

элПочтаПациента :: Пациент -> ЭлПочта  -- функция элПочтаПациента принимает значение типа Пациент фактически (Строка, Строка, Строка, Целое), и возвращает тип ЭлПочта фактически (Строка)
элПочтаПациента (_, _, ЭлПочта, _) = ЭлПочта

main :: IO ()
main =
  putStrLn (элПочтаПациента ( "63ab89d"
                         , "Иван Петров"
                         , "admin@ya.ru"
                         , 59
                         ))
```

Функция `элПочтаПациента` даёт нам почту пациента. Тип `Patient` &mdash; это псевдоним для кортежа из 4-х элементов: уникальный идентификатор, полное имя, адрес почты и возраст. Дополнительные псевдонимы делают наш код яснее: одно дело видеть безликую `String` и совсем другое &mdash; `ЭлПочта`.

Рассмотрим внутренность функции `элПочтаПациента`:

```haskell
элПочтаПациента (_, _, ЭлПочта, _) = ЭлПочта
```

Функция говорит нам: &laquo;Да, я знаю, что мой аргумент &mdash; это 4-х элементный кортеж, но меня в нём интересует исключительно 3-й по счёту элемент, соответствующий адресу почты, его я и верну&raquo;. Универсальный образец `_` делает наш код проще и понятнее, ведь он помогает нам опускать то, что нам неинтересно. Строго говоря, мы не обязаны использовать `_`, но с ним будет лучше.

## А если ошиблись?

При использовании сопоставления собразцом (паттерн матчинга) в отношении пары следует быть внимательным. Представим себе, что вышеупомянутый тип `Patient` был расширен:

```haskell
type UUID      = String
type ПолноеИмя = String
type ЭлПочта   = String
type Возраст   = Int
type КодЗаболевания = Int  -- Новый элемент.
type Пациент = ( UUID
               , ПолноеИмя
               , ЭлПочта
               , Age
               , КодЗаболевания
               )
```

Был добавлен идентификатор заболевания. И всё бы хорошо, но внести изменения в функцию `элПочтаПациента` мы забыли:

```haskell
элПочтаПациента :: Пациент -> ЭлПочта -- функция принимает тип Пациент - возвращате тип ЭлПочта  
элПочтаПациента (_, _, ЭлПочта, _) = ЭлПочта

              ^  ^  ^      ^  -- А пятый элемент где?
```

К счастью, в этом случае компилятор строго обратит наше внимание на ошибку:

```bash
Couldn't match type ‘(t0, t1, String, t2)’
               with ‘(UUID, ПолноеИмя, ЭлПочта, Возраст, КодЗаболевания)’
Expected type: Пациент
  Actual type: (t0, t1, String, t2)
In the pattern: (_, _, ЭлПочта, _)
```

Оно и понятно: функция `элПочтаПациента` использует образец, который уже некорректен. Вот почему при использовании сопоставления с образцом (паттерн матчинга) следует быть внимательным.

На этом наше знакомство с кортежем считаю завершённым, в последующих главах мы будем периодически их использовать.

## Для любопытных

Для работы с элементами многоэлементных кортежей можно использовать готовые библиотеки, во избежании получния длинных цепочек сопоставления с образцом. Например, пакет [tuple](http://hackage.haskell.org/package/tuple):

```haskell
Data.Tuple.Select

main :: IO ()
main = print (sel4 (123, 7, "hydra", "DC:4", 44, "12.04"))
```

Функция `sel4` из модуля `Data.Tuple.Select` извлекает 4-й по счёту элемент кортежа, в данном случае строку `"DC:4"`. Там есть функции вплоть до `sel32`, авторы вполне разумно сочли, что никто, находясь в здравом уме и твёрдой памяти, не станет оперировать кортежами, состоящими из более чем 32 элементов.

Кроме того, мы и обновлять элементы кортежа можем:

```haskell
import Data.Tuple.Update

main :: IO ()
main = print (upd2 2 ("si", 45)) -- функция upd2 обновляет 2-й элемент кортежа на "2"
```

Естественно, по причине неизменности кортежа, никакого обновления тут не происходит, но выглядит симпатично. При запуске получаем результат:

```bash
("si",2)
```

2-й элемент кортежа изменился с `45` на `2`.

