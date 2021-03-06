#Функции высшего порядка

<i>Цу-ли и Цу-су похвалялись размерами своих новых программ. «Двести тысяч строк»,- сказал Цу-ли,- «не считая комментариев!» Цу-су ответил: «Пф-ф, моя – почти миллион строк». Мастер Юнь-Ма сказал: «Моя лучшая программа занимает пятьсот строк». Услышав это, Цу-ли и Цу-су испытали просветление.

Мастер Юнь-Ма, Книга программирования

Есть два способа построения программ: сделать их настолько простыми, что там очевидно не будет ошибок, или же настолько сложными, что там не будет очевидных ошибок.

Энтони Хоар, 1980 лекция на вручении премии Тьюринга
</i>

Большая программа – затратная программа, и не только из-за времени её написания. Большой размер обычно означает сложность, а сложность сбивает с толку программистов. Сбитые с толку программисты делают ошибки в программах. Большая программа означает, что багам есть где спрятаться и их получается труднее отыскать.

Вернёмся ненадолго к двум примерам из введения. Первый самодостаточен, и занимает шесть строк.

```
var total = 0, count = 1;
while (count <= 10) {
  total += count;
  count += 1;
}
console.log(total);
```

Второй основан на двух внешних функциях и занимает одну строку.

```
console.log(sum(range(1, 10)));
```

В каком из них скорее встретится ошибка?

Если мы добавим размер определений sum и range, вторая программа тоже получится большой – больше первой. Но я всё равно утверждаю, что она скорее всего будет правильной.

Это будет потому, что выражение решения непосредственно относится к решаемой задаче. Суммирование числового промежутка – это не циклы и счётчики. Это суммы и промежутки.

Определения этого словаря (функции sum и range) будут включать циклы, счётчики и другие случайные детали. Но потому, что они выражают более простые концепции, чем вся программа, их проще сделать правильно.

##Абстракции
В программном контексте эти «словарные» определения часто называют абстракциями. Абстракции прячут детали и дают нам возможность разговаривать о задачах на высшем, или более абстрактном, уровне.

Сравните два рецепта горохового супа:

<i>Добавьте в ёмкость по одной чашке сухого гороха на порцию. Добавьте воды так, чтобы она покрыла горох. Оставьте его так минимум на 12 часов. Выньте горох из воды и поместите их на сковороду. Добавьте 4 чашки воды на порцию. Закройте сковороду и тушите горох два часа. Возьмите по половине луковицы на порцию. Порежьте на куски ножом, добавьте к гороху. Возьмите по одному стеблю сельдерея напорцию. Порежьте на куски ножом, добавьте к гороху. Возьмите по морковке на порцию. Порежьте на куски ножом, добавьте к гороху. Готовьте ещё 10 минут.</i>

Второй рецепт:

<i>На порцию: 1 чашка сухого гороха, половина луковицы, стебель сельдерея, морковка.
Вымачивайте горох 12 часов. Тушите 2 часа в 4 чашках воды на порцию. Порежьте и добавьте овощи. Готовьте ещё 10 минут.</i>

Второй – короче и проще. Но вам нужно знать чуть больше понятий, связанных с готовкой – вымачивание, тушение, резка (и овощи).

Программируя, мы не можем рассчитывать на то, что все необходимые слова будут в нашем словаре. Из-за этого вы можете скатиться до шаблона первого рецепта: диктовать компьютеру все мелкие шажки друг за другом, не замечая понятий более высокого уровня, которые они выражают.

Второй натурой программиста должно стать умение замечать, когда понятие умоляет придумать для него новое слово и вынести в абстракцию.

##Абстрагируем обход массива
Простые функции, которые мы использовали раньше, хороши для построения абстракций. Но иногда их бывает недостаточно.

В предыдущей главе мы несколько раз встречали такой цикл:

```
var array = [1, 2, 3];
for (var i = 0; i < array.length; i++) {
  var current = array[i];
  console.log(current);
}
```

Код пытается сказать: «для каждого элемента в массиве – вывести его в консоль». Но он использует обходной путь – с переменной для подсчёта i, проверкой длины массива, и объявлением дополнительной переменной current. Мало того, что он не очень красив, он ещё и является почвой для потенциальных ошибок. Мы можем случайно повторно использовать переменную i, вместо length написать lenght, перепутать переменные i и current, и т.п.

