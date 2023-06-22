> NEW LinkedIn Branch of Apache Kafka
=================

This is the version NEW of Kafka running at LinkedIn.

Kafka was born at LinkedIn. We run thousands of brokers to deliver trillions of
messages per day.  We run a slightly modified version of Apache Kafka trunk.
This branch contains the LinkedIn Kafka release.

This branch is made up of:

* Apache Kafka trunk (upstream) up to some branch point, see *-li* branch name for base version, you'll be able to get the exact commit from git 
* Cherry-picked commits from upstream after branch point
* Patches that are on their way upstream but we have deployed internally in the meantime
* Patches that are of no interest to upstream

We are making this branch available for people interested. We will be
documenting the changes in the near future with some more detailed explanations
in the [LinkedIn Engineering Blog](https://engineering.linkedin.com/blog).

If you are interested in learning more, we invite you to our [Streaming
Meetup](https://www.meetup.com/Stream-Processing-Meetup-LinkedIn/) where we
discuss streaming technologies like [Kafka](http://kafka.apache.org) and
[Samza](http://samza.apache.org).

You are encouraged to check out other Kafka projects from LinkedIn:

* [Cruise Control](https://github.com/linkedin/cruise-control)
* [Li-Apache-Kafka-Clients](https://github.com/linkedin/li-apache-kafka-clients)
* [Burrow](https://github.com/linkedin/Burrow)
* [Kafka Monitor](https://github.com/linkedin/kafka-monitor)

### CI ###
We are currently using Github Actions as the CI framework, and the testing results can be found [here](https://github.com/linkedin/kafka/actions).
To publish a release, go to [the release page](https://github.com/linkedin/kafka/releases) and manually create a new release.
Once the release tag is created, a test job will be triggered to run the necessary tests. And once the test passes, the artifacts
will be published to [the bintray hosting LinkedIn projects](https://dl.bintray.com/linkedin/maven/com/linkedin/kafka/kafka_2.12/).

### Contributing ###

At this moment we are not accepting external contributions directly. Please
contribute to [Apache Kafka](http://kafka.apache.org).

For security issues with this branch please review
[LinkedIn Security
Guidelines](https://www.linkedin.com/help/linkedin/answer/62924/security-vulnerabilities?lang=en).
General Kafka issues should be communicated via the Kafka community.


Apache Kafka
=================
See our [web site](https://kafka.apache.org) for details on the project.

You need to have [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) installed.

We build and test Apache Kafka with Java 8, 11 and 16. We set the `release` parameter in javac and scalac
to `8` to ensure the generated binaries are compatible with Java 8 or higher (independently of the Java version
used for compilation). Java 8 support has been deprecated since Apache Kafka 3.0 and will be removed in Apache
Kafka 4.0 (see [KIP-750](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=181308223) for more details).

Scala 2.12 and 2.13 are supported and 2.13 is used by default. Scala 2.12 support has been deprecated since
Apache Kafka 3.0 and will be removed in Apache Kafka 4.0 (see [KIP-751](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=181308218)
for more details). See below for how to use a specific Scala version or all of the supported Scala versions.

### Build a jar and run it ###
    ./gradlew jar

Follow instructions in https://kafka.apache.org/quickstart

### Build source jar ###
    ./gradlew srcJar

### Build aggregated javadoc ###
    ./gradlew aggregatedJavadoc

### Build javadoc and scaladoc ###
    ./gradlew javadoc
    ./gradlew javadocJar # builds a javadoc jar for each module
    ./gradlew scaladoc
    ./gradlew scaladocJar # builds a scaladoc jar for each module
    ./gradlew docsJar # builds both (if applicable) javadoc and scaladoc jars for each module

### Run unit/integration tests ###
    ./gradlew test # runs both unit and integration tests
    ./gradlew unitTest
    ./gradlew integrationTest
    
### Force re-running tests without code change ###
    ./gradlew cleanTest test
    ./gradlew cleanTest unitTest
    ./gradlew cleanTest integrationTest

### Running a particular unit/integration test ###
    ./gradlew clients:test --tests RequestResponseTest

### Running a particular test method within a unit/integration test ###
    ./gradlew core:test --tests kafka.api.ProducerFailureHandlingTest.testCannotSendToInternalTopic
    ./gradlew clients:test --tests org.apache.kafka.clients.MetadataTest.testMetadataUpdateWaitTime

### Running a particular unit/integration test with log4j output ###
Change the log4j setting in either `clients/src/test/resources/log4j.properties` or `core/src/test/resources/log4j.properties`

    ./gradlew clients:test --tests RequestResponseTest

### Specifying test retries ###
By default, each failed test is retried once up to a maximum of five retries per test run. Tests are retried at the end of the test task. Adjust these parameters in the following way:

    ./gradlew test -PmaxTestRetries=1 -PmaxTestRetryFailures=5
    
See [Test Retry Gradle Plugin](https://github.com/gradle/test-retry-gradle-plugin) for more details.

### Generating test coverage reports ###
Generate coverage reports for the whole project:

    ./gradlew reportCoverage -PenableTestCoverage=true -Dorg.gradle.parallel=false

Generate coverage for a single module, i.e.: 

    ./gradlew clients:reportCoverage -PenableTestCoverage=true -Dorg.gradle.parallel=false
    
### Building a binary release gzipped tar ball ###
    ./gradlew clean releaseTarGz

The release file can be found inside `./core/build/distributions/`.

### Building auto generated messages ###
Sometimes it is only necessary to rebuild the RPC auto-generated message data when switching between branches, as they could
fail due to code changes. You can just run:
 
    ./gradlew processMessages processTestMessages

### Running a Kafka broker in ZooKeeper mode

    ./bin/zookeeper-server-start.sh config/zookeeper.properties
    ./bin/kafka-server-start.sh config/server.properties

### Running a Kafka broker in KRaft (Kafka Raft metadata) mode

See [config/kraft/README.md](https://github.com/apache/kafka/blob/trunk/config/kraft/README.md).

### Cleaning the build ###
    ./gradlew clean

### Running a task with one of the Scala versions available (2.12.x or 2.13.x) ###
*Note that if building the jars with a version other than 2.13.x, you need to set the `SCALA_VERSION` variable or change it in `bin/kafka-run-class.sh` to run the quick start.*

You can pass either the major version (eg 2.12) or the full version (eg 2.12.7):

    ./gradlew -PscalaVersion=2.12 jar
    ./gradlew -PscalaVersion=2.12 test
    ./gradlew -PscalaVersion=2.12 releaseTarGz

### Running a task with all the scala versions enabled by default ###

Invoke the `gradlewAll` script followed by the task(s):

    ./gradlewAll test
    ./gradlewAll jar
    ./gradlewAll releaseTarGz

### Running a task for a specific project ###
This is for `core`, `examples` and `clients`

    ./gradlew core:jar
    ./gradlew core:test

Streams has multiple sub-projects, but you can run all the tests:

    ./gradlew :streams:testAll

### Listing all gradle tasks ###
    ./gradlew tasks

### Building IDE project ####
*Note that this is not strictly necessary (IntelliJ IDEA has good built-in support for Gradle projects, for example).*

    ./gradlew eclipse
    ./gradlew idea

The `eclipse` task has been configured to use `${project_dir}/build_eclipse` as Eclipse's build directory. Eclipse's default
build directory (`${project_dir}/bin`) clashes with Kafka's scripts directory and we don't use Gradle's build directory
to avoid known issues with this configuration.

### Publishing the jar for all projects to maven ###
The recommended command is:

    ./gradlew -Pversion=<RELEASE_VERSION_NUMBER> publish

For backwards compatibility, the following also works:

    ./gradlew -Pversion=<RELEASE_VERSION_NUMBER> uploadArchives

By default, this command will publish artifacts to a JFrog repository named "kafka" under an account specified by the `JFROG_USERNAME` environment variable; 
and the `JFROG_API_KEY` environment variable is used for the API key for that account.

If you want to publish for all supported Scala version, change `./gradlew` to `./gradlewAll`.

If you want to override this to use a different maven repository, you should create/update `${GRADLE_USER_HOME}/gradle.properties` (typically, `~/.gradle/gradle.properties`)
and assign the following variables

    mavenUrl=
    mavenUsername=
    mavenPassword=

Signing is disabled by default. If you need signing, please set the following variables in `gradle.properties` as well:

    signing.keyId=
    signing.password=
    signing.secretKeyRingFile=

### Publishing the streams quickstart archetype artifact to maven ###
For the Streams archetype project, one cannot use gradle to upload to maven; instead the `mvn deploy` command needs to be called at the quickstart folder:

    cd streams/quickstart
    mvn deploy

Please note for this to work you should create/update user maven settings (typically, `${USER_HOME}/.m2/settings.xml`) to assign the following variables

    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                           https://maven.apache.org/xsd/settings-1.0.0.xsd">
    ...                           
    <servers>
       ...
       <server>
          <id>apache.snapshots.https</id>
          <username>${maven_username}</username>
          <password>${maven_password}</password>
       </server>
       <server>
          <id>apache.releases.https</id>
          <username>${maven_username}</username>
          <password>${maven_password}</password>
        </server>
        ...
     </servers>
     ...


### Installing the jars to the local Maven repository ###
The recommended command is:

    ./gradlewAll publishToMavenLocal

For backwards compatibility, the following also works:

    ./gradlewAll install

### Building the test jar ###
    ./gradlew testJar

### Determining how transitive dependencies are added ###
    ./gradlew core:dependencies --configuration runtime

### Determining if any dependencies could be updated ###
    ./gradlew dependencyUpdates

### Running code quality checks ###
There are two code quality analysis tools that we regularly run, spotbugs and checkstyle.

#### Checkstyle ####
Checkstyle enforces a consistent coding style in Kafka.
You can run checkstyle using:

    ./gradlew checkstyleMain checkstyleTest

The checkstyle warnings will be found in `reports/checkstyle/reports/main.html` and `reports/checkstyle/reports/test.html` files in the
subproject build directories. They are also printed to the console. The build will fail if Checkstyle fails.

#### Spotbugs ####
Spotbugs uses static analysis to look for bugs in the code.
You can run spotbugs using:

    ./gradlew spotbugsMain spotbugsTest -x test

The spotbugs warnings will be found in `reports/spotbugs/main.html` and `reports/spotbugs/test.html` files in the subproject build
directories.  Use -PxmlSpotBugsReport=true to generate an XML report instead of an HTML one.

### JMH microbenchmarks ###
We use [JMH](https://openjdk.java.net/projects/code-tools/jmh/) to write microbenchmarks that produce reliable results in the JVM.
    
See [jmh-benchmarks/README.md](https://github.com/apache/kafka/blob/trunk/jmh-benchmarks/README.md) for details on how to run the microbenchmarks.

### Common build options ###

The following options should be set with a `-P` switch, for example `./gradlew -PmaxParallelForks=1 test`.

* `commitId`: sets the build commit ID as .git/HEAD might not be correct if there are local commits added for build purposes.
* `mavenUrl`: sets the URL of the maven deployment repository (`file://path/to/repo` can be used to point to a local repository).
* `maxParallelForks`: limits the maximum number of processes for each task.
* `ignoreFailures`: ignore test failures from junit
* `showStandardStreams`: shows standard out and standard error of the test JVM(s) on the console.
* `skipSigning`: skips signing of artifacts.
* `testLoggingEvents`: unit test events to be logged, separated by comma. For example `./gradlew -PtestLoggingEvents=started,passed,skipped,failed test`.
* `xmlSpotBugsReport`: enable XML reports for spotBugs. This also disables HTML reports as only one can be enabled at a time.
* `maxTestRetries`: the maximum number of retries for a failing test case.
* `maxTestRetryFailures`: maximum number of test failures before retrying is disabled for subsequent tests.
* `enableTestCoverage`: enables test coverage plugins and tasks, including bytecode enhancement of classes required to track said
coverage. Note that this introduces some overhead when running tests and hence why it's disabled by default (the overhead
varies, but 15-20% is a reasonable estimate).
* `scalaOptimizerMode`: configures the optimizing behavior of the scala compiler, the value should be one of `none`, `method`, `inline-kafka` or
`inline-scala` (the default is `inline-kafka`). `none` is the scala compiler default, which only eliminates unreachable code. `method` also
includes method-local optimizations. `inline-kafka` adds inlining of methods within the kafka packages. Finally, `inline-scala` also
includes inlining of methods within the scala library (which avoids lambda allocations for methods like `Option.exists`). `inline-scala` is
only safe if the Scala library version is the same at compile time and runtime. Since we cannot guarantee this for all cases (for example, users
may depend on the kafka jar for integration tests where they may include a scala library with a different version), we don't enable it by
default. See https://www.lightbend.com/blog/scala-inliner-optimizer for more details.

### Dependency Analysis ###

The gradle [dependency debugging documentation](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html) mentions using the `dependencies` or `dependencyInsight` tasks to debug dependencies for the root project or individual subprojects.

Alternatively, use the `allDeps` or `allDepInsight` tasks for recursively iterating through all subprojects:

    ./gradlew allDeps

    ./gradlew allDepInsight --configuration runtime --dependency com.fasterxml.jackson.core:jackson-databind

These take the same arguments as the builtin variants.

### Running system tests ###

See [tests/README.md](tests/README.md).

### Running in Vagrant ###

See [vagrant/README.md](vagrant/README.md).

### Contribution ###

Apache Kafka is interested in building the community; we would welcome any thoughts or [patches](https://issues.apache.org/jira/browse/KAFKA). You can reach us [on the Apache mailing lists](http://kafka.apache.org/contact.html).

To contribute follow the instructions here:
 * https://kafka.apache.org/contributing.html
