---
title: Linux Java-apps configureren
description: Meer informatie over het configureren van een vooraf gebouwde Java-container voor uw app. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
keywords: azure app service, web app, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 970701606811cbd61a9bfebe39ff82cdc91d5693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245834"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Een Linux Java-app voor Azure App Service configureren

Azure App Service op Linux laat Java-ontwikkelaars hun Tomcat- of Java Standard Edition-webtoepassingen snel bouwen, implementeren en schalen op een volledig beheerde Linux-gebaseerde service. Implementeer toepassingen met Maven-plug-ins vanaf de opdrachtregel of in editors zoals IntelliJ, Eclipse of Visual Studio Code.

Deze handleiding biedt belangrijke concepten en instructies voor Java-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, volgt u de [Snelstart Java.](quickstart-java.md)

## <a name="deploying-your-app"></a>Uw app implementeren

U [Maven Plugin voor Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) gebruiken om zowel .jar- als .war-bestanden te implementeren. Implementatie met populaire IdEs wordt ook ondersteund met [Azure Toolkit voor IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) of [Azure Toolkit voor Eclipse.](/java/azure/eclipse/azure-toolkit-for-eclipse)

Anders is uw implementatiemethode afhankelijk van uw archieftype:

- Als u .war-bestanden wilt implementeren `/api/wardeploy/` in Tomcat, gebruikt u het eindpunt om uw archiefbestand te posten. Voor meer informatie over deze API, zie [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Als u .jar-bestanden wilt implementeren op `/api/zipdeploy/` de Java SE-afbeeldingen, gebruikt u het eindpunt van de Kudu-site. Voor meer informatie over deze API, zie [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Implementeer je .war of .jar niet met FTP. Het FTP-hulpprogramma is ontworpen om opstartscripts, afhankelijkheden of andere runtime-bestanden te uploaden. Het is niet de optimale keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Apps voor logboekregistratie en foutopsporing

Prestatierapporten, verkeersvisualisaties en statuscontroles zijn beschikbaar voor elke app via de Azure-portal. Zie overzicht van [Azure App Service-diagnoses](../overview-diagnostics.md)voor meer informatie.

### <a name="ssh-console-access"></a>SSH-consoletoegang

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Zie [Logboeken streamen in Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell)voor meer informatie.

### <a name="app-logging"></a>App-logboekregistratie

Schakel [toepassingslogboekregistratie](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) via de Azure-portal of [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) in om App Service te configureren om de standaardconsoleuitvoer en standaardconsolefoutstreams van uw toepassing naar het lokale bestandssysteem of Azure Blob Storage te schrijven. Logboekregistratie bij het lokale app-bestandssysteem wordt 12 uur nadat deze is geconfigureerd uitgeschakeld. Als u langer moet worden bewaard, configureert u de toepassing om uitvoer naar een Blob-opslagcontainer te schrijven. Uw Java- en Tomcat-applogboeken zijn te vinden in de */home/LogFiles/Application/directory.*

Als uw toepassing [Logboekterug-](https://logback.qos.ch/) of [Log4j-tracering](https://logging.apache.org/log4j) gebruikt, u deze traceringen ter beoordeling doorsturen naar Azure Application Insights met behulp van de configuratie-instructies voor logboeken in [Trace-logboeken verkennen van Java in Application Insights.](/azure/application-insights/app-insights-java-trace-logs)

### <a name="troubleshooting-tools"></a>Hulpprogramma's voor probleemoplossing

De ingebouwde Java-afbeeldingen zijn gebaseerd op het [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) besturingssysteem. Gebruik `apk` de package manager om hulpprogramma's of opdrachten voor probleemoplossing te installeren.

### <a name="flight-recorder"></a>Vluchtrecorder

Alle Linux Java-afbeeldingen op App Service hebben Zulu Flight Recorder geïnstalleerd, zodat u gemakkelijk verbinding maken met de JVM en een profiler-opname starten of een heapdump genereren.

#### <a name="timed-recording"></a>Getimede opname

Om aan de slag te gaan, `jcmd` ssh in uw App Service en voer de opdracht om een lijst van alle Java-processen actief te zien. Naast jcmd zelf, zou u uw toepassing Java moeten zien die met een aantal procesIDENTITEITSKAART (pid) wordt uitgevoerd.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Voer de opdracht hieronder uit om een opname van 30 seconden van de JVM te starten. Hiermee wordt de JVM geprofileerd en wordt een JFR-bestand met de naam *jfr_example.jfr in* de home directory gemaakt. (Vervang 116 door de pid van uw Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Tijdens het interval van 30 seconden u `jcmd 116 JFR.check`de opname valideren door te draaien. Dit toont alle opnames voor het gegeven Java-proces.

#### <a name="continuous-recording"></a>Continue opname

U Zulu Flight Recorder gebruiken om uw Java-applicatie continu te profileren met minimale impact op de prestaties van runtime[(bron).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Voer hiervoor de volgende opdracht Azure CLI uit om een app-instelling met de naam JAVA_OPTS met de benodigde configuratie te maken. De inhoud van de JAVA_OPTS-app-instelling wordt doorgegeven aan de `java` opdracht wanneer uw app wordt gestart.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Zodra de opname is gestart, u de huidige `JFR.dump` opnamegegevens op elk gewenst moment dumpen met behulp van de opdracht.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Zie voor meer informatie de [Jcmd Command Reference.](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)

### <a name="analyzing-recordings"></a>Opnamen analyseren

Gebruik [FTPS](../deploy-ftp.md) om uw JFR-bestand naar uw lokale machine te downloaden. Als u het JFR-bestand wilt analyseren, downloadt en installeert [u Zulu Mission Control.](https://www.azul.com/products/zulu-mission-control/) Zie voor instructies over Zulu Mission Control de [Azul-documentatie](https://docs.azul.com/zmc/) en de [installatie-instructies.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

## <a name="customization-and-tuning"></a>Aanpassen en afstemmen

Azure App Service voor Linux ondersteunt out-of-the-box tuning en aanpassing via de Azure-portal en CLI. Bekijk de volgende artikelen voor niet-Java-specifieke web-app configuratie:

- [App-instellingen configureren](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Een aangepast domein instellen](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [SSL-bindingen configureren](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Een CDN toevoegen](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [De Kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Opties voor Java-runtime instellen

Als u toegewezen geheugen of andere GOM-runtime-opties wilt instellen in zowel de `JAVA_OPTS` Tomcat- als Java SE-omgevingen, maakt u een [app-instelling](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) met de naam met de opties. App Service Linux geeft deze instelling als een omgevingsvariabele aan de Java-runtime wanneer deze wordt gestart.

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

Als u een JAR-toepassing implementeert, moet deze *app.jar* worden genoemd, zodat de ingebouwde afbeelding uw app correct kan identificeren. (De Maven plugin doet deze naamsverandering automatisch.) Als u de naam van uw JAR niet wilt wijzigen in *app.jar,* u een shellscript uploaden met de opdracht om uw JAR uit te voeren. Plak het volledige pad naar dit script in het tekstvak [Opstartbestand](app-service-linux-faq.md#built-in-images) in de sectie Configuratie van de portal. Het opstartscript kan niet worden uitgevoerd vanuit de map waarin deze is geplaatst. Gebruik daarom altijd absolute paden om te verwijzen naar bestanden in het opstartscript (bijvoorbeeld: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Time-out van het opstarten aanpassen

Als uw Java-toepassing bijzonder groot is, moet u de opstarttermijn verhogen. Maak hiervoor een toepassingsinstelling `WEBSITES_CONTAINER_START_TIME_LIMIT` en stel deze in op het aantal seconden dat app-service moet wachten voordat de timing uitgaat. De maximale `1800` waarde is seconden.

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Om de prestaties van Tomcat-toepassingen te verbeteren, u uw JSP-bestanden compileren voordat u deze implementeert naar App Service. U de [Maven-plug-in](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) van Apache Sling gebruiken of dit [Ant-buildbestand gebruiken.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Beveiligde toepassingen

Java-toepassingen die worden uitgevoerd in App Service voor Linux hebben dezelfde set [van beveiligingsbest practices](/azure/security/security-paas-applications-using-app-services) als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (Easy Auth)

App-verificatie instellen in de Azure-portal met de optie **Verificatie en autorisatie.** Vanaf daar u verificatie inschakelen met Azure Active Directory of sociale aanmeldingen zoals Facebook, Google of GitHub. Azure-portalconfiguratie werkt alleen bij het configureren van één verificatieprovider. Zie Uw [App Service-app configureren voor het gebruik van Azure Active Directory-aanmelding](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) en de bijbehorende artikelen voor andere identiteitsproviders voor meer informatie. Als u meerdere aanmeldingsproviders moet inschakelen, volgt u de instructies in het verificatieartikel [appservice aanpassen.](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)

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

#### <a name="spring-boot"></a>Spring Boot

Spring Boot-ontwikkelaars kunnen de [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) gebruiken om toepassingen te beveiligen met behulp van bekende aantekeningen en API's voor Spring Security. Zorg ervoor dat u de maximale koptekstgrootte in het bestand *application.properties* verhoogt. Wij stellen een `16384`waarde van .

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in [De beveiligde aangepaste DNS-naam met een SSL-binding in Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) om een bestaand SSL-certificaat te uploaden en te binden aan de domeinnaam van uw toepassing. Standaard staat uw toepassing nog steeds toe dat HTTP-verbindingen de specifieke stappen in de zelfstudie volgen om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>KeyVault-referenties gebruiken

[Azure KeyVault](../../key-vault/key-vault-overview.md) biedt gecentraliseerd geheim beheer met toegangsbeleid en controlegeschiedenis. U geheimen (zoals wachtwoorden of verbindingstekenreeksen) opslaan in KeyVault en toegang krijgen tot deze geheimen in uw toepassing via omgevingsvariabelen.

Volg eerst de instructies voor [het verlenen van toegang tot Key Vault aan uw app](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) en maak een [KeyVault-verwijzing naar uw geheim in een toepassingsinstelling.](../app-service-key-vault-references.md#reference-syntax) U valideren dat de verwijzing naar het geheim wordt opgelost door de omgevingsvariabele af te drukken terwijl u op afstand toegang krijgt tot de App Service-terminal.

Als u deze geheimen wilt injecteren in uw configuratiebestand voor`${MY_ENV_VAR}`de lente of tomcat, gebruikt u de syntaxis van de omgevingsvariabele injectie ( ). Zie deze documentatie over [geexternaliseerde configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)voor configuratiebestanden in de lente.

### <a name="using-the-java-key-store"></a>De Java Key Store gebruiken

Standaard worden alle openbare of privécertificaten die naar App Service Linux worden [geüpload,](../configure-ssl-certificate.md) geladen in de respectievelijke Java Key Stores wanneer de container begint. Nadat u uw certificaat hebt geüpload, moet u uw app-service opnieuw starten om deze in de Java Key Store te kunnen laden. Openbare certificaten worden geladen in `$JAVA_HOME/jre/lib/security/cacerts`de Key Store op `$JAVA_HOME/lib/security/client.jks`, en particuliere certificaten worden opgeslagen in .

Extra configuratie kan nodig zijn voor het versleutelen van uw JDBC-verbinding met certificaten in de Java Key Store. Raadpleeg de documentatie voor de door u gekozen JDBC-chauffeur.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Initialiseren van de Java Key Store

Als u het `import java.security.KeyStore` object wilt initialiseren, laadt u het keystore-bestand met het wachtwoord. Het standaardwachtwoord voor beide belangrijke opslag is "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Het sleutelarchief handmatig laden

U certificaten handmatig laden in het sleutelarchief. Maak een app-instelling, `SKIP_JAVA_KEYSTORE_LOAD`met `1` een waarde van het uitschakelen van App Service van het laden van de certificaten in de sleutel winkel automatisch. Alle openbare certificaten die via de Azure-portal `/var/ssl/certs/`naar App Service zijn geüpload, worden opgeslagen onder . Privécertificaten worden `/var/ssl/private/`opgeslagen onder .

U de Java-sleuteltool gebruiken of debuggen door [een SSH-verbinding](app-service-linux-ssh-support.md) met uw appservice te openen en de opdracht `keytool`uit te voeren. Zie de [documentatie van het sleutelhulpprogramma](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) voor een lijst met opdrachten. Voor meer informatie over de KeyStore API verwijzen wij u naar [de officiële documentatie.](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)

## <a name="configure-apm-platforms"></a>APM-platforms configureren

In dit gedeelte ziet u hoe java-toepassingen die zijn geïmplementeerd op Azure App Service op Linux kunnen worden aangesloten met de APM-platforms (NewRelic en AppDynamics application performance monitoring).

### <a name="configure-new-relic"></a>Nieuwe relikwie configureren

1. Maak een NewRelic-account aan op [NewRelic.com](https://newrelic.com/signup)
2. Download de Java-agent van NewRelic, het zal een bestandsnaam vergelijkbaar met *newrelic-java-x.x.x.zip*.
3. Kopieer uw licentiesleutel, u hebt deze nodig om de agent later te configureren.
4. [SSH in uw App Service-exemplaar](app-service-linux-ssh-support.md) en maak een nieuwe directory */home/site/wwwroot/apm*.
5. Upload de uitgepakte NewRelic Java agent bestanden in een directory onder */home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */ home / site / wwwroot / apm / newrelic*.
6. Wijzig het YAML-bestand op */home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de licentiewaarde van de tijdelijke aanduiding door uw eigen licentiesleutel.
7. Blader in de Azure-portal naar uw toepassing in App-service en maak een nieuwe toepassingsinstelling.
    - Als uw app **Java SE**gebruikt, `JAVA_OPTS` maakt u `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`een omgevingsvariabele met de waarde .
    - Als u **Tomcat**gebruikt, maakt u `CATALINA_OPTS` een `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`omgevingsvariabele met de waarde .

### <a name="configure-appdynamics"></a>AppDynamics configureren

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Download de Java-agent van de AppDynamics-website, de bestandsnaam zal vergelijkbaar zijn met *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH in uw App Service-exemplaar](app-service-linux-ssh-support.md) en maak een nieuwe directory */home/site/wwwroot/apm*.
4. Upload de Java-agentbestanden in een map onder */home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */home/site/wwwroot/apm/appdynamics*zijn.
5. Blader in de Azure-portal naar uw toepassing in App-service en maak een nieuwe toepassingsinstelling.
    - Als u **Java SE**gebruikt, maakt `JAVA_OPTS` u een `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` omgevingsvariabele met de naam van de waarde waar uw App Service-naam is.
    - Als u **Tomcat**gebruikt, maakt u `CATALINA_OPTS` een `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` omgevingsvariabele met de waarde waar `<app-name>` uw appservicenaam is.

> [!NOTE]
> Als u al een `JAVA_OPTS` omgevingsvariabele hebt voor of `CATALINA_OPTS`de `-javaagent:/...` optie aan het einde van de huidige waarde toeteweet.

## <a name="configure-jar-applications"></a>JAR-toepassingen configureren

### <a name="starting-jar-apps"></a>JAR-apps starten

App Service verwacht standaard dat de naam van uw JAR-toepassing *app.jar*krijgt. Als deze naam is, wordt deze automatisch uitgevoerd. Voor Maven-gebruikers u de `<finalName>app</finalName>` JAR-naam instellen door deze op te nemen in het `<build>` gedeelte van uw *pom.xml.* [U hetzelfde doen in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) door het instellen van de `archiveFileName` eigenschap.

Als u een andere naam voor uw JAR wilt gebruiken, moet u ook het [opstartcommando](app-service-linux-faq.md#built-in-images) opgeven waarmee uw JAR-bestand wordt uitgevoerd. Bijvoorbeeld `java -jar my-jar-app.jar`. U de waarde voor uw opstartopdracht instellen in de portal, `STARTUP_COMMAND`onder Configuratie > Algemene instellingen of met een toepassingsinstelling met de naam.

### <a name="server-port"></a>Serverpoort

App Service Linux routes inkomende verzoeken naar poort 80, dus uw toepassing moet luisteren op poort 80 ook. U dit doen in de configuratie van uw toepassing (zoals het bestand *application.properties* van Spring) of in de opstartopdracht `java -jar spring-app.jar --server.port=80`(bijvoorbeeld). Raadpleeg de volgende documentatie voor gemeenschappelijke Java-frameworks:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava (SparkJava)](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx (Vertx)](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus Quarkus](https://quarkus.io/guides/application-configuration-guide)

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

#### <a name="shared-server-level-resources"></a>Bronnen op serverniveau

Als u een gedeelde gegevensbron op serverniveau toevoegt, moet u de server.xml van Tomcat bewerken. Upload eerst een [opstartscript](app-service-linux-faq.md#built-in-images) en stel het pad in op het script in**de opdracht Configuratieopstart** **Configuration** > . U het opstartscript uploaden met [FTP](../deploy-ftp.md).

Uw opstartscript maakt een [xsl-transformatie](https://www.w3schools.com/xml/xsl_intro.asp) naar het server.xml-bestand en voert het resulterende xml-bestand uit naar `/usr/local/tomcat/conf/server.xml`. Het opstartscript moet libxslt via apk installeren. Uw xsl-bestand en opstartscript kunnen worden geüpload via FTP. Hieronder is een voorbeeld opstarten script.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Hieronder vindt u een voorbeeld van xsl-bestand. In het voorbeeld xsl-bestand wordt een nieuw verbindingsknooppunt toegevoegd aan de Tomcat-server.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Configuratie afronden

Plaats ten slotte de JARs van de chauffeur in het Tomcat-klassepad en start uw App-service opnieuw.

1. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-classloader door ze in de *map /home/tomcat/lib te* plaatsen. (Maak deze map als deze nog niet bestaat.) Voer de volgende stappen uit om deze bestanden naar uw app-service-instantie te uploaden:

    1. Installeer in de [Cloud Shell](https://shell.azure.com)de webapp-extensie:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Voer de volgende OPDRACHT CLI uit om een SSH-tunnel te maken van uw lokale systeem naar appservice:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Maak verbinding met de lokale tunnelpoort met uw SFTP-client en upload de bestanden naar de *map /home/tomcat/lib.*

    U ook een FTP-client gebruiken om het JDBC-stuurprogramma te uploaden. Volg deze [instructies voor het verkrijgen van uw FTP-referenties.](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json)

2. Als u een gegevensbron op serverniveau hebt gemaakt, start u de Linux-toepassing App Service opnieuw. Tomcat wordt `CATALINA_BASE` `/home/tomcat` gereset naar en gebruikt de bijgewerkte configuratie.

### <a name="spring-boot"></a>Spring Boot

Als u verbinding wilt maken met gegevensbronnen in toepassingen voor springboot, raden we u aan verbindingstekenreeksen te maken en deze in het bestand *application.properties* te injecteren.

1. Stel in het gedeelte 'Configuratie' van de pagina App Service een naam in voor de tekenreeks, plakt uw JDBC-verbindingstekenreeks in het waardeveld en stel het type in op 'Aangepast'. U deze verbindingstekenreeks optioneel instellen als sleufinstelling.

    Deze verbindingstekenreeks is toegankelijk voor onze `CUSTOMCONNSTR_<your-string-name>`toepassing als een omgevingsvariabele met de naam . De verbindingstekenreeks die we hierboven hebben `CUSTOMCONNSTR_exampledb`gemaakt, krijgt bijvoorbeeld de naam .

2. Raadpleeg deze verbindingstekenreeks in het bestand *application.properties* met de naam omgevingsvariabele. Voor ons voorbeeld zouden we het volgende gebruiken.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Zie de [documentatie voor springboot over gegevenstoegang](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) en [geexternaliseerde configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) voor meer informatie over dit onderwerp.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Redis gebruiken als sessiecache met Tomcat

U Tomcat configureren om een extern sessiearchief te gebruiken, zoals [Azure Cache voor Redis.](/azure/azure-cache-for-redis/) Hiermee u de status van de gebruikerssessie (zoals winkelwagengegevens) behouden wanneer een gebruiker wordt overgebracht naar een ander exemplaar van uw app, bijvoorbeeld wanneer u automatisch wordt geschaald, opnieuw opstart of failover optreedt.

Als u Tomcat met Redis wilt gebruiken, moet u uw app configureren om een [PersistentManager-implementatie](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) te gebruiken. In de volgende stappen wordt dit proces uitgelegd met behulp van [Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) als voorbeeld.

1. Open een Bash-terminal en gebruik om `<variable>=<value>` elk van de volgende omgevingsvariabelen in te stellen.

    | Variabele                 | Waarde                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | De naam van de resourcegroep die uw app-service-exemplaar bevat.       |
    | WEBAPP_NAME              | De naam van uw app-service-exemplaar.                                     |
    | WEBAPP_PLAN_NAME         | De naam van uw App Service-abonnement.                                         |
    | REGIO                   | De naam van de regio waar uw app wordt gehost.                           |
    | REDIS_CACHE_NAME         | De naam van uw Azure-cache voor bijvoorbeeld Redis.                           |
    | REDIS_PORT               | De SSL-poort waarop uw Redis-cache luistert.                             |
    | REDIS_PASSWORD           | De primaire toegangssleutel voor uw exemplaar.                                  |
    | REDIS_SESSION_KEY_PREFIX | Een waarde die u opgeeft om sessiesleutels te identificeren die afkomstig zijn van uw app. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    U de naam, de poort en de toegangssleutelgegevens op de Azure-portal vinden door te kijken in de secties **Eigenschappen** of **Toegangssleutels** van uw service-instantie.

2. Het *src/main/webapp/META-INF/context.xml-bestand* van uw app maken of bijwerken met de volgende inhoud:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Dit bestand geeft de implementatie van sessiebeheer voor uw app op en configureert deze. Het maakt gebruik van de omgevingsvariabelen die u in de vorige stap hebt ingesteld om uw accountgegevens uit uw bronbestanden te houden.

3. Gebruik FTP om het JAR-bestand van de sessiebeheerder te uploaden naar uw app-service-exemplaar en deze in de map */home/tomcat/lib* te plaatsen. Zie [Uw app implementeren naar Azure App Service implementeren met FTP/S voor](https://docs.microsoft.com/azure/app-service/deploy-ftp)meer informatie.

4. Schakel de [sessieaffiniteitscookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) voor uw App Service-exemplaar uit. U dit doen vanuit de Azure-portal door naar uw app te navigeren en vervolgens Configuratie- > Algemene instellingen in te stellen **> ARR-affiniteit** **met Uit**. U afwisselend de volgende opdracht gebruiken:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    App Service gebruikt standaard sessieaffiniteitscookies om ervoor te zorgen dat clientaanvragen met bestaande sessies worden doorgestuurd naar hetzelfde exemplaar van uw toepassing. Dit standaardgedrag vereist geen configuratie, maar het kan de status van de gebruikerssessie niet behouden wanneer uw app-exemplaar opnieuw wordt gestart of wanneer het verkeer wordt omgeleid naar een andere instantie. Wanneer u [de bestaande ARR Instance Affinity-configuratie uitschakelt](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) om de sessiecookie-gebaseerde routering uit te schakelen, u het geconfigureerde sessiearchief zonder interferentie laten werken.

5. Navigeer naar de sectie **Eigenschappen** van uw app-service-instantie en zoek **aanvullende uitgaande IP-adressen**. Deze vertegenwoordigen alle mogelijke uitgaande IP-adressen voor uw app. Kopieer deze voor gebruik in de volgende stap.

6. Maak voor elk IP-adres een firewallregel in uw Azure-cache voor het exemplaar Van Redis. U dit doen op de Azure-portal vanuit het **firewallgedeelte** van uw Redis-exemplaar. Geef voor elke regel een unieke naam op en stel de **IP-adreswaarden start-** en **eind-IP-adres** in op hetzelfde IP-adres.

7. Navigeer naar het gedeelte **Geavanceerde instellingen** van uw Redis-instantie en stel Alleen toegang via **SSL toestaan** in op **Nee.** Hierdoor kan uw App Service-instantie communiceren met uw Redis-cache via de Azure-infrastructuur.

8. Werk `azure-webapp-maven-plugin` de configuratie in het *pom.xml-bestand* van uw app bij om te verwijzen naar uw Redis-accountgegevens. Dit bestand maakt gebruik van de omgevingsvariabelen die u eerder hebt ingesteld om uw accountgegevens uit uw bronbestanden te houden.

    Wijzig zo nodig `1.7.0` in de huidige versie van de [Maven-invoegtoepassing voor Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Uw app opnieuw opbouwen en opnieuw implementeren.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Uw app gebruikt nu uw Redis-cache voor sessiebeheer.

Zie de [scaling-stateful-java-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) repo op GitHub voor een voorbeeld dat u gebruiken om deze instructies te testen.

## <a name="docker-containers"></a>Docker-containers

Als u de door Azure ondersteunde Zulu JDK in uw containers wilt gebruiken, moet u de vooraf gebouwde afbeeldingen ophalen en gebruiken zoals gedocumenteerd op de [ondersteunde downloadpagina van Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/) of de voorbeelden van de `Dockerfile` Microsoft Java [GitHub-repo](https://github.com/Microsoft/java/tree/master/docker)gebruiken.

## <a name="statement-of-support"></a>Steunbetuigingen

### <a name="runtime-availability"></a>Beschikbaarheid van runtime

App Service voor Linux ondersteunt twee runtimes voor managed hosting van Java-webapplicaties:

- De [Tomcat servlet container](https://tomcat.apache.org/) voor het uitvoeren van applicaties verpakt als web archief (WAR) bestanden. Ondersteunde versies zijn 8.5 en 9.0.
- Java SE runtime-omgeving voor het uitvoeren van toepassingen verpakt als Java-archief (JAR) bestanden. Ondersteunde versies zijn Java 8 en 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versies en onderhoud

Azul Zulu Enterprise-builds van OpenJDK zijn een gratis, multi-platform, productieklare distributie van de OpenJDK voor Azure en Azure Stack, ondersteund door Microsoft en Azul Systems. Ze bevatten alle onderdelen voor het maken en uitvoeren van Java SE-toepassingen. U de JDK installeren vanaf [Java JDK Installation.](https://aka.ms/azure-jdks)

Ondersteunde JDK's worden automatisch op kwartaalbasis gepatcht in januari, april, juli en oktober van elk jaar.

### <a name="security-updates"></a>Beveiligingsupdates

Patches en fixes voor grote beveiligingsproblemen zullen worden vrijgegeven zodra ze beschikbaar zijn van Azul Systems. Een "grote" kwetsbaarheid wordt gedefinieerd door een basisscore van 9.0 of hoger op het [NIST Common Vulnerability Scoring System, versie 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Afschaffing en pensionering

Als een ondersteunde Java-runtime wordt uitgeschakeld, krijgen Azure-ontwikkelaars die de betreffende runtime gebruiken, ten minste zes maanden voordat de runtime wordt uitgeschakeld, een afschrijvingsbericht.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar het [Azure for Java Developers-centrum](/java/azure/) om Azure-snelstarts, zelfstudies en Java-referentiedocumentatie te vinden.

Algemene vragen over het gebruik van App Service voor Linux die niet specifiek zijn voor de Java-ontwikkeling worden beantwoord in de [App Service Linux FAQ.](app-service-linux-faq.md)
