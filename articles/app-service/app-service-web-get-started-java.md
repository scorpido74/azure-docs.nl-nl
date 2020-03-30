---
title: 'Snelstart: een Java-app maken op Windows'
description: Implementeer uw eerste Java Hello World in een paar minuten naar Azure App Service op Windows. De Azure Web App Plugin voor Maven maakt het handig om Java-apps te implementeren.
keywords: azure, app service, web app, windows, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ed477e3c1431048d60e4a696f59aa0593e1b3f1f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136326"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Snelstart: een Java-app maken op Azure App Service op Windows

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Windows. Als u wilt implementeren op App-service op _Linux,_ raadpleegt u [Java-web-app maken op Linux](./containers/quickstart-java.md).
>

[Azure App Service](overview.md) biedt een zeer schaalbare, zelfpatchende webhostingservice.  In deze quickstart wordt uitgelegd hoe u de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) gebruiken met de Azure [Web App-plug-in voor Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) om een War-bestand (Java-webarchief) te implementeren.

> [!NOTE]
> Hetzelfde kan ook worden gedaan met behulp van populaire IdEs zoals IntelliJ en Eclipse. Bekijk onze vergelijkbare documenten op [Azure Toolkit voor IntelliJ Quickstart](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) of [Azure Toolkit voor Eclipse Quickstart.](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)
>
![Voorbeeld-app die wordt uitgevoerd in Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Een Java-app maken

Voer de volgende Maven opdracht uit in de Cloud Shell-prompt om een ​​nieuwe app met de naam `helloworld` te maken:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>De Maven-invoegtoepassing configureren

Om te implementeren vanuit Maven, gebruikt u de code-editor in de Cloud Shell om het projectbestand `pom.xml` in de map `helloworld` te openen. 

```bash
code pom.xml
```

Voeg vervolgens de volgende invoegtoepassingsdefinitie toe aan het element `<build>` van het bestand `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> In dit artikel werken we alleen met Java-apps die verpakt zijn in WAR-bestanden. De invoegtoepassing biedt ook ondersteuning voor JAR-webtoepassingen. Ga naar [Een Java SE JAR-bestand implementeren in App Service in Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om dit uit te proberen.


Werk de volgende tijdelijke aanduidingen bij in de configuratie van de invoegtoepassing:

| Tijdelijke aanduiding | Beschrijving |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | De unieke ID van het abonnement waarop u uw app wilt implementeren. De ID van het standaardabonnement is te vinden `az account show` in de Cloud Shell of CLI met behulp van de opdracht. Gebruik de `az account list` opdracht voor alle beschikbare abonnementen.|
| `RESOURCEGROUP_NAME` | Naam voor de nieuwe resourcegroep waarin de app moet worden gemaakt. Door alle resources voor een app in een groep te plaatsen, kunt u ze samen beheren. Als u de resourcegroep verwijdert, worden bijvoorbeeld alle resources verwijderd die bij de app behoren. Werk deze waarde bij met een unieke naam van de nieuwe resourcegroep, bijvoorbeeld *myResourceGroup*. U gebruikt deze resourcegroepnaam om alle Azure-resources in een volgende sectie op te schonen. |
| `WEBAPP_NAME` | De naam van de app maakt deel uit van de hostnaam voor de app wanneer deze wordt geïmplementeerd in Azure (WEBAPP_NAME.azurewebsites.net). Wijzig deze waarde in een unieke naam voor de nieuwe App Service-app, die uw Java-app host, bijvoorbeeld *contoso*. |
| `REGION` | Een Azure-regio waar de app wordt gehost, bijvoorbeeld *westus2*. U kunt een lijst met regio's van de Cloud Shell of CLI ophalen met behulp van de opdracht `az account list-locations`. |

## <a name="deploy-the-app"></a>De app implementeren

Implementeer uw Java-app in Azure met de volgende opdracht:

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, bladert u naar de geïmplementeerde toepassing met behulp van de volgende URL in uw webbrowser, bijvoorbeeld `http://<webapp>.azurewebsites.net/`.

![Voorbeeld-app die wordt uitgevoerd in Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service op Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Verbinding maken met Azure SQL-database met Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor MySQL met Java](/azure/mysql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor PostgreSQL met Java](/azure/postgresql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Azure voor Java-ontwikkelaarsbronnen](/java/azure/)

> [!div class="nextstepaction"]
> [Aangepast domein toewijzen](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Meer informatie over Maven-plug-ins voor Azure](https://github.com/microsoft/azure-maven-plugins)