Давайте абстрагируем его в функцию. Можете придумать способ сделать это?

Довольно просто написать функцию, обходящую массив и вызывающую для каждого элемента console.log

```
function logEach(array) {
  for (var i = 0; i < array.length; i++)
    console.log(array[i]);
}
```

Но что, если нам надо делать что-то другое, нежели выводить элементы в консоль? Поскольку «делать что-то» можно представить как функцию, а функции – это просто переменные, мы можем передать это действие как аргумент:

```
function forEach(array, action) {
  for (var i = 0; i < array.length; i++)
    action(array[i]);
}

forEach(["Тили", "Мили", "Трямдия"], console.log);
// → Тили
// → Мили
// → Трямдия
```

Часто можно не передавать заранее определённую функцию в forEach, а создавать функцию прямо на месте.

```
var numbers = [1, 2, 3, 4, 5], sum = 0;
forEach(numbers, function(number) {
  sum += number;
});
console.log(sum);
// → 15
```

Выглядит похоже на классический цикл for, с телом цикла, записанным в блоке. Однако, теперь тело находится внутри функции, и также внутри скобок вызова forEach. Поэтому его нужно закрыть как фигурной, так и круглой скобкой.

Используя этот шаблон, мы можем задать имя переменной для текущего элемента массива (number), без необходимости выбирать его из массива вручную.

Вообще, нам даже не нужно писать самим forEach. Это стандартный метод массивов. Так как массив уже передан в качестве переменной, над которой мы работаем, forEach принимает только один аргумент – функцию, которую нужно выполнить для каждого элемента.

Для демонстрации удобства этого подхода вернёмся к функции из предыдущей главы. Она содержит два цикла, проходящих по массивам:

```
function gatherCorrelations(journal) {
  var phis = {};
  for (var entry = 0; entry < journal.length; entry++) {
    var events = journal[entry].events;
    for (var i = 0; i < events.length; i++) {
      var event = events[i];
      if (!(event in phis))
        phis[event] = phi(tableFor(event, journal));
    }
  }
  return phis;
}
```

Используя forEach мы делаем запись чуть короче и гораздо чище.

```
function gatherCorrelations(journal) {
  var phis = {};
  journal.forEach(function(entry) {
    entry.events.forEach(function(event) {
      if (!(event in phis))
        phis[event] = phi(tableFor(event, journal));
    });
  });
  return phis;
}
```

##Функции высшего порядка
Функции, оперирующие другими функциями — либо принимая их в качестве аргументов, либо возвращая их, называются <a href="https://ru.wikipedia.org/wiki/Функция_высшего_порядка">функциями высшего порядка</a>. Если вы уже поняли, что функции – это всего лишь переменные, ничего особенного в существовании таких функций нет. Термин происходит из математики, где различия между функциями и другими значениями воспринимаются более строго.

Функции высшего порядка позволяют нас абстрагировать действия, а не только значения. Они бывают разными. Например, можно сделать функцию, создающую новые функции.

```
function greaterThan(n) {
  return function(m) { return m > n; };
}
var greaterThan10 = greaterThan(10);
console.log(greaterThan10(11));
// → true
```

Можно сделать функцию, меняющую другие функции.

```
function noisy(f) {
  return function(arg) {
    console.log("calling with", arg);
    var val = f(arg);
    console.log("called with", arg, "- got", val);
    return val;
  };
}
noisy(Boolean)(0);
// → calling with 0
// → called with 0 - got false
```

Можно даже делать функции, создающие новые типы управления потоком выполнения программы.

```
function unless(test, then) {
  if (!test) then();
}
function repeat(times, body) {
  for (var i = 0; i < times; i++) body(i);
}

repeat(3, function(n) {
  unless(n % 2, function() {
    console.log(n, "is even");
  });
});
// → 0 is even
// → 2 is even
```

