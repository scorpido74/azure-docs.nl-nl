---
title: Linux java-apps configureren-Azure App Service | Microsoft Docs
description: Meer informatie over het configureren van Java-apps die worden uitgevoerd in Azure App Service op Linux.
keywords: Azure app service, Web-app, Linux, OSS, Java, Java EE, JEE, javaee
services: app-service
author: bmitchell287
manager: barbkess
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: e63d8f03b26c9039fe4093cf15b13522dbb49af9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081476"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Een Linux java-app voor Azure App Service configureren

Met Azure App Service op Linux kunnen Java-Ontwikkel aars snel hun Tomcat of Java Standard Edition (SE) verpakte webtoepassingen bouwen, implementeren en schalen op een volledig beheerde, op Linux gebaseerde service. Implementeer toepassingen met maven plugins vanaf de opdracht regel of in editors zoals IntelliJ, eclips of Visual Studio code.

Deze hand leiding bevat belang rijke concepten en instructies voor Java-Ontwikkel aars die gebruikmaken van een ingebouwde Linux-container in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [Java-Snelstartgids](quickstart-java.md) en [Java met postgresql zelf studie](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Uw app implementeren

U kunt de [maven-invoeg toepassing voor Azure app service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) gebruiken voor het implementeren van zowel jar-als. War-bestanden. Implementatie met populaire Ide's wordt ook ondersteund met [Azure-Toolkit voor IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) of [Azure-Toolkit voor eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Anders is uw implementatie methode afhankelijk van het type archief:

- Als u een WAR-bestand wilt implementeren in Tomcat, gebruikt u het `/api/wardeploy/`-eind punt om het archief bestand te plaatsen. Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)voor meer informatie over deze API.
- Gebruik het `/api/zipdeploy/`-eind punt van de kudu-site om jar-bestanden op de Java SE-installatie kopieën te implementeren. Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)voor meer informatie over deze API.

Implementeer uw War-of JAR-protocol niet met FTP. Het FTP-hulp programma is ontworpen voor het uploaden van opstart scripts, afhankelijkheden of andere runtime bestanden. Het is niet de beste keuze voor het implementeren van web-apps.

## <a name="logging-and-debugging-apps"></a>Apps registreren en fouten opsporen

Prestatie rapporten, visualisaties van verkeer en de status checkups zijn beschikbaar voor elke app via de Azure Portal. Zie [Azure app service Diagnostics-overzicht](../overview-diagnostics.md)voor meer informatie.

### <a name="ssh-console-access"></a>SSH-console toegang

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Zie [Stream-Logboeken in Cloud shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell)voor meer informatie.

### <a name="app-logging"></a>App-logboek registratie

