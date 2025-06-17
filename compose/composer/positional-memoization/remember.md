# Remember
Задача функции remember – один раз вычислить значение и закешировать его в SlotTable и в дальнейшем использовать этот результат без повторного вычисления значения при перезапуске Composable функции. Remember сохраняет значение до тех пор, пока оно не покинет Composition

### remember(key)
remember создает объект один раз и возвращает нам его все последующие вызовы. Но иногда нам необходимо пересоздать объект из remember. Для таких случаев у remember есть параметр key:

`remember(key) { ... }`

Пока key не меняется, remember каждый раз возвращает нам ранее созданный объект. Но если при очередном вызове remember у нас сменился key, то remember снова выполняет код по созданию объекта. И теперь каждый раз будет возвращать нам этот новый объект, пока key снова не поменяется.

### rememberSaveable 
API rememberSaveable ведет себя аналогично remember , поскольку сохраняет состояние при рекомпозиции, а также при воссоздании активности или процесса с использованием механизма сохраненного состояния экземпляра. Например, это происходит при повороте экрана.

#### Способы хранения состояния
Все типы данных, которые добавляются в Bundle , сохраняются автоматически. Если вы хотите сохранить что-то, что не может быть добавлено в Bundle , есть несколько вариантов:

**Parcelable**
Самое простое решение — добавить аннотацию @Parcelize

**MapSaver**
Вы можете использовать mapSaver , чтобы определить собственное правило преобразования объекта в набор значений, которые система может сохранить в Bundle

```
data class City(val name: String, val country: String)

val CitySaver = run {
    val nameKey = "Name"
    val countryKey = "Country"
    mapSaver(
        save = { mapOf(nameKey to it.name, countryKey to it.country) },
        restore = { City(it[nameKey] as String, it[countryKey] as String) }
    )
}

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable(stateSaver = CitySaver) {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

**ListSaver**
Чтобы избежать необходимости определять ключи для карты, вы также можете использовать listSaver и использовать его индексы в качестве ключей:


```
data class City(val name: String, val country: String)

val CitySaver = listSaver<City, Any>(
    save = { listOf(it.name, it.country) },
    restore = { City(it[0] as String, it[1] as String) }
)

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable(stateSaver = CitySaver) {
        mutableStateOf(City("Madrid", "Spain"))
    }
}

```