Правила лексических областей видимости, которые мы обсуждали в главе 3, работают нам на пользу в таких случаях. В последнем примере переменная n – это аргумент внешней функции. Поскольку внутренняя функция живёт в окружении внешней, она может использовать n. Тела таких внутренних функций имеют доступ к переменным, окружающим их. Они могут играть роль блоков {}, используемых в обычных циклах и условных выражениях. Важное отличие в том, что переменные, объявленные внутри внутренних функций, не попадают в окружение внешней. И обычно это только к лучшему.

##Передача аргументов
Функция noisy, объявленная ранее, которая передаёт свой аргумент в другую функцию, не совсем удобна.

```
function noisy(f) {
  return function(arg) {
    console.log("calling with", arg);
    var val = f(arg);
    console.log("called with", arg, "- got", val);
    return val;
  };
}
```

Если f принимает больше одного параметра, она получит только первый. Можно было бы добавить кучу аргументов к внутренней функции (arg1, arg2 и т.д.) и передать всех их в f, но ведь неизвестно, какого количества нам хватит. Кроме того, функция f не могла бы корректно работать с arguments.length. Так как мы всё время передавали бы одинаковое число аргументов, было бы неизвестно, сколько аргументов нам было задано изначально.

Для таких случаев у функций в JavaScript есть метод apply. Ему передают массив (или объект в виде массива) из аргументов, а он вызывает функцию с этими аргументами.

```
function transparentWrapping(f) {
  return function() {
    return f.apply(null, arguments);
  };
}
```

Данная функция бесполезна, но она демонстрирует интересующий нас шаблон – возвращаемая ею функция передаёт в f все полученные ею аргументы, но не более того. Происходит это при помощи передачи её собственных аргументов, хранящихся в объекте arguments, в метод apply. Первый аргумент метода apply, которому мы в данном случае присваиваем null, можно использовать для эмуляции вызова метода. Мы вернёмся к этому вопросу в следующей главе.

##JSON
Функции высшего порядка, которые каким-то образом применяют функцию к элементам массива, широко распространены в JavaScript. Метод forEach – одна из самых примитивных подобных функций. В качестве методов массивов нам доступно много других вариантов функций. Для знакомства с ними давайте поиграем с другим набором данных.

Несколько лет назад кто-то обследовал много архивов и сделал целую книгу по истории моей фамилии. Я открыл её, надеясь найти там рыцарей, пиратов и алхимиков… Но оказалось, что она заполнена в основном фламандскими фермерами. Для развлечения я извлёк информацию по моим непосредственным предкам и оформил в формате, пригодном для чтения компьютером.

Файл выглядит примерно так:

```
[
  {"name": "Emma de Milliano", "sex": "f",
   "born": 1876, "died": 1956,
   "father": "Petrus de Milliano",
   "mother": "Sophia van Damme"},
  {"name": "Carolus Haverbeke", "sex": "m",
   "born": 1832, "died": 1905,
   "father": "Carel Haverbeke",
   "mother": "Maria van Brussel"},
  … и так далее
]
```

Этот формат называется JSON, что означает JavaScript Object Notation (разметка объектов JavaScript). Он широко используется в хранении данных и сетевых коммуникациях.

JSON похож на JavaScript по способу записи массивов и объектов – с некоторыми ограничениями. Все имена свойств должны быть заключены в двойные кавычки, а также допускаются только простые величины – никаких вызовов функций, переменных, ничего что включало бы вычисления. Также не допускаются комментарии.

JavaScript предоставляет функции JSON.stringify и JSON.parse, которые преобразовывают данные из этого формата и в этот формат. Первая принимает значение и возвращает строчку с JSON. Вторая принимает такую строчку и возвращает значение.

```
var string = JSON.stringify({name: "X", born: 1980});
console.log(string);
// → {"name":"X","born":1980}
console.log(JSON.parse(string).born);
// → 1980
```

Переменная ANCESTRY_FILE <a href="http://eloquentjavascript.net/code/ancestry.js">доступна здесь</a>. Она содержит JSON файл в виде строки. Давайте её раскодируем и посчитаем количество упомянутых людей.

```
var ancestry = JSON.parse(ANCESTRY_FILE);
console.log(ancestry.length);
// → 39
```

##Фильтруем массив
Чтобы найти людей, которые были молоды в 1924 году, может пригодиться следующая функция. Она отфильтровывает элементы массива, которые не проходят проверку.

