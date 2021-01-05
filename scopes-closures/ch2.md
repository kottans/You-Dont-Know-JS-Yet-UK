# You Don't Know JS Yet: Області видимості та замикання - Друге видання

# Розділ 2: Ілюстрація лексичної області видимості

In Chapter 1, we explored how scope is determined during code compilation, a model called "lexical scope." The term "lexical" refers to the first stage of compilation (lexing/parsing).

У розділі 1, ми розглянули як область видимості була визначена під час компіляції, модель "лексичної області видимості". Період "лексичності" відбувається на першому етапі компіляції(токенізація/лексинг).

To properly _reason_ about our programs, it's important to have a solid conceptual foundation of how scope works. If we rely on guesses and intuition, we may accidentally get the right answers some of the time, but many other times we're far off. This isn't a recipe for success.

Щоб _міркувати_ як програми, важливо мати чітке уявлення того, як працює область видимості. Якщо ми покладатимось на здогадки та інтуїцію, іноді ми випадково отримаєм правильні відповіді, але зрештою ми далекі від ідеалу. Так не піде(Тут так не працює).

Like way back in grade school math class, getting the right answer isn't enough if we don't show the correct steps to get there! We need to build accurate and helpful mental models as foundation moving forward.

Згадуючи свій шкільний математичний клас, будо недостатньо отримати правильну відповідь, поки не покажеш хід своїх думок! У нашому випадку потрібно будувати точні та корисні розумові моделі,щоб рухатись вперед(далі/для наступного етапу).

This chapter will illustrate _scope_ with several metaphors. The goal here is to _think_ about how your program is handled by the JS engine in ways that more closely align with how the JS engine actually works.

Цей розділ проілюструє _області видимості_ кількома метафорами. Головна мета: *обміркувати* як обробляється ваша програма за допомогою механізму JS таким чином, щоб тісніше відповідати тому, як насправді працює движок JS.

## Marbles, and Buckets, and Bubbles... Oh My!

## Кульки та Відра, до того ж(ще й) Оболонка ... Ой, лишенько!

One metaphor I've found effective in understanding scope is sorting colored marbles into buckets of their matching color.

Однією з метафор, яку я визнав ефективною для розуміння області видимості є сортування кольорових кульок у відра відповідного кольору.

Imagine you come across a pile of marbles, and notice that all the marbles are colored red, blue, or green. Let's sort all the marbles, dropping the red ones into a red bucket, green into a green bucket, and blue into a blue bucket. After sorting, when you later need a green marble, you already know the green bucket is where to go to get it.

Уявіть, що ви натрапили на купу кульок і помічаєте, що всі кульки пофарбовані в червоний, синій або зелений кольори. Давайте відсортуємо всі кульки відповідно по кольорам: червоні у червоне відро, зелені у зелене відро, а сині у синє відро. Після сортування, коли пізніше вам знадобиться зелений мармур, ви знатимете про зелене відно й звідки потрібно дістати його.

In this metaphor, the marbles are the variables in our program. The buckets are scopes (functions and blocks), which we just conceptually assign individual colors for our discussion purposes. The color of each marble is thus determined by which _color_ scope we find the marble originally created in.

У цій метафорі кульки - це змінні у нашій програмі. Відра - це області видимості (функції та блоки), яким ми концептуально призначаємо окремими кольорами для подальших кроків. Таким чином, колір кожної кульки визначається тим, в якій _кольоровій_ області видимості ми знаходимо кульку.

Let's annotate the running program example from Chapter 1 with scope color labels:

Давайте підсумуєм приклад запущеної програми з розділу 1 кольоровими позначками області видимості:

```js
// зовнішня/глобальна область видимості: ЧЕРВОНИЙ

var students = [
  { id: 14, name: "Кайл" },
  { id: 73, name: "Сюзі" },
  { id: 112, name: "Френк" },
  { id: 6, name: "Сара" },
];

function getStudentName(studentID) {
  // область видимості функції: СИНІЙ

  for (let student of students) {
    // видимості функції циклу: ЗЕЛЕНИЙ

    if (student.id == studentID) {
      return student.name;
    }
  }
}

var nextStudent = getStudentName(73);
console.log(nextStudent); // Сюзі
```

We've designated three scope colors with code comments: RED (outermost global scope), BLUE (scope of function `getStudentName(..)`), and GREEN (scope of/inside the `for` loop). But it still may be difficult to recognize the boundaries of these scope buckets when looking at a code listing.

Ми позначили три кольори області видимості з помітками: ЧЕРВОНИЙ (крайня(сама зовнішня) глобальна область), СИНИЙ (область функції `getStudentName (..)`) та ЗЕЛЕНИЙ (область дії всередині циклу `for`). Але все одно, проглядаючи код, може бути складно розпізнати межі кожної області видимості(відер).

Figure 2 helps visualize the boundaries of the scopes by drawing colored bubbles (aka, buckets) around each:

Рисунок 2 допоможе візуалізувати межі областей, малюючи кольорові бульбашки (вони ж відра) навколо кожного:

<figure>
    <img src="images/fig2.png" width="500" alt="Colored Scope Bubbles" align="center">
    <figcaption><em>Fig. 2: Colored Scope Bubbles</em></figcaption>
</figure>

