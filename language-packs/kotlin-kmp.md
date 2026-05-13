<!--
  Language-pack: Kotlin Multiplatform (KMP), 2.1+ on Gradle 8+ (Kotlin DSL).
  Governed by: BLUEPRINT-SPEC.md §5 + §6.1
  Source of conventions: Kotlin official guidance (kotlinlang.org),
  JetBrains' KMP documentation, the kotlinx.* ecosystem
  (coroutines, serialization, datetime, io), and the Sodium-
  consumer pattern (Natrium consumes Sodium's UniFFI Kotlin
  bindings from a KMP library).

  KMP-specific guidance follows spec §6.1 "Plattform-Varianten
  derselben Sprache" — KMP is *one* language with platform
  variants, not a polyglot setup. Per-platform Swift / Java
  glue counts as the platform, not as a separate language pack.

  No first-party Schwarz Digits KMP component has shipped at the
  time of writing; this pack will be updated in place once one
  does and the TBD-marked sections elaborated.
-->

# Language-Pack: Kotlin Multiplatform

## Build manifest

- **Manifest:** `build.gradle.kts` per module + `settings.gradle.kts`
  at repo root. Kotlin DSL (not Groovy) is mandatory — it gives
  type-safe build scripts and is the path JetBrains tooling
  optimises for.
- **Plugin:** `kotlin("multiplatform")` version `2.1.x` or
  later (K2 compiler is stable since 2.0; do not pin to pre-2.0
  for new components).
- **Gradle:** 8.x minimum. Use the Gradle Wrapper
  (`gradle-wrapper.jar` + `gradle/wrapper/gradle-wrapper.properties`)
  so contributors and CI run identical Gradle versions without
  installing it.
- **Java Toolchain:** 17 minimum (`kotlin { jvmToolchain(17) }`).
  Newer LTS (21) is fine; pre-17 is rejected.
- **Dependency catalog:** `gradle/libs.versions.toml` (Gradle
  Version Catalog) — central place for dependency versions,
  type-safe accessors in build scripts, single source of truth
  for upgrades.
- **License:** declared via the `LICENSE` file at repo root and
  in the `pom` block of `mavenPublishing` when the artefact is
  published.

Example skeleton (`build.gradle.kts` for a single-module KMP library):

```kotlin
plugins {
    kotlin("multiplatform") version "2.1.0"
    kotlin("plugin.serialization") version "2.1.0"
}

kotlin {
    jvmToolchain(17)

    jvm()
    androidTarget { publishLibraryVariants("release") }
    iosX64()
    iosArm64()
    iosSimulatorArm64()
    macosX64()
    macosArm64()
    linuxX64()

    sourceSets {
        commonMain.dependencies {
            implementation(libs.kotlinx.coroutines.core)
            implementation(libs.kotlinx.serialization.json)
        }
        commonTest.dependencies {
            implementation(libs.kotlin.test)
        }
    }
}
```

## Module / package layout

```
<component>/
├── build.gradle.kts                 # root or per-module build script
├── settings.gradle.kts              # plugin management + module declarations
├── gradle.properties
├── gradle/
│   ├── wrapper/
│   │   ├── gradle-wrapper.jar
│   │   └── gradle-wrapper.properties
│   └── libs.versions.toml           # version catalog
├── gradlew + gradlew.bat            # Gradle Wrapper scripts
├── src/
│   ├── commonMain/kotlin/           # platform-agnostic Kotlin (the main code)
│   ├── commonTest/kotlin/           # platform-agnostic tests
│   ├── jvmMain/kotlin/              # JVM `actual` implementations + JVM-only API
│   ├── jvmTest/kotlin/              # JVM-specific tests (JUnit 5 etc.)
│   ├── androidMain/kotlin/          # Android `actual` implementations
│   ├── androidUnitTest/kotlin/      # Android unit tests (run on host JVM)
│   ├── iosMain/kotlin/              # shared across iosX64/iosArm64/iosSimulatorArm64
│   ├── iosTest/kotlin/              # iOS-specific tests
│   ├── nativeMain/kotlin/           # shared across macOS/Linux native targets
│   └── nativeTest/kotlin/
└── docs/                            # blueprint-mandated docs
```