```
function filter(array, test) {
  var passed = [];
  for (var i = 0; i < array.length; i++) {
    if (test(array[i]))
      passed.push(array[i]);
  }
  return passed;
}

console.log(filter(ancestry, function(person) {
  return person.born > 1900 &amp;&amp; person.born < 1925;
}));
// → [{name: "Philibert Haverbeke", …}, …]
```

Используется аргумент с именем test – это функция, которая производит вычисления проверки. Она вызывается для каждого элемента, а возвращаемое ею значение определяет, попадает ли этот элемент в возвращаемый массив.

В файле оказалось три человека, которые были молоды в 1924 – дедушка, бабушка и двоюродная бабушка.

Обратите внимание — функция filter не удаляет элементы из существующего массива, а строит новый, содержащий только прошедшие проверку элементы. Это чистая функция, потому что она не портит переданный ей массив.

Как и forEach, filter – это один из стандартных методов массива. В примере мы описали такую функцию, только чтобы показать, что она делает внутри. Отныне мы будем использовать её просто:

c```
onsole.log(ancestry.filter(function(person) {
  return person.father == "Carel Haverbeke";
}));
// → [{name: "Carolus Haverbeke", …}]
```

##Преобразования при помощи map
Допустим, есть у нас архив объектов, представляющих людей, который был получен фильтрацией массива предков. Но нам нужен массив имён, который было бы проще прочесть.

Метод map преобразовывает массив, применяя функцию ко всем его элементам и строя новый массив из возвращаемых значений. У нового массива будет та же длина, что у входного, но его содержимое будет преобразовано в новый формат.

```
function map(array, transform) {
  var mapped = [];
  for (var i = 0; i < array.length; i++)
    mapped.push(transform(array[i]));
  return mapped;
}

var overNinety = ancestry.filter(function(person) {
  return person.died - person.born > 90;
});
console.log(map(overNinety, function(person) {
  return person.name;
}));
// → ["Clara Aernoudts", "Emile Haverbeke",
//    "Maria Haverbeke"]
```

Что интересно, люди, которые прожили хотя бы до 90 лет – это те самые, что мы видели ранее, которые были молоды в 1920-х годах. Это как раз самое новое поколение в моих записях. Видимо, медицина серьёзно улучшилась.

Как и forEach и filter, map также является стандартным методом у массивов.

##Суммирование с reduce
Другой популярный пример работы с массивами – получение одиночного значения на основе данных в массиве. Один пример – уже знакомое нам суммирование списка номеров. Другой – поиск человека, родившегося раньше всех.

Операция высшего порядка такого типа называется reduce (уменьшение; или иногда fold, свёртывание). Можно представить её в виде складывания массива, по одному элементу за раз. При суммировании чисел мы начинали с нуля, и для каждого элемента комбинировали его с текущей суммой при помощи сложения.

Параметры функции reduce, кроме массива – комбинирующая функция и начальное значение. Эта функция чуть менее понятная, чем filter или map, поэтому обратите на неё пристальное внимание.

```
function reduce(array, combine, start) {
  var current = start;
  for (var i = 0; i < array.length; i++)
    current = combine(current, array[i]);
  return current;
}

console.log(reduce([1, 2, 3, 4], function(a, b) {
  return a + b;
}, 0));
// → 10
```

Стандартный метод массивов reduce, который, конечно, работает так же, ещё более удобен. Если массив содержит хотя бы один элемент, вы можете не указывать аргумент start. Метод возьмёт в качестве стартового значения первый элемент массива и начнёт работу со второго.

Чтобы при помощи reduce найти самого древнего из известных моих предков, мы можем написать нечто вроде:

```
console.log(ancestry.reduce(function(min, cur) {
  if (cur.born < min.born) return cur;
  else return min;
}));
// → {name: "Pauwels van Haverbeke", born: 1535, …}
```

##Компонуемость
Как бы мы могли написать предыдущий пример (поиск человека с самой ранней датой рождения) без функций высшего порядка? На самом деле, код не такой уж и ужасный:

```
var min = ancestry[0];
for (var i = 1; i < ancestry.length; i++) {
  var cur = ancestry[i];
  if (cur.born < min.born)
    min = cur;
}
console.log(min);
// → {name: "Pauwels van Haverbeke", born: 1535, …}
```

