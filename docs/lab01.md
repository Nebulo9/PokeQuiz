# Lab 1 - M2 Cyber

Membres du groupe:
* Mathéo DESSAUVAGES
* Daoud KARAM
* Elias TURKI

## Etape 5

### Q1

Le projet contient 4 modules Gradle + la racine:
    * `androidApp`
    * `desktopApp`
    * `shared`
    * `webApp`

Ils sont chacun déclarés dans le fichier `settings.gradle.kts` en utilisant la commande `include`.

Mais `iosApp` n'en fait pas partie car celui-ci est projet XCode natif compilé avec les outils d'Apple indépendants de Gradle.

### Q2

Le fichier `gradle/libs.versions.toml` contient les versions utilisées dans ce projet pour Kotlin (2.4.20), Compose Multiplatform (1.12.1) ainsi qu'AGP (9.1.1).
La version de Gradle (9.5.1) peut être trouvé dans le fichier `gradle/wrapper/gradle-wrapper.properties`.

### Q3

Dans le fichier `shared/build.gradle.kts`, l'on peut retrouver les plugins suivants:
* `kotlinMultiplatform (org.jetbrains.kotlin.multiplatform)`
* `androidMultiplatformLibrary (com.android.kotlin.multiplatform.library)`
* `composeMultiplatform (org.jetbrains.compose)`
* `composeCompiler (org.jetbrains.compose.compiler)`

Dans le fichier `androidApp/build.gradle.kts`, l'on peut retrouver les plugins suivants:
* `androidApplication (com.android.application)`
* `composeCompiler (org.jetbrains.kotlin.plugin.compose)`

La raison pour laquelle `androidApp` n'applique pas le plugin est qu'à partir d'AGP 9, le plugin `com.android.application` prend en charge directement Kotlin. L'ajout de `org.jetbrains.kotlin.android` n'est donc pas nécessaire.

### Q4

La bibliothèque partagée `shared` se trouve sous forme de Catalog dans le fichier de configuration `shared/build.gradle.kts`, de même pour la bibliothèque de l'application qui se trouve dans `androidApp/build.gradle.kts`.
La valeur de `minSdk` est centralisée dans le Version Catalog `gradle/libs.versions.toml` via la clé `android-minSdk`.

### Q5

Liste des dossiers de shared/src :

* `androidHostTest/`
* `androidMain/`
* `commonMain/`
* `commonTest/`
* `iosMain/`
* `iosTest/`
* `jsMain/`
* `jvmMain/`
* `jvmTest/`
* `wasmJsMain/`
* `webTest/`

La raison pour laquelle il n'y a pas de dossier `iosArm64Main` réside dans le fait que Kotlin Multiplatform utilise une hiérarchie de source sets par défaut qui regroupe les cibles iOS (`iosArm64` et `iosSimulatorArm64` déclarées dans `shared/build.gradle.kts`) au sein d'un dossier parent commun : `iosMain`.
Le code partagé pour iOS est placé dans `iosMain`. Un dossier `iosMainArm64` ne serait créé qu'en cas de besoin spécifique à une architecture ARM64.


### Q6

La fonction `getPlatform` est déclarée dans le fichier `shared/src/commonMain/kotlin/fr/m2/pokequiz/Platform.kt`.
Il existe 5 implémentations d'actual dans les fichiers suivants:

* `shared/src/androidMain/kotlin/fr/m2/pokequiz/Platform.android.kt` (Android)
* `shared/src/iosMain/kotlin/fr/m2/pokequiz/Platform.ios.kt` (iOS)
* `shared/src/jsMain/kotlin/fr/m2/pokequiz/Platform.js.kt` (JS)
* `shared/src/jvmMain/kotlin/fr/m2/pokequiz/Platform.jvm.kt` (Desktop JVM)
* `shared/src/wasmJsMain/kotlin/fr/m2/pokequiz/Platform.wasmJs.kt` (Wasm JS)

### Q7

L'`applicationId` Android est définie dans `androidApp/build.gradle.kts`, la `mainClass` Desktop dans `desktopApp/build.gradle.kts` et la `entryPoint` Web dans `webApp/build.gradle.kts`.
Le suffixe `Kt` sert de point d'entrée à la compilation sur la fonction `MainKt`

### Q8

La fonction `App()` est définie dans le fichier `shared/src/commonMain/kotlin/fr/m2/pokequiz/App.kt`.

Elle est appelée depuis 5 endroits distincts :

1. `androidApp/src/main/kotlin/fr/m2/pokequiz/MainActivity.kt` (ligne 16) : Dans `onCreate()` pour l'application Android.
2. `androidApp/src/main/kotlin/fr/m2/pokequiz/MainActivity.kt` (ligne 24) : Dans la fonction d'aperçu `@Preview AppAndroidPreview()`.
3. `desktopApp/src/main/kotlin/fr/m2/pokequiz/main.kt` (ligne 11) : Dans la fonction `main()` pour l'application Desktop.
4. `shared/src/iosMain/kotlin/fr/m2/pokequiz/MainViewController.kt` (ligne 5) : Dans `MainViewController()` pour l'application iOS.
5. `webApp/src/webMain/kotlin/fr/m2/pokequiz/main.kt` (ligne 9) : Dans la fonction `main()` pour l'application Web.

### Q9

Le fichier `.gitignore` situé à la racine du projet contient les règles d'exclusion suivantes :

* Fichiers de projet IDE et système (`*.iml`, `.idea`, `.DS_Store`, `xcuserdata`).
* Repertoires de compilation et de cache (`.gradle`, `.kotlin`, `**/build/`, `.cxx`, `.externalNativeBuild`, `captures`, `node_modules/`).
* Paramètres locaux (`local.properties`).
* Fichiers temporaires ou spécifiques Xcode (`*.xcodeproj/*`, avec des règles d'inclusion pour la structure du projet).

Le fichier `local.properties` contient des configurations propres à l'ordinateur du développeur, comme le chemin d'accès local au SDK Android (`sdk.dir`). Il varie d'une machine à l'autre et peut contenir des clés d'API locales. Son exclusion évite de polluer le dépôt et de casser la compilation sur les autres machines ou les serveurs d'intégration continue (CI).

### Q10

**Ce que dit l'IDE :**
L'IDE affiche l'erreur suivante : `Unresolved reference 'java'`.

**Pourquoi ?**
Le fichier `App.kt` est situé dans le source set `commonMain`, réservé au code commun Kotlin Multiplatform. Ce code doit pouvoir être compilé sur toutes les plateformes cibles (iOS, Web, Desktop JVM, Android).

La classe `java.io.File` fait partie de la bibliothèque standard Java (JVM) et n'est pas disponible sur les plateformes non-JVM (comme iOS ou WebAssembly/JS). Il est donc impossible d'utiliser des paquets `java.*` directement dans `commonMain`.

## Etape 7

5. Une `UIException` est levée
