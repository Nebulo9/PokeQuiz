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

### Questions

## 1. Développement UI sur cible Desktop

* **Boucle de rétroaction ultra-rapide :** L'exécution et le Hot Reload sur Desktop s'exécutent en quelques millisecondes, sans avoir à lancer ni charger un émulateur mobile lourd.
* **Économie de ressources :** Ne nécessite pas de consommer la mémoire RAM et le CPU requis par l'émulation Android/iOS.
* **Gain de productivité :** Permet d'ajuster l'ergonomie, les composants et la logique d'affichage très rapidement avant les tests finaux sur mobiles.

## 2. Hot Reload vs Redémarrage

* **Hot Reload :** Injecte dynamiquement les modifications de code de l'interface utilisateur dans l'application en cours d'exécution, sans réinitialiser son état ni relancer le processus JVM.
* **Redémarrage complet :** Relance le processus à zéro et réinitialise entièrement l'état de l'application.
* **Changements ayant nécessité un redémarrage à l'étape 7 :** Les modifications de signatures de fonctions (comme l'ajout de `sayHelloFR`), la création de nouvelles fonctions de niveau supérieur et les modifications de structure qui ne peuvent pas être rechargées à chaud.

## 3. Ajout d'un bloc `androidTarget { }` de 2023

* Le build échouera avec une erreur de syntaxe DSL Gradle (`Unresolved reference: androidTarget`).
* Avec la version moderne d'AGP 9+ et du plugin Android Multiplatform Library, l'ancienne syntaxe `androidTarget { }` a été remplacée par le bloc `android { }` configuré via la nouvelle API.

## 4. Mécanisme du texte d'accueil spécifique par plateforme

* Le mécanisme repose sur le motif **`expect` / `actual`** de Kotlin Multiplatform.
* Dans `commonMain`, la classe `Greeting` fait appel à la fonction `expect fun getPlatform(): Platform`.
* Chaque plateforme (`androidMain`, `iosMain`, `jvmMain`, etc.) fournit son propre fichier avec `actual fun getPlatform(): Platform` retournant un nom propre au système d'exploitation (`Android`, `iOS`, `Desktop (Mac OS X)`, etc.).

## 5. Analyse du code et sécurité (DEX, JVM bytecode, binaire natif)

* **Le plus facile à analyser :** Le **bytecode JVM** (Desktop) et le fichier **DEX** (Android).
* **Pourquoi :** Ces formats conservent la structure des classes et des métadonnées, rendant la décompilation très aisée vers un code Java/Kotlin lisible avec des outils comme JADX ou JD-GUI. Le binaire iOS (Mach-O) est en binaire natif assembleur, beaucoup plus dur à décompiler.
* **Importance pour `commonMain` :** Tout code ou secret (clés d'API, algorithme confidentiel) placé dans `commonMain` sera présent dans l'APK Android ou le JAR Desktop. Un attaquant n'a qu'à décompiler la version Android/Desktop pour extraire le code de `commonMain`, même si l'application iOS est compilée en natif.

## 6. Tâches Gradle d'exécution et Hot Reload

* **Tâches d'exécution :**
    * `:desktopApp:run` (Exécution Desktop standard)
    * `:desktopApp:hotRun` (Exécution Desktop avec **Hot Reload**)
    * `:webApp:wasmJsBrowserDevelopmentRun` (Exécution Web Wasm)
    * `:webApp:jsBrowserDevelopmentRun` (Exécution Web JS)
* **Tâche de Hot Reload :** `:desktopApp:hotRun` (souvent utilisée avec le fanion `--auto`).

## 7. Sorties de compilation (`.class` et `.klib`)

* **Emplacement des fichiers `.class` :** Ils se trouvent dans le répertoire `shared/build/classes/kotlin/jvm/main/` pour la cible JVM.
* **Fichiers/dossiers `.klib` :** Ils se trouvent dans `shared/build/classes/kotlin/iosSimulatorArm64/main/` (ou dans le cache des klibs).
* **Rôle des `.klib` :** Une `klib` (Kotlin Library) est le format d'archive intermédiaire binaire spécifique à Kotlin Multiplatform. Elle contient l'IR (représentation intermédiaire) Kotlin et les métadonnées d'interopérabilité nécessaires au compilateur Kotlin/Native pour générer le binaire iOS final.

## 8. Arbre de dépendances runtime Desktop et Skiko

* **Bibliothèque présente :** `org.jetbrains.skiko:skiko-awt-*` (ex: `skiko-awt-runtime-macosx-arm64`).
* **Pourquoi :** Compose Multiplatform s'appuie sur le moteur graphique 2D **Skia**. **Skiko** (Skia for Kotlin) fournit la liaison entre Kotlin et Skia. Sur Desktop JVM, Compose s'intègre avec AWT/Swing et utilise Skiko-AWT pour effectuer le rendu visuel accéléré par le GPU.

## 9. Rôle des Preflight checks

* **Rôle :** Ces vérifications contrôlent avant la compilation que l'environnement dispose des outils nécessaires (installations de Xcode, `xcode-select`, SDK Android, version de JDK compatible, simulateurs).
* **Impact de la désactivation :** Si une vérification iOS est désactivée et qu'un outil manque, l'erreur ne sera pas détectée préventivement par l'IDE mais provoquera un échec brutal et obscur lors de la phase de compilation native par Gradle/Xcode.

## 10. Comparaison du rendu du bouton iOS vs Android

* **Visuel :** Le composable `Button` venant de la bibliothèque commune Compose Material 3 (`androidx.compose.material3`), l'apparence visuelle du bouton (forme, couleur, typographie) est identique sur iOS et Android.
* **Comportement :** L'animation de clic (effet de vague / ripple), le défilement tactile et la gestion du retour système s'adaptent aux composants et au canvas graphique natif de chaque OS.