Чуть больше переменных, на две строчки длиннее – но пока достаточно понятный код.

Функции высшего порядка раскрывают свои возможности по-настоящему, когда вам приходится комбинировать функции. К примеру, напишем код, находящий средний возраст мужчин и женщин в наборе.

```
function average(array) {
  function plus(a, b) { return a + b; }
  return array.reduce(plus) / array.length;
}
function age(p) { return p.died - p.born; }
function male(p) { return p.sex == "m"; }
function female(p) { return p.sex == "f"; }

console.log(average(ancestry.filter(male).map(age)));
// → 61.67
console.log(average(ancestry.filter(female).map(age)));
// → 54.56
```

(Глупо, что нам приходится определять сложение как функцию plus, но операторы в JavaScript не являются значениями, поэтому их не передашь в качестве аргументов).

Вместо того чтобы впутывать алгоритм в большой цикл, всё распределено по концепциям, которые нас интересуют – определение пола, подсчёт возраста и усреднение чисел. Мы применяем их по очереди для получения результата.

Для написания понятного кода это прямо-таки сказочная возможность. Конечно, ясность не достаётся бесплатно.

##Цена
В счастливом краю элегантного кода и красивых радуг живёт гадское чудище по имени Неэффективность.

Программа, обрабатывающая массив, красивее всего представляется в виде последовательности явно разделённых шагов, каждый из которых что-то делает с массивом и возвращает новый массив. Но наслоение всех этих промежуточных массивов стоит дорого.

Точно так же, передача функции в forEach, чтобы та прошлась по массиву за нас, удобна и проста в понимании. Но вызов функций в JavaScript обходится дороже по сравнению с циклами.

Так же обстоят дела со многими техниками, улучшающими читаемость программ. Абстракции добавляют слои между чистой работой компьютера и теми концепциями, с которыми мы работаем – и в результате компьютер делает больше работы. Это не железное правило – есть языки, которые позволяют добавлять абстракции без ухудшения эффективности, и даже в JavaScript опытный программист может найти способы писать абстрактный и быстрый код. Но это проблема встречается часто.

К счастью, большинство компьютеров безумно быстрые. Если ваш набор данных не слишком велик, или время работы должно быть всего лишь достаточно быстрым с точки зрения человека (например, делать что-то каждый раз, когда пользователь жмёт на кнопку) — тогда не имеет значения, написали вы красивое решение, которое работает половину миллисекунды, или очень оптимизированное, которое работает одну десятую миллисекунды.

Удобно примерно подсчитывать, как часто будет вызываться данный кусочек кода. Если у вас есть цикл в цикле (напрямую, или же через вызов в цикле функции, которая внутри также работает с циклом), то код будет выполнен N*M раз, где N – количество повторений внешнего цикла, а M – внутреннего. Если во внутреннем цикле есть ещё один цикл, повторяющийся P раз, тогда мы уже получим N*M*P – и так далее. Это может приводить к большим числам, и когда программа тормозит, проблему часто можно свести к небольшому кусочку кода, находящемуся внутри самого внутреннего цикла.

##Пра-пра-пра-пра-пра-…
Мой дед, Филиберт Хавербеке, упомянут в файле с данными. Начиная с него, я могу отследить свой род в поисках самого древнего из предков, Паувелса ван Хавербеке, моего прямого предка. Теперь я хочу подсчитать, какой процент ДНК у меня от него (в теории).

Чтобы пройти от имени предка до объекта, представляющего его, мы строим объект, который сопоставляет имена и людей.

```
var byName = {};
ancestry.forEach(function(person) {
  byName[person.name] = person;
});

console.log(byName["Philibert Haverbeke"]);
// → {name: "Philibert Haverbeke", …}
```

Задача – не просто найти у каждой из записей отца и посчитать, сколько шагов получается до Паувелса. В истории семьи было несколько браков между двоюродными родственниками (ну, маленькие деревни и т.д.). В связи с этим ветви семейного дерева в некоторых местах соединяются с другими, поэтому генов у меня получается больше, чем 1/2G (G – количество поколений между Паувелсом и мною). Эта формула исходит из предположения, что каждое поколение расщепляет генетический фонд надвое.