1. **Bubble 1** (RED) encompasses the global scope, which holds three identifiers/variables: `students` (line 1), `getStudentName` (line 8), and `nextStudent` (line 16).

1. **Оболонка 1** (ЧЕРВОНИЙ) містить глобальну область, яка містить три ідентифікатори / змінні: `students` (рядок 1),` getStudentName` (рядок 8) та `nextStudent` (рядок 16).

1. **Bubble 2** (BLUE) encompasses the scope of the function `getStudentName(..)` (line 8), which holds just one identifier/variable: the parameter `studentID` (line 8).

1. **Оболонка 2** (СИНІЙ) містить область дії функції `getStudentName (..)` (рядок 8), яка містить лише один ідентифікатор/змінну: параметр `studentID` (рядок 8).

1. **Bubble 3** (GREEN) encompasses the scope of the `for`-loop (line 9), which holds just one identifier/variable: `student` (line 9).

1. **Оболонка 3** (ЗЕЛЕНИЙ) містить область циклу `for` (рядок 9), який містить лише один ідентифікатор / змінну: `student` (рядок 9).

| Примітка:                                                                                                                                                                                                    |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Technically, the parameter `studentID` is not exactly in the BLUE(2) scope. We'll unwind that confusion in "Implied Scopes" in Appendix A. For now, it's close enough to label `studentID` a BLUE(2) marble. |

