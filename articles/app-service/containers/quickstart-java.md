---
title: 'Quickstart: Maak een Linux Java app'
description: Ga aan de slag met Linux-apps in Azure App Service door uw eerste Java-app te implementeren in een Linux-container in App Service.
keywords: azure, app service, web app, linux, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8f2e99ffc9f9ee5c5553e8d933d82f83999c8ab2
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732886"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Snelstart: maak een Java-app op Azure App Service op Linux

[App Service op Linux](app-service-linux-intro.md) biedt een zeer schaalbare, self-patching, web hosting service met behulp van het Linux-besturingssysteem. In deze quickstart wordt uitgelegd hoe u de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) gebruiken met de Azure [Web App-plug-in voor Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) om een War-bestand (Java-webarchief) te implementeren op het Linux-besturingssysteem.

> [!NOTE]
>
> Hetzelfde kan ook worden gedaan met behulp van populaire IdEs zoals IntelliJ, Eclipse en VS Code. Bekijk onze vergelijkbare documenten op [Azure Toolkit voor IntelliJ Quickstart](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), [Azure Toolkit voor Eclipse Quickstart](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) of [VS Code Quickstart](https://code.visualstudio.com/docs/java/java-webapp).
>
![Voorbeeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

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

Het implementatieproces voor Azure App Service maakt gebruik van accountreferenties van de Azure CLI. [Meld u aan bij de Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voordat u verdergaat.

```azurecli
az login
```

Vervolgens u de implementatie configureren, de opdracht maven uitvoeren in de opdrachtprompt en de standaardconfiguraties gebruiken door op **ENTER** te drukken totdat u de prompt **Bevestigen (Y/N)** krijgt en vervolgens op **'y'** drukken en de configuratie is uitgevoerd. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
Een voorbeeldproces ziet eruit als volgt:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
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

Navigeer `pom.xml` naar opnieuw om te zien dat de configuratie van de plug-in is bijgewerkt, U indien nodig andere configuraties voor App Service rechtstreeks in uw pom-bestand wijzigen, enkele veelvoorkomende zijn hieronder weergegeven:

 Eigenschap | Vereist | Beschrijving | Versie
---|---|---|---
`<schemaVersion>` | false | Geef de versie van het configuratieschema op. Ondersteunde waarden `v1`zijn: `v2`, . | 1.5.2
`<resourceGroup>` | waar | Azure Resource Group voor uw web-app. | 0,1.0+
`<appName>` | waar | De naam van uw web-app. | 0,1.0+
`<region>` | waar | Hiermee geeft u het gebied op waar uw web-app wordt gehost. de standaardwaarde is **West-Europa**. Alle geldige regio's in de sectie [Ondersteunde regio's.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0,1.0+
`<pricingTier>` | false | De prijscategorie voor uw web-app. De standaardwaarde is **P1V2**.| 0,1.0+
`<runtime>` | waar | De configuratie van de runtime-omgeving, u de details [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)zien. | 0,1.0+
`<deployment>` | waar | De implementatieconfiguratie, u de details [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)zien. | 0,1.0+

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>De app implementeren

Implementeer uw Java-app in Azure met de volgende opdracht:

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, bladert u naar de geïmplementeerde toepassing met behulp van de volgende URL in uw webbrowser, bijvoorbeeld `http://<webapp>.azurewebsites.net`. 

![Voorbeeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service on Linux.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze bronnen in de toekomst niet meer nodig verwacht, verwijdert u de brongroep uit portal of voert u de volgende opdracht uit in de Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met Azure SQL-database met Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor MySQL met Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor PostgreSQL met Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Java-app configureren](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD met Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Andere Azure for Java Developers Resources](/java/azure/)

> [!div class="nextstepaction"]
> [Meer informatie over Maven-plug-ins voor Azure](https://github.com/microsoft/azure-maven-plugins)