Разумно будет провести аналогию с reduce, где массив низводится до единственного значения путём последовательного комбинирования данных слева направо. Здесь нам тоже надо получить единственное число, но при этом нужно следовать линиям наследственности. А они формируют не простой список, а дерево.

Мы считаем это значение для конкретного человека, комбинируя эти значения его предков. Это можно сделать рекурсивно. Если нам нужен какой-то человек, нам надо подсчитать нужную величину для его родителей, что в свою очередь требует подсчёта её для его прародителей, и т.п. По идее нам придётся обойти бесконечное множество узлов дерева, но так как наш набор данных конечен, нам надо будет где-то остановиться. Мы просто назначим значение по умолчанию для всех людей, которых нет в нашем списке. Логично будет назначить им нулевое значение – люди, которых нет в списке, не несут в себе ДНК нужного нам предка.

Принимая человека, функцию для комбинирования значений от двух предков и значение по умолчанию, функция reduceAncestors «конденсирует» значение из семейного древа.

```
function reduceAncestors(person, f, defaultValue) {
  function valueFor(person) {
    if (person == null)
      return defaultValue;
    else
      return f(person, valueFor(byName[person.mother]),
                       valueFor(byName[person.father]));
  }
  return valueFor(person);
}
```

Внутренняя функция valueFor работает с одним человеком. Благодаря рекурсивной магии она может вызвать себя для обработки отца и матери этого человека. Результаты вместе с объектом person передаются в f, которая и вычисляет нужное значение для этого человека.

Теперь мы можем использовать это для подсчёта процента ДНК, которое мой дедушка разделил с Паувелсом ванн Хавербеке, и поделить это на четыре.

```
function sharedDNA(person, fromMother, fromFather) {
  if (person.name == "Pauwels van Haverbeke")
    return 1;
  else
    return (fromMother + fromFather) / 2;
}
var ph = byName["Philibert Haverbeke"];
console.log(reduceAncestors(ph, sharedDNA, 0) / 4);
// → 0.00049
```

Человек по имени Паувелсом ванн Хавербеке, очевидно, делит 100% ДНК с Паувелсом ванн Хавербеке (полных тёзок в списке данных нет), поэтому для него функция возвращает 1. Все остальные делят средний процент их родителей.

Статистически у меня примерно 0.05% ДНК совпадает с моим предком из 16 века. Это, конечно, приблизительное число. Это довольно мало, но так как наш генетический материал составляет примерно 3 миллиарда базовых пар, во мне есть что-то от моего предка.

Можно было бы подсчитать это число и без использования reduceAncestors. Но разделение общего подхода (обход древа) и конкретного случая (подсчёт ДНК) позволяет нам писать более понятный код и использовать вновь части кода для других задач. Например, следующий код выясняет процент известных предков данного человека, доживших до 70 лет.

```
function countAncestors(person, test) {
  function combine(person, fromMother, fromFather) {
    var thisOneCounts = test(person);
    return fromMother + fromFather + (thisOneCounts ? 1 : 0);
  }
  return reduceAncestors(person, combine, 0);
}
function longLivingPercentage(person) {
  var all = countAncestors(person, function(person) {
    return true;
  });
  var longLiving = countAncestors(person, function(person) {
    return (person.died - person.born) >= 70;
  });
  return longLiving / all;
}
console.log(longLivingPercentage(byName["Emile Haverbeke"]));
// → 0.145
```

Не нужно относиться к таким расчётам слишком серьёзно, так как наш набор содержит произвольную выборку людей. Но код демонстрирует, что reduceAncestors – полезная часть общего словаря для работы со структурой данных типа фамильного древа.

##Связывание
Метод bind, который есть у всех функций, создаёт новую функцию, которая вызовет оригинальную, но с некоторыми фиксированными аргументами.

Следующий пример показывает, как это работает. В нём мы определяем функцию isInSet, которая говорит, есть ли имя человека в заданном наборе. Для вызова filter мы можем либо написать выражение с функцией, которое вызывает isInSet, передавая ей набор строк в качестве первого аргумента, или применить функцию isInSet частично.
 