Теоретично параметр `studentID` не зовсім в СИНІЙ (2) області. Ми розберем цю плутанину в "Припускаються сферах" у Додатку А. Наразі це припустимо позначити `studentID 'СИНЬОЮ (2) кулькою.

Scope bubbles are determined during compilation based on where the functions/blocks of scope are written, the nesting inside each other, and so on. Each scope bubble is entirely contained within its parent scope bubble—a scope is never partially in two different outer scopes.

Оболонка області видимості визначаються під час компіляції, залежачи від того, де записані функції/блоки сфери та вкладеності один в одного тощо. Тобто кожна дочірня оболонка міститься у своїй оболонці батьківської області визначеності. Варто зауважити,що область ніколи не є частково у двох різних зовнішніх областях.

Each marble (variable/identifier) is colored based on which bubble (bucket) it's declared in, not the color of the scope it may be accessed from (e.g., `students` on line 9 and `studentID` on line 10).

Кожна кулька (змінна/ідентифікатор) забарвлюється залежно від того, в якій оболонці (відрі) він оголошений, а не за кольором області, до якої він може отримати доступ (наприклад, `students` у рядку 9 та` studentID` у рядку 10).

| Примітка:                                                                                                                                                                                                                                                                                                                                      |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Remember we asserted in Chapter 1 that `id`, `name`, and `log` are all properties, not variables; in other words, they're not marbles in buckets, so they don't get colored based on any the rules we're discussing in this book. To understand how such property accesses are handled, see the third book in the series, _Objects & Classes_. |

| Згадаймо в главі 1 ми стверджували, що "id", "name" і "log" - це все властивості, а не змінні; іншими словами, це не кульки у відрах, тому вони не забарвлюються базуючись по тим правилам, які ми обговорюємо в цій книзі. Щоб зрозуміти, як обробляються такі властивості, дивіться у третій книзі серії _Objects & Classes_. |

As the JS engine processes a program (during compilation), and finds a declaration for a variable, it essentially asks, "Which _color_ scope (bubble or bucket) am I currently in?" The variable is designated as that same _color_, meaning it belongs to that bucket/bubble.

Коли рушій JS обробляє програму (під час компіляції) і знаходить оголошення для змінної, він по суті запитує: "Якого _кольору_ області видимості (оболонка чи відро) я зараз перебуваю?" Змінна позначається того самого _кольору_ враховуючи, що вона належить цьому відру/оболонці.

The GREEN(3) bucket is wholly nested inside of the BLUE(2) bucket, and similarly the BLUE(2) bucket is wholly nested inside the RED(1) bucket. Scopes can nest inside each other as shown, to any depth of nesting as your program needs.

ЗЕЛЕНЕ (3) відро вкладене всередину СИНЬОГО (2) відра, а також СИНЄ (2) відро вкладене всередину ЧЕРВОНОГО (1) відра. Області визначеності можуть бути визначені всередині один в одного(вкладається всередині іншого), як показано, на будь-яку глибину вкладеності, наскільки це потрібно вашій програмі.

References (non-declarations) to variables/identifiers are allowed if there's a matching declaration either in the current scope, or any scope above/outside the current scope, but not with declarations from lower/nested scopes.

Посилання (не-оголошені) на змінні/ідентифікатори дозволяються, якщо є відповідне оголошення або в поточній області, або в будь-якій області вище/поза поточною областю визначення, але не з оголошеними з нижчих/вкладених областей.

An expression in the RED(1) bucket only has access to RED(1) marbles, **not** BLUE(2) or GREEN(3). An expression in the BLUE(2) bucket can reference either BLUE(2) or RED(1) marbles, **not** GREEN(3). And an expression in the GREEN(3) bucket has access to RED(1), BLUE(2), and GREEN(3) marbles.

Вираз у ЧЕРВОНОМУ (1) відрі має доступ лише до ЧЕРВОНИХ (1) кульок, **ні** до СИНІХ (2) чи ЗЕЛЕНИХ (3). Вираз у СИНЬОМУ (2) відрі може посилатися як СИНІ (2) так і на ЧЕРВОНІ (1) кульки,але **не** до ЗЕЛЕНИХ (3). А вираз у ЗЕЛЕНОМУ (3) відрі має доступ до ЧЕРВОНИХ (1), СИНІХ (2) та ЗЕЛЕНИХ (3) кульок.

We can conceptualize the process of determining these non-declaration marble colors during runtime as a lookup. Since the `students` variable reference in the `for`-loop statement on line 9 is not a declaration, it has no color. So we ask the current BLUE(2) scope bucket if it has a marble matching that name. Since it doesn't, the lookup continues with the next outer/containing scope: RED(1). The RED(1) bucket has a marble of the name `students`, so the loop-statement's `students` variable reference is determined to be a RED(1) marble.

Ми можемо представити процес визначення цих не оголошених кольорих кульок, під час роботи як підстановку/пошук. Оскільки посилання на змінну `students` в інструкції циклу` for` у рядку 9 не є оголошеним, воно не має кольору. Тож ми запитуємо в поточному СИНЬОМУ (2) відрі, чи є в ньому кулька, що відповідає цій назві. Оскільки вона не відповідає, пошук продовжується із наступним зовнішнім/що містить(включаючу) область видимості: ЧЕРВОНИЙ (1).ЧЕРВОНЕ відро (1) має кульку із назвою `students`, тому посилання на змінну `students` із інструкції циклу визначається(позначаєтьмя) як ЧЕРВОНА (1) кулька.

The `if (student.id == studentID)` statement on line 10 is similarly determined to reference a GREEN(3) marble named `student` and a BLUE(2) marble `studentID`.

Інструкція `if (student.id == studentID)` у рядку 10 аналогічно має доступ до(посилається на) ЗЕЛЕНІ (3) кульки з ім'ям `student` та СИНІ (2) кульки `studentID`.

| Примітка:                                                                                                                                                                                                                                                                                                                                                                                                               |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The JS engine doesn't generally determine these marble colors during runtime; the "lookup" here is a rhetorical device to help you understand the concepts. During compilation, most or all variable references will match already-known scope buckets, so their color is already determined, and stored with each marble reference to avoid unnecessary lookups as the program runs. More on this nuance in Chapter 3. |

| Рушій JS зазвичай не визначає кольору кульок під час роботи; "пошук" - це чарівна палочка, яка допомагає усвідомити концепцію. Під час компіляції більшість або всі посилання на змінні уже відомі областям визначеності,тому мають відповідний колір та зберігають відповідене посилання кульки. Це робиться для того щоб уникнути зайвих пошуків під час роботи програми. Більш детальніше про це описується у Розділі 3. |

The key take-aways from marbles & buckets (and bubbles!):

Підводячи підсумки з кульками,відрами (та оболонок!):

- Variables are declared in specific scopes, which can be thought of as colored marbles from matching-color buckets.

- Змінні,які оголошені в конкретних областях визначеності можна сприймати як кольорові кульки з відповідними кольорами відрів.

- Any variable reference that appears in the scope where it was declared, or appears in any deeper nested scopes, will be labeled a marble of that same color—unless an intervening scope "shadows" the variable declaration; see "Shadowing" in Chapter 3.

- Будь-яке посилання на змінну, яке з’являється в оголошеній області визначеності, або з’являється у будь-якій більш глибокій вкладеній області визначеності, буде помічено кулькою того самого кольору, якщо тільки проміжний обсяг не заміщує оголошену змінну; див. "Заміщенням (shadowing)" у главі 3.

- The determination of colored buckets, and the marbles they contain, happens during compilation. This information is used for variable (marble color) "lookups" during code execution.

- Під час компіляції відбувається визначення кольорових відер та кульок, яким вони належать. Ця інформація використовується для "пошуку" змінних (колір кульок) під час виконання коду.

## A Conversation Among Friends

## Розмова між друзями

Another useful metaphor for the process of analyzing variables and the scopes they come from is to imagine various conversations that occur inside the engine as code is processed and then executed. We can "listen in" on these conversations to get a better conceptual foundation for how scopes work.

Іншою важливою метафорою процесу аналізу змінних та обсягу, з якого вони походять, є уявлення різних розмов, які відбуваються всередині рушія під час обробки та виконання коду. Ми можемо «підслухати» ці розмови, щоб отримати краще розуміння основ, як працюють сфери.

Let's now meet the members of the JS engine that will have conversations as they process our program:

Давайте тепер познайомимось із членами механізму JS, які будуть вести бесіди під час обробки нашої програми:

- _Engine_: responsible for start-to-finish compilation and execution of our JavaScript program.

- _Рушій_: відповідає за компіляцію від початку до кінця та виконання нашої JavaScript програми.

- _Compiler_: one of _Engine_'s friends; handles all the dirty work of parsing and code-generation (see previous section).

- _Компілятор_: один із друзів _Рушія_; виконує всю брудну роботу аналізу та генерації коду (див. попередній розділ).

- _Scope Manager_: another friend of _Engine_; collects and maintains a lookup list of all the declared variables/identifiers, and enforces a set of rules as to how these are accessible to currently executing code.

- _Завідуючий області видимості_: ще один друг _Рушія_; збирає та підтримує список пошуку всіх оголошених змінних/ідентифікаторів та забезпечує набір правил щодо того, коли вони доступні під час виконуваному коду.

For you to _fully understand_ how JavaScript works, you need to begin to _think_ like _Engine_ (and friends) think, ask the questions they ask, and answer their questions likewise.

Для _успішного зрозуміння_, як працює JavaScript, вам слід почати _мислити_, як _Рушій_ (та його друзі),відповідно задавати запитання, як вони та відповідати на їхні запитання так само.

To explore these conversations, recall again our running program example:

Щоб дослідити ці розмови, знову згадайте наш приклад запущеної програми:

```js
var students = [
  { id: 14, name: "Кайл" },
  { id: 73, name: "Сюзі" },
  { id: 112, name: "Френк" },
  { id: 6, name: "Сара" },
];

