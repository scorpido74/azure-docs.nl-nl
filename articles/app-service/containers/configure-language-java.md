---
title: Linux java-apps configureren
description: Meer informatie over het configureren van een vooraf ontwikkelde Java-container voor uw app. In dit artikel vindt u de meest voorkomende configuratie taken.
keywords: Azure app service, Web-app, Linux, OSS, Java, Java EE, JEE, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: ffc7c289fd675a68c8b02af1777fea3d4530e17a
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889502"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Een Linux Java-app voor Azure App Service configureren

Met Azure App Service op Linux kunnen Java-Ontwikkel aars snel hun Tomcat of Java Standard Edition (SE) verpakte webtoepassingen bouwen, implementeren en schalen op een volledig beheerde, op Linux gebaseerde service. Implementeer toepassingen met maven plugins vanaf de opdracht regel of in editors zoals IntelliJ, eclips of Visual Studio code.

Deze hand leiding bevat belang rijke concepten en instructies voor Java-Ontwikkel aars die gebruikmaken van een ingebouwde Linux-container in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u de [Java Quick](quickstart-java.md)start.

## <a name="deploying-your-app"></a>Uw app implementeren

U kunt de [maven-invoeg toepassing voor Azure app service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) gebruiken voor het implementeren van zowel jar-als. War-bestanden. Implementatie met populaire Ide's wordt ook ondersteund met [Azure-Toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/) of [Azure-Toolkit voor eclipse](/azure/developer/java/toolkit-for-eclipse).

Anders is uw implementatie methode afhankelijk van het type archief:

- Als u een WAR-bestand wilt implementeren in Tomcat `/api/wardeploy/` , gebruikt u het eind punt om het archief bestand te plaatsen. Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)voor meer informatie over deze API.
- Gebruik het `/api/zipdeploy/` eind punt van de kudu-site om jar-bestanden op de Java SE-installatie kopieën te implementeren. Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)voor meer informatie over deze API.

Implementeer uw War-of JAR-protocol niet met FTP. Het FTP-hulp programma is ontworpen voor het uploaden van opstart scripts, afhankelijkheden of andere runtime bestanden. Het is niet de beste keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Apps registreren en fouten opsporen

Prestatie rapporten, visualisaties van verkeer en de status checkups zijn beschikbaar voor elke app via de Azure Portal. Zie [Azure app service Diagnostics-overzicht](../overview-diagnostics.md)voor meer informatie.

### <a name="ssh-console-access"></a>SSH-console toegang

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Zie [Stream-Logboeken in Cloud shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell)voor meer informatie.

### <a name="app-logging"></a>App-logboek registratie

