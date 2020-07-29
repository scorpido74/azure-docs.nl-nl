---
title: Windows java-apps configureren
description: Meer informatie over het configureren van Java-Apps voor uitvoering op de Windows VM-exemplaren in Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
keywords: Azure app service, Web-app, Windows, OSS, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: d0850b2192df1392a245f27588ffcb8ddb337347
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319334"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Een Windows java-app configureren voor Azure App Service

Met Azure App Service kunnen Java-Ontwikkel aars hun Tomcat-webtoepassingen snel bouwen, implementeren en schalen op een volledig beheerde, op Windows gebaseerde service. Implementeer toepassingen met maven plugins vanaf de opdracht regel of in editors zoals IntelliJ, eclips of Visual Studio code.

Deze hand leiding bevat belang rijke concepten en instructies voor Java-Ontwikkel aars die in App Service worden gebruikt. Als u Azure App Service nog nooit hebt gebruikt, lees dan eerst de [Java-Snelstartgids](app-service-web-get-started-java.md) . Algemene vragen over het gebruik van App Service die niet specifiek zijn voor de Java-ontwikkeling, worden beantwoord in de [Veelgestelde vragen over app service Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Uw app implementeren

U kunt de [Azure web app-invoeg toepassing voor maven gebruiken voor](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) het implementeren van uw War-bestanden. Implementatie met populaire Ide's wordt ook ondersteund met [Azure-Toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/) of [Azure-Toolkit voor eclipse](/azure/developer/java/toolkit-for-eclipse).

Anders is uw implementatie methode afhankelijk van het type archief:

- Als u een WAR-bestand wilt implementeren in Tomcat, gebruikt u het `/api/wardeploy/` eind punt om het archief bestand te plaatsen. Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)voor meer informatie over deze API.
- Gebruik het `/api/zipdeploy/` eind punt van de kudu-site om jar-bestanden te implementeren in Java SE. Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)voor meer informatie over deze API.

Implementeer uw War niet met FTP. Het FTP-hulp programma is ontworpen voor het uploaden van opstart scripts, afhankelijkheden of andere runtime bestanden. Het is niet de beste keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Apps registreren en fouten opsporen

Prestatie rapporten, visualisaties van verkeer en de status checkups zijn beschikbaar voor elke app via de Azure Portal. Zie [Azure app service Diagnostics-overzicht](overview-diagnostics.md)voor meer informatie.

### <a name="use-flight-recorder"></a>Vlucht recorder gebruiken

Alle Java-Runtimes op App Service met behulp van de Azul-JVMs worden geleverd met de Zulu Flight-recorder. U kunt dit gebruiken om gebeurtenissen van het JVM-, systeem-en Java-niveau op te nemen om het gedrag te controleren en problemen in uw Java-toepassingen op te lossen.

Als u een getimede opname wilt maken, hebt u de PID (proces-ID) van de Java-toepassing nodig. Als u de PID wilt vinden, opent u een browser voor de SCM-site van uw web-app op https://<uw-site naam>. scm.azurewebsites.net/ProcessExplorer/. Op deze pagina worden de actieve processen in uw web-app weer gegeven. Zoek het proces met de naam ' Java ' in de tabel en kopieer de bijbehorende PID (proces-ID).

Open vervolgens de **console fout opsporing** in de bovenste werk balk van de SCM-site en voer de volgende opdracht uit. Vervang door `<pid>` de proces-id die u eerder hebt gekopieerd. Met deze opdracht wordt een registratie van 30 seconden voor het maken van een profilering van uw Java-toepassing gestart en wordt een bestand `timed_recording_example.jfr` met de naam in de `D:\home` map gegenereerd.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Raadpleeg de [Jcmd-opdracht verwijzing](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)voor meer informatie.

#### <a name="analyze-jfr-files"></a>`.jfr`Bestanden analyseren