function getStudentName(studentID) {
  for (let student of students) {
    if (student.id == studentID) {
      return student.name;
    }
  }
}

var nextStudent = getStudentName(73);

console.log(nextStudent);
// Сюзі
```

Let's examine how JS is going to process that program, specifically starting with the first statement. The array and its contents are just basic JS value literals (and thus unaffected by any scoping concerns), so our focus here will be on the `var students = [ .. ]` declaration and initialization-assignment parts.

Давайте вивчимо, як JS буде обробляти цю програму, зокрема, починаючи з першого твердження. Масив та його вміст - це лише основні літерали значення JS (отже, на них не впливають будь-які проблеми, пов’язані з областю визначеності), тому наша увага тут буде зосереджена на декларації `var students = [..]` та частинах присвоювання-ініціалізації.

We typically think of that as a single statement, but that's not how our friend _Engine_ sees it. In fact, JS treats these as two distinct operations, one which _Compiler_ will handle during compilation, and the other which _Engine_ will handle during execution.

Зазвичай ми думаємо про це як про одне твердження, але не так як бачить це наш друг _Рушій_. Насправді JS розглядає їх як дві різні операції, одну з яких _Компілятор_ обробляє під час компіляції, в той час іншу _Рушій_ оброблює під час виконання.

The first thing _Compiler_ will do with this program is perform lexing to break it down into tokens, which it will then parse into a tree (AST).

Перше, що _Компілятор_ зробить з цією програмою, - це виконає лексинг, щоб розбити її на токени, які пізніше розпарситься на дерево (АСД).

Once _Compiler_ gets to code generation, there's more detail to consider than may be obvious. A reasonable assumption would be that _Compiler_ will produce code for the first statement such as: "Allocate memory for a variable, label it `students`, then stick a reference to the array into that variable." But that's not the whole story.

Як тільки _Компілятор_ перейде до генерації коду, слід розглянути більше деталей, адже не все так очевидно. Розумним припущенням буде те, що _Компілятор_ створить код для першого твердження, таке як: "Виділіть пам'ять для змінної, позначте її як `студент`, а потім вставить посилання у масиві до цієї змінної." Але це ще не вся історія.

Here's the steps _Compiler_ will follow to handle that statement:

Наступні дії, які _Компілятор_ буде виконувати для обробки цього твердження:

1. Encountering `var students`, _Compiler_ will ask _Scope Manager_ to see if a variable named `students` already exists for that particular scope bucket. If so, _Compiler_ would ignore this declaration and move on. Otherwise, _Compiler_ will produce code that (at execution time) asks _Scope Manager_ to create a new variable called `students` in that scope bucket(сегмент/відро).

1. Зустрічаючи `var students`, _Компілятор_ попросить _Завідуючого області видимості_ перевірити, чи вже існує змінна з назвою` students` для цього конкретного сегменту області визначеності. Якщо так, _Компілятор_ проігнорує цю декларацію і піде далі. В іншому випадку _Компілятор_ видасть код, який (під час виконання) просить _Завідуючий області видимості_ створити нову змінну під назвою `students` у цьому сегменті області визначеності.

1. _Compiler_ then produces code for _Engine_ to later execute, to handle the `students = []` assignment. The code _Engine_ runs will first ask _Scope Manager_ if there is a variable called `students` accessible in the current scope bucket. If not, _Engine_ keeps looking elsewhere (see "Nested Scope" below). Once _Engine_ finds a variable, it assigns the reference of the `[ .. ]` array to it.

1. Потім _Компілятор_ створює код для _Рушія_ для подальшого виконання, для обробки `students = []`. Спершу як код _Рушія_ запуститься, запитає _Завідуючого області видимості_, чи є в поточному сегменті області доступна змінна під назвою `students`. Якщо ні, _Рушій_ продовжує шукати в іншому місці (див. "Вкладена область визначеності" нижче). Після того, як _Рушій_ знаходить змінну, він присвоює їй посилання на/до масив/у `[..]`.

In conversational form, the first phase of compilation for the program might play out between _Compiler_ and _Scope Manager_ like this:

> **_Compiler_**: Hey, _Scope Manager_ (of the global scope), I found a formal declaration for an identifier called `students`, ever heard of it?

> **_(Global) Scope Manager_**: Nope, never heard of it, so I just created it for you.

> **_Compiler_**: Hey, _Scope Manager_, I found a formal declaration for an identifier called `getStudentName`, ever heard of it?

> **_(Global) Scope Manager_**: Nope, but I just created it for you.

> **_Compiler_**: Hey, _Scope Manager_, `getStudentName` points to a function, so we need a new scope bucket.

> **_(Function) Scope Manager_**: Got it, here's the scope bucket.

> **_Compiler_**: Hey, _Scope Manager_ (of the function), I found a formal parameter declaration for `studentID`, ever heard of it?

> **_(Function) Scope Manager_**: Nope, but now it's created in this scope.

> **_Compiler_**: Hey, _Scope Manager_ (of the function), I found a `for`-loop that will need its own scope bucket.

> ...

У розмовній формі перший етап компіляції для програми може розігруватися між _Компілятор_ та _Завідуючого області видимості_ наступним чином:

> **_Компілятор_**: Агов, _завідуювач області видимості_ (глобального сегменту), я знайшов зовнішню декларацію для ідентифікатора під назвою `students`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Ні, не приходилось, тому я просто створю його для тебе.

> **_Компілятор_**: Агов, _Завідувач області видимості_, я знайшов зовнішню декларацію для ідентифікатора під назвою `getStudentName`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Ні, але я щойно створив його для тебе.

> **_Компілятор_**: Агов, _Завідувач області видимості_, `getStudentName` вказує на функцію, тому нам потрібен новий сегмент(нове відро) області визначеності.

> **_(Функціональний) завідувач області видимості_** Зрозумів, ось сегмент області визначеності.

> **_Компілятор_**: Агов, _Завідувач області видимості_ (функції), я знайшов зовнішню декларацію параметра для `studentID`, коли-небудь чув про це?

> **_(Функціональний) завідувач області видимості_**: Ні, але зараз він створений у цій області визначеності.

> **_Компілятор_**: Агов, _Завідувач області видимості_ (функції), я знайшов цикл `for`, який потребуватиме власного сегмента області визначеності.

> ...

The conversation is a question-and-answer exchange, where **Compiler** asks the current _Scope Manager_ if an encountered identifier declaration has already been encountered. If "no," _Scope Manager_ creates that variable in that scope. If the answer is "yes," then it's effectively skipped over since there's nothing more for that _Scope Manager_ to do.

Розмова - це обмін запитання-відповідь, де **Компілятор** запитує поточного _Завідувача області видимості_ чи вже зустрічалася декларація ідентифікатора. Якщо "ні", _Завідувач області видимості_ створює цю змінну у цій області видимості. Якщо відповідь "так", то її фактично пропускають, оскільки _Завідувачу області видимості_ більше нічого робити.

_Compiler_ also signals when it runs across functions or block scopes, so that a new scope bucket and _Scope Manager_ can be instantiated.

_Компілятор_ також повідомляє, коли він запускається через функції або блоками області видимості, так що можна створити екземпляр нового сегмента області видимості та _Завідувач області видимості_.

Later, when it comes to execution of the program, the conversation will shift to _Engine_ and _Завідувач області видимості_, and might play out like this:

Пізніше, коли справа стосується виконання програми, розмова перейде до _Рушія_ та _Завідувач області видимості_, і може розігруватися так:

> **_Engine_**: Hey, _Scope Manager_ (of the global scope), before we begin, can you look up the identifier `getStudentName` so I can assign this function to it?

> **_(Global) Scope Manager_**: Yep, here's the variable.

> **_Engine_**: Hey, _Scope Manager_, I found a _target_ reference for `students`, ever heard of it?

> **_(Global) Scope Manager_**: Yes, it was formally declared for this scope, so here it is.

> **_Engine_**: Thanks, I'm initializing `students` to `undefined`, so it's ready to use.

> Hey, _Scope Manager_ (of the global scope), I found a _target_ reference for `nextStudent`, ever heard of it?

> **_(Global) Scope Manager_**: Yes, it was formally declared for this scope, so here it is.

> **_Engine_**: Thanks, I'm initializing `nextStudent` to `undefined`, so it's ready to use.

> Hey, _Scope Manager_ (of the global scope), I found a _source_ reference for `getStudentName`, ever heard of it?

> **_(Global) Scope Manager_**: Yes, it was formally declared for this scope. Here it is.

> **_Engine_**: Great, the value in `getStudentName` is a function, so I'm going to execute it.

> **_Engine_**: Hey, _Scope Manager_, now we need to instantiate the function's scope.

> ...

> **_Рушій_**: Агов, _Завідувач області видимості_ (глобального сегменту), перед тим, як ми почнемо, чи можете ти знайшов ідентифікатор `getStudentName`, щоб я міг призначити йому цю функцію?

> **_(Глобальний) завідувач області видимості_**: Так, ось така змінна.

> **_Рушій_**: Агов, _Завідувач області видимості_, я знайшов _цільове_ посилання для `students`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Так, це було формально оголошено для цієї області визначеності, отже, ось воно.

> **_Рушій_**: Дякую, я ініціюю `students` до `undefined`, тому він готовий до використання.

> Гей, _завідувач області видимості_ (глобальний), я знайшов _цільове_ посилання для `nextStudent`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Так, це було формально оголошено для цього обсягу, тому ось воно.

> **_Рушій_**: Дякую, я ініціалізую `nextStudent` до` undefined`, тому він готовий до використання.

> Гей, **_(Глобальний) завідувач області видимості_**, я знайшов посилання на _source_ для `getStudentName`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Так, це було формально оголошено для цієї цієї області визначеності. Ось.

> **_Рушій_**: Чудово, значення в `getStudentName` є функцією, тому я збираюся її виконати.

> **_Рушій_**: Агов, _Завідувач області видимості_, тепер нам потрібно створити функціональну області визначеності.

> ...

This conversation is another question-and-answer exchange, where _Engine_ first asks the current _Scope Manager_ to look up the hoisted `getStudentName` identifier, so as to associate the function with it. _Engine_ then proceeds to ask _Scope Manager_ about the _target_ reference for `students`, and so on.

Ця розмова є черговим обміном запитання-відповідь, де _Рушій_ спершу попросить поточного _Завідувач області видимості_ знайти піднятий ідентифікатор `getStudentName`, щоб пов'язати з ним функцію. Потім _Рушій_ запитує _Завідувач області видимості_ про _цільове_ посилання для `students` тощо.

To review and summarize how a statement like `var students = [ .. ]` is processed, in two distinct steps:

Щоб переглянути та підсумувати, як обробляється твердження типу `var students = [..]`, у два окремі кроки:

1. _Compiler_ sets up the declaration of the scope variable (since it wasn't previously declared in the current scope).

1. _Компілятор_ встановлює оголошення змінної області видимості (оскільки вона раніше не була оголошена в поточній області видимості).

1. While _Engine_ is executing, to process the assignment part of the statement, _Engine_ asks _Scope Manager_ to look up the variable, initializes it to `undefined` so it's ready to use, and then assigns the array value to it.

1. Поки _Рушій_ виконується для обробки частини присвоєння оператора, _Рушій_ просить _Завідувач області видимості_ шукати змінну, ініціалізує її до `undefined`, щоб вона була готова до використання, а потім присвоює їй значення до масиву.

## Nested Scope

## Вкладена функція

When it comes time to execute the `getStudentName()` function, _Engine_ asks for a _Scope Manager_ instance for that function's scope, and it will then proceed to look up the parameter (`studentID`) to assign the `73` argument value to, and so on.

Коли приходить час виконати функцію `getStudentName ()`, _Рушій_ запитує _Завідувач області видимості_ екземпляр для області визначеності цієї функції, а потім продовжить пошук параметра (`studentID`), щоб присвоїти значення аргументу` 73` , і так далі.

The function scope for `getStudentName(..)` is nested inside the global scope. The block scope of the `for`-loop is similarly nested inside that function scope. Scopes can be lexically nested to any arbitrary depth as the program defines.

Область функцій для `getStudentName (..)` вкладена всередину глобальної області. Сфера блоку циклу `for` аналогічно вкладена всередину цієї функції. Області можуть бути лексично вкладені в будь-яку довільну глибину, як це визначає програма.

Each scope gets its own _Scope Manager_ instance each time that scope is executed (one or more times). Each scope automatically has all its identifiers registered at the start of the scope being executed (this is called "variable hoisting"; see Chapter 5).

Кожного разу, коли виконується область видимості, кожна з них отримує свій власний екземпляр _Завідувач області видимості_ (один або кілька разів). Кожна область видимості автоматично реєструє всі свої ідентифікатори на початку виконання свого сегменту (це називається "підняття змінної"; див. Розділ 5).

At the beginning of a scope, if any identifier came from a `function` declaration, that variable is automatically initialized to its associated function reference. And if any identifier came from a `var` declaration (as opposed to `let`/`const`), that variable is automatically initialized to `undefined` so that it can be used; otherwise, the variable remains uninitialized (aka, in its "TDZ," see Chapter 5) and cannot be used until its full declaration-and-initialization are executed.

На початку області видимості, якщо будь-який ідентифікатор походить з оголошення `function`, ця змінна автоматично ініціалізується до пов'язаного з нею посилання на функцію. До того ж будь-який ідентифікатор має походження від декларації `var` (на відміну від` let` / `const`), ця змінна автоматично ініціалізується до `undefined`, щоб її можна було використовувати; в іншому випадку змінна залишається неініціалізованою (вона ж, у своїй Тимчасова мертва зона "ТМЗ"("TDZ"), див. розділ 5) і не може бути використана, поки не буде виконано її повне декларування та ініціалізацію.

In the `for (let student of students) {` statement, `students` is a _source_ reference that must be looked up. But how will that lookup be handled, since the scope of the function will not find such an identifier?

To explain, let's imagine that bit of conversation playing out like this:

У твердженні `for (let student of students) {`statement, `students` - це _першо_ посилання, яке потрібно шукати. Але як буде оброблятися цей пошук, коли область визначення функції не знайде такого ідентифікатора?

Щоб пояснити, давайте уявимо, що така розмова розігрується так:

> **_Рушій_**: Hey, _Scope Manager_ (for the function), I have a _source_ reference for `students`, ever heard of it?

> **_(Функціональний) завідувач області видимості_**: Nope, never heard of it. Try the next outer scope.

> **_Рушій_**: Hey, _Scope Manager_ (for the global scope), I have a _source_ reference for `students`, ever heard of it?

> **_(Глобальний) завідувач області видимості_**: Yep, it was formally declared, here it is.

> ...

One of the key aspects of lexical scope is that any time an identifier reference cannot be found in the current scope, the next outer scope in the nesting is consulted; that process is repeated until an answer is found or there are no more scopes to consult.

> **_Рушій_**: Агов, _Завідувач області видимості_(функції), у мене є _першо_ посилання для `students`, коли-небудь чув про це?

> **_(Функціональний) Завідувач області видимості_**: Ні, ніколи про це не чув. Спробуй наступну зовнішню область визначеності.

> **_Рушій_**: Агов, _Завідувач області видимості_ (глобальний), у мене є _першо_ посилання для `students`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Так, це було формально оголошено, ось воно.

> ...

Одним з ключових аспектів лексичної області визначеності є те, що кожного разу, коли посилання на ідентифікатор не може бути знайдене в поточній області визначеності, проводиться консультація щодо наступного зовнішнього обсягу вкладеності; цей процес повторюється до тих пір, поки не буде знайдена відповідь або не буде більше сфери для консультацій.

### Lookup Failures

### Хибність пошуку

When _Engine_ exhausts all _lexically available_ scopes (moving outward) and still cannot resolve the lookup of an identifier, an error condition then exists. However, depending on the mode of the program (strict-mode or not) and the role of the variable (i.e., _target_(подія) vs. _source_; see Chapter 1), this error condition will be handled differently.

Коли _Рушій_ вичерпує всі _лексично доступні_ області визначеності (рухаючись назовні) і все ще не може завершити пошук ідентифікатора, тоді виникає стан помилки. Однак, залежно від режиму програми (строгий режим чи ні) та ролі змінної (тобто _цільова_ (подія) проти _початковий_; див. Розділ 1), ця умова помилки буде оброблятися по-різному.

#### Undefined Mess

#### Невизначений безлад

If the variable is a _source_, an unresolved identifier lookup is considered an undeclared (unknown, missing) variable, which always results in a `ReferenceError` being thrown. Also, if the variable is a _target_, and the code at that moment is running in strict-mode, the variable is considered undeclared and similarly throws a `ReferenceError`.

Якщо зміна є _початковою_, невирішений пошук ідентифікатора вважається незадекларованою (невідомою, відсутньою) змінною, що завжди призводить до "ReferenceError". Крім того, якщо змінною є _цілевою_, а код на той момент працює в строгому режимі, змінна вважається незадекларованою і аналогічно видає `ReferenceError`.

The error message for an undeclared variable condition, in most JS environments, will look like, "Reference Error: XYZ is not defined." The phrase "not defined" seems almost identical to the word "undefined," as far as the English language goes. But these two are very different in JS, and this error message unfortunately creates a persistent confusion.

Повідомлення про помилку для незадекларованої умови змінної у більшості середовищ JS буде виглядати так: "Помилка посилання: XYZ не визначено". Фраза "не визначено" здається майже ідентичною слову "невизначений", що стосується англійської мови. Але ці двоє дуже різні в JS, і це повідомлення про помилку, на жаль, створює стійку плутанину.

"Not defined" really means "not declared"—or, rather, "undeclared," as in a variable that has no matching formal declaration in any _lexically available_ scope. By contrast, "undefined" really means a variable was found (declared), but the variable otherwise has no other value in it at the moment, so it defaults to the `undefined` value.

"Не визначено" насправді означає "не оголошено" - або, швидше, "незадекларовано", як у змінній, яка не має відповідного (формального) оголошення в жодній _лексично доступній_ області визначеності. На відміну від цього, "невизначений" насправді означає, що змінна була знайдена (оголошена), але в іншому випадку змінна не має іншого значення на даний момент, тому за замовчуванням вона має значення `невизначений`.

To perpetuate the confusion even further, JS's `typeof` operator returns the string `"undefined"` for variable references in either state:

Щоб ще більше отримати плутанину, оператор JS `typeof` повертає рядок `undefined` для посилань на змінні в будь-якому стані:

```js
var studentName;
typeof studentName; // "undefined"

typeof doesntExist; // "undefined"
```

These two variable references are in very different conditions, but JS sure does muddy the waters. The terminology mess is confusing and terribly unfortunate. Unfortunately, JS developers just have to pay close attention to not mix up _which kind_ of "undefined" they're dealing with!

Ці два посилання на змінні знаходяться в дуже різних умовах, але JS впевнено робить каламутні води. Термінологічний безлад це завжди заплутано і страшенно невдало. На жаль, розробники JS просто повинні приділяти пильну увагу, щоб не змішувати _яких_ "невизначених", з якими вони мають справу!

#### Global... What!?

#### Глобальне... Що!?

If the variable is a _target_ and strict-mode is not in effect, a confusing and surprising legacy behavior kicks in. The troublesome outcome is that the global scope's _Scope Manager_ will just create an **accidental global variable** to fulfill that target assignment!

Consider:

Якщо змінною є _цільовою_, а строгий режим не діє, починається заплутана та дивовижна застаріла поведінка. Проблемним результатом є те, що _Завідувач області видимості_ глобальної області видимості просто створить **випадкову глобальну змінну** для виконання цього цільового призначення !

Розглянемо:

```js
function getStudentName() {
  // assignment to an undeclared variable  присвоєння незадекларованій змінній :(
  nextStudent = "Сюзі";
}

getStudentName();

console.log(nextStudent);
// "Сюзі" -- oops, an accidental-global variable! ой, випадково-глобальна змінна!
```

Here's how that _conversation_ will proceed:

> **_Engine_**: Hey, _Scope Manager_ (for the function), I have a _target_ reference for `nextStudent`, ever heard of it?

> **_(Function) Scope Manager_**: Nope, never heard of it. Try the next outer scope.

> **_Engine_**: Hey, _Scope Manager_ (for the global scope), I have a _target_ reference for `nextStudent`, ever heard of it?

> **_(Global) Scope Manager_**: Nope, but since we're in non-strict-mode, I helped you out and just created a global variable for you, here it is!

Ось як буде тривати(проходити) ця _розмова_:

> **_Рушій_**: Агов, _Завідувач області видимості_ (для функції), у мене є _цільове_ посилання для `nextStudent`, коли-небудь чув про це?

> **_(Функціональний) Завідувач області видимості_**: Ні, ніколи про це не чув. пробуй наступну зовнішню область визначеності.

> **_Рушій_**: Агов, _Завідувач області видимості_ (для глобальної), у мене є _цільове_ посилання для `nextStudent`, коли-небудь чув про це?

> **_(Глобальний) завідувач області видимості_**: Ні, але оскільки ми перебуваємо в нестрогому режимі, я тобі створив послугу та просто створив для тебе глобальну змінну, ось вона!

Yuck.

This sort of accident (almost certain to lead to bugs eventually) is a great example of the beneficial protections offered by strict-mode, and why it's such a bad idea _not_ to be using strict-mode. In strict-mode, the **_Global Scope Manager_** would instead have responded:

Гидота(Бридко).

Така аварія (майже напевно, що врешті-решт призведе до помилок) є чудовим прикладом корисного захисту, який пропонує строгий режим, і чому є настільки поганою ідеєю _не_ використання строгого режиму. У суворому режимі **_Глобальний завідувач області видимості_** натомість відповів би:

> **_(Global) Scope Manager_**: Nope, never heard of it. Sorry, I've got to throw a `ReferenceError`.

> **_(Глобальний) завідувач області видимості_**: Ні, ніколи про це не чув. Вибачай, але я мушу видати `ReferenceError`.

Assigning to a never-declared variable _is_ an error, so it's right that we would receive a `ReferenceError` here.

Призначення ніколи-не-оголошеної змінної _є_ помилка, тому правильно, що ми отримаємо тут `ReferenceError`.

Never rely on accidental global variables. Always use strict-mode, and always formally declare your variables. You'll then get a helpful `ReferenceError` if you ever mistakenly try to assign to a not-declared variable.

Ніколи не покладайтесь на випадкові глобальні змінні. Завжди використовуйте строгий режим і завжди формально заявляйте про свої змінні. Тоді ви отримаєте корисну `ReferenceError`, якщо коли-небудь помилково спробуєте призначити не оголошену змінну.

### Building On Metaphors

### Беремо за основи метафори

To visualize nested scope resolution, I prefer yet another metaphor, an office building, as in Figure 3:

Для візуалізації вкладеної роздільної здатності я віддаю перевагу ще одній метафорі - офісна будівля, як на малюнку 3:

<figure>
    <img src="images/fig3.png" width="250" alt="Scope &quot;Building&quot;" align="center">
    <figcaption><em>Fig. 3: Scope "Building"</em></figcaption>
    <br><br>
</figure>

The building represents our program's nested scope collection. The first floor of the building represents the currently executing scope. The top level of the building is the global scope.

Будівля представляє вкладену колекцію областей визначеності нашої програми. Перший поверх будівлі відповідає поточному об'єкту. Верхній рівень будівлі - це глобальна область визначеності.

You resolve a _target_ or _source_ variable reference by first looking on the current floor, and if you don't find it, taking the elevator to the next floor (i.e., an outer scope), looking there, then the next, and so on. Once you get to the top floor (the global scope), you either find what you're looking for, or you don't. But you have to stop regardless.

Ви вирішуєте посилання на _цільову_ або _початкову_ змінну , спершу переглянувши на поточний поверх, і якщо не знайдете її, піднімаєтесь ліфтом на наступний поверх (тобто зовнішню область визначеності), заглянувши туди, потім на наступний тощо. . Дійшовши до останнього поверху (глобальну область визначеності), ви або знайдете те, що шукаєте, або ні. Але зупинятися доводиться незалежно.

## Continue the Conversation

## Продовжуючи розмову

By this point, you should be developing richer mental models for what scope is and how the JS engine determines and uses it from your code.

До цього моменту у вас повинно бути багатіше розуміння ідеї, що таке область визначеності та як рушій JS визначає та використовує його з вашого коду.

Before _continuing_, go find some code in one of your projects and run through these conversations. Seriously, actually speak out loud. Find a friend and practice each role with them. If either of you find yourself confused or tripped up, spend more time reviewing this material.

Перш ніж _продовжувати_, гляньте код в одному зі своїх проектів і пройдіть ці розмови. Серйозно, проговоріть це вголос. Покличте друга та попрактикуйте з ними кожну роль. Якщо хтось із вас виявився розгубленим або невпевненим, витратьте більше часу на перегляд цього матеріалу.

As we move (up) to the next (outer) chapter, we'll explore how the lexical scopes of a program are connected in a chain.

Переходячи (вгору) до наступного (зовнішнього) розділу, ми дослідимо, як лексичні області визначеності програм пов’язані ланцюгом.