Source-set hierarchy is set up by `kotlin { }` block — default
hierarchy templates cover the common cases (jvm + android +
ios + native). Custom intermediate source sets via
`applyDefaultHierarchyTemplate { ... }` only when truly needed.

## Test position convention

- **Unit tests:** colocated with the source set they cover.
  `commonTest` for platform-agnostic logic, `jvmTest` for
  JVM-specific code, etc. Use `kotlin.test` (`@Test`, `assertEquals`,
  `assertFailsWith`) in commonTest — it's the multiplatform
  layer that resolves to JUnit on JVM and the relevant native
  framework on each native target.
- **Platform-specific tests** in their respective source sets.
  Add JUnit 5 (`testImplementation(libs.junit.jupiter)`) to
  `jvmTest` for richer assertions; on iOS / native, stay with
  `kotlin.test`.
- **Integration tests:** by convention `src/<target>IntegrationTest/`
  source sets, registered as separate Gradle tasks (e.g.
  `tasks.named("jvmIntegrationTest")`). Gated by the `integration`
  build feature or invoked explicitly.
- **Doc-tests / examples:** Kotlin has no built-in doc-test
  runner. Examples live under `samples/<scenario>/` as runnable
  Gradle subprojects, or as `@Sample`-annotated code referenced
  from KDoc that Dokka renders.

## Language-neutral operations → concrete commands

