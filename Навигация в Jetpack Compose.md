Для навигации в Jetpack Compose используется библиотека *Jetpack Navigation*, которая позволяет размещать отдельные экраны в разных Composables и навигироваться по ним с помощью NavHostController

## Dependencies

### Libs
```kotlin
navigationCompose = "2.8.0" // earlier version use string routes
```
```kotlin
androidx-navigation-compose = { module = "androidx.navigation:navigation-compose", version.ref = "navigationCompose" }
```
### Gradle
```kotlin
implementation(libs.androidx.navigation.compose)
```

## Простой пример

```kotlin
sealed class Screen {
    @Serializable data object Home : Screen()
    @Serializable data object Contacts : Screen()
    @Serializable data class Profile(name: String) : Screen()
}
```
```kotlin
val navController = rememberNavController()

NavHost(navController = navController, startDestination = Screen.Home) {

    composable<Screen.Home>() { Home() }

    composable<Screen.Contacts() { Contacts()  }

    composable<Screen.About>() { Profile(name = "Mike") }
}
```

## NavOptions

В `Jetpack Compose Navigation`, при вызове метода `navController.navigate()` можно использовать дополнительные опции, передавая объект `NavOptions`. Эти опции позволяют управлять поведением навигации, таким как очищение back stack, анимации, восстановление состояния и другие.

### Основные опции навигации:

#### **`popUpTo(screen)`**
   - Позволяет очистить стек до определенного экрана.
   - Параметры:
     - `inclusive`: Если `true`, удаляет и сам указанный экран. Если `false` (по умолчанию), оставляет указанный экран в стеке.
     - `saveState`: Сохраняет состояние экрана, если он будет возвращен позже с использованием `restoreState = true`.

   Пример:
   ```kotlin
   navController.navigate("screenB") {
       popUpTo("screenA") { inclusive = false }
   }
   ```

#### **`launchSingleTop`**
   - Если указать `launchSingleTop = true`, то при повторной навигации на уже находящийся на вершине back stack экран не будет создан новый экземпляр этого экрана, а будет использован текущий (то есть, экран не дублируется).
   
   Пример:
   ```kotlin
   navController.navigate("screenA") {
       launchSingleTop = true
   }
   ```

#### **`restoreState`**
   - Используется в комбинации с `popUpTo`. Если установлен `restoreState = true`, то восстанавливается состояние экрана, если он ранее был сохранен с `saveState`.
   
   Пример:
   ```kotlin
   navController.navigate("screenA") {
       popUpTo("screenA") {
           inclusive = false
           saveState = true
       }
       restoreState = true
   }
   ```

#### **`saveState`**
   - Сохраняет состояние экрана, который удаляется из стека при использовании `popUpTo`. Позволяет восстановить состояние экрана позже с помощью `restoreState`.
   
   Пример:
   ```kotlin
   navController.navigate("screenB") {
       popUpTo("screenA") {
           saveState = true
       }
   }
   ```

#### **`enterAnim` и `exitAnim`**
   - Позволяют задать пользовательские анимации при переходах между экранами. Это доступно через класс `NavOptions`.
   
   Пример:
   ```kotlin
   navController.navigate("screenB") {
       anim {
           enter = R.anim.slide_in_right
           exit = R.anim.slide_out_left
           popEnter = R.anim.slide_in_left
           popExit = R.anim.slide_out_right
       }
   }
   ```

#### **`popEnterAnim` и `popExitAnim`**
   - Настраивают анимации при возврате назад (поп из стека).

   Пример:
   ```kotlin
   navController.navigate("screenA") {
       anim {
           popEnter = R.anim.slide_in_left
           popExit = R.anim.slide_out_right
       }
   }
   ```

### Пример использования всех NavOptions:

```kotlin
navController.navigate("screenB") {
    // Удаляем все экраны после "screenA", но оставляем "screenA" в стеке
    popUpTo("screenA") { inclusive = false }
    
    // Не создаем новый экран, если "screenB" уже на вершине стека
    launchSingleTop = true
    
    // Восстанавливаем состояние, если оно было сохранено
    restoreState = true
    
    // Настраиваем пользовательские анимации
    anim {
        enter = R.anim.slide_in_right
        exit = R.anim.slide_out_left
        popEnter = R.anim.slide_in_left
        popExit = R.anim.slide_out_right
    }
}
```

### Другие полезные опции:
- **`popBackStack()`** — возвращает назад по стеку, удаляя текущий экран из стека.
- **`navigateUp()`** — аналог кнопки "Назад", возвращает на предыдущий экран в стеке.
