# Positional Memoization
- Классическом виде мемоизация бы учитывала параметры функции, но позиционная учитывает еще и место, в котором функция выполняется (call site).
- Мемоизация локальна и ограничена контекстом родительской composable функциии.

Пример: 
Здесь два идентичных вызова, но обратим внимание, что у них разная позиция в коде.

kotlin
```
@Composable
fun myFun(condition: Boolean) {
    if (condition) {
        Text("Static text")
    } else {
        Text("Static text")
    }
}
``` 

декомпилируем

```
public static final void myFun(boolean condition, @Nullable Composer $composer, int $changed) {
      $composer = $composer.startRestartGroup(-201342111);

         if (condition) {
            $composer.startReplaceGroup(-1061497002);
            //Text("Static text")
            $composer.endReplaceGroup();
         } else {
            $composer.startReplaceGroup(-1061456330);
            //Text("Static text")
            $composer.endReplaceGroup();
         }

         if (ComposerKt.isTraceInProgress()) {
            ComposerKt.traceEventEnd();
         }
      }

      ScopeUpdateScope var4 = $composer.endRestartGroup();
}
``` 

И видим, что у нас получилось две разных группы, не смотря на то, что они идентичны. При переключении condition с true на false произойдет перекючения групп и функция будет вычислена заново.