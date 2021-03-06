# hustle-script

## Цель работы

В данной работе я пытаюсь разработать формальный язык описания перемещения танцоров по паркету во время исполнения танца "хастл". Зачем: если удастся разработать соответствующий формализм, на его основе можно будет собирать новые связки из существующих элементов (просто стыкуя описания уже известных движений друг к другу), а также создавать новые элементы путем перебора соответствующих элементов создаваемого языка.

## Постановка задачи

В рамках данной работы описывается **только** перемещение танцоров по паркету. Работа рук, корпуса, лица и проч находится вне рассмотрения. Также в рамках данной работы не рассматриваются существенно акробатические элементы, так как их суть не в перемещении, а в взаимодействии танцоров в воздухе.

**Танцем** в данной работе будет называться последовательность **элементов**, которые могут следовать друг за другом (этому условию удовлетворяют не все элементы; детально это понятие будет описано ниже). 

**Элементом** называется последовательность из двух состояний пары. 

**Состояние пары** - это совокупность параметров пары, которая однозначно позционирует танцоров друг относительно друга. Все состояния пары инвариантны относительно поворота и перемещения пары как твердого целого. То есть "американка" или "раскрытие", станцованные на зрителя и от него, описываются одной и той же последовательностью состояний. В данной работе сознательно не вводится такое понятие как "ракурс".

В рамках данной работы требуется разработать символический язык, позволяющий минимальным количеством признаков описать произвольный танец.

## Состояние пары

Состояние пары предлагается описывать следующими признаками:
- **Стойка (stance)** 
  1. Левая нога впереди. Left Stance. `L`
  2. Правая нога впереди. Right Stance. `R`
  3. Центральная стояка - ноги расставлены в стороны. Middle Stance. `M`
  4. Стойка на левой ноге. Left only Stance. `LO`
  5. Стойка на правой ноге. Right only Stance. `RO`
- **Хват (grip).** Сначала идет рука партнера, потом рука партнерши.
  1. Однорукий левая за левую. Single hand left to left. `SLL`
  2. Однорукий левая за правую. Single hand left to right. `SLR`
  3. Однорукий правая за левую. Single hand right to left. `SRL`
  4. Однорукий правая за правую. Single hand right to right. `SRR`
  5. Двурукий за разноименные руки. Two opposite hands. `TO`
  6. Двурукий за одноименные руки, левая рука партнера сверху. Two same hands left on top. `TSL`
  7. Двурукий за одноименные руки, правая рука партнера сверху. Two same hands right on top. `TSR`
- **Поворот танцора (dancer's rotation)**. Определяется как угол, между линией пары (от головы танцора к голове его партнера) и направлением взгляда танцора (линия взгляда всегда предполагается перпендикулярной линии плеч). Во всех базовых фигурах, вроде, ограничивается, 4 значениями: 0, 90&deg;, -90&deg;, 180&deg;.

Кратко позиция пары может быть описана в следующем виде (на примере стойки перед 3-4 основного шага):
```
{
  stance: {
    male: L, // стойка партнера
    female: R, // стойка партнерши
  },
  rotation: {
    male: 0, // поворот партнера относительно центральной линии пары
    female: 0, // поворот партнерши относительно центральной линии пары
  },
  grip: SLR,
}
```

## Элемент

Элемент определяется как последовательность из двух краевых состояний, дополненная направлением движения каждого из танцоров. Направление движения, как везде выше, отсчитывается относительно линии взгляда.

Кратко элемент может быть описан в следующем виде:
```
{
  start: {
    // начальное состояние пары
  },
  finish: {
    // конечное состояние пары
  },
  motion: {
    male: 0, // направление движения партнера. 0 означает шаг вперед
    female: 0, // направление движения партнерши. 0 означает шаг вперед
  }
}
```

## Примеры описания простых элементов в предложенной нотации.

В качестве демонстрации использования предлагаемой нотации давайте опишем несколько наиболее распространенных элементов.

### Основной шаг

Поскольку в основном шаге не меняется ни хват, ни поворот партнеров относительно линии пары, для простоты эти параметры опущены. Также во всех элементах, кроме первого, старт совпадает с финишем предыдущего. Поэтому в этих элементах, кроме первого, поле `start` также опущено.

```
basic = {
  elements: [
    {
      start: {stance: {male: R, female: L}},
      finish: {stance: {male: L, female: R}}
      motion: {male: 0, female: 0}
    },
    {
      finish: {stance: {male: R, female: L}}
      motion: {male: -180, female: -180}
    },
    {
      finish: {stance: {male: L, female: R}}
      motion: {male: -180, female: -180}
    },
    {
      finish: {stance: {male: R, female: L}}
      motion: {male: 0, female: 0}
    },
  ]
}
```

### Американка за две руки

Хват на протяжении всей американки не меняется, поэтому он везде опускается

```
twoHandedAmerican = {
  elements: [
    {
      start: {stance: {male: R, female: L}},
      finish: {stance: {male: L, female: R}}
      motion: {male: 0, female: 0}
    }
    ...
  ]
}
```


