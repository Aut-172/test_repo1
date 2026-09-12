# Maven/Spring Boot scaffold decisions

This note records the repository review and the inputs to the follow-up
scaffolding task. It intentionally does not create application or build files.

## Repository evidence (2026-09-12)

- `README.md` contains only “This is a readme file.”
- IntelliJ metadata is present under `.idea/`; `.idea/.gitignore` ignores IDE
  workspace and local data files.
- No `pom.xml` exists.
- No `src/main` or `src/test` directory exists.
- No `.github/workflows` directory or workflow file exists.
- No Maven Wrapper files exist: `mvnw`, `mvnw.cmd`,
  `.mvn/wrapper/maven-wrapper.properties`, or
  `.mvn/wrapper/maven-wrapper.jar`.
- The checked-in IntelliJ project metadata declares JDK/language level 24
  (`.idea/misc.xml`). This is the only Java-version evidence in the repository.

## Coordinates and versions for scaffolding

| Setting | Decision |
| --- | --- |
| `groupId` | `com.aut172` |
| `artifactId` | `test_repo1` (matches the repository name) |
| Project version | `0.0.1-SNAPSHOT` |
| Java/JDK release | `24` (aligns with `.idea/misc.xml`) |
| Spring Boot | `3.5.5` (pinned baseline for the scaffold; verify availability when generating) |
| Base package | `com.aut172.testrepo1` (Java-safe form of the artifact name) |
| Main application class | `TestRepo1Application` |
| GitHub Actions workflow | `.github/workflows/maven.yml`, display name `Java CI with Maven` |

The initial application should be a minimal Spring Boot entry point only; no
business endpoints, persistence, messaging, or security configuration is part
of this decision.

## Maven Wrapper

Add the Maven Wrapper as part of the scaffold (`mvnw`, `mvnw.cmd`, and
`.mvn/wrapper/*`). It is not present today, so adding it will be an intentional
new file set rather than an overwrite of existing project files.
