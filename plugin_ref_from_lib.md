# How to use plugin definition from `libs.versions.toml`

`libs.versions.toml`
````toml
[versions]
liquibaseGradlePlugin = "2.2.2"

[plugins]
liquibase = { id = "org.liquibase.gradle", version.ref = "liquibaseGradlePlugin" }
````
`build.gradle.kts`
````kotlin
plugins {
    alias(libs.plugins.liquibase)
}
````