Gebruik [FTPS](deploy-ftp.md) om uw JFR-bestand te downloaden naar uw lokale computer. Down load en Installeer [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)om het JFR-bestand te analyseren. Zie de [Azul-documentatie](https://docs.azul.com/zmc/) en de [installatie-instructies](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)voor instructies voor Zulu-missie beheer.

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Zie [Stream-Logboeken in Cloud shell](troubleshoot-diagnostic-logs.md#in-cloud-shell)voor meer informatie.

### <a name="app-logging"></a>App-logboek registratie

Schakel [toepassings logboeken](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) in via de Azure portal of [Azure cli](/cli/azure/webapp/log#az-webapp-log-config) om app service te configureren voor het schrijven van de standaard console-uitvoer van de toepassing en de standaard console fout stromen naar het lokale bestands systeem of Azure Blob Storage. Logboek registratie naar het lokale App Service bestandssysteem exemplaar is uitgeschakeld 12 uur nadat het is geconfigureerd. Als u meer retentie nodig hebt, configureert u de toepassing voor het schrijven van uitvoer naar een BLOB storage-container. Uw Java-en tomcat-app-Logboeken kunt u vinden in de */logfiles/Application/* -map.

Als uw toepassing gebruikmaakt van [logback](https://logback.qos.ch/) of [Log4j](https://logging.apache.org/log4j) voor tracering, kunt u deze traceringen door sturen naar Azure-toepassing Insights met behulp van de configuratie-instructies voor logboek registratie in [Java-traceer Logboeken in Application Insights verkennen](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Aanpassing en afstemming

Azure App Service biedt geen ondersteuning voor het afstemmen en aanpassen van het kader via de Azure Portal en CLI. Raadpleeg de volgende artikelen voor een niet-Java-specifieke Web-app-configuratie:

- [App-instellingen configureren](configure-common.md#configure-app-settings)
- [Een aangepast domein instellen](app-service-web-tutorial-custom-domain.md)
- [TLS-bindingen configureren](configure-ssl-bindings.md)
- [Een CDN toevoegen](../cdn/cdn-add-to-web-app.md)
- [De kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java runtime-opties instellen

Als u toegewezen geheugen of andere JVM runtime opties wilt instellen, maakt u een [app-instelling](configure-common.md#configure-app-settings) `JAVA_OPTS` met de naam met de opties. App Service geeft deze instelling als een omgevings variabele door aan de Java-runtime wanneer deze wordt gestart.

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling `JAVA_OPTS` met de naam die de aanvullende instellingen bevat, zoals `-Xms512m -Xmx1204m` .

Als u de app-instelling wilt configureren vanuit de Maven-invoeg toepassing, voegt u instellingen/waarde-tags toe in de sectie Azure-invoeg toepassing. In het volgende voor beeld wordt een specifieke minimale en maximale grootte voor Java-heap ingesteld:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Ontwikkel aars die één toepassing met één implementatie sleuf in hun App Service-abonnement uitvoeren, kunnen de volgende opties gebruiken:

- B1 en S1-instanties:`-Xms1024m -Xmx1024m`
- B2-en S2-instanties:`-Xms3072m -Xmx3072m`
- B3-en S3-instanties:`-Xms6144m -Xmx6144m`

Bij het afstemmen van de instellingen voor de heap van toepassingen, raadpleegt u de details van het App Service plan en houdt u rekening met meerdere toepassingen en implementatie sleuven om de optimale toewijzing van het geheugen te vinden.

### <a name="turn-on-web-sockets"></a>Websockets inschakelen

Schakel ondersteuning voor websockets in de Azure Portal in de **Toepassings instellingen** voor de toepassing. U moet de toepassing opnieuw opstarten om de instelling van kracht te laten worden.

Schakel de ondersteuning voor websockets in met behulp van de Azure CLI met de volgende opdracht:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Start de toepassing vervolgens opnieuw:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Standaard teken codering instellen

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling met de naam `JAVA_OPTS` met waarde `-Dfile.encoding=UTF-8` .

U kunt de app-instelling ook configureren met behulp van de App Service maven-invoeg toepassing. Voeg de instellingen naam en waarde tags toe aan de configuratie van de invoeg toepassing:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Als u de prestaties van Tomcat-toepassingen wilt verbeteren, kunt u uw JSP-bestanden compileren voordat u implementeert in App Service. U kunt de [maven-invoeg toepassing](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) van Apache strop gebruiken of dit [Ant build-bestand](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)gebruiken.

## <a name="secure-applications"></a>Veilige toepassingen

Java-toepassingen die worden uitgevoerd in App Service hebben dezelfde [aanbevolen beveiligings procedures](/azure/security/security-paas-applications-using-app-services) als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (eenvoudige verificatie)

Stel app-verificatie in het Azure Portal in met de optie **verificatie en autorisatie** . Van daaruit kunt u verificatie inschakelen met behulp van Azure Active Directory of sociale aanmeldingen, zoals Facebook, Google of GitHub. Azure Portal configuratie werkt alleen bij het configureren van één verificatie provider. Zie [uw app service-app configureren voor het gebruik van Azure Active Directory login](configure-authentication-provider-aad.md) en de verwante artikelen voor andere id-providers voor meer informatie. Als u meerdere aanmeld providers wilt inschakelen, volgt u de instructies in het artikel [customize app service-verificatie](app-service-authentication-how-to.md) .

#### <a name="tomcat"></a>Tomcat

Uw Tomcat-toepassing kan rechtstreeks vanuit de servlet toegang krijgen tot de claims van de gebruiker door het Principal-object naar een kaart object te casten. Het kaart object wijst elk claim type toe aan een verzameling van de claims voor dat type. In de onderstaande code `request` is een exemplaar van `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kunt u het `Map` object voor een specifieke claim controleren. Het volgende code fragment loopt bijvoorbeeld door alle claim typen en drukt de inhoud van elke verzameling af.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Als u gebruikers wilt afmelden, gebruikt u het `/.auth/ext/logout` pad. Raadpleeg de documentatie over [app service verificatie en autorisatie gebruik](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)om andere acties uit te voeren. Er is ook officiële documentatie over de Tomcat [HttpServletRequest-interface](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) en de bijbehorende methoden. De volgende servlet-methoden worden ook gehydrateerd op basis van uw App Service configuratie:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Als u deze functie wilt uitschakelen, maakt u een toepassings instelling `WEBSITE_AUTH_SKIP_PRINCIPAL` met de naam met een waarde van `1` . Als u alle Servlet-filters die zijn toegevoegd door App Service wilt uitschakelen, maakt u een instelling `WEBSITE_SKIP_FILTERS` met de naam met een waarde van `1` .

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in het gedeelte [een aangepaste DNS-naam beveiligen met een TLS-binding in azure app service](configure-ssl-bindings.md) om een bestaand TLS/SSL-certificaat te uploaden en te binden aan de domein naam van uw toepassing. Uw toepassing staat standaard nog HTTP-verbindingen toe. Volg de specifieke stappen in de zelf studie om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>Verwijzingen naar de sleutel kluis gebruiken

[Azure](../key-vault/general/overview.md) -sleutel kluis biedt gecentraliseerd geheim beheer met toegangs beleid en controle geschiedenis. U kunt geheimen (zoals wacht woorden of verbindings reeksen) opslaan in de sleutel kluis en toegang krijgen tot deze geheimen in uw toepassing via omgevings variabelen.

Volg eerst de instructies voor het [verlenen van toegang tot Key Vault van uw app](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) en het [maken van een verwijzing naar een sleutel kluis in een toepassings instelling](app-service-key-vault-references.md#reference-syntax). U kunt controleren of de verwijzing naar het geheim wordt omgezet door de omgevings variabele af te drukken terwijl u extern toegang hebt tot de App Service Terminal.

Als u deze geheimen wilt injecteren in het configuratie bestand voor de lente of het Tomcat, gebruikt u de syntaxis voor het injecteren van omgevings variabelen ( `${MY_ENV_VAR}` ). Raadpleeg deze documentatie over [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)voor lente configuratie bestanden.


## <a name="configure-apm-platforms"></a>APM-platforms configureren

In deze sectie wordt beschreven hoe u Java-toepassingen die zijn geïmplementeerd op Azure App Service op Linux verbindt met de NewRelic-en AppDynamics-platformen voor het controleren van de prestaties van toepassingen (APM).

### <a name="configure-new-relic"></a>Nieuwe Relic configureren

1. Een nieuw Relic-account maken op [NewRelic.com](https://newrelic.com/signup)
2. Down load de Java-Agent van NewRelic. deze heeft een bestands naam die vergelijkbaar is met *newrelic-java-x.x.x.zip*.
3. Kopieer uw licentie sleutel, u hebt deze later nodig om de agent te configureren.
4. Gebruik de [kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) om een nieuwe directory */Home/site/wwwroot/apm*te maken.
5. Upload de uitgepakte nieuwe Relic Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/newrelic*zijn.
6. Wijzig het YAML-bestand op */Home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de tijdelijke aanduiding voor de licentie waarde door uw eigen licentie code.
7. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.
    - Als uw app **Java SE**gebruikt, maakt u een omgevings variabele `JAVA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Als u **Tomcat**gebruikt, maakt u een omgevings variabele `CATALINA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>AppDynamics configureren

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. De Java-agent downloaden van de AppDynamics-website, de bestands naam is vergelijkbaar met *AppServerAgent-x.x.x.xxxxx.zip*
3. Gebruik de [kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) om een nieuwe directory */Home/site/wwwroot/apm*te maken.
4. Upload de Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/appdynamics*zijn.
5. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.
    - Als u **Java SE**gebruikt, maakt u een omgevings variabele `JAVA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waar de naam van `<app-name>` uw app service is.
    - Als u **Tomcat**gebruikt, maakt u een omgevings variabele `CATALINA_OPTS` met de naam met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waar de naam van `<app-name>` uw app service is.

>  Als u al een omgevings variabele voor `JAVA_OPTS` of hebt `CATALINA_OPTS` , voegt u de `-javaagent:/...` optie toe aan het einde van de huidige waarde.

## <a name="data-sources"></a>Gegevensbronnen

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle database verbindingen. U moet tijdelijke aanduidingen vullen met de naam van de stuurprogrammanaam en het JAR-bestand van uw gekozen data base. Gegeven is een tabel met klasse-namen en down loads van Stuur Programma's voor algemene data bases.

| Database   | Naam stuur programma klasse                             | JDBC-stuur programma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (Selecteer ' platform onafhankelijk ') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Als u Tomcat wilt configureren voor het gebruik van de Java Data Base Connectivity (JDBC) of de Java Persistence API (JPA), moet u eerst de `CATALINA_OPTS` omgevings variabele aanpassen die in Tomcat wordt gelezen tijdens het opstarten. Stel deze waarden in via een app-instelling in de [maven-invoeg toepassing van app service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevings variabelen in op de pagina **configuratie**  >  **Toepassings instellingen** in het Azure Portal.

Bepaal vervolgens of de gegevens bron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevens bronnen op toepassings niveau

1. Maak een *context.xml* -bestand in de *META-INF/-* map van uw project. Maak de *META-INF/-* map als deze niet bestaat.

2. Voeg in *context.xml*een `Context` element toe om de gegevens bron te koppelen aan een JNDI-adres. Vervang de `driverClassName` tijdelijke aanduiding door de naam van de klasse van uw stuur programma uit de bovenstaande tabel.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Werk de *web.xml* van uw toepassing bij om de gegevens bron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Configuratie volt ooien

Ten slotte gaan we de potten van het stuur programma in het Tomcat CLASSPATH plaatsen en de App Service opnieuw opstarten. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-ClassLoader door ze te plaatsen in de map */Home/tomcat/lib* . (Maak deze map als deze nog niet bestaat.) Als u deze bestanden wilt uploaden naar uw App Service-exemplaar, voert u de volgende stappen uit:

1. Installeer de webapp-extensie in de [Cloud shell](https://shell.azure.com):

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Voer de volgende CLI-opdracht uit om een SSH-tunnel van uw lokale systeem te maken om te App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Maak verbinding met de lokale tunnel poort met uw SFTP-client en upload de bestanden naar de map */Home/tomcat/lib* .

U kunt ook een FTP-client gebruiken om het JDBC-stuur programma te uploaden. Volg deze [instructies voor het ophalen van uw FTP-referenties](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Tomcat configureren

Als u de Tomcat `server.xml` of andere configuratie bestanden wilt bewerken, moet u eerst een notitie maken van de primaire versie van Tomcat in de portal.

1. Zoek de Tomcat Home Directory voor uw versie door de opdracht uit te voeren `env` . Zoek naar de omgevings variabele die begint met `AZURE_TOMCAT` en overeenkomt met uw primaire versie. Bijvoorbeeld: `AZURE_TOMCAT85_HOME` verwijst naar de Tomcat-map voor Tomcat 8,5.
1. Wanneer u de Tomcat-basismap voor uw versie hebt geïdentificeerd, kopieert u de configuratiemap naar `D:\home` . Als `AZURE_TOMCAT85_HOME` er bijvoorbeeld een waarde van `D:\Program Files (x86)\apache-tomcat-8.5.37` is, is het nieuwe pad van de gekopieerde map `D:\home\apache-tomcat-8.5.37` .

Start ten slotte App Service opnieuw. Uw implementaties moeten naar `D:\home\site\wwwroot\webapps` net zo eerder gaan.

## <a name="configure-java-se"></a>Java SE configureren

Bij het uitvoeren van een. JAR-toepassing op Java SE op Windows `server.port` wordt door gegeven als opdracht regel optie wanneer uw toepassing wordt gestart. U kunt de HTTP-poort hand matig oplossen op basis van de omgevings variabele `HTTP_PLATFORM_PORT` . De waarde van deze omgevings variabele is de HTTP-poort waarover uw toepassing luistert. 

## <a name="java-runtime-statement-of-support"></a>Java runtime-instructie van ondersteuning

### <a name="jdk-versions-and-maintenance"></a>JDK-versies en onderhoud

De ondersteunde Java Development Kit (JDK) van Azure wordt [Zulu](https://www.azul.com/downloads/azure-only/zulu/) geboden via [Azul-systemen](https://www.azul.com/).

Primaire versie-updates worden verschaft via nieuwe runtime-opties in Azure App Service voor Windows. Klanten werken bij naar deze nieuwere versies van Java door hun App Service-implementatie te configureren en verantwoordelijk te zijn voor het testen en ervoor te zorgen dat de belang rijke update aan hun behoeften voldoet.

Ondersteunde JDKs worden automatisch op een driemaandelijkse patch uitgevoerd in januari, april, juli en oktober van elk jaar. Raadpleeg [Dit ondersteunings document](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)voor meer informatie over java in Azure.

### <a name="security-updates"></a>Beveiligingsupdates

Patches en oplossingen voor belang rijke beveiligings problemen worden vrijgegeven zodra deze beschikbaar worden gesteld via Azul-systemen. Een ' belang rijk ' beveiligingslek is gedefinieerd met een basis Score van 9,0 of hoger in het [gemeen schappelijke beveiligings risico van het NIST-systeem versie 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 is [sinds 30 September 2018 geëindigd (EOL)](https://tomcat.apache.org/tomcat-80-eol.html). Terwijl de runtime nog steeds avialable op Azure App Service, zullen Azure geen beveiligings updates Toep assen op Tomcat 8,0. Migreer, indien mogelijk, uw toepassingen naar Tomcat 8,5 of 9,0. Zowel Tomcat 8,5 als 9,0 zijn beschikbaar op Azure App Service. Raadpleeg de [officiële Tomcat-site](https://tomcat.apache.org/whichversion.html) voor meer informatie. 

### <a name="deprecation-and-retirement"></a>Afschaffing en buiten gebruik stellen

Als een ondersteunde Java-runtime wordt ingetrokken, krijgen Azure-ontwikkel aars die de betrokken runtime gebruiken, een afschaffing die ten minste zes maanden duurt voordat de runtime wordt afgetrokken.

### <a name="local-development"></a>Lokale ontwikkeling

Ontwikkel aars kunnen de productie-editie van Azul Zulu Enter prise JDK downloaden voor lokale ontwikkeling vanaf [de download site van Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Ontwikkelings ondersteuning

Product ondersteuning voor de door [Azure ondersteunde Azul ZULU jdk](https://www.azul.com/downloads/azure-only/zulu/) is beschikbaar via micro soft bij het ontwikkelen voor azure of [Azure stack](https://azure.microsoft.com/overview/azure-stack/) met een [gekwalificeerd ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Runtime-ondersteuning

Ontwikkel aars kunnen [een probleem](/azure/azure-portal/supportability/how-to-create-azure-support-request) met de Azul Zulu JDKs via Azure-ondersteuning openen als ze een [ondersteunings plan](https://azure.microsoft.com/support/plans/)hebben.

## <a name="next-steps"></a>Volgende stappen

Dit onderwerp bevat de Java runtime-instructie voor de ondersteuning van Azure App Service in Windows.

- Zie [app Service Overview](overview.md)voor meer informatie over het hosten van webtoepassingen met Azure app service.
- Zie [Azure voor Java-ontwikkelaars centrum](https://docs.microsoft.com/java/azure/?view=azure-java-stable)voor meer informatie over java in azure Development.
