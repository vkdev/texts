# Composable function

- Функция помечена аннотацией @Composable. Все composable-функции должны быть помечены этой аннотацией, она сообщает компилятору Compose, что функция предназначена для преобразования данных в пользовательский интерфейс. 

- Функция принимает данные. Composable-функции могут принимать параметры, с помощью которых логика приложения может описать пользовательский интерфейс. В данном случае наш виджет принимает String, чтобы поприветствовать пользователя по имени.

- Функция отображает текст в UI. Для этого она вызывает composable-функцию Text(), которая, фактически, и создает текстовый UI-элемент. Composable-функции создают иерархию пользовательского интерфейса, вызывая другие composable-функции.

- Функция ничего не возвращает. Compose-функции, которые создают пользовательский интерфейс, не должны ничего возвращать, поскольку они описывают желаемое состояние экрана, а не создают UI-виджеты.

- Эта функция быстрая, идемпотентная и **не имеет side-эффектов** (побочных эффектов). 

  - Функция ведет себя одинаково при многократном вызове с одним и тем же аргументом и не использует другие значения, такие как глобальные переменные или результаты вызова функции random().
  - Функция описывает пользовательский интерфейс без каких-либо side-эффектов, таких как изменение свойств или глобальных переменных. 
