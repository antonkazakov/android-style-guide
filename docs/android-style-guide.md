# Набор соглашений по написанию кода на android-проектах

# Содержание
1. [Константы](#constants)
2. [Фрагменты](#fragments)
3. [Логирование](#log)

## <a name='constants'>Константы</a>
* Для констант, которые используются как **ключи к `SharedPreferences`** необходимо придерживатся следующих соглашений:
    * Имя константы должно начинаться с префикса `PREF_`
    * Значение константы должно формироваться по следующему правилу: `{feature package name}.{flag}`. Это необходимо, что бы ключи не пересекались, если используется один файл для SharedPreferences
    
    ```kotlin
    const val PREF_IS_OAUTH_ENABLED = "ru.hh.network_source.is_oauth_enabled"
    ```
* Для констант, которые используются как **тэг при логировании** необходимо именовать константу `LOG_TAG`. Если названием для константы является название класса, и оно не помещается в 23 символа (ограничение LogCat-a), то можно удалить символы с конца строки.
    ```kotlin
    private const val LOG_TAG = "InternetAvailabilityInt"
    ```
* Для констант, которые используются как **тэг для экземпляра класса `Fragment`**, стоит именовать тэг `TAG` и оставлять его с публичным доступом. Т.о. при добавлении экземпляра `Fragment` в `FragmentManager` можно будет использовать этот тэг.

* Константы, которые используются как значения ключей для объектов типа `Bundle`, должны иметь модификатор `private` и именоваться: 
    * с префиксом `ARGS_`, если объект используется для предачи параметров между компонентами приложения или при передачи аргументов в объект класса `Fragment`
    * с префиксом `STATE_`, если объект используется для сохранения состояния экрана внутри метода `onSaveInstanceState`  
* Константы, которые используются как **аргумент `requestCode` в методе  `startActivityForResult`**, именуются с префиксом `REQUEST_CODE_`
* При первой имплементации интерфейса `Serializable` для какого-л. класса проставлять значение константы `serialVersionUID` равной **`1L`**
    ```kotlin
        private const val serialVersionUID = 1L
    ```

## <a name='fragments'>Фрагменты</a>
* Создавать экземпляры класса `Fragment` всегда необходимо через статический метод `newInstance` самого класса с передачей всех аргументов в этот метод. Аргументы не должны быть типа `Bundle`.

## <a name='log'>Логирование</a>
* При использовании статического метода `Timber.e(args)` в качестве аргументов всегда передавать экземпляр класса `Throwable`, даже несмотря на то, что в качестве аргументов можно передать только строку текста. Это необходимо, т.к. все ошибки у нас логируются в Crashlytics и затем отображаются как non-fatal, и важно что бы вершина stacktrace-a была в том месте, где ошибка возникает, т.о. ошибки будут выделяться отдельным элементом в Crashlytics. 
    ```kotlin
    Timber.tag(LOG_TAG).e(Exception("Unknown currency code: $currency"))
    ```
    Если же у нас уже есть экземпляр класса `Throwable`, то можно использовать метод `Timber.e(t: Throwable, message: String)`
    
    ```kotlin
    try {
        jsonValidator.validate(veryLongJSON)
    } catch (e: JSONException) {
        Timber.tag(LOG_TAG).e(e, "Cannot validate input JSON")
    }
    ```
    