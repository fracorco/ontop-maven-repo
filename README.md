# Ontop GitHub-Hosted Maven Repository

This Git repository provides a versioned [Maven repository](https://maven.apache.org/guides/introduction/introduction-to-repositories.html) for publicly-available Maven artifacts (JARs) used in / related to [Ontop](https://github.com/ontop) projects and not available on [Maven central](https://repo.maven.apache.org/maven2/).

For third-party artifacts in this repository, we include POM tags `<URL>` and `<licenses>` that respectively link to an artifact web page providing further information and download instructions and specify the artifact license (using [SPDX](https://spdx.org/licenses/) identifiers if possible).

**Usage of third-party artifacts is subject to their respective license terms.** Most of them (e.g., JDBC driver) are only used for the automated testing of Ontop and are in no way part of distributed Ontop bundles (i.e., Ontop users will have to obtain them from their respective web sites, if needed).

**Usage of this repository outside Ontop projects is discouraged**. While publicly accessible (to allow anyone to compile Ontop projects), artifacts in this repository may be *changed* or *deleted* without notice according to Ontop projects needs, breaking third-party use.

## How to import artifacts from this repository

Add the following to the `pom.xml` file of your Maven project:
```xml
<repositories>
    <repository>
        <id>ontop-maven-repo</id>
        <url>https://raw.githubusercontent.com/ontop/ontop-maven-repo/master/repository/</url>
    </repository>
</repositories>
```

After that, artifacts can be imported as [regular Maven dependencies](https://maven.apache.org/pom.html#Dependencies). Browse [here](https://github.com/fracorco/third-party-jars/tree/master/repository) to see available artifacts and obtain their maven coordinates (and `pom.xml` files, if needed).

## How to deploy an artifact JAR file to this repository

**Requirements**.
You need write access to this Git repository and of course the JAR file to deploy. If available online, you should also retrieve the `pom.xml` file for the artifact, otherwise you will have to supply one yourself (see later).

**Create a pom.xml file (if needed)**.
If not available, e.g., because the JAR does not come from a Maven project, you may create a `pom.xml` file by filling this template:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <modelVersion>4.0.0</modelVersion>

    <groupId>GROUP_ID</groupId>
    <artifactId>ARTIFACT_ID</artifactId>
    <version>VERSION</version>
    
    <url>URL</url>

    <licenses>
        <license>
            <name>LICENSE_ID</name>
        </license>
    </licenses>
    
    <depedencies>
        <!-- any Maven dependency you want to list -->
    </dependencies>
    
</project>
```

Notes:
* placeholders `GROUP_ID`, `ARTIFACT_ID`, `VERSION` are mandatory and must be filled;
* if possible, placeholders `URL` and `LICENSE_ID` should be filled for third-party artifacts, referring to licensing information provided online or embedded in the JAR file itself (open the JAR file as a ZIP file and look for license files or metadata); this step is important to document artifact origin and license in both human and machine-readable ways;
* artifact dependencies are optional, but if present and listed here they help users importing this artifact, who will not have to manually list them in their `pom.xml` files, relying instead on Maven transitive dependency inclusion;
* while Maven can automatically generate a missing `pom.xml` file at deploy time (see `generatePom` option of [maven-deploy-plugin](https://maven.apache.org/plugins/maven-deploy-plugin/deploy-file-mojo.html)), the generated file will not include any URL or license information.

**Clone this repository (if not done before)**.
In a terminal, simply execute:
```shell
git clone git@github.com:ontop/ontop-maven-repo.git
cd ontop-maven-repo
```

**Deploy the JAR artifact locally**.
From the top-level directory of this repository (if elsewhere, replace `./repository` with the appropriate path), execute the following commands filling the `PATH_TO_JAR_FILE` and `PATH_TO_POM_FILE` placeholders:
```shell
mvn install:install-file \
    -DcreateChecksum=true \
    -DlocalRepositoryPath=./repository \
    -Dfile=PATH_TO_JAR_FILE \
    -DpomFile=PATH_TO_POM_FILE
```

**Commit and push the local change**.
Execute the following commands:
```shell
git add -A
git commit
git push
```
