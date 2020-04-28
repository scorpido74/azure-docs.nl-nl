---
title: Migreren van Java SDK naar maven
description: U moet de oudere Java-toepassingen die de Service Fabric Java SDK gebruikten, bijwerken om Service Fabric Java-afhankelijkheden op te halen van Maven. Als u de stappen in dit artikel hebt uitgevoerd, moeten de oudere Java-toepassingen zonder problemen kunnen worden gebouwd.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.author: rapatchi
ms.openlocfilehash: b5e126ebdf3b89470472391c59d378c7a6d39b86
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75609805"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Bestaande Java Service Fabric-toepassingen bijwerken voor het ophalen van Java-bibliotheken van Maven
We hebben onlangs de binaire bestanden van Java Service Fabric verplaatst van de Service Fabric Java SDK naar Maven-hosting. U kunt nu **mavencentral** gebruiken om de meest recente Service Fabric Java-afhankelijkheden op te halen. Deze Quick Start helpt u om uw bestaande Java-toepassingen bij te werken, zodat deze compatibel zijn met de op Maven gebaseerde build. Deze toepassingen werden eerder gemaakt voor gebruik met Service Fabric Java SDK, met behulp van een Yeoman-sjabloon of Eclipse.

## <a name="prerequisites"></a>Vereisten
1. U moet eerst de bestaande Java SDK verwijderen.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```
2. Installeer de meest recente Service Fabric-CLI door [deze stappen](service-fabric-cli.md) te volgen.

3. U kunt alleen Service Fabric-Java-toepassingen bouwen en hieraan werken als JDK 1.8 en Gradle zijn geïnstalleerd. Als dat nog is gebeurd, voert u de volgende opdrachten uit om JDK 1.8 (openjdk-8-jdk) en Gradle te installeren:

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```
4. Werk de scripts voor het installeren/verwijderen van uw toepassing bij, zodat ze de nieuwe Service Fabric-CLI gebruiken. Volg hiervoor [deze stappen](service-fabric-application-lifecycle-sfctl.md). U kunt als naslag verwijzen naar onze [voorbeelden](https://github.com/Azure-Samples/service-fabric-java-getting-started) om snel aan de slag te gaan.

>[!TIP]
> Na het verwijderen van de Service Fabric Java SDK, werkt Yeoman niet meer. Zorg dat u voldoet aan de vereisten die [hier](service-fabric-create-your-first-linux-application-with-java.md) worden vermeld om de Yeoman-sjabloongenerator van Service Fabric Java werkend te krijgen.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric-Java-bibliotheken op Maven
Service Fabric Java-bibliotheken worden gehost in Maven. U kunt de afhankelijkheden toevoegen in de ``pom.xml`` of ``build.gradle`` van uw projecten om voortaan de Service Fabric-Java-bibliotheken op **mavenCentral** te gebruiken.

### <a name="actors"></a>Actoren

Ondersteuning voor betrouwbare actoren in Service Fabric voor uw toepassing.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Services

Betrouwbare ondersteuning voor stateless services in Service Fabric voor uw toepassing.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Andere
#### <a name="transport"></a>Transport

Ondersteuning van transportlaag voor Service Fabric Java-toepassing. U hoeft deze afhankelijkheid niet expliciet toe te voegen aan uw Reliable Actor- of Service-toepassingen, tenzij u programmeert op de transportlaag.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-ondersteuning

Ondersteuning op systeemniveau voor Service Fabric, dat communiceert met native Service Fabric op het moment van uitvoering. U hoeft deze afhankelijkheid niet expliciet toe te voegen aan uw Reliable Actor- of Service-toepassingen. Dit wordt automatisch opgehaald van Maven wanneer u de andere bovenstaande afhankelijkheden opneemt.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Stateless service in Service Fabric migreren

Als u uw bestaande Service Fabric stateless Java-service wilt bouwen met behulp van Service Fabric-afhankelijkheden die zijn opgehaald van Maven, moet u het bestand ``build.gradle`` bijwerken in de service. Eerder zag dit bestand er zo uit:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
Om nu afhankelijkheden op te halen van Maven, moet het **bijgewerkte bestand ** ``build.gradle`` de volgende onderdelen bevatten:
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
Over het algemeen kunt u een goed beeld krijgen van een script voor het bouwen van een stateless Service Fabric Java-service door de voorbeelden te raadplegen die we hebben samengesteld om u snel op weg te helpen. Hier vindt u bijvoorbeeld de [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) voor het voorbeeld met EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Actor-service van Service Fabric migreren

Als u uw bestaande Service Fabric stateless Actor Java-toepassing wilt bouwen met behulp van Service Fabric-afhankelijkheden die zijn opgehaald van Maven, moet u het bestand ``build.gradle`` bijwerken in het interfacepakket en in het servicepakket. Als u een TestClient-pakket hebt, moet u de wijziging daar ook doorvoeren. In het geval van uw actor ``Myactor`` zijn op de volgende plaatsen aanpassingen nodig:
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Build-script voor het interfaceproject bijwerken

Eerder zag dit bestand er zo uit:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
Om nu afhankelijkheden op te halen van Maven, moet het **bijgewerkte bestand ** ``build.gradle`` de volgende onderdelen bevatten:
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Build-script voor het actorproject bijwerken

Eerder zag dit bestand er zo uit:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
Om nu afhankelijkheden op te halen van Maven, moet het **bijgewerkte bestand ** ``build.gradle`` de volgende onderdelen bevatten:
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Build-script voor het TestClient-project bijwerken

De wijzigingen hier zijn vergelijkbaar met de wijzigingen uit de vorige sectie, over het actor-project. Eerder werd een Gradle-script gebruikt dat er ongeveer als volgt uitziet:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
Om nu afhankelijkheden op te halen van Maven, moet het **bijgewerkte bestand ** ``build.gradle`` de volgende onderdelen bevatten:
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste Service Fabric Java-toepassing in Linux maken en implementeren met behulp van Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Uw eerste Service Fabric Java-toepassing in Linux maken en implementeren met behulp van de Service Fabric-invoegtoepassing voor Eclipse](service-fabric-get-started-eclipse.md)
* [Werken met Service Fabric-clusters via de Service Fabric-CLI](service-fabric-cli.md)
