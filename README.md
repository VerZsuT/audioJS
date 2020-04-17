# AudioJS
Небольшая библиотека для удобной работы с аудио.
Представляет собой класс-обёртку над стандартным классом _Audio_.

# Установка
Чтобы использовать **AudioJS** достаточно скачать файл _AudioJS.js_ либо скопировать код и поключить его к странице.

# Пример использования
В данном примере мы запустим очередь из нескольких аудиофайлов. Для этого достаточно создать экземпляр класса **AudioJS** и передать в его конструктор массив с URL-ми файлов, которые будут проигрываться:
```js
let audio = new AudioJS(['URL1', 'URL2'])
audio.play()
```
Но в таком случае файлы в очереди не будут автоматчески переключаться, нам придётся это делать вручную:
```js
audio.play(1)
```
Где единица - индекс проигрываемого файла в массиве. В нашем примере проиграется _URL2_.

Чтобы всё-таки они переключались автоматически нужно изменить флаг _autoplay_, сделать это можно двумя способами.
**Первый:** изменяем свойство объекта
```js
audio.autoplay = true
```
**Второй:** передаём его в конструкторе(оптимальный вариант)
```js
let audio = new AudioJS({
  queue: ['URL1', 'URL2'],
  autoplay: true
})
```
Вы могли заметить, что мы передаём в конструктор уже не массив, а объект. Дело в том, что конструктор **AudioJS** имеет 4 "перегрузки":
```js
let audio = new AudioJS('FILE_URL')       // Передаётся URL файла, который надо проиграть.
let audio = new AudioJS(['URL1', 'URL2']) // Здесь уже массив из адресов, который является очередью.
let audio = new AudioJS({                 // А тут объект с параметрами.
  src: 'URL',
  loopTrack: true
})
let audio = new AudioJS()                 // Вообще без параметров.
```

В **AudioJS** есть система _событий_. В следующем примере мы запустим очередь и по её окончанию запустим новую:
```js
let audio = new AudioJS({
  queue: ['URL1', 'URL2', 'URL3'],     // Очередь.
  autoplay: true,                      // Автопроигрывание.
  onQueueEnd: function() {             // Подписка на событие окончания очереди.
    console.log('Первая очередь закончилась.')
    audio.queue = ['URL4', 'URL5']     // Запускаем вторую очередь.
  }
})
```
Для подписки на события достаточно передать обрабочик в параметрах. Либо с помощью метода **on**:
```js
audio.on('trackStop', function() {
  console.log('Трек был остановлен.')
})
```
Всего событий 5, вот их краткое описание:
* **queueEnd** - вызывается после окончания очереди.
* **trackPlay** - вызывается после запуска аудиофайла.
* **trackPause** - вызывается когда аудиофайл поставили на паузу.
* **trackStop** - вызывается когда аудиофайл был остановлен с помощью метода _stop_.
* **trackEnd** - вызывается когда аудиофайл закончился.

# Методы
В классе есть такие методы, как **play**, **pause**, **stop**, которые управляют проигрываением аудиофайла. Также вы можете переключать проигрываение файла в очереди с помощью **next** и **back**.

Все свойства класса при присвоении проверяют значения на валидность, что помогает вам избавиться от неурядицы с типами, но усложняет поиск ошибок в коде (для улучшения этой ситуции вскоре будет введена система выбрасывания ошибок).

Метод **play** может принимать как позицию (индекс) в очереди, так и полноценный URL в виде строки.

**stop** на самом деле сбрасывает текущее время проигрывания на ноль и ставит аудиофайл на паузу, также вызывается событие _trackStop_.

Метод **on** имеет флаг _root_, с помощью котрого можно сделать переданный обработчик "корневым", то есть он будет заменять стандартное поведение, такое как переключение на следующий аудиофайл в очереди при окончании предыдущего.

Если стоит флаг _loopQueue_, то при вызове метода **next** на последнем аудиофайле в очереди будет начато проигрывание с начала.

В теории с помощью событий можно зациклить код, так что будьте внимательней.

# Примечание
Все предложения и отчёты об ошибках можете писать в **Issues**, приятного использования.
