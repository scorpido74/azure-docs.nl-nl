---
title: JAR-afhankelijkheden beheren - Azure HDInsight
description: In dit artikel worden best practices besproken voor het beheren van Java Archive (JAR) afhankelijkheden voor HDInsight-toepassingen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135731"
---
# <a name="jar-dependency-management-best-practices"></a>Best practices voor jar-afhankelijkheidsbeheer

Componenten die zijn geïnstalleerd op HDInsight-clusters hebben afhankelijkheden van bibliotheken van derden. Meestal wordt een specifieke versie van gemeenschappelijke modules zoals Guava verwezen door deze ingebouwde componenten. Wanneer u een toepassing met zijn afhankelijkheden indient, kan deze een conflict veroorzaken tussen verschillende versies van dezelfde module. Als de componentversie waarnaar u eerst verwijst in het klassenpad, kunnen ingebouwde componenten uitzonderingen maken vanwege versie-incompatibiliteit. Als ingebouwde componenten echter eerst hun afhankelijkheden naar het classpath `NoSuchMethod`injecteren, kan uw toepassing fouten zoals .

Als u conflict met de versie wilt voorkomen, u overwegen afhankelijkheden van uw toepassing te arceren.

## <a name="what-does-package-shading-mean"></a>Wat betekent pakketarcering?
Arcering biedt een manier om afhankelijkheden op te nemen en de naam te wijzigen. Het verplaatst de klassen en herschrijft beïnvloede bytecode en bronnen om een privékopie van uw afhankelijkheden te maken.

## <a name="how-to-shade-a-package"></a>Hoe schaduw je een pakket?

### <a name="use-uber-jar"></a>Gebruik uber-jar
Uber-jar is een enkel potje bestand dat zowel de toepassing pot en de afhankelijkheden bevat. De afhankelijkheden in Uber-jar zijn standaard niet in de schaduw. In sommige gevallen kan dit een conflict in de versie introduceren als andere onderdelen of toepassingen verwijzen naar een andere versie van die bibliotheken. Om dit te voorkomen, u een Uber-Jar-bestand bouwen met een aantal (of alle) afhankelijkheden in de schaduw.

### <a name="shade-package-using-maven"></a>Schaduwpakket met Maven
Maven kan applicaties bouwen die zowel in Java als in Scala zijn geschreven. Maven-shade-plugin kan u helpen om gemakkelijk een schaduwrijke uber-pot te maken.

Het onderstaande voorbeeld `pom.xml` toont een bestand dat is bijgewerkt om een pakket te schaduw met behulp van maven-shade-plugin.  De XML-sectie `<relocation>…</relocation>` verplaatst `com.google.guava` klassen `com.google.shaded.guava` van pakket naar pakket door de bijbehorende JAR-bestandsvermeldingen te verplaatsen en de getroffen bytecode opnieuw te schrijven.

Na `pom.xml`het veranderen, `mvn package` u uitvoeren om de schaduwrijke uber-pot te bouwen.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Schaduwpakket met SBT
SBT is ook een build tool voor Scala en Java. SBT heeft geen schaduw plugin zoals maven-shade-plugin. U kunt `build.sbt` het bestand wijzigen in schaduwpakketten. 

U bijvoorbeeld `com.google.guava`de opdracht hieronder toevoegen `build.sbt` aan het bestand:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Dan `sbt clean` u `sbt assembly` uitvoeren en om de schaduwrijke pot bestand te bouwen. 

## <a name="next-steps"></a>Volgende stappen

* [HdInsight IntelliJ-hulpprogramma's gebruiken](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Maak een Scala Maven-toepassing voor Spark in IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
