# Correct git-versioning usage example
 See https://github.com/qoomon/gradle-git-versioning-plugin

````kotlin
plugins {
    id("me.qoomon.git-versioning") version "6.4.4"
}

version = "0.0.0-SNAPSHOT"
gitVersioning.apply {
    refs {
        // use tags on branches
        considerTagsOnBranches = true
        // tag configuration first
        tag("v(?<version>.*)") {
            version = "\${ref.version}"
        }
        // branch configuration second
        branch(".+") {
            version = "\${ref}-SNAPSHOT"
        }
    }
    rev {
        version = "\${commit.short}-SNAPSHOT"
    }
}
````

