---
title: Java-toepassingen in elke omgeving controleren - Azure Monitor Application Insights
description: Toepassingsprestatiebewaking voor Java-toepassingen die worden uitgevoerd op elke omgeving met Java standalone agent zonder de app te instrumenteren. Gedistribueerde traceer- en toepassingskaart.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641873"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>JvM args Java standalone agent configureren voor Azure Monitor Application Insights



## <a name="azure-environments"></a>Azure-omgevingen

[App-services configureren](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Voeg het JVM-arg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ergens eerder toe, `-jar <myapp.jar>`bijvoorbeeld:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args geplaatst `-jar <myapp.jar>` na worden doorgegeven aan de app als programma args.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat geïnstalleerd `apt-get` via of`yum`

Als u Tomcat `apt-get` `yum`via of , dan `/etc/tomcat8/tomcat8.conf`moet je een bestand hebben .  Voeg deze regel toe aan het einde van dat bestand:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat geïnstalleerd via download en unzip

Als u Tomcat geïnstalleerd via [https://tomcat.apache.org](https://tomcat.apache.org)download en unzip `<tomcat>/bin/catalina.sh`uit, dan moet je een bestand hebben .  Maak een nieuw bestand in `<tomcat>/bin/setenv.sh` dezelfde map met de volgende inhoud:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Als het `<tomcat>/bin/setenv.sh` bestand al bestaat, wijzigt `CATALINA_OPTS`u dat bestand en voegt u toe aan `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Tomcat uitvoeren vanaf de opdrachtregel

Zoek het `<tomcat>/bin/catalina.bat`bestand .  Maak een nieuw bestand in `<tomcat>/bin/setenv.bat` dezelfde map met de volgende inhoud:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Offertes zijn niet nodig, maar als je ze wilt opnemen, is de juiste plaatsing:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Als het `<tomcat>/bin/setenv.bat` bestand al bestaat, wijzigt `CATALINA_OPTS`u dat bestand en voegt u toe aan `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` .

### <a name="running-tomcat-as-a-windows-service"></a>Tomcat uitvoeren als Windows-service

Zoek het `<tomcat>/bin/tomcat8w.exe`bestand .  Voer dat uitvoerbaar `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` uit `Java Options` en `Java` voeg toe aan het tabblad.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Zelfstandige server

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan `JAVA_OPTS` de bestaande omgevingsvariabele in het bestand `JBOSS_HOME/bin/standalone.conf` (Linux) of `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Domeinserver

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan `jvm-options` het `JBOSS_HOME/domain/configuration/host.xml`bestaande in :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Als u meerdere beheerde servers op één host `applicationinsights.agent.id` uitvoert, `system-properties` moet `server`u voor elk:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

De `applicationinsights.agent.id` opgegeven waarde moet uniek zijn. Het wordt gebruikt om een subdirectory te maken onder de map applicationinsights, omdat elk JVM-proces zijn eigen lokale applicationinsights config en local applicationinsights log file nodig heeft. Als het bestand wordt gemeld `applicationinsights.properties` aan de centrale verzamelaar, wordt het `applicationinsights.agent.id` bestand ook gedeeld `agent.id` door de meerdere beheerde servers en is het opgegeven nodig om de instelling in dat gedeelde bestand te overschrijven. `applicationinsights.agent.rollup.id`kan op dezelfde manier worden `system-properties` opgegeven in de `agent.rollup.id` server als u de instelling per beheerde server moet overschrijven.


## <a name="jetty-9"></a>Steiger 9

Deze regels toevoegen aan`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aan `jvm-options` het `glassfish/domains/domain1/config/domain.xml`bestaande in :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSfeer 8

Open Management Console gaat naar **servers > WebSphere-toepassingsservers > Application servers,** kies de juiste toepassingsservers en klik op: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Voeg in "Generieke ARGUMENTEN van JVM" het volgende toe:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Sla daarna de toepassingsserver op en start deze opnieuw op.


## <a name="openliberty-18"></a>OpenLiberty 18

Maak een `jvm.options` nieuw bestand in de `<openliberty>/usr/servers/defaultServer`servermap (bijvoorbeeld) en voeg deze regel toe:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