Schakel [toepassings logboeken](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) in via de Azure portal of [Azure cli](/cli/azure/webapp/log#az-webapp-log-config) om app service te configureren voor het schrijven van de standaard console-uitvoer van de toepassing en de standaard console fout stromen naar het lokale bestands systeem of Azure Blob Storage. Logboek registratie naar het lokale App Service bestandssysteem exemplaar is uitgeschakeld 12 uur nadat het is geconfigureerd. Als u meer retentie nodig hebt, configureert u de toepassing voor het schrijven van uitvoer naar een BLOB storage-container. Uw Java-en tomcat-app-Logboeken kunt u vinden in de */Home/logfiles/Application/* -map.

Als uw toepassing gebruikmaakt van [logback](https://logback.qos.ch/) of [Log4j](https://logging.apache.org/log4j) voor tracering, kunt u deze traceringen door sturen naar Azure-toepassing Insights met behulp van de configuratie-instructies voor logboek registratie in [Java-traceer Logboeken in Application Insights verkennen](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Hulpprogram ma's voor probleem oplossing

De ingebouwde Java-installatie kopieën zijn gebaseerd op het besturings systeem [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Gebruik `apk` Package Manager om hulpprogram ma's voor probleem oplossing of opdrachten te installeren.

### <a name="flight-recorder"></a>Vlucht recorder

Voor alle Linux Java-installatie kopieën op App Service is Zulu-vlucht recorder geïnstalleerd zodat u eenvoudig verbinding kunt maken met de JVM en een profilerings-dump moet starten of genereren.

#### <a name="timed-recording"></a>Getimede opname

Om aan de slag te gaan, SSH in uw App Service `jcmd` en voer de opdracht uit om een lijst weer te geven van alle Java-processen die worden uitgevoerd. Naast jcmd wordt uw Java-toepassing met een proces-ID (PID) weer geven.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Voer de onderstaande opdracht uit om een 30-seconden-opname van de JVM te starten. Hiermee wordt het JVM profiel gemaakt en wordt er een JFR-bestand met de naam *jfr_example. JFR* in de hoofdmap. (Vervang 116 door de PID van uw Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Tijdens het interval van 30 seconden kunt u controleren of de registratie plaatsvindt door uit te `jcmd 116 JFR.check`voeren. Hiermee worden alle opnamen voor het gegeven Java-proces weer gegeven.

#### <a name="continuous-recording"></a>Doorlopende opname

U kunt Zulu Flight recorder gebruiken om uw Java-toepassing continu te profileren met minimale gevolgen voor de prestaties van runtime ([bron](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Als u dit wilt doen, voert u de volgende Azure CLI-opdracht uit om een app-instelling met de naam JAVA_OPTS te maken met de vereiste configuratie. De inhoud van de instelling van de JAVA_OPTS app wordt door `java` gegeven aan de opdracht wanneer uw app wordt gestart.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Zodra de opname is gestart, kunt u de huidige opname gegevens op elk gewenst moment dumpen `JFR.dump` met behulp van de opdracht.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Raadpleeg de [Jcmd-opdracht verwijzing](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)voor meer informatie.

### <a name="analyzing-recordings"></a>Opnamen analyseren

Gebruik [FTPS](../deploy-ftp.md) om uw JFR-bestand te downloaden naar uw lokale computer. Down load en Installeer [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)om het JFR-bestand te analyseren. Zie de [Azul-documentatie](https://docs.azul.com/zmc/) en de [installatie-instructies](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)voor instructies voor Zulu-missie beheer.

## <a name="customization-and-tuning"></a>Aanpassing en afstemming

Azure App Service voor Linux ondersteunt het afstemmen en aanpassen van het kader via de Azure Portal en CLI. Raadpleeg de volgende artikelen voor een niet-Java-specifieke Web-app-configuratie:

- [App-instellingen configureren](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Een aangepast domein instellen](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [SSL-bindingen configureren](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Een CDN toevoegen](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [De kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java runtime-opties instellen

Als u toegewezen geheugen of andere JVM runtime opties wilt instellen in zowel de Tomcat-als Java-SE-omgeving, `JAVA_OPTS` maakt u een app- [instelling](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) met de naam met de opties. App Service Linux geeft deze instelling als een omgevings variabele door aan de Java-runtime wanneer deze wordt gestart.

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling met `JAVA_OPTS` de naam die de aanvullende instellingen bevat, `-Xms512m -Xmx1204m`zoals.

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

Als u een JAR-toepassing implementeert, moet deze de naam *app. jar* hebben, zodat de ingebouwde afbeelding uw app correct kan identificeren. (De Maven-invoeg toepassing wijzigt dit automatisch.) Als u de naam van uw JAR naar *app. jar*niet wilt wijzigen, kunt u een shell script uploaden met de opdracht om uw jar uit te voeren. Plak het volledige pad naar dit script in het tekstvak [Opstartbestand](app-service-linux-faq.md#built-in-images) in de sectie Configuratie van de portal. Het opstartscript kan niet worden uitgevoerd vanuit de map waarin deze is geplaatst. Gebruik daarom altijd absolute paden om te verwijzen naar bestanden in het opstartscript (bijvoorbeeld: `java -jar /home/myapp/myapp.jar`).

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

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling met `JAVA_OPTS` de naam `-Dfile.encoding=UTF-8`met waarde.

U kunt de app-instelling ook configureren met behulp van de App Service maven-invoeg toepassing. Voeg de instellingen naam en waarde tags toe aan de configuratie van de invoeg toepassing:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Time-out voor opstarten aanpassen

Als uw Java-toepassing bijzonder groot is, moet u de tijds limiet voor opstarten verhogen. U doet dit door een toepassings instelling te maken `WEBSITES_CONTAINER_START_TIME_LIMIT` en deze in te stellen op het aantal seconden dat app service moet wachten voordat een time-out optreedt. De maximum waarde is `1800` seconden.

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Als u de prestaties van Tomcat-toepassingen wilt verbeteren, kunt u uw JSP-bestanden compileren voordat u implementeert in App Service. U kunt de [maven-invoeg toepassing](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) van Apache strop gebruiken of dit [Ant build-bestand](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)gebruiken.

## <a name="secure-applications"></a>Veilige toepassingen

Java-toepassingen die worden uitgevoerd in App Service voor Linux, hebben dezelfde set [aanbevolen beveiligings procedures](/azure/security/security-paas-applications-using-app-services) als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (eenvoudige verificatie)

Stel app-verificatie in het Azure Portal in met de optie **verificatie en autorisatie** . Van daaruit kunt u verificatie inschakelen met behulp van Azure Active Directory of sociale aanmeldingen, zoals Facebook, Google of GitHub. Azure Portal configuratie werkt alleen bij het configureren van één verificatie provider. Zie [uw app service-app configureren voor het gebruik van Azure Active Directory login](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) en de verwante artikelen voor andere id-providers voor meer informatie. Als u meerdere aanmeld providers wilt inschakelen, volgt u de instructies in het artikel [customize app service-verificatie](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) .

#### <a name="tomcat"></a>Tomcat

Uw Tomcat-toepassing kan rechtstreeks vanuit de servlet toegang krijgen tot de claims van de gebruiker door het Principal-object naar een kaart object te casten. Het kaart object wijst elk claim type toe aan een verzameling van de claims voor dat type. In de onderstaande code `request` is een exemplaar van. `HttpServletRequest`

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

Als u gebruikers wilt afmelden, `/.auth/ext/logout` gebruikt u het pad. Raadpleeg de documentatie over [app service verificatie en autorisatie gebruik](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)om andere acties uit te voeren. Er is ook officiële documentatie over de Tomcat [HttpServletRequest-interface](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) en de bijbehorende methoden. De volgende servlet-methoden worden ook gehydrateerd op basis van uw App Service configuratie:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Als u deze functie wilt uitschakelen, maakt u een `WEBSITE_AUTH_SKIP_PRINCIPAL` toepassings instelling met de `1`naam met een waarde van. Als u alle Servlet-filters die zijn toegevoegd door App Service wilt uitschakelen `WEBSITE_SKIP_FILTERS` , maakt u een `1`instelling met de naam met een waarde van.

#### <a name="spring-boot"></a>Spring Boot

Spring boot-ontwikkel aars kunnen de [Azure Active Directory Spring boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) gebruiken om toepassingen te beveiligen met behulp van vertrouwde beveiligings aantekeningen en-api's. Zorg ervoor dat u de maximale header-grootte in het bestand *Application. Properties* verhoogt. We suggereren een waarde van `16384`.

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in het gedeelte [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) om een bestaand SSL-certificaat te uploaden en te binden aan de domein naam van uw toepassing. Uw toepassing staat standaard nog HTTP-verbindingen toe. Volg de specifieke stappen in de zelf studie om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>Verwijzingen naar de sleutel kluis gebruiken

[Azure](../../key-vault/general/overview.md) -sleutel kluis biedt gecentraliseerd geheim beheer met toegangs beleid en controle geschiedenis. U kunt geheimen (zoals wacht woorden of verbindings reeksen) opslaan in de sleutel kluis en toegang krijgen tot deze geheimen in uw toepassing via omgevings variabelen.

Volg eerst de instructies voor het [verlenen van toegang tot Key Vault van uw app](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) en het [maken van een verwijzing naar een sleutel kluis in een toepassings instelling](../app-service-key-vault-references.md#reference-syntax). U kunt controleren of de verwijzing naar het geheim wordt omgezet door de omgevings variabele af te drukken terwijl u extern toegang hebt tot de App Service Terminal.

Als u deze geheimen wilt injecteren in het configuratie bestand voor de lente of het Tomcat, gebruikt`${MY_ENV_VAR}`u de syntaxis voor het injecteren van omgevings variabelen (). Raadpleeg deze documentatie over [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)voor lente configuratie bestanden.

### <a name="using-the-java-key-store"></a>Het Java-sleutel archief gebruiken

Standaard worden alle open bare of persoonlijke certificaten [die zijn geüpload naar app service Linux](../configure-ssl-certificate.md) geladen in de respectieve Java-sleutel archieven wanneer de container wordt gestart. Nadat u het certificaat hebt geüpload, moet u de App Service opnieuw opstarten zodat het in het Java-sleutel archief wordt geladen. Open bare certificaten worden geladen in de sleutel opslagplaats `$JAVA_HOME/jre/lib/security/cacerts`op en persoonlijke certificaten worden opgeslagen in `$JAVA_HOME/lib/security/client.jks`.

Er is mogelijk extra configuratie nodig voor het versleutelen van uw JDBC-verbinding met certificaten in het Java-sleutel archief. Raadpleeg de documentatie voor het gekozen JDBC-stuur programma.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Het Java-sleutel archief initialiseren

Als u het `import java.security.KeyStore` object wilt initialiseren, laadt u het bestand met de opslag plaats met het wacht woord. Het standaard wachtwoord voor beide sleutel archieven is "changeit".

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

#### <a name="manually-load-the-key-store"></a>Het sleutel archief hand matig laden

U kunt certificaten hand matig laden naar de sleutel opslag. Maak een app-instelling `SKIP_JAVA_KEYSTORE_LOAD`, met de waarde app service `1` uit te scha kelen, zodat de certificaten niet automatisch in het sleutel archief worden geladen. Alle open bare certificaten die zijn geüpload naar App Service via de Azure Portal `/var/ssl/certs/`worden opgeslagen onder. Persoonlijke certificaten worden opgeslagen onder `/var/ssl/private/`.

U kunt het Java-sleutel programma interactief gebruiken of fouten opsporen door [een SSH-verbinding](app-service-linux-ssh-support.md) met uw app service `keytool`te openen en de opdracht uit te voeren. Raadpleeg de [documentatie van het belang rijk hulp programma](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) voor een lijst met opdrachten. Raadpleeg [de officiële documentatie](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)voor meer informatie over de API voor de-opslag.

## <a name="configure-apm-platforms"></a>APM-platforms configureren

In deze sectie wordt beschreven hoe u Java-toepassingen die zijn geïmplementeerd op Azure App Service op Linux verbindt met de NewRelic-en AppDynamics-platformen voor het controleren van de prestaties van toepassingen (APM).

### <a name="configure-new-relic"></a>Nieuwe Relic configureren

1. Een NewRelic-account maken op [NewRelic.com](https://newrelic.com/signup)
2. Down load de Java-Agent van NewRelic. deze heeft een bestands naam die lijkt op *newrelic-Java-x. x. x. zip*.
3. Kopieer uw licentie sleutel, u hebt deze later nodig om de agent te configureren.
4. [Ssh in uw app service-exemplaar](app-service-linux-ssh-support.md) en maak een nieuwe directory */Home/site/wwwroot/apm*.
5. Upload de uitgepakte NewRelic Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/newrelic*zijn.
6. Wijzig het YAML-bestand op */Home/site/wwwroot/apm/newrelic/newrelic.yml* en vervang de tijdelijke aanduiding voor de licentie waarde door uw eigen licentie code.
7. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.
    - Als uw app **Java SE**gebruikt, maakt u een omgevings variabele `JAVA_OPTS` met de naam `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`met de waarde.
    - Als u **Tomcat**gebruikt, maakt u een omgevings variabele `CATALINA_OPTS` met de naam `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`met de waarde.

### <a name="configure-appdynamics"></a>AppDynamics configureren

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. De Java-agent downloaden van de AppDynamics-website, de bestands naam is vergelijkbaar met *AppServerAgent-x. x. x. xxxxx. zip*
3. [Ssh in uw app service-exemplaar](app-service-linux-ssh-support.md) en maak een nieuwe directory */Home/site/wwwroot/apm*.
4. Upload de Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/appdynamics*zijn.
5. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.
    - Als u **Java SE**gebruikt, maakt u een omgevings variabele `JAVA_OPTS` met de naam `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` met `<app-name>` de waarde waar de naam van uw app service is.
    - Als u **Tomcat**gebruikt, maakt u een omgevings variabele `CATALINA_OPTS` met de naam `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` met `<app-name>` de waarde waar de naam van uw app service is.

> [!NOTE]
> Als u al een omgevings variabele voor `JAVA_OPTS` of `CATALINA_OPTS`hebt, voegt `-javaagent:/...` u de optie toe aan het einde van de huidige waarde.

## <a name="configure-jar-applications"></a>JAR-toepassingen configureren

### <a name="starting-jar-apps"></a>JAR-apps starten

App Service verwacht dat uw JAR-toepassing standaard de naam *app. jar*heeft. Als het deze naam heeft, wordt deze automatisch uitgevoerd. Voor maven-gebruikers kunt u de JAR-naam instellen door `<finalName>app</finalName>` deze op `<build>` te nemen in het gedeelte van uw *pom. XML*. [U kunt hetzelfde doen in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) door de `archiveFileName` eigenschap in te stellen.

Als u een andere naam voor uw JAR wilt gebruiken, moet u ook de [opstart opdracht](app-service-linux-faq.md#built-in-images) opgeven waarmee het jar-bestand wordt uitgevoerd. Bijvoorbeeld `java -jar my-jar-app.jar`. U kunt de waarde voor de opstart opdracht in de Portal instellen, onder configuratie > algemene instellingen, of met een toepassings instelling met `STARTUP_COMMAND`de naam.

### <a name="server-port"></a>Server poort

App Service Linux stuurt binnenkomende aanvragen naar poort 80, dus uw toepassing moet ook op poort 80 worden geluisterd. U kunt dit doen in de configuratie van uw toepassing (zoals het bestand *toepassing. Properties* ) of in de opstart opdracht (bijvoorbeeld `java -jar spring-app.jar --server.port=80`). Raadpleeg de volgende documentatie voor algemene Java-frameworks:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Framework afspelen](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Gegevensbronnen

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle database verbindingen. U moet tijdelijke aanduidingen vullen met de naam van de stuurprogrammanaam en het JAR-bestand van uw gekozen data base. Gegeven is een tabel met klasse-namen en down loads van Stuur Programma's voor algemene data bases.

| Database   | Naam stuur programma klasse                             | JDBC-stuur programma                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (Selecteer ' platform onafhankelijk ') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Als u Tomcat wilt configureren voor het gebruik van de Java Data Base Connectivity (JDBC) of de Java Persistence API ( `CATALINA_OPTS` JPA), moet u eerst de omgevings variabele aanpassen die in Tomcat wordt gelezen tijdens het opstarten. Stel deze waarden in via een app-instelling in de [maven-invoeg toepassing van app service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevings variabelen in op de pagina **configuratie** > **Toepassings instellingen** in het Azure Portal.

Bepaal vervolgens of de gegevens bron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevens bronnen op toepassings niveau

1. Maak een *context. XML-* bestand in de *META-INF/* map van uw project. Maak de *META-INF/-* map als deze niet bestaat.

2. Voeg in *context. XML*een `Context` element toe om de gegevens bron te koppelen aan een JNDI-adres. Vervang de `driverClassName` tijdelijke aanduiding door de naam van de klasse van uw stuur programma uit de bovenstaande tabel.

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

3. Werk de *Web. XML* van uw toepassing bij om de gegevens bron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Resources op gedeelde server niveau

Als u een gedeelde gegevens bron op server niveau wilt toevoegen, moet u de server. XML van Tomcat bewerken. Upload eerst een [opstart script](app-service-linux-faq.md#built-in-images) en stel het pad naar het script in de **Configuration** > **opstart opdracht**van de configuratie in. U kunt het opstart script uploaden met [FTP](../deploy-ftp.md).

Met het opstart script maakt u een [XSL-trans formatie](https://www.w3schools.com/xml/xsl_intro.asp) naar het bestand server. XML en voert u het `/usr/local/tomcat/conf/server.xml`resulterende XML-bestand uit naar. Het opstart script moet libxslt installeren via APK. Het XSL-bestand en het opstart script kunnen worden geüpload via FTP. Hieronder ziet u een voor beeld van een opstart script.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Hieronder vindt u een voor beeld van een XSL-bestand. Het XSL-voorbeeld bestand voegt een nieuw connector knooppunt toe aan de Tomcat-server. XML.

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

#### <a name="finalize-configuration"></a>Configuratie volt ooien

Plaats tot slot de potten van het stuur programma in het Tomcat CLASSPATH en start uw App Service opnieuw op.

1. Zorg ervoor dat de JDBC-stuurprogrammabestanden beschikbaar zijn voor de Tomcat-ClassLoader door ze te plaatsen in de map */Home/tomcat/lib* . (Maak deze map als deze nog niet bestaat.) Als u deze bestanden wilt uploaden naar uw App Service-exemplaar, voert u de volgende stappen uit:

    1. Installeer de webapp-extensie in de [Cloud shell](https://shell.azure.com):

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Voer de volgende CLI-opdracht uit om een SSH-tunnel van uw lokale systeem te maken om te App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Maak verbinding met de lokale tunnel poort met uw SFTP-client en upload de bestanden naar de map */Home/tomcat/lib* .

    U kunt ook een FTP-client gebruiken om het JDBC-stuur programma te uploaden. Volg deze [instructies voor het ophalen van uw FTP-referenties](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Als u een gegevens bron op server niveau hebt gemaakt, start u de App Service Linux-toepassing opnieuw. Tomcat wordt opnieuw `CATALINA_BASE` ingesteld `/home/tomcat` op en gebruikt de bijgewerkte configuratie.

### <a name="spring-boot"></a>Spring Boot

Om verbinding te maken met gegevens bronnen in veer boot-toepassingen, wordt u geadviseerd om verbindings reeksen te maken en deze in uw toepassing te injecteren *. eigenschappen* bestand.

1. Stel in het gedeelte ' configuratie ' van de pagina App Service een naam in voor de teken reeks, plak uw JDBC-connection string in het waardeveld en stel het type in op aangepast. U kunt deze connection string eventueel instellen als sleuf instelling.

    Deze connection string is toegankelijk voor onze toepassing als een omgevings variabele `CUSTOMCONNSTR_<your-string-name>`met de naam. De connection string die u hierboven hebt gemaakt, krijgen bijvoorbeeld de `CUSTOMCONNSTR_exampledb`naam.

2. In uw *toepassing. Properties* -bestand verwijst u naar deze Connection String met de naam van de omgevings variabele. In ons voor beeld gebruiken we het volgende.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Raadpleeg de [Spring boot-documentatie over gegevens toegang](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) en [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) voor meer informatie over dit onderwerp.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Redis als sessie cache gebruiken met Tomcat

U kunt Tomcat configureren voor het gebruik van een externe sessie opslag, zoals [Azure cache voor redis](/azure/azure-cache-for-redis/). Zo kunt u de status van de gebruikers sessie (zoals de gegevens van de winkel wagen) behouden wanneer een gebruiker wordt overgezet naar een ander exemplaar van uw app, bijvoorbeeld wanneer automatisch schalen, opnieuw opstarten of failover wordt uitgevoerd.

Als u Tomcat met redis wilt gebruiken, moet u uw app configureren voor het gebruik van een [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) -implementatie. In de volgende stappen wordt dit proces uitgelegd met behulp van [Pivot Session Manager: redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) als voor beeld.

1. Open een bash-Terminal en `<variable>=<value>` gebruik deze om elk van de volgende omgevings variabelen in te stellen.

    | Variabele                 | Waarde                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | De naam van de resource groep met uw App Service-exemplaar.       |
    | WEBAPP_NAME              | De naam van uw App Service-exemplaar.                                     |
    | WEBAPP_PLAN_NAME         | De naam van uw App Service-abonnement.                                         |
    | REGIO                   | De naam van de regio waar uw app wordt gehost.                           |
    | REDIS_CACHE_NAME         | De naam van uw Azure-cache voor redis-instantie.                           |
    | REDIS_PORT               | De SSL-poort waarop uw redis-cache wordt geluisterd.                             |
    | REDIS_PASSWORD           | De primaire toegangs sleutel voor uw exemplaar.                                  |
    | REDIS_SESSION_KEY_PREFIX | Een waarde die u opgeeft om sessie sleutels te identificeren die afkomstig zijn van uw app. |

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

    U kunt de naam, de poort en toegangs sleutel gegevens op de Azure Portal vinden door te kijken in de secties **Eigenschappen** of **toegangs sleutels** van uw service-exemplaar.

2. Het *src/main/webapp/META-INF/context. XML-* bestand van uw app maken of bijwerken met de volgende inhoud:

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

    Met dit bestand wordt de sessie beheer implementatie voor uw app opgegeven en geconfigureerd. Hierbij worden de omgevings variabelen gebruikt die u in de vorige stap hebt ingesteld om uw account gegevens uit uw bron bestanden te laten staan.

3. Gebruik FTP om het JAR-bestand van de sessie beheerder naar uw App Service-exemplaar te uploaden en het in de map */Home/tomcat/lib* te plaatsen. Zie [uw app implementeren voor Azure app service met behulp van FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)voor meer informatie.

4. Schakel het [sessie affiniteits cookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) voor uw app service-exemplaar uit. U kunt dit doen vanuit de Azure Portal door te navigeren naar uw app en vervolgens **configuratie > algemene instellingen te configureren > ARR** -affiniteit **in te**stellen. U kunt ook de volgende opdracht gebruiken:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    App Service maakt standaard gebruik van cookies voor sessie affiniteit om ervoor te zorgen dat client aanvragen met bestaande sessies worden doorgestuurd naar hetzelfde exemplaar van uw toepassing. Voor dit standaard gedrag is geen configuratie vereist, maar de gebruikers sessie status kan niet worden behouden als uw app-exemplaar opnieuw wordt gestart of wanneer het verkeer wordt omgeleid naar een ander exemplaar. Wanneer u de bestaande configuratie van de functie voor het configureren van de [ARR-instantie uitschakelt](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) om de op cookies gebaseerde route ring uit te scha kelen, mag de geconfigureerde sessie Store zonder storingen worden uitgevoerd.

5. Ga naar de sectie **Eigenschappen** van uw app service-exemplaar en zoek **extra uitgaande IP-adressen**. Hiermee worden alle mogelijke uitgaande IP-adressen voor uw app weer gegeven. Kopieer deze voor gebruik in de volgende stap.

6. Maak voor elk IP-adres een firewall regel in uw Azure-cache voor redis-instantie. U kunt dit doen in het gedeelte Azure Portal van de **firewall** van uw redis-exemplaar. Geef een unieke naam op voor elke regel en stel de waarden voor **IP-begin adres** en **IP-eind adres** in op hetzelfde IP-adres.

7. Ga naar de sectie **Geavanceerde instellingen** van uw redis-exemplaar en stel **alleen toegang toestaan via SSL** in op **Nee**. Hiermee kan uw App Service-exemplaar communiceren met uw redis-cache via de Azure-infra structuur.

8. Werk de `azure-webapp-maven-plugin` configuratie in het *pom. XML-* bestand van uw app bij om te verwijzen naar uw redis-account gegevens. In dit bestand worden de omgevings variabelen gebruikt die u eerder hebt ingesteld om uw account gegevens uit uw bron bestanden te beveiligen.

    Wijzig zo nodig `1.9.1` in de huidige versie van de [Maven-invoegtoepassing voor Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

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

9. Bouw en implementeer uw app opnieuw.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Uw app maakt nu gebruik van uw redis-cache voor sessie beheer.

Voor een voor beeld dat u kunt gebruiken om deze instructies te testen, raadpleegt u de [schaal baarheid-stateful-Java-Web-app-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) opslag plaats op github.

## <a name="docker-containers"></a>Docker-containers

Als u de door Azure ondersteunde Zulu-JDK in uw containers wilt gebruiken, moet u ervoor zorgen dat u de vooraf gemaakte installatie kopieën ophaalt en gebruikt, zoals beschreven op de [pagina ondersteunde Azul Zulu Enter prise for Azure down load](https://www.azul.com/downloads/azure-only/zulu/) of de `Dockerfile` voor beelden uit de [micro soft Java github opslag plaats](https://github.com/Microsoft/java/tree/master/docker)gebruiken.

## <a name="statement-of-support"></a>Ondersteunings verklaring

### <a name="runtime-availability"></a>Beschik baarheid van runtime

App Service voor Linux ondersteunt twee Runtimes voor beheerde hosting van Java-webtoepassingen:

- De [Tomcat servlet-container](https://tomcat.apache.org/) voor het uitvoeren van toepassingen die zijn verpakt als Web Archive (War)-bestanden. Ondersteunde versies zijn 8,5 en 9,0.
- Java SE Runtime Environment voor het uitvoeren van toepassingen die zijn verpakt als Java Archive-bestanden (JAR). Ondersteunde versies zijn Java 8 en 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versies en onderhoud

Azul Zulu Enter prise builds van OpenJDK zijn een gratis, op productie gebaseerde distributie van de OpenJDK voor Azure en Azure Stack ondersteund door micro soft en Azul Systems. Ze bevatten alle onderdelen voor het maken en uitvoeren van Java SE-toepassingen. U kunt de JDK installeren vanuit de [Java JDK-installatie](https://aka.ms/azure-jdks).

Ondersteunde JDKs worden automatisch op een driemaandelijkse patch uitgevoerd in januari, april, juli en oktober van elk jaar.

### <a name="security-updates"></a>Beveiligingsupdates

Patches en oplossingen voor belang rijke beveiligings problemen worden vrijgegeven zodra deze beschikbaar worden gesteld via Azul-systemen. Een ' belang rijk ' beveiligingslek is gedefinieerd met een basis Score van 9,0 of hoger in het [gemeen schappelijke beveiligings risico van het NIST-systeem versie 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Afschaffing en buiten gebruik stellen

Als een ondersteunde Java-runtime wordt ingetrokken, krijgen Azure-ontwikkel aars die de betrokken runtime gebruiken, een afschaffing die ten minste zes maanden duurt voordat de runtime wordt afgetrokken.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar [Azure voor Java-ontwikkelaars](/java/azure/) centrum voor meer informatie over Azure Quick starts, zelf studies en naslag informatie voor Java.

Algemene vragen over het gebruik van App Service voor Linux die niet specifiek zijn voor de Java-ontwikkeling, worden beantwoord in de [Veelgestelde vragen over app service Linux](app-service-linux-faq.md).
