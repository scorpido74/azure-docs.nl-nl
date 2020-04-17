---
title: Windows Java-apps configureren
description: Meer informatie over het configureren van Java-apps om te worden uitgevoerd op de Windows VM-exemplaren in Azure App Service. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
keywords: azure app service, web app, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 179a2120714460c955d1fd3e345ebcd963ae564d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453554"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Een Windows Java-app configureren voor Azure App Service

Azure App Service stelt Java-ontwikkelaars in staat om hun Tomcat-webtoepassingen snel te bouwen, te implementeren en te schalen op een volledig beheerde Windows-service. Implementeer toepassingen met Maven-plug-ins vanaf de opdrachtregel of in editors zoals IntelliJ, Eclipse of Visual Studio Code.

Deze handleiding biedt belangrijke concepten en instructies voor Java-ontwikkelaars die gebruik maken van app service. Als u Azure App Service nog nooit hebt gebruikt, moet u eerst de [Java-quickstart](app-service-web-get-started-java.md) lezen. Algemene vragen over het gebruik van App Service die niet specifiek zijn voor de Java-ontwikkeling worden beantwoord in de [Veelgestelde vragen over App Service Windows.](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Uw app implementeren

U [Azure Web App Plugin voor Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) gebruiken om uw .war-bestanden te implementeren. Implementatie met populaire IdEs wordt ook ondersteund met [Azure Toolkit voor IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) of [Azure Toolkit voor Eclipse.](/java/azure/eclipse/azure-toolkit-for-eclipse)

Anders is uw implementatiemethode afhankelijk van uw archieftype:

- Als u .war-bestanden wilt implementeren `/api/wardeploy/` in Tomcat, gebruikt u het eindpunt om uw archiefbestand te posten. Voor meer informatie over deze API, zie [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Als u .jar-bestanden wilt implementeren `/api/zipdeploy/` op Java SE, gebruikt u het eindpunt van de Kudu-site. Voor meer informatie over deze API, zie [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Implementeer uw .war niet met FTP. Het FTP-hulpprogramma is ontworpen om opstartscripts, afhankelijkheden of andere runtime-bestanden te uploaden. Het is niet de optimale keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Apps voor logboekregistratie en foutopsporing

Prestatierapporten, verkeersvisualisaties en statuscontroles zijn beschikbaar voor elke app via de Azure-portal. Zie overzicht van [Azure App Service-diagnoses](overview-diagnostics.md)voor meer informatie.

### <a name="use-flight-recorder"></a>Flight Recorder gebruiken

Alle Java-runtimes op App Service met behulp van de Azul JVMs worden geleverd met de Zulu Flight Recorder. U dit gebruiken om gebeurtenissen op JVM-, systeem- en Java-niveau op te nemen om het gedrag te controleren en problemen in uw Java-toepassingen op te lossen.

Om een getimede opname te maken heb je de PID (Process ID) van de Java-applicatie nodig. Als u de PID wilt vinden, opent u een browser naar de SCM-site van uw web-app op https://<uw-site-naam>.scm.azurewebsites.net/ProcessExplorer/. Deze pagina toont de lopende processen in uw web-app. Zoek het proces met de naam "java" in de tabel en kopieer de bijbehorende PID (Process ID).

Open vervolgens de **foutopsporingsconsole** op de bovenste werkbalk van de SCM-site en voer de volgende opdracht uit. Vervang `<pid>` door de proces-ID die u eerder hebt gekopieerd. Met deze opdracht wordt een profileropname van 30 seconden `timed_recording_example.jfr` van `D:\home` uw Java-toepassing gestart en wordt een bestand gegenereerd met de naam in de map.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Zie voor meer informatie de [Jcmd Command Reference.](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)

#### <a name="analyze-jfr-files"></a>Bestanden `.jfr` analyseren

Gebruik [FTPS](deploy-ftp.md) om uw JFR-bestand naar uw lokale machine te downloaden. Als u het JFR-bestand wilt analyseren, downloadt en installeert [u Zulu Mission Control.](https://www.azul.com/products/zulu-mission-control/) Zie voor instructies over Zulu Mission Control de [Azul-documentatie](https://docs.azul.com/zmc/) en de [installatie-instructies.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Zie [Logboeken streamen in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell)voor meer informatie.

### <a name="app-logging"></a>App-logboekregistratie

Schakel [toepassingslogboekregistratie](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via de Azure-portal of [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) in om App Service te configureren om de standaardconsoleuitvoer en standaardconsolefoutstreams van uw toepassing naar het lokale bestandssysteem of Azure Blob Storage te schrijven. Logboekregistratie bij het lokale app-bestandssysteem wordt 12 uur nadat deze is geconfigureerd uitgeschakeld. Als u langer moet worden bewaard, configureert u de toepassing om uitvoer naar een Blob-opslagcontainer te schrijven. Uw Java- en Tomcat-applogboeken zijn te vinden in de */LogFiles/Application/directory.*

Als uw toepassing [Logboekterug-](https://logback.qos.ch/) of [Log4j-tracering](https://logging.apache.org/log4j) gebruikt, u deze traceringen ter beoordeling doorsturen naar Azure Application Insights met behulp van de configuratie-instructies voor logboeken in [Trace-logboeken verkennen van Java in Application Insights.](/azure/application-insights/app-insights-java-trace-logs)


## <a name="customization-and-tuning"></a>Aanpassen en afstemmen

Azure App Service ondersteunt out-of-the-box tuning en aanpassing via de Azure-portal en CLI. Bekijk de volgende artikelen voor niet-Java-specifieke web-app configuratie:

- [App-instellingen configureren](configure-common.md#configure-app-settings)
- [Een aangepast domein instellen](app-service-web-tutorial-custom-domain.md)
- [TLS-bindingen configureren](configure-ssl-bindings.md)
- [Een CDN toevoegen](../cdn/cdn-add-to-web-app.md)
- [De Kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Opties voor Java-runtime instellen

Als u toegewezen geheugen of andere GOM-runtime-opties wilt instellen, maakt u een [app-instelling](configure-common.md#configure-app-settings) met de naam `JAVA_OPTS` opties. App Service geeft deze instelling door als een omgevingsvariabele aan de Java-runtime wanneer deze wordt gestart.

Maak in de Azure-portal onder **Toepassingsinstellingen** voor de `JAVA_OPTS` web-app een nieuwe `-Xms512m -Xmx1204m`app-instelling met de naam met de extra instellingen, zoals .

Als u de app-instelling wilt configureren vanuit de Plug-in Maven, voegt u instellings-/waardetags toe in de sectie Azure-plug-in. In het volgende voorbeeld wordt een specifieke minimum- en maximale Java-heapgrootte ingesteld:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Ontwikkelaars die één toepassing uitvoeren met één implementatiesleuf in hun App Service-abonnement, kunnen de volgende opties gebruiken:

- B1- en S1-exemplaren:`-Xms1024m -Xmx1024m`
- B2- en S2-exemplaren:`-Xms3072m -Xmx3072m`
- B3- en S3-exemplaren:`-Xms6144m -Xmx6144m`

Controleer bij het afstemmen van de instellingen voor toepassingsheap de details van uw App Service-abonnement en houdt rekening met meerdere toepassingen en moet de optimale toewijzing van geheugen worden gevonden.

### <a name="turn-on-web-sockets"></a>Websockets inschakelen

Schakel ondersteuning in voor websockets in de Azure-portal in de **toepassingsinstellingen** voor de toepassing. U moet de toepassing opnieuw starten om de instelling van kracht te laten worden.

Schakel websocketondersteuning in met de volgende opdracht Azure CLI:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Start vervolgens de toepassing opnieuw:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Standaardtekencodering instellen

Maak in de Azure-portal onder **Toepassingsinstellingen** voor de `JAVA_OPTS` web-app een nieuwe app-instelling met de naam waarde. `-Dfile.encoding=UTF-8`

U ook de app-instelling configureren met de Plug-in App Service Maven. Voeg de instellingsnaam en waardelabels toe aan de configuratie van de plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Om de prestaties van Tomcat-toepassingen te verbeteren, u uw JSP-bestanden compileren voordat u deze implementeert naar App Service. U de [Maven-plug-in](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) van Apache Sling gebruiken of dit [Ant-buildbestand gebruiken.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Beveiligde toepassingen

Java-toepassingen die worden uitgevoerd in App Service hebben dezelfde set [van beveiligingsbest practices](/azure/security/security-paas-applications-using-app-services) als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (Easy Auth)

App-verificatie instellen in de Azure-portal met de optie **Verificatie en autorisatie.** Vanaf daar u verificatie inschakelen met Azure Active Directory of sociale aanmeldingen zoals Facebook, Google of GitHub. Azure-portalconfiguratie werkt alleen bij het configureren van één verificatieprovider. Zie Uw [App Service-app configureren voor het gebruik van Azure Active Directory-aanmelding](configure-authentication-provider-aad.md) en de bijbehorende artikelen voor andere identiteitsproviders voor meer informatie. Als u meerdere aanmeldingsproviders moet inschakelen, volgt u de instructies in het verificatieartikel [appservice aanpassen.](app-service-authentication-how-to.md)

#### <a name="tomcat"></a>Tomcat

Uw Tomcat-toepassing heeft rechtstreeks vanuit de servlet toegang tot de claims van de gebruiker door het hoofdobject naar een mapobject te gieten. Het object Kaart brengt elk claimtype in kaart aan een verzameling van de claims voor dat type. In de onderstaande code `request` `HttpServletRequest`is een instantie van .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu u `Map` het object inspecteren op een specifieke claim. Het volgende codefragment doorloopt bijvoorbeeld alle claimtypen en drukt de inhoud van elke verzameling af.

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

Als u gebruikers wilt `/.auth/ext/logout` afmelden, gebruikt u het pad. Zie de documentatie over [verificatie- en autorisatiegebruik van app-service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)om andere acties uit te voeren. Er is ook officiële documentatie over de Tomcat [HttpServletRequest interface](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) en de methoden. De volgende servlet-methoden worden ook gehydrateerd op basis van uw App Service-configuratie:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Als u deze functie wilt `WEBSITE_AUTH_SKIP_PRINCIPAL` uitschakelen, maakt `1`u een toepassingsinstelling met de naam . Als u alle servletfilters wilt uitschakelen die `WEBSITE_SKIP_FILTERS` door App `1`Service zijn toegevoegd, maakt u een instelling met de naam .

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in [de Secure een aangepaste DNS-naam met een TLS-binding in Azure App Service](configure-ssl-bindings.md) om een bestaand TLS/SSL-certificaat te uploaden en te binden aan de domeinnaam van uw toepassing. Standaard staat uw toepassing nog steeds toe dat HTTP-verbindingen de specifieke stappen in de zelfstudie volgen om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>KeyVault-referenties gebruiken

[Azure KeyVault](../key-vault/general/overview.md) biedt gecentraliseerd geheim beheer met toegangsbeleid en controlegeschiedenis. U geheimen (zoals wachtwoorden of verbindingstekenreeksen) opslaan in KeyVault en toegang krijgen tot deze geheimen in uw toepassing via omgevingsvariabelen.

Volg eerst de instructies voor [het verlenen van toegang tot Key Vault aan uw app](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) en maak een [KeyVault-verwijzing naar uw geheim in een toepassingsinstelling.](app-service-key-vault-references.md#reference-syntax) U valideren dat de verwijzing naar het geheim wordt opgelost door de omgevingsvariabele af te drukken terwijl u op afstand toegang krijgt tot de App Service-terminal.

Als u deze geheimen wilt injecteren in uw configuratiebestand voor`${MY_ENV_VAR}`de lente of tomcat, gebruikt u de syntaxis van de omgevingsvariabele injectie ( ). Zie deze documentatie over [geexternaliseerde configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)voor configuratiebestanden in de lente.


## <a name="configure-apm-platforms"></a>APM-platforms configureren

In dit gedeelte ziet u hoe java-toepassingen die zijn geïmplementeerd op Azure App Service op Linux kunnen worden aangesloten met de APM-platforms (NewRelic en AppDynamics application performance monitoring).

### <a name="configure-new-relic"></a>Nieuwe relikwie configureren

1. Maak een New Relic-account aan bij [NewRelic.com](https://newrelic.com/signup)
2. Download de Java-agent van NewRelic, het zal een bestandsnaam vergelijkbaar met *newrelic-java-x.x.x.zip*.
3. Kopieer uw licentiesleutel, u hebt deze nodig om de agent later te configureren.
4. Gebruik de [Kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) om een nieuwe map */home/site/wwwroot/apm*te maken.
5. Upload de uitgepakte New Relic Java agent bestanden in een directory onder */home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */ home / site / wwwroot / apm / newrelic*.
6. Wijzig het YAML-bestand op */home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de licentiewaarde van de tijdelijke aanduiding door uw eigen licentiesleutel.
7. Blader in de Azure-portal naar uw toepassing in App-service en maak een nieuwe toepassingsinstelling.
    - Als uw app **Java SE**gebruikt, `JAVA_OPTS` maakt u `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`een omgevingsvariabele met de waarde .
    - Als u **Tomcat**gebruikt, maakt u `CATALINA_OPTS` een `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`omgevingsvariabele met de waarde .

### <a name="configure-appdynamics"></a>AppDynamics configureren

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Download de Java-agent van de AppDynamics-website, de bestandsnaam zal vergelijkbaar zijn met *AppServerAgent-x.x.x.xxxxx.zip*
3. Gebruik de [Kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) om een nieuwe map */home/site/wwwroot/apm*te maken.
4. Upload de Java-agentbestanden in een map onder */home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */home/site/wwwroot/apm/appdynamics*zijn.
5. Blader in de Azure-portal naar uw toepassing in App-service en maak een nieuwe toepassingsinstelling.
    - Als u **Java SE**gebruikt, maakt `JAVA_OPTS` u een `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` omgevingsvariabele met de naam van de waarde waar uw App Service-naam is.
    - Als u **Tomcat**gebruikt, maakt u `CATALINA_OPTS` een `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` omgevingsvariabele met de waarde waar `<app-name>` uw appservicenaam is.

>  Als u al een `JAVA_OPTS` omgevingsvariabele hebt voor of `CATALINA_OPTS`de `-javaagent:/...` optie aan het einde van de huidige waarde toeteweet.

## <a name="data-sources"></a>Gegevensbronnen

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle databaseverbindingen. U moet tijdelijke aanduidingen invullen met de naam van de stuurprogrammaklasse en het JAR-bestand van de door u gekozen database. Voorzien is een tabel met klassenamen en stuurprogrammadownloads voor veelvoorkomende databases.

| Database   | Naam rijklasse                             | JDBC-stuurprogramma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (Selecteer 'Platform onafhankelijk') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Als u Tomcat wilt configureren om Java Database Connectivity (JDBC) of `CATALINA_OPTS` de Java Persistence API (JPA) te gebruiken, past u eerst de omgevingsvariabele aan die door Tomcat bij het opstarten wordt ingelezen. Stel deze waarden in via een app-instelling in de [App Service Maven-plug-in:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevingsvariabelen in op de pagina > **Configuratietoepassingsinstellingen** in de Azure-portal. **Configuration**

Bepaal vervolgens of de gegevensbron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die op de Tomcat-servlet worden uitgevoerd.

#### <a name="application-level-data-sources"></a>Gegevensbronnen op toepassingsniveau

1. Maak een *context.xml-bestand* in de *DIRECTORY META-INF/van* uw project. Maak de *META-INF/directory* als deze niet bestaat.

2. Voeg in *context.xml*een `Context` element toe om de gegevensbron te koppelen aan een JNDI-adres. Vervang `driverClassName` de tijdelijke aanduiding door de klassenaam van uw chauffeur in de bovenstaande tabel.

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

3. Werk het *web.xml* van uw toepassing bij om de gegevensbron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Configuratie afronden

Ten slotte plaatsen we de bestuurder-JARs in het Tomcat-klassepad en starten we uw App Service opnieuw. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-classloader door ze in de *map /home/tomcat/lib te* plaatsen. (Maak deze map als deze nog niet bestaat.) Voer de volgende stappen uit om deze bestanden naar uw app-service-instantie te uploaden:

1. Installeer in de [Cloud Shell](https://shell.azure.com)de webapp-extensie:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Voer de volgende OPDRACHT CLI uit om een SSH-tunnel te maken van uw lokale systeem naar appservice:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Maak verbinding met de lokale tunnelpoort met uw SFTP-client en upload de bestanden naar de *map /home/tomcat/lib.*

U ook een FTP-client gebruiken om het JDBC-stuurprogramma te uploaden. Volg deze [instructies voor het verkrijgen van uw FTP-referenties.](deploy-configure-credentials.md)

## <a name="configuring-tomcat"></a>Tomcat configureren

Als u de configuratiebestanden `server.xml` van Tomcat of andere configuratie wilt bewerken, neemt u eerst een notitie van uw Hoofdversie van Tomcat in de portal.

1. Zoek de Tomcat-thuismap voor `env` uw versie door de opdracht uit te voeren. Zoek naar de omgevingsvariabele `AZURE_TOMCAT`die begint met en overeenkomt met uw belangrijkste versie. Wijst bijvoorbeeld `AZURE_TOMCAT85_HOME` naar de Tomcat-map voor Tomcat 8.5.
1. Zodra u de Tomcat-thuismap voor uw versie hebt `D:\home`geïdentificeerd, kopieert u de configuratiemap naar . Als u `AZURE_TOMCAT85_HOME` bijvoorbeeld een `D:\Program Files (x86)\apache-tomcat-8.5.37`waarde van , het nieuwe pad `D:\home\apache-tomcat-8.5.37`van de gekopieerde map zou zijn .

Start ten slotte App Service opnieuw. Uw implementaties moeten `D:\home\site\wwwroot\webapps` gaan naar net als voorheen.

## <a name="configure-java-se"></a>Java SE configureren

Bij het uitvoeren van een . JAR applicatie op Java SE op Windows, `server.port` wordt doorgegeven als een command line optie als uw toepassing begint. U de HTTP-poort handmatig oplossen `HTTP_PLATFORM_PORT`vanuit de omgevingsvariabele. De waarde van deze omgevingsvariabele is de HTTP-poort waarop uw toepassing moet luisteren. 

## <a name="java-runtime-statement-of-support"></a>Java runtime verklaring van ondersteuning

### <a name="jdk-versions-and-maintenance"></a>JDK-versies en onderhoud

Azure's ondersteunde Java Development Kit (JDK) is [Zulu](https://www.azul.com/downloads/azure-only/zulu/) die wordt geleverd via [Azul Systems.](https://www.azul.com/)

Belangrijke versie-updates worden geleverd via nieuwe runtime-opties in Azure App Service voor Windows. Klanten updaten naar deze nieuwere versies van Java door hun App Service-implementatie te configureren en zijn verantwoordelijk voor het testen en ervoor zorgen dat de grote update aan hun behoeften voldoet.

Ondersteunde JDK's worden automatisch op kwartaalbasis gepatcht in januari, april, juli en oktober van elk jaar. Zie [dit ondersteuningsdocument voor](https://docs.microsoft.com/azure/java/jdk/)meer informatie over Java op Azure.

### <a name="security-updates"></a>Beveiligingsupdates

Patches en fixes voor grote beveiligingsproblemen zullen worden vrijgegeven zodra ze beschikbaar zijn van Azul Systems. Een "grote" kwetsbaarheid wordt gedefinieerd door een basisscore van 9.0 of hoger op het [NIST Common Vulnerability Scoring System, versie 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 heeft [bereikt End of Life (EOL) met 19 september 2018](https://tomcat.apache.org/tomcat-80-eol.html). Hoewel de runtime nog steeds avialable is op Azure App Service, past Azure geen beveiligingsupdates toe op Tomcat 8.0. Migreer indien mogelijk uw toepassingen naar Tomcat 8.5 of 9.0. Zowel Tomcat 8.5 als 9.0 zijn beschikbaar op Azure App Service. Zie de [officiële Tomcat site](https://tomcat.apache.org/whichversion.html) voor meer informatie. 

### <a name="deprecation-and-retirement"></a>Afschaffing en pensionering

Als een ondersteunde Java-runtime wordt uitgeschakeld, krijgen Azure-ontwikkelaars die de betreffende runtime gebruiken, ten minste zes maanden voordat de runtime wordt uitgeschakeld, een afschrijvingsbericht.

### <a name="local-development"></a>Lokale ontwikkeling

Ontwikkelaars kunnen de Production Edition van Azul Zulu Enterprise JDK downloaden voor lokale ontwikkeling van de downloadsite van [Azul.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Ontwikkelingsondersteuning

Productondersteuning voor de [door Azure ondersteunde Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) is beschikbaar via Microsoft bij het ontwikkelen voor Azure of [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) met een [gekwalificeerd Azure-ondersteuningsplan.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Ondersteuning voor Runtime

Ontwikkelaars kunnen [een probleem openen](/azure/azure-portal/supportability/how-to-create-azure-support-request) met de Azul Zulu JDKs via Azure Support als ze een gekwalificeerd [ondersteuningsplan](https://azure.microsoft.com/support/plans/)hebben.

## <a name="next-steps"></a>Volgende stappen

Dit onderwerp biedt de Java Runtime-ondersteuningsverklaring voor Azure App Service op Windows.

- Zie [App Service-overzicht](overview.md)voor meer informatie over het hosten van webtoepassingen met Azure App Service.
- Zie Azure for Java [Dev Center voor](https://docs.microsoft.com/java/azure/?view=azure-java-stable)informatie over Java op Azure-ontwikkeling .
