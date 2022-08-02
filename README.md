# OSGi enRoute Tutorial - Updated

This repository contains an updated version of the OSGi enRoute Microservices tutorial.  

[OSGi enRoute Microservices Tutorial](https://enroute.osgi.org/tutorial/030-tutorial_microservice.html)  
[OSGi enRoute Persistence with JPA Tutorial](https://enroute.osgi.org/tutorial/032-tutorial_microservice-jpa.html)

The following parts are updated/changed:
* Bndtools 6.3.1
* OSGi R8
* Java 11
* Hibernate 2.x
* Removed JSON-P to avoid confusion

## Step 1:  
Created the projects according to the OSGi enRoute Tutorials.

## Step 2:
After following the original tutorials, the first action to perform is the update of the Bndtools version and the repositories.
Otherwise the build might not work because Bndtools can not be resolved:

* __microservice/pom.xml__
    * Update `bnd.version` to 6.3.1
    * Update the `repositories` and `pluginRepositories`  
    (for details look at the sources)

## Step 3:  
Update the parent pom.xml __microservice/pom.xml__:

* Replace `maven.compiler.source` and `maven.compiler.target` with `java.version=11`
* Modified the `dependencyManagement` section
    * Removed all `org.osgi.enroute` dependencies
    * Added dependencies to all current OSGi R8 specifications and implementations
    * Added dependencies for Java 11 XML processing
* Modified the `pluginManagment` section
    * Added the `maven-compiler-plugin`
    * Updated the version of the `maven-jar-plugin`

## Step 4:
In every project pom.xml the `org.osgi.enroute` dependencies need to be removed and replaced by corresponding OSGi R8 dependencies.  

### `org.osgi.enroute:osgi-api`
Replace

    <dependency>
      <groupId>org.osgi.enroute</groupId>
      <artifactId>osgi-api</artifactId>
      <type>pom</type>
    </dependency>

with

    <dependency>
      <groupId>org.osgi</groupId>
      <artifactId>osgi.core</artifactId>
    </dependency>
    <dependency>
      <groupId>org.osgi</groupId>
      <artifactId>osgi.annotation</artifactId>
    </dependency>

add the following dependency if your bundle contains declarative services:

    <dependency>
      <groupId>org.osgi</groupId>
      <artifactId>org.osgi.service.component.annotations</artifactId>
    </dependency>

Add additional dependencies if needed (e.g. `org.osgi:org.osgi.service.configurator`, `org.osgi:org.osgi.service.http.whiteboard`, `org.osgi:org.osgi.service.jaxrs` , etc.). They were previously collected in `org.osgi:osgi.cmpn`).

### `org.osgi.enroute:test-bundles`
Replace

    <dependency>
      <groupId>org.osgi.enroute</groupId>
      <artifactId>test-bundles</artifactId>
      <type>pom</type>
    </dependency>

with 

    <dependency>
      <groupId>org.apache.servicemix.bundles</groupId>
      <artifactId>org.apache.servicemix.bundles.junit</artifactId>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-core</artifactId>
    </dependency>

### `org.osgi.enroute:enterprise-api`
Replace

    <dependency>
      <groupId>org.osgi.enroute</groupId>
      <artifactId>enterprise-api</artifactId>
      <type>pom</type>
    </dependency>

with the following dependent on what is needed:

    <dependency>
      <groupId>org.apache.felix</groupId>
      <artifactId>org.apache.felix.http.servlet-api</artifactId>
    </dependency>
    <dependency>
      <groupId>org.apache.aries.spec</groupId>
      <artifactId>org.apache.aries.javax.jax.rs-api</artifactId>
    </dependency>
    <dependency>
      <groupId>org.apache.aries.jpa.javax.persistence</groupId>
      <artifactId>javax.persistence_2.1</artifactId>
    </dependency>

## Step 5
Add additional dependencies that are not automatically resolved:

### dao-impl

Add the OSGi Transaction Control bundles

    <dependency>
      <groupId>org.apache.aries.tx-control</groupId>
      <artifactId>tx-control-service-xa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.apache.aries.tx-control</groupId>
      <artifactId>tx-control-provider-jdbc-xa</artifactId>
    </dependency>

### dao-impl-jpa

Add the OSGi Transaction Control bundles

    <dependency>
      <groupId>org.apache.aries.tx-control</groupId>
      <artifactId>tx-control-service-xa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.apache.aries.tx-control</groupId>
      <artifactId>tx-control-provider-jpa-xa</artifactId>
    </dependency>

### rest-service

Add OSGi Http Whiteboard and OSGi JAX-RS Whiteboard

    <dependency>
      <groupId>org.osgi</groupId>
      <artifactId>org.osgi.service.http.whiteboard</artifactId>
    </dependency>
    <dependency>
      <groupId>org.osgi</groupId>
      <artifactId>org.osgi.service.jaxrs</artifactId>
    </dependency>

For simplification we drop the Apache Johnzon JSON-P implementation.  
* Remove the `org.apache.johnzon:johnzon-core` dependency
* Delete the class `JsonpConvertingPlugin`

__Note:__  
The JSON conversion will be done by including `org.apache.aries.jax.rs:org.apache.aries.jax.rs.jackson` to the runtime. 

### rest-app

* Add OSGi Converter

      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.service.configurator</artifactId>
      </dependency>

* Remove `org.osgi.enroute:impl-index` and `org.osgi.enroute:debug-bundles`  
* Remove the `org.apache.johnzon:johnzon-core` dependency
* Update `com.h2database:h2` to version 2.1.214

* Add the following OSGi implementations as dependencies

      <!--  The OSGi framework RI is Equinox  -->
      <dependency>
        <groupId>org.eclipse.platform</groupId>
        <artifactId>org.eclipse.osgi</artifactId>
      </dependency>
      <!--  Declarative Services  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.scr</artifactId>
      </dependency>
      <!--  Configuration Admin  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.configadmin</artifactId>
      </dependency>
      <!--  Metatype  -->
      <dependency>
        <groupId>org.eclipse.platform</groupId>
        <artifactId>org.eclipse.equinox.metatype</artifactId>
      </dependency>
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.service.metatype</artifactId>
      </dependency>
      <!--  Event Admin  -->
      <dependency>
        <groupId>org.eclipse.platform</groupId>
        <artifactId>org.eclipse.equinox.event</artifactId>
      </dependency>
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.service.event</artifactId>
      </dependency>
      <!--  Log Stream Service  -->
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.service.log</artifactId>
      </dependency>
      <dependency>
        <groupId>org.eclipse.platform</groupId>
        <artifactId>org.eclipse.equinox.log.stream</artifactId>
      </dependency>
      <!--  Http Whiteboard  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.http.jetty</artifactId>
      </dependency>
      <!--  Http Servlet 3.1 API with contract  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.http.servlet-api</artifactId>
      </dependency>
      <!--  JAX-RS Whiteboard  -->
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.service.jaxrs</artifactId>
      </dependency>
      <dependency>
        <groupId>org.apache.aries.jax.rs</groupId>
        <artifactId>org.apache.aries.jax.rs.whiteboard</artifactId>
      </dependency>
      <!--  JAX-RS 2.1 API with contract  -->
      <dependency>
        <groupId>org.apache.aries.spec</groupId>
        <artifactId>org.apache.aries.javax.jax.rs-api</artifactId>
      </dependency>
      <!--  JAX-RS Jackson  -->
      <dependency>
        <groupId>org.apache.aries.jax.rs</groupId>
        <artifactId>org.apache.aries.jax.rs.jackson</artifactId>
      </dependency>
      <!--  OSGi Function  -->
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.util.function</artifactId>
      </dependency>
      <!--  OSGi Promise  -->
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.util.promise</artifactId>
      </dependency>
      <!--  OSGi PushStream  -->
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.util.pushstream</artifactId>
      </dependency>
      <!--  OSGi Converter  -->
      <dependency>
        <groupId>org.osgi</groupId>
        <artifactId>org.osgi.util.converter</artifactId>
      </dependency>
      <!--  OSGi Configurator  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.configurator</artifactId>
      </dependency>

      <!--  Several implementations need to log using SLF4J  -->
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
      </dependency>
      <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
      </dependency>
      <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-core</artifactId>
      </dependency>

      <!-- Java 11 XML -->
      <dependency>
        <groupId>com.sun.activation</groupId>
        <artifactId>jakarta.activation</artifactId>
      </dependency>
      <dependency>
        <groupId>com.sun.xml.bind</groupId>
        <artifactId>jaxb-osgi</artifactId>
      </dependency>
      <dependency>
        <groupId>jakarta.xml.bind</groupId>
        <artifactId>jakarta.xml.bind-api</artifactId>
      </dependency>
      <dependency>
        <groupId>jakarta.xml.ws</groupId>
        <artifactId>jakarta.xml.ws-api</artifactId>
      </dependency>

      <dependency>
        <groupId>org.apache.geronimo.specs</groupId>
        <artifactId>geronimo-jaxrs_2.1_spec</artifactId>
      </dependency>

      <!--  The Web Console  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.webconsole</artifactId>
      </dependency>
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.webconsole.plugins.ds</artifactId>
      </dependency>
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.inventory</artifactId>
      </dependency>

      <!--  The Gogo Shell  -->
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.gogo.shell</artifactId>
      </dependency>
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.gogo.runtime</artifactId>
      </dependency>
      <dependency>
        <groupId>org.apache.felix</groupId>
        <artifactId>org.apache.felix.gogo.command</artifactId>
      </dependency>

* Update `rest-app.bndrun`

      -runrequires: \
        osgi.identity;filter:='(osgi.identity=org.osgi.enroute.examples.microservice.rest-service)',\
        osgi.identity;filter:='(osgi.identity=org.osgi.enroute.examples.microservice.rest-app)',\
        osgi.identity;filter:='(osgi.identity=org.apache.aries.jax.rs.jackson)',\
        osgi.identity;filter:='(osgi.identity=com.sun.xml.bind.jaxb-osgi)',\
        osgi.identity;filter:='(osgi.identity=com.h2database)'
      -runee: JavaSE-11


### rest-app-jpa

* Update the pom.xml the same way as rest-app
* Additionally add/update the following dependencies:  

      <dependency>
        <groupId>org.javassist</groupId>
        <artifactId>javassist</artifactId>
        <version>3.29.0-GA</version>
      </dependency>
      <dependency>
        <groupId>org.apache.aries.jpa</groupId>
        <artifactId>org.apache.aries.jpa.container</artifactId>
      </dependency>
      <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-osgi</artifactId>
        <version>5.2.17.Final</version>
        <!-- newer versions don't work -->
        <!--<version>5.3.27.Final</version>-->
        <!--<version>5.4.33.Final</version>-->
        <!--<version>5.5.9.Final</version>-->
        <!--<version>5.6.10.Final</version>-->
      </dependency>
      <dependency>
        <groupId>org.apache.servicemix.bundles</groupId>
        <artifactId>org.apache.servicemix.bundles.antlr</artifactId>
        <version>2.7.7_5</version>
      </dependency>
      <dependency>
        <groupId>org.apache.servicemix.bundles</groupId>
        <artifactId>org.apache.servicemix.bundles.dom4j</artifactId>
        <version>2.1.3_1</version>
      </dependency>

* Update `rest-app-jpa.bndrun`

      -runrequires: \
        osgi.identity;filter:='(osgi.identity=org.osgi.enroute.examples.microservice.rest-service)',\
        osgi.identity;filter:='(osgi.identity=org.osgi.enroute.examples.microservice.rest-app-jpa)',\
        osgi.identity;filter:='(osgi.identity=org.apache.aries.jax.rs.jackson)',\
        osgi.identity;filter:='(osgi.identity=com.sun.xml.bind.jaxb-osgi)',\
        osgi.identity;filter:='(osgi.identity=com.h2database)'
      -runee: JavaSE-11

      -runsystempackages: \
          javax.xml.stream;version=1.0.0,\
          javax.xml.stream.events;version=1.0.0,\
          javax.xml.stream.util;version=1.0.0
        
    * also remove the `runpath` entry

* Update `src/main/resources/OSGI-INF/configurator/configuration.json`  
As we updated to Hibernate 2.x, `;MODE=LEGACY` needs to be added to the JDBC URL

      {
          // Global Settings
          ":configurator:resource-version" : 1,
          ":configurator:symbolic-name" : "org.osgi.enroute.examples.microservice.jpa.config",
          ":configurator:version" : "0.0.1.SNAPSHOT",
      
      
          // Configure a JPA resource provider
          "org.apache.aries.tx.control.jpa.xa~microservice": {
                "name": "microservice.database",
                "osgi.jdbc.driver.class": "org.h2.Driver",
                "url": "jdbc:h2:./data/database;MODE=LEGACY",
                "osgi.unit.name": "microservice-dao" },
      
          // Target the Dao impls at the provider we configured
          "org.osgi.enroute.examples.microservice.dao.impl.jpa.PersonDaoImpl": {
                "provider.target": "(name=microservice.database)" },
          "org.osgi.enroute.examples.microservice.dao.impl.jpa.AddressDaoImpl": {
                "provider.target": "(name=microservice.database)" }
      }

__Note:__ I was not able to get Hibernate > 5.2.17 to work. And according to [this post](https://discourse.hibernate.org/t/hibernate-jpa-and-osgi/5643) it seems that Hibernate with OSGi is not further supported.
    