---
title: 'Zelf studie: Java Enter prise-app in Linux'
description: Meer informatie over het verkrijgen van een Java-bedrijfs-app in Wildfly op Azure App Service op Linux, met verbinding met een PostgreSQL-data base in Azure.
author: JasonFreeberg
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 84f22d52e9a92707a26a4e64f194e82cca87757d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687436"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Zelfstudie: een Java EE- en Postgres-web-app in Azure maken

In deze zelf studie leert u hoe u een web-app voor Java Enter prise Edition (EE) op Azure App Service maakt en hoe u deze kunt verbinden met een post gres-data base. Wanneer u klaar bent, hebt u een [WildFly](https://www.wildfly.org/about/)-toepassing die gegevens opslaat in [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/) en wordt uitgevoerd op [Azure App Service voor Linux](app-service-linux-intro.md).

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Java EE-app in Azure implementeren via Maven
> * Een Postgres-database maken in Azure
> * De WildFly-server configureren om Postgres te gebruiken
> * De app bijwerken en opnieuw implementeren
> * Moduletests uitvoeren op WildFly

## <a name="prerequisites"></a>Vereisten

1. [Download en installeer Git](https://git-scm.com/)
2. [Download and install Maven 3](https://maven.apache.org/install.html) (Maven downloaden en installeren)
3. [Azure CLI downloaden en installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Voorbeeld-app klonen en bewerken

In deze stap kloont u de voorbeeld toepassing en configureert u het Maven-project object model (POM of *pom. XML*) voor implementatie.

### <a name="clone-the-sample"></a>Het voorbeeld klonen

Navigeer naar een werkmap in het terminalvenster en kloon de [voorbeeldopslagplaats](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Maven-POM bijwerken

Werk de Maven-invoegtoepassing van Azure bij met de gewenste naam en resourcegroep van uw App Service. U hoeft niet van tevoren het App Service-plan of -exemplaar te maken. Met de Maven-invoegtoepassing worden de resourcegroep en App Service gemaakt indien deze nog niet aanwezig zijn.

U kunt naar beneden scrollen naar de sectie `<plugins>` van *pom.xml*, regel 200, om de wijzigingen te maken.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Vervang `YOUR_APP_NAME` en `YOUR_RESOURCE_GROUP` met de namen van uw App Service en de resourcegroep.

## <a name="build-and-deploy-the-application"></a>De toepassing compileren en implementeren

We gaan Maven gebruiken om de toepassing te maken en in App Service te implementeren.

### <a name="build-the-war-file"></a>Het WAR-bestand maken

De POM in dir project wordt geconfigureerd om de toepassing in te pakken in een webarchiefbestand (WAR). De toepassing maken met Maven:

```bash
mvn clean install -DskipTests
```

De testscenario's in deze toepassing zijn bedoeld om te worden uitgevoerd als de toepassing in WildFly wordt geïmplementeerd. De tests die lokaal worden gebouwd, worden overgeslagen. U voert de tests uit zodra de toepassing in App Service is geïmplementeerd.

### <a name="deploy-to-app-service"></a>Implementeren naar App Service

Nu het WAR-bestand klaar is, kan de Azure-invoegtoepassing worden gebruikt om het bestand in App Service te implementeren:

```bash
mvn azure-webapp:deploy
```

Als de implementatie klaar is, gaat u verder met de volgende stap.

### <a name="create-a-record"></a>Record maken

Open een browser en ga naar `https://<your_app_name>.azurewebsites.net/`. Gefeliciteerd, u hebt een Java EE-toepassing geïmplementeerd in Azure App Service.

De toepassing gebruikt nu een in-memory H2-database. Klik in de navigatiebalk op Beheerder en maak een nieuwe categorie. De record in uw in-memory database gaat verloren als het App Service-exemplaar opnieuw start. In de volgende stappen lost u dit op door een Postgres-database in Azure in te richten en WildFly te configureren om deze te gebruiken.

![Locatie knop Beheerder](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Postgres-database inrichten

Als u een post gres-database server wilt inrichten, opent u een Terminal en gebruikt u de opdracht [AZ post gres server Create](https://docs.microsoft.com/cli/azure/postgres/server) , zoals in het volgende voor beeld wordt weer gegeven. Vervang de tijdelijke aanduidingen (inclusief de punt haken) door de waarden van uw keuze met behulp van dezelfde resource groep die u eerder hebt ingesteld voor uw App Service-exemplaar. De door u verstrekte beheerders referenties bieden de mogelijkheid om toekomstige toegang in te stellen. Zorg er dus voor dat u ze noteert voor later gebruik.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Nadat u deze opdracht hebt uitgevoerd, bladert u naar de Azure Portal en navigeert u naar de post gres-data base. Als de blade actief is, kopieert u de waarden voor Servernaam en Aanmeldingsnaam van de serverbeheerder. Deze hebt u later nodig.

### <a name="allow-access-to-azure-services"></a>Toegang tot Azure-services toestaan

Zet in het paneel **Verbindingsbeveiliging** van de blade Azure-database de knop Toegang tot Azure-services toestaan in de positie **AAN**.

![Toegang tot Azure-services toestaan](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>De Java-app bijwerken voor Postgres

We gaan nu enkele wijzigingen aan de Java-toepassing aanbrengen, zodat deze gebruik kan maken van de Postgres-database.

### <a name="add-postgres-credentials-to-the-pom"></a>Referenties voor Postgres aan de POM toevoegen

Vervang in *pom.xml* de hoofdletterwaarden in de tijdelijke aanduiding door de naam van de Postgres-server, de aanmeldingsnaam van de beheerder en het wachtwoord. Deze velden bevinden zich binnen de Maven-invoegtoepassing van Azure. (Vervang `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`, en `YOUR_PG_PASSWORD` in de `<value>`-tags... niet binnen de `<name>`-tags!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Java Transaction API bijwerken

Vervolgens moet de configuratie van de Java Transaction API (JTA) worden bewerkt, zodat de Java-toepassing kan communiceren met Postgres in plaats van de in-memory H2-database die we eerder hebben gebruikt. Open een editor voor *src/main/resources/META-INF/persistence.xml*. Vervang de waarde voor `<jta-data-source>` door `java:jboss/datasources/postgresDS`. De XML van de JTA moet nu de volgende instelling hebben:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>WildFly-toepassingsserver configureren

Voordat de opnieuw geconfigureerde toepassing kan worden geïmplementeerd, moet de WildFly-toepassingsserver met de Postgres-module en de bijbehorende afhankelijkheden worden bijgewerkt. Meer informatie over de configuratie vindt [u op WildFly-server configureren](configure-language-java.md#configure-java-ee-wildfly).

Voor het configureren van de server zijn de vier bestanden in de *wildfly_config/* map vereist:

- **postgresql-42.2.5.jar**: dit JAR-bestand is het JDBC-stuurprogramma voor Postgres. Zie de [officiële website](https://jdbc.postgresql.org/index.html) (Engelstalig) voor meer informatie.
- **postgres-module.xml**: dit XML-bestand declareert een naam voor de Postgres-module (org.postgres). Het geeft teven de resources en afhankelijkheden op die nodig zijn voor de te gebruiken module.
- **jboss_cli_commands. cli**: dit bestand bevat configuratie opdrachten die worden uitgevoerd door de JBoss cli. Met de opdrachten voegt u de post gres-module toe aan de WildFly-toepassings server, geeft u de referenties op, declareert u een JNDI-naam, stelt u de drempel waarde voor time-out, enzovoort Zie de [officiële documentatie](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)als u niet bekend bent met de JBoss cli.
- **startup_script.sh**: ten slotte wordt dit script uitgevoerd als uw App Service-exemplaar wordt gestart. Het script voert slechts één functie uit: sluizen de opdrachten in *jboss_cli_commands. cli* naar de JBoss cli.

U wordt ten sterkste aangeraden de inhoud van deze bestanden te lezen, met name *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>Configuratiebestanden via FTP verzenden

De inhoud van *wildfly_config/* naar ons app service exemplaar moet worden FTP. U kunt uw FTP-referenties ophalen door op de knop **Publicatieprofiel ophalen** te klikken in de App Service-blade in de Azure-portal. De gebruikersnaam en het wachtwoord voor de FTP staan in het gedownloade XML-document. Zie [dit document](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials) voor meer informatie over het publicatieprofiel.

Gebruik een FTP-hulp programma voor het overdragen van de vier bestanden in *wildfly_config/* naar */Home/site/Deployments/tools/* . (Draag alleen de bestanden over, niet de hele map.)

### <a name="finalize-app-service"></a>App Service voltooien

Ga in de App Service-blade naar het paneel Toepassingsinstellingen. Stel onder runtime het veld opstart bestand in op */home/site/deployments/tools/startup_script. sh*. Dit zorgt ervoor dat het shell script wordt uitgevoerd nadat het App Service exemplaar is gemaakt, maar voordat de WildFly-server wordt gestart.

Start ten slotte App Service opnieuw. De knop bevindt zich in het paneel Overzicht.

## <a name="redeploy-the-application"></a>De toepassing opnieuw implementeren

U kunt de toepassing opnieuw samenstellen en implementeren in een terminalvenster.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Gefeliciteerd! Uw toepassing gebruikt nu een post gres-data base en eventuele records die in de toepassing worden gemaakt, worden opgeslagen in post gres in plaats van de vorige H2-in-memory database. U kunt dit controleren door een record te maken en App Service opnieuw te starten. De records zijn nog steeds aanwezig als u de toepassing opnieuw start.

## <a name="clean-up"></a>Opruimen

Als u deze resources niet voor een andere zelfstudie nodig hebt (zie Volgende stappen), kunt u ze verwijderen door de volgende opdracht uit te voeren:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Java EE-app in Azure implementeren via Maven
> * Een Postgres-database maken in Azure
> * De WildFly-server configureren om Postgres te gebruiken
> * De app bijwerken en opnieuw implementeren
> * Moduletests uitvoeren op WildFly

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Java-app configureren](configure-language-java.md)
