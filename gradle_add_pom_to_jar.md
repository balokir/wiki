#How to include pom file in the jar in Kotlin DSL

`build.gradle.kts`
````kotlin
plugins {
    // Apply the java Plugin to add support for Java.
    java
    id("maven-publish")
}

publishing {
    publications {
        create<MavenPublication>("maven") {
            groupId = rootProject.group.toString()
            artifactId = project.name
            version = project.version.toString()
            from(components["java"])
        }
    }
}

tasks {
    jar {
        duplicatesStrategy = DuplicatesStrategy.EXCLUDE

        into("META-INF/maven/${rootProject.group}/${project.name}") {
            val generatePomFileForMavenPublication by tasks.getting(GenerateMavenPom::class)
            from(generatePomFileForMavenPublication) {
                rename(".*", "pom.xml")
            }

            manifest {
                attributes["Bundle-Version"] = version
                attributes["Bundle-Name"] = project.name
                attributes["Automatic-Module-Name"] = rootProject.group
                attributes["Bundle-SymbolicName"] = rootProject.group
            }
        }
    }
}
````

