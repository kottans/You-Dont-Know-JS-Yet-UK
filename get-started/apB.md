# Ти ще не знаєш JS: Початок. Друге видання
# Додаток B: Практика, практика, практика!

У цьому додатку ми розглянемо деякі вправи та запропоновані нами рішення. Ці вправи лише допоможуть вам *розпочати* застосовувати ідеє з цієї книги на практиці.

## Вправа на порівняння

Давайте потренуємось працювати з типами значень та порівняннями (Глава 4, Опора 3), де потрібно буде залучити приведення типів.

Функція `scheduleMeeting(..)` очікує час початку роботи (у 24-годинному форматі у вигляді рядка "hh:mm") та тривалість зустрічі (кількість хвилин). Вона повинна повернути `true`, якщо зустріч повністю потрапляє в рамки робочого дня (відповідно до часу, зазначеного в `dayStart` і `dayEnd`); повернути `false`, якщо зустріч виходить за межі робочого дня.

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    // ..TODO..
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

Спробуйте спочатку вирішити це завдання самостійно. Рекомендуємо згадати про оператори рівності та оператори відносного порівняння та як приведення типів впливає на цей код. Отримавши працюючий код, *порівняйте* свої рішення з кодом у "Пропонованих рішеннях" в кінці цього додатка.

## Вправа з використання замикань

Тепер давайте потренуємось із замиканнями (Глава 4, Опора 1).

Функція `range(..)` приймає число як перший аргумент, представляючи перше число у бажаному діапазоні чисел. Другий аргумент – це також число, що представляє кінець бажаного діапазону (включно). Якщо другий аргумент опущено, слід повернути іншу функцію, яка очікує цей аргумент.


```js
function range(start,end) {
    // ..TODO..
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

Спробуйте спочатку вирішити це завдання самостійно.

Отримавши працюючий код, *порівняйте* свої рішення з кодом у "Пропонованих рішеннях" в кінці цього додатка.

## Вправа з використання прототипів

Насамкінець, давайте попрацюємо над `this` та об'єктами, пов'язаними через прототип (Глава 4, Опора 2).

Визначте ігровий автомат з трьома барабанами, які можуть окремо крутитися (метод `spin()`), а потім показати поточний вміст усіх барабанів (`display()`).

Основна поведінка одного барабана визначена в об'єкті `reel` нижче. Але ігровому автомату потрібні окремі барабани - об'єкти, які делегують до `reel` і кожна з яких має властивість "position".

Барабан вміє *тільки* відображати (`display()`) свій поточний символ слота, але ігровий автомат зазвичай показує три символи на барабані: поточний слот (`позиція`), один слот зверху (`позиція - 1`) і один слот внизу (`позиція + 1`). Отже, відображення ігрового автомата повинно закінчитися відображенням сітки символів ігрових автоматів 3 x 3.

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        // цей автомат потребує трьох окремих барабанів
        // підказка: Object.create(..)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        // TODO
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

Спробуйте спочатку вирішити це завдання самостійно.

Підказки:

* Використовуйте оператор ділення з остачею `%` для обертання `position`, коли ви посилаєтеся на символи  навколо барабана.

* Використовуйте ʻObject.create(..) `, щоб створити об'єкт і зв'язати його прототипом з іншим об'єктом. Після зв’язку делегування дозволяє об’єктам обмінюватися контекстом `this` під час виклику методу.

* Замість того, щоб безпосередньо модифікувати об'єкт барабану, щоб показати кожну з трьох позицій, ви можете використовувати інший тимчасовий об'єкт (`Object.create(..)` знову стане в пригоді) зі своїм `position` для делегування.

Отримавши код, який працює, *порівняйте* свої рішення з кодом у "Пропонованих рішеннях" в кінці цього додатка.

## Пропоновані рішення

Майте на увазі, що ці запропоновані рішення - це просто пропозиції. Існує багато різних способів вирішити ці практичні вправи. Порівняйте свій підхід з тим, що ви бачите тут, і розгляньте плюси і мінуси кожного.

Запропоноване рішення для практики "Порівняння" (Опора 3):

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    var [ , meetingStartHour, meetingStartMinutes ] =
        startTime.match(/^(\d{1,2}):(\d{2})$/) || [];

    durationMinutes = Number(durationMinutes);

    if (
        typeof meetingStartHour == "string" &&
        typeof meetingStartMinutes == "string"
    ) {
        let durationHours =
            Math.floor(durationMinutes / 60);
        durationMinutes =
            durationMinutes - (durationHours * 60);
        let meetingEndHour =
            Number(meetingStartHour) + durationHours;
        let meetingEndMinutes =
            Number(meetingStartMinutes) +
            durationMinutes;

        if (meetingEndMinutes >= 60) {
            meetingEndHour = meetingEndHour + 1;
            meetingEndMinutes =
                meetingEndMinutes - 60;
        }

        // збираємо повні рядкові представлення часу
        // (щоб спростити порівняння)
        let meetingStart = `${
            meetingStartHour.padStart(2,"0")
        }:${
            meetingStartMinutes.padStart(2,"0")
        }`;
        let meetingEnd = `${
            String(meetingEndHour).padStart(2,"0")
        }:${
            String(meetingEndMinutes).padStart(2,"0")
        }`;

        // ПРИМІТКА: через те, що усі вирази це рядки,
        // порівняння проходить за алфавітом, але в цьому but it's
        // випадку це безпечно, бо це повні значення часу
        // (тобто, "07:15" < "07:30")
        return (
            meetingStart >= dayStart &&
            meetingEnd <= dayEnd
        );
    }

    return false;
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

----

Пропоноване рішення для практики "Замикання" (Опора 1):

```js
function range(start,end) {
    start = Number(start) || 0;

    if (end === undefined) {
        return function getEnd(end) {
            return getRange(start,end);
        };
    }
    else {
        end = Number(end) || 0;
        return getRange(start,end);
    }


    // **********************

    function getRange(start,end) {
        var ret = [];
        for (let i = start; i <= end; i++) {
            ret.push(i);
        }
        return ret;
    }
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

----

Пропоноване рішення для практики "Прототипи" (Опора 2):

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        Object.create(reel),
        Object.create(reel),
        Object.create(reel)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        var lines = [];

        // показати усі 3 лінії ігнового автомата
        for (
            let linePos = -1; linePos <= 1; linePos++
        ) {
            let line = this.reels.map(
                function getSlot(reel){
                    var slot = Object.create(reel);
                    slot.position = (
                        reel.symbols.length +
                        reel.position +
                        linePos
                    ) % reel.symbols.length;
                    return slot.display();
                }
            );
            lines.push(line.join(" | "));
        }

        return lines.join("\n");
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

Ця книга дійшла кінця. А зараз настав час шукати реальні проекти, на яких можна практикувати ідеї з неї. Просто продовжуйте писати код, бо це найкращий спосіб вчитися!
