---
title: 'Snelstartgids: een Linux java-app maken'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste Java-app te implementeren in een Linux-container in App Service.
keywords: Azure, app service, Web-app, Linux, Java, Maven, Quick Start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: faea0759f86e9d12530df6c647d903eacdade5c4
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228051"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Quick Start: een Java-app maken op Azure App Service in Linux

[App Service in Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. In deze Quick start ziet u hoe u de [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) gebruikt met de [Azure web app-invoeg toepassing voor maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) om een WAR-bestand (Java Web Archive) te implementeren in het Linux-besturings systeem.

> [!NOTE]
>
> Dit kan ook worden gedaan met populaire Ide's zoals IntelliJ, eclips en VS code. Bekijk onze gelijkaardige documenten op [Azure-Toolkit voor IntelliJ Quick](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)start, [Azure-Toolkit voor eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) Quick Start of de [VS code Quick](https://code.visualstudio.com/docs/java/java-webapp)start.
>
![Voor beeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Een Java-app maken

Voer de volgende Maven opdracht uit in de Cloud Shell-prompt om een ​​nieuwe app met de naam `helloworld` te maken:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```
Wijzig vervolgens uw werkmap in de projectmap:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>De Maven-invoegtoepassing configureren

Het implementatie proces voor Azure App Service maakt gebruik van account referenties van de Azure CLI. [Meld u aan met de Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voordat u doorgaat.

```azurecli
az login
```

Vervolgens kunt u de implementatie configureren, de Maven-opdracht uitvoeren in de opdracht prompt en de standaard configuraties gebruiken door op **Enter** te drukken totdat u de prompt **bevestigen (j/N)** krijgt en vervolgens op **y** drukt en de configuratie is voltooid. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
```
Een voorbeeld proces ziet er als volgt uit:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.8.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> In dit artikel werken we alleen met Java-apps die verpakt zijn in WAR-bestanden. De invoegtoepassing biedt ook ondersteuning voor JAR-webtoepassingen. Ga naar [Een Java SE JAR-bestand implementeren in App Service in Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om dit uit te proberen.

Ga opnieuw naar `pom.xml` om te zien of de configuratie van de invoeg toepassing is bijgewerkt, kunt u indien nodig ook andere configuraties voor App Service rechtstreeks in uw pom-bestand wijzigen:

 Eigenschap | Vereist | Beschrijving | Version
---|---|---|---
`<schemaVersion>` | onwaar | Geef de versie van het configuratie schema op. Ondersteunde waarden zijn: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Azure-resource groep voor uw web-app. | 0.1.0+
`<appName>` | true | De naam van uw web-app. | 0.1.0+
`<region>` | true | Hiermee geeft u de regio waar uw web-app wordt gehost. de standaard waarde is **Europa West**. Alle geldige regio's in de sectie [ondersteunde regio's](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . | 0.1.0+
`<pricingTier>` | onwaar | De prijs categorie voor uw web-app. De standaard waarde is **P1V2**.| 0.1.0+
`<runtime>` | true | De configuratie van de runtime-omgeving, u kunt [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)de details zien. | 0.1.0+
`<deployment>` | true | De implementatie configuratie, kunt u [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)de details zien. | 0.1.0+

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>De app implementeren

Implementeer uw Java-app in Azure met de volgende opdracht:

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, bladert u naar de geïmplementeerde toepassing met behulp van de volgende URL in uw webbrowser, bijvoorbeeld `http://<webapp>.azurewebsites.net`. 

![Voor beeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service on Linux.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources in de toekomst niet meer nodig hebt, verwijdert u de resource groep uit de portal of voert u de volgende opdracht uit in de Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met Azure SQL database met Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor MySQL met Java](/azure/mysql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor PostgreSQL met Java](/azure/postgresql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Java-app configureren](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD met Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Andere bronnen van Azure voor Java-Ontwikkel aars](/java/azure/)

> [!div class="nextstepaction"]
> [Meer informatie over maven-invoeg toepassingen voor Azure](https://github.com/microsoft/azure-maven-plugins)
