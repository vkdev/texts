# Control flow and SlotTable

Slot Table — это внутренняя структура данных, которая используется для хранения информации о композиции UI.

Она используется Compose Runtime для:

- отслеживания состояния (remember, mutableStateOf и т. д.)
- оптимизации рекомпозиции
- связывания компонентов и их ключей
- реализации "smart recomposition" — т.е. пересчёта только тех частей UI, которые действительно изменились

Чтобы избежать полного пересоздания всех компонентов при каждом изменении состояния, Compose использует Slot Table для сравнения старой и новой композиции. Slot Table помогает Compose понять, где были изменения, и перестроить только нужные элементы, сохраняя производительность. В отличии от Map, SlotTable - построена не на деревьях, а плоская структура, что обеспечивает высокое быстродействие.

В итоге получается такая таблица

| Group name   | Function  |
|:-------------|:----------|
| Group 1      | Code 1    |
| Group 2      | Code 2    |
| ...          | ...       |
| Group N      | Code N    |

### Группы                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           
Компилятор генерирует специальные маркеры, герерируя ассоциированный с блоком кода уникальный объект группы (group object).
Есть несколько типов таких групп, которые выбираются в зависимости от структуры кода функции, внутри которой создаются эти группы:
- Reastartable-группы используются для перезапускаемых функций, то есть почти для всех по-умолчанию
- Movable-группы используются только при работе с последовательностями
- Replaceble-группы, которые используются в if-else

**Restartable**

kotlin

```
@Composable
fun myFun() {
    Text("")
}
```

decompiled
```
public static final void myFun(@Nullable Composer $composer, int $changed) {
      $composer = $composer.startRestartGroup(-1939719805);

		... // Text("")

      ScopeUpdateScope var2 = $composer.endRestartGroup();
      if (var2 != null) {
         var2.updateScope(MyFileKt::myFun$lambda$0);
      }

}
```

**Movable**

kotlin
```
@Composable
fun myFun(count: Int) {
    for(i in 0 until count) {
        key(i) {
            Text("Text $i")
        }
    }
}
```

decompiled
```
public static final void myFun(int count, @Nullable Composer $composer, int $changed) {
      $composer = $composer.startRestartGroup(525915194);
         ...
         for(int i = 0; i < count; ++i) {
            $composer.startMovableGroup(-1281166313, i);
         ...
            $composer.endMovableGroup();
         }
         ...
      }

      ScopeUpdateScope var6 = $composer.endRestartGroup();
}
```

**Replaceble**
kotlin

```
@Composable
fun myFun(condition: Boolean) {
    if(condition) {
        Text("Text if condition == true")
    } else {
        Text("Text if condition == false")
    }
}
```

decompiled
```
public static final void myFun(boolean condition, @Nullable Composer $composer, int $changed) {
            ...
         if (condition) {
            $composer.startReplaceGroup(-1061462158);
            ...
            $composer.endReplaceGroup();
         } else {
            $composer.startReplaceGroup(-1061417518);
            ...
            $composer.endReplaceGroup();
         }
   }

```

### Key
key — это вспомогательный композиционный элемент, который используется для "группировки" или "привязки" блока выполнения внутри композиции. Это иногда необходимо для корректной работы в условиях управляющих конструкций, которые могут привести к тому, что один и тот же вызываемый композиционный элемент выполнится несколько раз во время процесса композиции.
Значение ключа не обязательно должно быть уникальным глобально — оно должно быть уникальным только среди вызовов key в данной точке композиции.