# Aggregate JUnit reports for multi-projects setup


1. Include `java-reporting-conventions` plugin
````kotlin
plugins {
    id("java-reporting-conventions")
    id("me.qoomon.git-versioning") version "6.4.4"
}
````

2. Define `java-reporting-conventions` plugin
`java-reporting-conventions.gradle.kts`
````kotlin
plugins {
    base
    id("test-report-aggregation")
}

// do not resolve dependencies for projects 
configurations.named("aggregateTestReportResults") {
    isTransitive = false
}

// configure reporting
reporting {
    reports {
        register<AggregateTestReport>("testAggregateTestReport") {
            testType = TestSuiteType.UNIT_TEST
        }
        register<AggregateTestReport>("integrationTestAggregateTestReport") {
            testType = TestSuiteType.INTEGRATION_TEST
        }
    }
}


tasks {
    register("reports") {
        dependsOn(reporting.reports.withType<AggregateTestReport>().map { it.reportTask })
    }

    // pack aggregated reports to single zip file to simplify fetching it as gitlab artefact 
    register<Zip>("packageTestReport") {
        from(project.layout.buildDirectory.dir("reports/tests"))
        destinationDirectory.set(layout.buildDirectory.dir("dist"))
        archiveFileName.set("test_report.zip")
    }
    
    named("reports") {
        finalizedBy("packageTestReport")
    }

    named("check") {
        finalizedBy("reports")
    }
}

/**
 * Get java projects.
 */
fun org.gradle.api.Project.javaProjects(): List<Project> {
    return subprojects.filter { File(it.projectDir, "src").exists() }
}


dependencies {
    javaProjects().forEach {
        testReportAggregation(project(it.path))
    }
}
````