Schakel [toepassings logboeken](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) in via de Azure portal of [Azure cli](/cli/azure/webapp/log#az-webapp-log-config) om app service te configureren voor het schrijven van de standaard console-uitvoer van de toepassing en de standaard console fout stromen naar het lokale bestands systeem of Azure Blob Storage. Logboek registratie naar het lokale App Service bestandssysteem exemplaar is uitgeschakeld 12 uur nadat het is geconfigureerd. Als u meer retentie nodig hebt, configureert u de toepassing voor het schrijven van uitvoer naar een BLOB storage-container. Uw Java-en tomcat-app-Logboeken kunt u vinden in de */Home/logfiles/Application/* -map.

Als uw toepassing gebruikmaakt van [logback](https://logback.qos.ch/) of [Log4j](https://logging.apache.org/log4j) voor tracering, kunt u deze traceringen door sturen naar Azure-toepassing Insights met behulp van de configuratie-instructies voor logboek registratie in [Java-traceer Logboeken in Application Insights ](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Hulpprogram ma's voor probleem oplossing

De ingebouwde Java-installatie kopieën zijn gebaseerd op het besturings systeem [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Gebruik het `apk`-pakket beheer om hulpprogram ma's of opdrachten voor probleem oplossing te installeren.

### <a name="flight-recorder"></a>Vlucht recorder

Voor alle Linux Java-installatie kopieën op App Service is Zulu-vlucht recorder geïnstalleerd zodat u eenvoudig verbinding kunt maken met de JVM en een profilerings-dump moet starten of genereren.

#### <a name="timed-recording"></a>Getimede opname

Om aan de slag te gaan, SSH in uw App Service en voer de `jcmd` opdracht uit om een lijst weer te geven van alle Java-processen die worden uitgevoerd. Naast jcmd wordt uw Java-toepassing met een proces-ID (PID) weer geven.

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

Tijdens het interval van 30 seconden kunt u valideren dat de opname plaatsvindt door `jcmd 116 JFR.check`uit te voeren. Hiermee worden alle opnamen voor het gegeven Java-proces weer gegeven.

#### <a name="continuous-recording"></a>Doorlopende opname

U kunt Zulu Flight recorder gebruiken om uw Java-toepassing continu te profileren met minimale gevolgen voor de prestaties van runtime ([bron](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Als u dit wilt doen, voert u de volgende Azure CLI-opdracht uit om een app-instelling met de naam JAVA_OPTS te maken met de vereiste configuratie. De inhoud van de JAVA_OPTS app-instelling wordt door gegeven aan de `java` opdracht wanneer uw app wordt gestart.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Zodra de opname is gestart, kunt u de huidige opname gegevens op elk gewenst moment dumpen met behulp van de `JFR.dump` opdracht.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Raadpleeg de [Jcmd-opdracht verwijzing](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)voor meer informatie.

### <a name="analyzing-recordings"></a>Opnamen analyseren

Gebruik [FTPS](../deploy-ftp.md) om uw JFR-bestand te downloaden naar uw lokale computer. Down load en Installeer [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)om het JFR-bestand te analyseren. Zie de [Azul-documentatie](https://docs.azul.com/zmc/) en de [installatie-instructies](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)voor instructies voor Zulu-missie beheer.

## <a name="customization-and-tuning"></a>Aanpassing en afstemming

Azure App Service voor Linux ondersteunt het afstemmen en aanpassen van het kader via de Azure Portal en CLI. Raadpleeg de volgende artikelen voor een niet-Java-specifieke Web-app-configuratie:

- [App-instellingen configureren](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Een aangepast domein instellen](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL-bindingen configureren](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Een CDN toevoegen](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [De kudu-site configureren](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java runtime-opties instellen

Als u toegewezen geheugen of andere JVM runtime opties wilt instellen in zowel de Tomcat-als Java-SE-omgeving, maakt u een [app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) met de naam `JAVA_OPTS` met de opties. App Service Linux geeft deze instelling als een omgevings variabele door aan de Java-runtime wanneer deze wordt gestart.

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling met de naam `JAVA_OPTS` die de aanvullende instellingen bevat, zoals `-Xms512m -Xmx1204m`.

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

- B1 en S1 instanties: `-Xms1024m -Xmx1024m`
- B2-en S2-instanties: `-Xms3072m -Xmx3072m`
- B3-en S3-instanties: `-Xms6144m -Xmx6144m`

Bij het afstemmen van de instellingen voor de heap van toepassingen, raadpleegt u de details van het App Service plan en houdt u rekening met meerdere toepassingen en implementatie sleuven om de optimale toewijzing van het geheugen te vinden.

Als u een JAR-toepassing implementeert, moet deze de naam *app. jar* hebben, zodat de ingebouwde afbeelding uw app correct kan identificeren. (De Maven-invoeg toepassing wijzigt dit automatisch.) Als u de naam van uw JAR naar *app. jar*niet wilt wijzigen, kunt u een shell script uploaden met de opdracht om uw jar uit te voeren. Plak het volledige pad naar dit script in het tekstvak [opstart bestand](app-service-linux-faq.md#built-in-images) in de configuratie sectie van de portal. Het opstart script kan niet worden uitgevoerd vanuit de directory waarin het wordt geplaatst. Gebruik daarom altijd absolute paden om te verwijzen naar bestanden in het opstart script (bijvoorbeeld: `java -jar /home/myapp/myapp.jar`).

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

Maak in de Azure Portal onder **Toepassings instellingen** voor de web-app een nieuwe app-instelling met de naam `JAVA_OPTS` met de waarde `-Dfile.encoding=UTF-8`.

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

Als uw Java-toepassing bijzonder groot is, moet u de tijds limiet voor opstarten verhogen. Als u dit wilt doen, maakt u een toepassings instelling `WEBSITES_CONTAINER_START_TIME_LIMIT` en stelt u deze in op het aantal seconden dat App Service moet wachten voordat een time-out optreedt. De maximum waarde is `1800` seconden.

### <a name="pre-compile-jsp-files"></a>JSP-bestanden vooraf compileren

Als u de prestaties van Tomcat-toepassingen wilt verbeteren, kunt u uw JSP-bestanden compileren voordat u implementeert in App Service. U kunt de [maven-invoeg toepassing](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) van Apache strop gebruiken of dit [Ant build-bestand](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)gebruiken.

## <a name="secure-applications"></a>Veilige toepassingen

Java-toepassingen die worden uitgevoerd in App Service voor Linux, hebben dezelfde set [aanbevolen beveiligings procedures](/azure/security/security-paas-applications-using-app-services) als andere toepassingen.

### <a name="authenticate-users-easy-auth"></a>Gebruikers verifiëren (eenvoudige verificatie)

Stel app-verificatie in het Azure Portal in met de optie **verificatie en autorisatie** . Van daaruit kunt u verificatie inschakelen met behulp van Azure Active Directory of sociale aanmeldingen, zoals Facebook, Google of GitHub. Azure Portal configuratie werkt alleen bij het configureren van één verificatie provider. Zie [uw app service-app configureren voor het gebruik van Azure Active Directory login](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) en de verwante artikelen voor andere id-providers voor meer informatie. Als u meerdere aanmeld providers wilt inschakelen, volgt u de instructies in het artikel [customize app service-verificatie](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat en Wildfly

Uw tomcat-of Wildfly-toepassing kan rechtstreeks vanuit de servlet toegang krijgen tot de claims van de gebruiker door het Principal-object naar een kaart object te casten. Het kaart object wijst elk claim type toe aan een verzameling van de claims voor dat type. In de onderstaande code is `request` een instantie van `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

U kunt nu het `Map`-object controleren op elke specifieke claim. Het volgende code fragment loopt bijvoorbeeld door alle claim typen en drukt de inhoud van elke verzameling af.

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

Als u deze functie wilt uitschakelen, maakt u een toepassings instelling met de naam `WEBSITE_AUTH_SKIP_PRINCIPAL` met de waarde `1`. Als u alle Servlet-filters die zijn toegevoegd door App Service wilt uitschakelen, maakt u een instelling met de naam `WEBSITE_SKIP_FILTERS` met de waarde `1`.

#### <a name="spring-boot"></a>Spring Boot

Spring boot-ontwikkel aars kunnen de [Azure Active Directory Spring boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) gebruiken om toepassingen te beveiligen met behulp van vertrouwde beveiligings aantekeningen en-api's. Zorg ervoor dat u de maximale header-grootte in het bestand *Application. Properties* verhoogt. We suggereren een waarde van `16384`.

### <a name="configure-tlsssl"></a>TLS/SSL configureren

Volg de instructies in het gedeelte [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om een bestaand SSL-certificaat te uploaden en te binden aan de domein naam van uw toepassing. Uw toepassing staat standaard nog HTTP-verbindingen toe. Volg de specifieke stappen in de zelf studie om SSL en TLS af te dwingen.

### <a name="use-keyvault-references"></a>Verwijzingen naar de sleutel kluis gebruiken

[Azure](../../key-vault/key-vault-overview.md) -sleutel kluis biedt gecentraliseerd geheim beheer met toegangs beleid en controle geschiedenis. U kunt geheimen (zoals wacht woorden of verbindings reeksen) opslaan in de sleutel kluis en toegang krijgen tot deze geheimen in uw toepassing via omgevings variabelen.

Volg eerst de instructies voor het [verlenen van toegang tot Key Vault van uw app](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) en het [maken van een verwijzing naar een sleutel kluis in een toepassings instelling](../app-service-key-vault-references.md#reference-syntax). U kunt controleren of de verwijzing naar het geheim wordt omgezet door de omgevings variabele af te drukken terwijl u extern toegang hebt tot de App Service Terminal.

Als u deze geheimen wilt injecteren in het configuratie bestand voor de lente of het Tomcat, gebruikt u de syntaxis voor het injecteren van omgevings variabelen (`${MY_ENV_VAR}`). Raadpleeg deze documentatie over [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)voor lente configuratie bestanden.

### <a name="using-the-java-key-store"></a>Het Java-sleutel archief gebruiken

Standaard worden alle open bare of persoonlijke certificaten [die zijn geüpload naar app service Linux](../configure-ssl-certificate.md) geladen in het Java-sleutel archief wanneer de container wordt gestart. Dit betekent dat uw geüploade certificaten beschikbaar zijn in de verbindings context bij het maken van uitgaande TLS-verbindingen. Nadat u het certificaat hebt geüpload, moet u de App Service opnieuw opstarten zodat het in het Java-sleutel archief wordt geladen.

U kunt het Java-sleutel programma interactief gebruiken of fouten opsporen door [een SSH-verbinding](app-service-linux-ssh-support.md) met uw app service te openen en de opdracht `keytool`uit te voeren. Raadpleeg de [documentatie van het belang rijk hulp programma](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) voor een lijst met opdrachten. De certificaten worden opgeslagen in de standaard bestands locatie voor de opslag van een archief `$JAVA_HOME/jre/lib/security/cacerts`.

Er is mogelijk aanvullende configuratie nodig voor het versleutelen van uw JDBC-verbinding. Raadpleeg de documentatie voor het gekozen JDBC-stuur programma.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

#### <a name="manually-initialize-and-load-the-key-store"></a>De sleutel opslag hand matig initialiseren en laden

U kunt de sleutel opslag initialiseren en certificaten hand matig toevoegen. Maak een app-instelling `SKIP_JAVA_KEYSTORE_LOAD`, met de waarde `1` om App Service uit te scha kelen voor het automatisch laden van de certificaten in de sleutel opslag. Alle open bare certificaten die zijn geüpload naar App Service via Azure Portal, worden opgeslagen onder `/var/ssl/certs/`. Persoonlijke certificaten worden opgeslagen onder `/var/ssl/private/`.

Raadpleeg [de officiële documentatie](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)voor meer informatie over de API voor de-opslag.

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
    - Als uw app **Java SE**gebruikt, maakt u een omgevings variabele met de naam `JAVA_OPTS` met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Als u **Tomcat**gebruikt, maakt u een omgevings variabele met de naam `CATALINA_OPTS` met de waarde `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Als u **WildFly**gebruikt, raadpleegt u de nieuwe Relic-documentatie [hier](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) voor meer informatie over het installeren van de Java-Agent en de configuratie van JBoss.

### <a name="configure-appdynamics"></a>AppDynamics configureren

1. Een AppDynamics-account maken op [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. De Java-agent downloaden van de AppDynamics-website, de bestands naam is vergelijkbaar met *AppServerAgent-x. x. x. xxxxx. zip*
3. [Ssh in uw app service-exemplaar](app-service-linux-ssh-support.md) en maak een nieuwe directory */Home/site/wwwroot/apm*.
4. Upload de Java-Agent bestanden naar een map onder */Home/site/wwwroot/apm*. De bestanden voor uw agent moeten in */Home/site/wwwroot/apm/appdynamics*zijn.
5. In de Azure Portal, bladert u naar uw toepassing in App Service en maakt u een nieuwe toepassings instelling.
    - Als u **Java SE**gebruikt, maakt u een omgevings variabele met de naam `JAVA_OPTS` met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waarbij `<app-name>` uw app service naam is.
    - Als u **Tomcat**gebruikt, maakt u een omgevings variabele met de naam `CATALINA_OPTS` met de waarde `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` waarbij `<app-name>` uw app service naam is.
    - Als u **WildFly**gebruikt, raadpleegt u de AppDynamics-documentatie [hier](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) voor meer informatie over het installeren van de Java-Agent en de configuratie van JBoss.

>  Als u al een omgevings variabele hebt voor `JAVA_OPTS` of `CATALINA_OPTS`, voegt u de optie `-javaagent:/...` toe aan het einde van de huidige waarde.

## <a name="configure-jar-applications"></a>JAR-toepassingen configureren

### <a name="starting-jar-apps"></a>JAR-apps starten

App Service verwacht dat uw JAR-toepassing standaard de naam *app. jar*heeft. Als het deze naam heeft, wordt deze automatisch uitgevoerd. Voor maven-gebruikers kunt u de JAR-naam instellen door `<finalName>app</finalName>` op te nemen in de `<build>` sectie van uw *pom. XML*. [U kunt hetzelfde doen in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) door de eigenschap `archiveFileName` in te stellen.

Als u een andere naam voor uw JAR wilt gebruiken, moet u ook de [opstart opdracht](app-service-linux-faq.md#built-in-images) opgeven waarmee het jar-bestand wordt uitgevoerd. Bijvoorbeeld `java -jar my-jar-app.jar`. U kunt de waarde voor de opstart opdracht in de Portal instellen, onder Configuratie > Algemene instellingen, of met een toepassings instelling met de naam `STARTUP_COMMAND`.

### <a name="server-port"></a>Server poort

App Service Linux stuurt binnenkomende aanvragen naar poort 80, dus uw toepassing moet ook op poort 80 worden geluisterd. U kunt dit doen in de configuratie van uw toepassing (zoals het bestand *toepassing. Properties* ) of in de opstart opdracht (bijvoorbeeld `java -jar spring-app.jar --server.port=80`). Raadpleeg de volgende documentatie voor algemene Java-frameworks:

- [Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Framework afspelen](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Gegevensbronnen

### <a name="tomcat"></a>Tomcat

Deze instructies zijn van toepassing op alle database verbindingen. U moet tijdelijke aanduidingen vullen met de naam van de stuurprogrammanaam en het JAR-bestand van uw gekozen data base. Gegeven is een tabel met klasse-namen en down loads van Stuur Programma's voor algemene data bases.

| Database   | Naam stuur programma klasse                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Downloaden](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Downloaden](https://dev.mysql.com/downloads/connector/j/) (Selecteer ' platform onafhankelijk ') |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Downloaden](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Als u Tomcat wilt configureren voor het gebruik van Java Data Base Connectivity (JDBC) of de Java Persistence API (JPA), moet u eerst de `CATALINA_OPTS` omgevings variabele aanpassen die in Tomcat wordt gelezen tijdens het opstarten. Stel deze waarden in via een app-instelling in de [maven-invoeg toepassing van app service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Of stel de omgevings variabelen in op de pagina **configuratie** - > **Toepassings instellingen** in de Azure Portal.

Bepaal vervolgens of de gegevens bron beschikbaar moet zijn voor één toepassing of voor alle toepassingen die worden uitgevoerd op de Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Gegevens bronnen op toepassings niveau

1. Maak een *context. XML-* bestand in de *META-INF/* map van uw project. Maak de *META-INF/-* map als deze niet bestaat.

2. Voeg in *context. XML*een `Context`-element toe om de gegevens bron te koppelen aan een JNDI-adres. Vervang de tijdelijke aanduiding `driverClassName` door de naam van de klasse van het stuur programma uit de bovenstaande tabel.

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

1. Kopieer de inhoud van */usr/local/Tomcat/conf* naar */home/tomcat/conf* op uw app service Linux-exemplaar met SSH als u nog geen configuratie hebt.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Voeg een context element toe aan de *server. XML* binnen het `<Server>`-element.

    ```xml
    <Server>
    ...
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
    ...
    </Server>
    ```

3. Werk de *Web. XML* van uw toepassing bij om de gegevens bron in uw toepassing te gebruiken.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
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

    U kunt ook een FTP-client gebruiken om het JDBC-stuur programma te uploaden. Volg deze [instructies voor het ophalen van uw FTP-referenties](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Als u een gegevens bron op server niveau hebt gemaakt, start u de App Service Linux-toepassing opnieuw. Met Tomcat wordt `CATALINA_BASE` opnieuw ingesteld op `/home/tomcat` en wordt de bijgewerkte configuratie gebruikt.

### <a name="spring-boot"></a>Spring Boot

Om verbinding te maken met gegevens bronnen in veer boot-toepassingen, wordt u geadviseerd om verbindings reeksen te maken en deze in uw toepassing te injecteren *. eigenschappen* bestand.

1. Stel in het gedeelte ' configuratie ' van de pagina App Service een naam in voor de teken reeks, plak uw JDBC-connection string in het waardeveld en stel het type in op aangepast. U kunt deze connection string eventueel instellen als sleuf instelling.

    Deze connection string is toegankelijk voor onze toepassing als een omgevings variabele met de naam `CUSTOMCONNSTR_<your-string-name>`. De connection string die hierboven zijn gemaakt, krijgen bijvoorbeeld de naam `CUSTOMCONNSTR_exampledb`.

2. In uw *toepassing. Properties* -bestand verwijst u naar deze Connection String met de naam van de omgevings variabele. In ons voor beeld gebruiken we het volgende.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Raadpleeg de [Spring boot-documentatie over gegevens toegang](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) en [externe configuraties](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) voor meer informatie over dit onderwerp.

## <a name="configure-java-ee-wildfly"></a>Java-EE (WildFly) configureren

> [!NOTE]
> Java Enter prise Edition op App Service Linux is momenteel beschikbaar als preview-versie. Deze stack wordt **niet** aanbevolen voor productie gerichte werkzaamheden.

Met Azure App Service op Linux kunnen Java-Ontwikkel aars Java-toepassingen (Java-EE) bouwen, implementeren en schalen op een volledig beheerde, op Linux gebaseerde service.  De onderliggende Java-bedrijfs runtime-omgeving is de open-source [WildFly](https://wildfly.org/) -toepassings server.

Deze sectie bevat de volgende subsecties:

- [Schalen met App Service](#scale-with-app-service)
- [De configuratie van de toepassings server aanpassen](#customize-application-server-configuration)
- [Modules en afhankelijkheden installeren](#install-modules-and-dependencies)
- [Gegevens bronnen configureren](#configure-data-sources)
- [Aanbieders van berichten inschakelen](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>Schalen met App Service

De WildFly-toepassings server in App Service in Linux wordt uitgevoerd in de zelfstandige modus, niet in een domein configuratie. Wanneer u het App Service plan uitbreidt, wordt elk WildFly-exemplaar geconfigureerd als een zelfstandige server.

Schaal uw toepassing verticaal of horizon taal met [schaal regels](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) en [Verhoog het aantal instanties](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>De configuratie van de toepassings server aanpassen

Web app-exemplaren zijn stateless, dus elke nieuwe instantie die is gestart, moet bij het opstarten worden geconfigureerd ter ondersteuning van de WildFly-configuratie die nodig is voor de toepassing.
U kunt een startup bash-script schrijven om de WildFly CLI aan te roepen naar:

- Gegevens bronnen instellen
- Aanbieders van berichten configureren
- Voeg andere modules en afhankelijkheden toe aan de WildFly-server configuratie.

Het script wordt uitgevoerd wanneer WildFly actief is, maar voordat de toepassing wordt gestart. Het script moet de [JBoss cli](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) van */opt/JBoss/wildfly/bin/JBoss-cli.sh* gebruiken om de toepassings server te configureren met elke configuratie of wijzigingen die nodig zijn nadat de server is gestart.

Gebruik niet de interactieve modus van de CLI om WildFly te configureren. In plaats daarvan kunt u een script met opdrachten aan de JBoss CLI geven met behulp van de opdracht `--file`, bijvoorbeeld:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Gebruik FTP om het opstart script te uploaden naar een locatie in uw App Service-exemplaar onder uw */Home* -Directory, zoals */Home/site/Deployments/tools*. Zie [uw app implementeren voor Azure app service met behulp van FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)voor meer informatie.

Stel het veld met het **opstart script** in de Azure Portal in op de locatie van het opstart shell-script, bijvoorbeeld */Home/site/Deployments/tools/your-startup-script.sh*.

Geef de [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in de toepassings configuratie op voor het door geven van omgevings variabelen voor gebruik in het script. Toepassings instellingen houden verbindings reeksen en andere geheimen op die nodig zijn voor het configureren van uw toepassing uit versie beheer.

### <a name="install-modules-and-dependencies"></a>Modules en afhankelijkheden installeren

Als u modules en hun afhankelijkheden wilt installeren in het WildFly klassenpad via de JBoss CLI, moet u de volgende bestanden in hun eigen map maken. Voor sommige modules en afhankelijkheden is mogelijk aanvullende configuratie vereist, zoals JNDI naam of andere API-specifieke configuratie. deze lijst is dus een minimale set van wat u nodig hebt om een afhankelijkheid in de meeste gevallen te configureren.

- Een [XML-module descriptor](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Dit XML-bestand definieert de naam, kenmerken en afhankelijkheden van uw module. In dit [voorbeeld bestand module. XML](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) wordt een post gres-module, het jar-bestand JDBC-afhankelijkheid en andere vereiste module-afhankelijkheden gedefinieerd.
- Alle benodigde bestands afhankelijkheden van JAR voor uw module.
- Een script met uw JBoss CLI-opdrachten voor het configureren van de nieuwe module. Dit bestand bevat de opdrachten die door de JBoss CLI moeten worden uitgevoerd om de server te configureren voor het gebruik van de afhankelijkheid. Raadpleeg [dit document](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)voor documentatie over de opdrachten voor het toevoegen van modules, gegevens bronnen en aanbieders van berichten.
- Een bash-opstart script voor het aanroepen van de JBoss CLI en het uitvoeren van het script in de vorige stap. Dit bestand wordt uitgevoerd wanneer uw App Service-exemplaar opnieuw wordt gestart of wanneer er nieuwe exemplaren worden ingericht tijdens een uitschalen. Met dit opstart script kunt u andere configuraties voor uw toepassing uitvoeren, omdat de JBoss-opdrachten worden door gegeven aan de JBoss-CLI. Dit bestand kan mini maal één opdracht zijn voor het door geven van uw JBoss CLI-opdracht script naar de JBoss CLI:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Zodra u de bestanden en inhoud voor uw module hebt, volgt u de onderstaande stappen om de module toe te voegen aan de WildFly-toepassings server.

1. Gebruik FTP om uw bestanden te uploaden naar een locatie in uw App Service-exemplaar onder uw */Home* -adres lijst, zoals */Home/site/Deployments/tools*. Zie [uw app implementeren voor Azure app service met behulp van FTP/S](../deploy-ftp.md)voor meer informatie.
2. Stel op de pagina **algemene instellingen** van **configuratie** > van de Azure Portal het veld **Startup script** in op de locatie van het opstart shell-script, bijvoorbeeld */Home/site/Deployments/tools/startup.sh*.
3. Start uw App Service-exemplaar opnieuw op door op de knop **opnieuw opstarten** in het gedeelte **overzicht** van de portal te klikken of de Azure CLI te gebruiken.

### <a name="configure-data-sources"></a>Gegevens bronnen configureren

Als u WildFly/JBoss wilt configureren voor toegang tot een gegevens bron, gebruikt u het algemene proces dat hierboven wordt beschreven in de sectie modules en afhankelijkheden installeren. In de volgende sectie vindt u specifieke informatie over dit proces voor PostgreSQL-, MySQL-en SQL Server-gegevens bronnen.

In deze sectie wordt ervan uitgegaan dat u al een app, een App Service exemplaar en een Azure data base service-exemplaar hebt. De onderstaande instructies verwijzen naar uw App Service naam, de resource groep en de verbindings gegevens van uw data base. U kunt deze informatie vinden op de Azure Portal.

Zie [zelf studie: een Java EE-en post gres-web-app bouwen in azure](tutorial-java-enterprise-postgresql-app.md)als u het hele proces wilt door lopen vanaf het begin met een voor beeld-app.

In de volgende stappen worden de vereisten voor het koppelen van uw bestaande App Service en data base uitgelegd.

1. Down load het JDBC-stuur programma voor [postgresql](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)of [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Pak het gedownloade archief uit om het bestand Driver. jar op te halen.

2. Maak een bestand met een naam als *module. XML* en voeg de volgende opmaak toe. Vervang de tijdelijke aanduiding voor `<module name>` (inclusief de punt haken) door `org.postgres` voor PostgreSQL, `com.mysql` voor MySQL of `com.microsoft` voor SQL Server. Vervang `<JDBC .jar file path>` door de naam van het jar-bestand uit de vorige stap, met inbegrip van het volledige pad naar de locatie waar u het bestand plaatst in uw App Service-exemplaar. Dit kan een wille keurige locatie zijn in de */Home* -map.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Maak een bestand met een naam zoals *DataSource-commands. cli* en voeg de volgende code toe. Vervang `<JDBC .jar file path>` door de waarde die u in de vorige stap hebt gebruikt. Vervang `<module file path>` door de bestands naam en het App Service pad uit de vorige stap, bijvoorbeeld */Home/module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Dit bestand wordt uitgevoerd door het opstart script dat in de volgende stap wordt beschreven. Hiermee wordt het JDBC-stuur programma geïnstalleerd als een WildFly-module, wordt de bijbehorende WildFly-gegevens bron gemaakt en wordt de server opnieuw geladen om ervoor te zorgen dat de wijzigingen van kracht worden.

4. Maak een bestand met een naam zoals *Startup.sh* en voeg de volgende code toe. Vervang `<JBoss CLI script>` door de naam van het bestand dat u in de vorige stap hebt gemaakt. Zorg ervoor dat u het volledige pad naar de locatie opgeeft waar u het bestand plaatst in uw App Service-exemplaar, bijvoorbeeld */Home/DataSource-commands.cli*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Gebruik FTP om het JDBC. jar-bestand, het XML-bestand van de module, het JBoss CLI-script en het opstart script naar uw App Service-exemplaar te uploaden. Plaats deze bestanden op de locatie die u in de vorige stappen hebt opgegeven, zoals */Home*. Zie [uw app implementeren voor Azure app service met behulp van FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)voor meer informatie over FTP.

6. Gebruik de Azure CLI om instellingen toe te voegen aan uw App Service die uw database verbindings gegevens bevatten. Vervang `<resource group>` en `<webapp name>` door de waarden die uw App Service gebruikt. Vervang `<database server name>`, `<database name>`, `<admin name>`en `<admin password>` door de verbindings gegevens van uw data base. U kunt uw App Service en Data Base-informatie ophalen uit de Azure Portal.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    De DATABASE_CONNECTION_URL waarden verschillen voor elke database server en verschillen van de waarden op de Azure Portal. De URL-indelingen die hier worden weer gegeven (en in de bovenstaande fragmenten) zijn vereist voor gebruik door WildFly:

    * **Postgresql:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Ga in het Azure Portal naar uw App Service en zoek de pagina **configuratie** > **algemene instellingen** . Stel het veld **Startup script** in op de naam en locatie van het opstart script, bijvoorbeeld */Home/startup.sh*.

De volgende keer dat uw App Service opnieuw wordt opgestart, wordt het opstart script uitgevoerd en worden de benodigde configuratie stappen uitgevoerd. Als u wilt testen of deze configuratie correct wordt uitgevoerd, hebt u via SSH toegang tot uw App Service en voert u het opstart script zelf uit vanaf de bash-prompt. U kunt ook de App Service-logboeken bekijken. Zie [Logboeken en fout opsporing in apps](#logging-and-debugging-apps)voor meer informatie over deze opties.

Vervolgens moet u de WildFly-configuratie voor uw app bijwerken en opnieuw implementeren. Voer de volgende stappen uit:

1. Open het bestand *src/main/resources/META-INF/persistentie. XML* voor uw app en zoek het `<jta-data-source>`-element. Vervang de inhoud zoals hier wordt weer gegeven:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Bouw en implementeer uw app opnieuw met behulp van de volgende opdracht in de bash-prompt:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Start uw App Service-exemplaar opnieuw op door op de knop **opnieuw opstarten** in het gedeelte **overzicht** van de Azure portal te drukken of door de Azure CLI te gebruiken.

Uw App Service-exemplaar is nu geconfigureerd voor toegang tot uw data base.

Zie [postgresql](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)of [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)voor meer informatie over het configureren van database connectiviteit met WildFly.

### <a name="enable-messaging-providers"></a>Aanbieders van berichten inschakelen

Voor het inschakelen van op berichten gebaseerde bonen met Service Bus als het bericht mechanisme:

1. Gebruik de [Apache QPID JMS Messa ging-bibliotheek](https://qpid.apache.org/proton/index.html). Neem deze afhankelijkheid op in uw pom. XML-bestand (of een ander buildbestand) voor de toepassing.

2. [Service Bus resources](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)maken. Maak een Azure Service Bus naam ruimte en een wachtrij in die naam ruimte en een gedeeld toegangs beleid met functies voor verzenden en ontvangen.

3. Geef de sleutel van het gedeelde toegangs beleid door aan uw code door de primaire sleutel van uw beleid te coderen of door [de service bus SDK te gebruiken](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Volg de stappen die worden beschreven in de sectie modules en afhankelijkheden installeren met uw module XML-descriptor, jar-afhankelijkheden, JBoss CLI-opdrachten en opstart script voor de JMS-provider. Naast de vier bestanden moet u ook een XML-bestand maken waarin de naam van de JNDI voor de JMS-wachtrij en het onderwerp wordt gedefinieerd. Zie [deze opslag plaats](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) voor referentie configuratie bestanden.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Redis als sessie cache gebruiken met Tomcat

U kunt Tomcat configureren voor het gebruik van een externe sessie opslag, zoals [Azure cache voor redis](/azure/azure-cache-for-redis/). Zo kunt u de status van de gebruikers sessie (zoals de gegevens van de winkel wagen) behouden wanneer een gebruiker wordt overgezet naar een ander exemplaar van uw app, bijvoorbeeld wanneer automatisch schalen, opnieuw opstarten of failover wordt uitgevoerd.

Als u Tomcat met redis wilt gebruiken, moet u uw app configureren voor het gebruik van een [PersistentManager](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) -implementatie. In de volgende stappen wordt dit proces uitgelegd met behulp van [Pivot Session Manager: redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) als voor beeld.

1. Open een bash-Terminal en gebruik `export <variable>=<value>` om elk van de volgende omgevings variabelen in te stellen.

    | Variabele                 | Waarde                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | De naam van de resource groep met uw App Service-exemplaar.       |
    | WEBAPP_NAME              | De naam van uw App Service-exemplaar.                                     |
    | WEBAPP_PLAN_NAME         | De naam van uw App Service-abonnement                                          |
    | REGIO                   | De naam van de regio waar uw app wordt gehost.                           |
    | REDIS_CACHE_NAME         | De naam van uw Azure-cache voor redis-instantie.                           |
    | REDIS_PORT               | De SSL-poort waarop uw redis-cache wordt geluisterd.                             |
    | REDIS_PASSWORD           | De primaire toegangs sleutel voor uw exemplaar.                                  |
    | REDIS_SESSION_KEY_PREFIX | Een waarde die u opgeeft om sessie sleutels te identificeren die afkomstig zijn van uw app. |

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

8. Werk de configuratie van de `azure-webapp-maven-plugin` in het *pom. XML-* bestand van uw app bij om te verwijzen naar uw redis-account gegevens. In dit bestand worden de omgevings variabelen gebruikt die u eerder hebt ingesteld om uw account gegevens uit uw bron bestanden te beveiligen.

    Indien nodig wijzigt u `1.7.0` in de huidige versie van de [maven-invoeg toepassing voor Azure app service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

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

9. Bouw en implementeer uw app opnieuw.

    ```bash
    mvn package
    mvn azure-webapp:deploy
    ```

Uw app maakt nu gebruik van uw redis-cache voor sessie beheer.

Voor een voor beeld dat u kunt gebruiken om deze instructies te testen, raadpleegt u de [schaal baarheid-stateful-Java-Web-app-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) opslag plaats op github.

## <a name="docker-containers"></a>Docker-containers

Als u de door Azure ondersteunde Zulu JDK in uw containers wilt gebruiken, moet u ervoor zorgen dat u de vooraf gemaakte installatie kopieën ophaalt en gebruikt, zoals beschreven op de [pagina ondersteunde Azul Zulu Enter prise for Azure down load](https://www.azul.com/downloads/azure-only/zulu/) of de `Dockerfile`-voor beelden uit de [micro soft Java github opslag plaats](https://github.com/Microsoft/java/tree/master/docker)gebruiken.

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

## <a name="next-steps"></a>Volgende stappen

Ga naar [Azure voor Java-ontwikkelaars](/java/azure/) centrum voor meer informatie over Azure Quick starts, zelf studies en naslag informatie voor Java.

Algemene vragen over het gebruik van App Service voor Linux die niet specifiek zijn voor de Java-ontwikkeling, worden beantwoord in de [Veelgestelde vragen over app service Linux](app-service-linux-faq.md).