```
var theSet = ["Carel Haverbeke", "Maria van Brussel",
              "Donald Duck"];
function isInSet(set, person) {
  return set.indexOf(person.name) > -1;
}

console.log(ancestry.filter(function(person) {
  return isInSet(theSet, person);
}));
// → [{name: "Maria van Brussel", …},
//    {name: "Carel Haverbeke", …}]
console.log(ancestry.filter(isInSet.bind(null, theSet)));
// → … same result
```

Вызов bind возвращает функцию, которая вызовет isInSet с первым аргументом theSet, и последующими аргументами такими же, какие были переданы в bind.

Первый аргумент, который сейчас установлен в null, используется для вызовов методов – так же, как было в apply. Мы поговорим об этом позже.

##Итог
Возможность передавать вызов функции другим функциям – не просто игрушка, но очень полезное свойство JavaScript. Мы можем писать выражения «с пробелами» в них, которые затем будут заполнены при помощи значений, возвращаемых функциями.

У массивов есть несколько полезных методов высшего порядка – forEach, чтобы сделать что-то с каждым элементом, filter – чтобы построить новый массив, где некоторые значения отфильтрованы, map – чтобы построить новый массив, каждый элемент которого пропущен через функцию, reduce – для комбинации всех элементов массива в одно значение.

У функций есть метод apply, для передачи им аргументов в виде массива. Также у них есть метод bind для создания копии функции с частично заданными аргументами.

##Упражнения
###Свёртка
Используйте метод reduce в комбинации с concat для свёртки массива массивов в один массив, у которого есть все элементы входных массивов.

```
var arrays = [[1, 2, 3], [4, 5], [6]];
// Ваш код тут
// → [1, 2, 3, 4, 5, 6]
```

###Разница в возрасте матерей и их детей
Используя набор данных из примера, подсчитайте среднюю разницу в возрасте между матерями и их детьми (это возраст матери во время появления ребёнка). Можно использовать функцию average, приведённую в главе.

Обратите внимание – не все матери, упомянутые в наборе, присутствуют в нём. Здесь может пригодиться объект byName, который упрощает процедуру поиска объекта человека по имени.

```
function average(array) {
  function plus(a, b) { return a + b; }
  return array.reduce(plus) / array.length;
}

var byName = {};
ancestry.forEach(function(person) {
  byName[person.name] = person;
});

// Ваш код тут

// → 31.2
```

###Историческая ожидаемая продолжительность жизни
Мы считали, что только последнее поколение людей дожило до 90 лет. Давайте рассмотрим этот феномен поподробнее. Подсчитайте средний возраст людей для каждого из столетий. Назначаем столетию людей, беря их год смерти, деля его на 100 и округляя: Math.ceil(person.died / 100).

```
function average(array) {
  function plus(a, b) { return a + b; }
  return array.reduce(plus) / array.length;
}

// Тут ваш код

// → 16: 43.5
//   17: 51.2
//   18: 52.8
//   19: 54.8
//   20: 84.7
//   21: 94
```

В качестве призовой игры напишите функцию groupBy, абстрагирующую операцию группировки. Она должна принимать массив и функцию, которая подсчитывает группу для элементов массива, и возвращать объект, который сопоставляет названия групп массивам членов этих групп.

###Every и some
У массивов есть ещё стандартные методы every и some. Они принимают как аргумент некую функцию, которая, будучи вызванной с элементом массива в качестве аргумента, возвращает true или false. Так же, как &amp;&amp; возвращает true, только если выражения с обеих сторон оператора возвращают true, метод every возвращает true, когда функция возвращает true для всех элементов массива. Соответственно, some возвращает true, когда заданная функция возвращает true при работе с любым из элементов массива. Они не обрабатывают больше элементов, чем необходимо – например, если some получает true для первого элемента, он не обрабатывает оставшиеся.

Напишите функции every и some, которые работают так же, как эти методы, только принимают массив в качестве аргумента.

```
// Ваш код тут

console.log(every([NaN, NaN, NaN], isNaN));
// → true
console.log(every([NaN, NaN, 4], isNaN));
// → false
console.log(some([NaN, 3, 4], isNaN));
// → true
console.log(some([2, 3, 4], isNaN));
// → false
```