| Language-neutral operation | Concrete command |
|---|---|
| `pre-commit smoke`     | `./gradlew ktlintCheck detekt :commonTest :jvmTest` |
| `pre-merge gate`       | `pre-commit smoke && ./gradlew allTests check` *(`allTests` covers every target including iOS / native)* |
| `lint`                 | `./gradlew ktlintCheck detekt` |
| `format`               | `./gradlew ktlintFormat` |
| `format-check`         | `./gradlew ktlintCheck` |
| `unit-tests`           | `./gradlew allTests` *(or `:commonTest` for the fastest feedback loop during development)* |
| `integration-tests`    | `./gradlew jvmIntegrationTest iosIntegrationTest …` *(per-target tasks; aggregate via a custom `integrationTest` umbrella)* |
| `dep-license-check`    | `./gradlew checkLicense` *(via `com.github.jk1.dependency-license-report` plugin)* |
| `api-docs-generate`    | `./gradlew dokkaHtml` *(Dokka — Kotlin's `cargo doc` analog; HTML output under `build/dokka/html/`)* |
| `release-build`        | `./gradlew assemble` *(builds every target; for iOS XCFramework: `./gradlew assembleSharedXCFramework`)* |

Notes on the gate:

- **`./gradlew allTests`** runs every test in every target's
  source set — slow but the only way to catch platform-specific
  regressions. Keep it in `pre-merge gate`, not `pre-commit
  smoke`. For pre-commit, `:commonTest` + `:jvmTest` is the
  fast subset (~seconds rather than minutes).
- **ktlint + detekt** are the two-headed lint stack: `ktlint`
  enforces Kotlin's official style, `detekt` adds rule-based
  issue detection (complexity, naming, empty blocks, …).
  Configure detekt via `detekt.yml` at repo root.
- **`./gradlew check`** is the meta-task that aggregates
  `*Test`, `*lint*`, and other verification tasks. It is the
  "everything you can verify locally" entry point.
- **Native iOS tests** require macOS host. On CI matrices, the
  iOS test job runs on `macos-latest`; the JVM/Android job
  runs on `ubuntu-latest`. Don't try to run iOS tests on Linux —
  the toolchain fails before it starts.

## Platform variants — `expect` / `actual` convention

KMP's signature mechanism for per-platform code. In `commonMain`
you declare a contract; in each platform source set you supply
the implementation.

```kotlin
// commonMain/kotlin/security/SecureStore.kt
expect class SecureStore() {
    fun put(key: String, value: ByteArray)
    fun get(key: String): ByteArray?
}

// jvmMain/kotlin/security/SecureStore.kt
actual class SecureStore actual constructor() {
    // delegates to java.security.KeyStore
}

// iosMain/kotlin/security/SecureStore.kt
actual class SecureStore actual constructor() {
    // delegates to platform.Security.SecKeychain*
}
```

Rules:

- **`expect`/`actual` signatures must match exactly** —
  parameter names, types, default values, type parameters,
  modifiers. Drift fails the build at the platform-actual side,
  not at the common-expect side; check both.
- **Prefer interfaces + factory functions over `expect class`**
  when the per-platform implementation diverges significantly.
  An interface in commonMain plus a per-platform `actual fun
  createSecureStore(): SecureStore` is often cleaner than
  expect-classing the type itself.
- **Native glue code** (minimal Swift on iOS, Objective-C
  interop, JNI on Android) counts as the platform per spec §6.1
  — it doesn't trigger a polyglot setup. Keep it inside the
  platform source set (e.g. `iosMain/kotlin/.../<Class>.kt`
  consuming `platform.*` packages) or in `iosMain/swift/` if a
  separate Swift file is needed.
- **iOS source set is shared across architectures**
  (`iosX64Main`, `iosArm64Main`, `iosSimulatorArm64Main` all
  inherit from `iosMain`) — write iOS code once.

## Error-handling idiom

- **Public surface (Kotlin idioms):** exceptions for unrecoverable
  errors, `Result<T>` or sealed classes for expected outcomes
  callers must handle.
- **Sealed classes for domain errors:** the canonical pattern
  when callers should `when`-exhaust over outcomes:
  ```kotlin
  sealed class LoginError {
      data class InvalidCredentials(val attempt: Int) : LoginError()
      data class NetworkFailure(val cause: Throwable) : LoginError()
      object RateLimited : LoginError()
  }
  ```
- **`Result<T>`** for try-wrappers:
  ```kotlin
  val result: Result<User> = runCatching { repository.fetchUser(id) }
  ```
- **commonMain Throwable hierarchy is narrow.** Only the
  exception types that exist across all platforms are usable in
  commonMain (`Throwable`, `Exception`, `RuntimeException`,
  `IllegalArgumentException`, `IllegalStateException`,
  `UnsupportedOperationException`, `IndexOutOfBoundsException`,
  `NoSuchElementException`, `NumberFormatException`,
  `ConcurrentModificationException`, `ArithmeticException`,
  `NullPointerException`, `ClassCastException`, `AssertionError`).
  Need a JVM-specific exception type? Catch it in jvmMain
  `actual` code and translate to a common error class.
- **No silent catches in library code.** `runCatching { … }.getOrNull()`
  is an explicit decision; `try { … } catch (e: Exception) { }` with
  empty body is a defect.

## Common pitfalls

- **`expect`/`actual` signature drift.** Adding a default value
  on one side without the other, renaming a parameter, changing
  visibility — each silently breaks one of the actual sites.
  IntelliJ catches most of this; CI verifies on each platform.
- **iOS-specific build flakiness.** First clean build can take
  minutes; subsequent incremental builds are fast. Don't `rm -rf
  build/` casually. `./gradlew --refresh-dependencies` and
  `./gradlew clean` are the recovery tools.
- **`Long` vs `NSInteger` on iOS.** Kotlin/Native's `Long` is
  64-bit on every iOS architecture; Swift's `Int` is 32-bit on
  iPhone 6 era 32-bit devices. Modern (arm64) iOS is 64-bit
  everywhere, but if your library targets 32-bit historical
  devices, audit explicit `Int` vs `Long` choices.
- **Kotlin/Native memory model.** Since Kotlin 1.7.20 the new
  memory model is default and matches JVM semantics
  (mutable shared state across threads is allowed, locks /
  atomics are responsibilities of the programmer). Pre-1.7.20
  legacy code may have `@SharedImmutable` annotations and
  `freeze()` calls — those are obsolete and should be removed.
- **Coroutines on iOS:** the Main dispatcher needs explicit
  initialisation in iOS apps that consume the KMP library
  (`Dispatchers.setMain(...)`). KMP libraries should not
  bind `Dispatchers.Main` themselves — let the consumer
  configure it.
- **kotlinx.datetime, not java.time.** `java.time.*` is JVM-only;
  in commonMain use `kotlinx.datetime.Instant`, `LocalDateTime`,
  etc. Same for `java.io` → `kotlinx.io`.
- **Atomicfu for thread-safe counters / refs.** Use
  `kotlinx.atomicfu` in commonMain; it compiles to
  `java.util.concurrent.atomic.*` on JVM and `kotlin.native.concurrent.*`
  on Native.
- **CocoaPods integration is optional, not required.** The
  `cocoapods` Gradle plugin lets iOS consumers depend on the
  KMP library via Podfile. If you publish XCFrameworks via
  Swift Package Manager instead, skip CocoaPods entirely.
- **Resources are first-class only since Kotlin 1.9+** via the
  `compose.components.resources` Gradle dependency (for Compose
  Multiplatform) or `kotlinx.multiplatform-resources` standalone.
  Older patterns (manual per-platform `MR.string.…`) are out.
- **Don't put `expect`/`actual` on Companion objects.** The
  compiler does not enforce signature parity correctly there.
  Use top-level `expect fun create…(): …` factories instead.

## Recommended dependencies

| Concern | Library | Rationale |
|---|---|---|
| HTTP client    | `io.ktor:ktor-client-core` + per-platform engine (`ktor-client-okhttp` JVM, `ktor-client-darwin` iOS, `ktor-client-cio` linux) | The Kotlin team's HTTP client, multiplatform from day one. |
| JSON / CBOR    | `org.jetbrains.kotlinx:kotlinx-serialization-json` (or `-cbor`) | The Kotlin team's serialization. Compile-time, no reflection. |
| coroutines     | `org.jetbrains.kotlinx:kotlinx-coroutines-core` | Built-in async; the cancellation-and-structured-concurrency contract every public API takes. |
| date/time      | `org.jetbrains.kotlinx:kotlinx-datetime` | Multiplatform replacement for `java.time.*`. |
| filesystem / IO| `org.jetbrains.kotlinx:kotlinx-io` | Multiplatform replacement for `java.io.*`. |
| persistence    | `app.cash.sqldelight:runtime` + per-platform driver | SQL-first, multiplatform; generates type-safe Kotlin from `.sq` schema files. |
| DI             | `io.insert-koin:koin-core` | The de-facto KMP DI framework; service-locator + DSL, no reflection. |
| logging        | `io.github.oshai:kotlin-logging` (or `io.github.aakira:napier` for KMP-native multiplatform) | Multiplatform logging facade; resolves to SLF4J on JVM, OSLog on iOS, console on JS. |
| atomics        | `org.jetbrains.kotlinx:atomicfu` | Compile-time atomic-references that compile to platform primitives. |
| testing        | `org.jetbrains.kotlin:kotlin-test` (common) + `org.junit.jupiter:junit-jupiter` (jvm) + `app.cash.turbine` (Flow testing) | kotlin-test is the multiplatform assertion API; JUnit 5 adds ergonomics on JVM; Turbine tames `Flow` assertions. |
| mocking        | `io.mockk:mockk` (jvm only) | Kotlin-native mocking; iOS / Native test mocking is typically done via hand-written fakes. |
| ktlint         | `org.jlleitschuh.gradle:ktlint-gradle` | Gradle plugin around the ktlint CLI. |
| detekt         | `io.gitlab.arturbosch.detekt:detekt-gradle-plugin` | Rule-based static analysis. |
| Dokka          | `org.jetbrains.dokka:dokka-gradle-plugin` | API docs generator (Kotlin's javadoc analog). |
| license report | `com.github.jk1:gradle-license-report` | Generates per-dependency license report; pairs with the dep-license-check operation. |
| publishing     | `com.vanniktech:gradle-maven-publish-plugin` | KMP-aware Maven publish (handles per-target artefact coordinates, sources/javadoc jars, signing). |

Stdlib first; reach for a third-party only when the standard
library or the kotlinx.* ecosystem falls short. The
JetBrains-maintained packages (`org.jetbrains.kotlinx:*` and
`io.ktor:*`) are first-class multiplatform; many older
"multiplatform" community packs are JVM-only in practice —
verify before adopting.

## Release & versioning

<!-- TBD: no first-party Schwarz Digits KMP release has shipped
     yet, so the exact publish flow is generic. Update once the
     first component cuts a release. -->

- **Versioning:** SemVer. `vMAJOR.MINOR.PATCH` annotated tags.
- **Publication coordinates:** typically
  `<group>:<artifact>:<version>` with per-target classifiers
  produced by the multiplatform plugin (KMP publishes a "common"
  metadata artefact plus per-target artefacts; consumers pick
  the right one automatically via Gradle metadata).
- **Publish flow (`vanniktech-maven-publish`):**
  1. Bump version in `gradle.properties` and `libs.versions.toml`.
  2. Run `pre-merge gate`.
  3. `./gradlew publishAllPublicationsToMavenCentralRepository`
     (or to your Schwarz Digits-internal Maven repository).
  4. Tag and push.
- **iOS XCFramework distribution:**
  `./gradlew assembleSharedXCFramework` produces a `.xcframework`
  bundle for Swift consumers. Publish via Swift Package Manager
  (recommended) or CocoaPods (if legacy consumers need it).
- **Android-only AAR:** `./gradlew :assembleRelease` produces the
  AAR; consume via Maven coordinates the same way as any
  Android library.

## Cross-compilation targets

For KMP every target is "cross-compilation" by definition.
Default target set for a Schwarz Digits component:

| Target                | Build command                          | Host requirement |
|-----------------------|----------------------------------------|------------------|
| `jvm`                 | `./gradlew :compileKotlinJvm`          | any |
| `androidTarget`       | `./gradlew :compileReleaseKotlinAndroid` | any (Android SDK installed) |
| `iosX64`              | `./gradlew :compileKotlinIosX64`       | **macOS** |
| `iosArm64`            | `./gradlew :compileKotlinIosArm64`     | **macOS** |
| `iosSimulatorArm64`   | `./gradlew :compileKotlinIosSimulatorArm64` | **macOS** |
| `macosX64`            | `./gradlew :compileKotlinMacosX64`     | **macOS** |
| `macosArm64`          | `./gradlew :compileKotlinMacosArm64`   | **macOS** |
| `linuxX64`            | `./gradlew :compileKotlinLinuxX64`     | Linux or macOS |

`./gradlew assemble` builds every target the host supports. CI
should split into a Linux job (jvm + android + linuxX64) and a
macOS job (everything else); the macOS job is the slow one.

**iosFat / XCFramework:** when shipping to Swift consumers, the
three iOS targets are bundled into one XCFramework via
`./gradlew assembleSharedXCFramework` (where "Shared" is your
shared module's name). Apple Silicon hosts run `iosSimulatorArm64`
in the simulator; Intel hosts run `iosX64`; physical devices
run `iosArm64`.

## IDE / editor setup

- **IntelliJ IDEA Ultimate** (or the free Community edition for
  pure-Kotlin work without Android) — the bundled Kotlin plugin
  handles KMP source-set inspection, `expect`/`actual` navigation,
  Gradle sync, and Dokka preview.
- **Android Studio** — for Android-target-heavy components, AS
  is IDEA + Android tooling pre-bundled. Same KMP support.
- **Fleet** (JetBrains, currently in beta but maturing) — designed
  with multiplatform / multi-language workflows in mind. Worth
  trying for KMP + Swift simultaneous editing.
- **Format on save:** wire ktlint into your IDE (IntelliJ's
  ktlint plugin or via `./gradlew ktlintFormat` on the file).
- **Detekt on save:** detekt IDE plugin shows rule violations
  inline.
- **AppCode** — JetBrains' Swift / Objective-C IDE; discontinued
  in 2022 but historical KMP-iOS docs may still reference it.
  Use Xcode for the Swift side instead.
- **Xcode** — for the iOS-side of a KMP project: open the iOS
  app's Xcode project, point its dependencies at the
  XCFramework produced by `./gradlew assembleSharedXCFramework`,
  iterate. Xcode does not understand the KMP build; treat the
  XCFramework as an opaque binary library.
