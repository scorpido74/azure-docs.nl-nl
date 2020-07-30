---
title: 'Quickstart: Een Java-app maken in Linux'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste Java-app te implementeren in een Linux-container in App Service.
keywords: azure, app service, web-app, linux, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 92bfaa7718a8e094fcfc1479e9dba6790438f7a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322700"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Quickstart: Een Java web-app maken in Azure App Service in Linux

[App Service in Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. In deze quickstart ziet u hoe u de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) gebruikt met de [Azure Web App-invoegtoepassing voor Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) om een webarchiefbestand (WAR) voor Java te implementeren in het Linux-besturingssysteem.

> [!NOTE]
>
> Dit kunt u overigens ook doen met populaire IDE's zoals IntelliJ, Eclipse en VS Code. Bekijk onze vergelijkbare documenten via [Quickstart: Azure-toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app), [Quickstart: Azure-toolkit voor Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app) of [Quickstart voor VS Code](https://code.visualstudio.com/docs/java/java-webapp).
>
![Voorbeeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Een Java-app maken

Voer de volgende Maven opdracht uit in de Cloud Shell-prompt om een ​​nieuwe app met de naam `helloworld` te maken:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```
Wijzig vervolgens uw werkmap in de projectmap:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>De Maven-invoegtoepassing configureren

Bij het implementeren naar Azure App Service kunnen uw Azure-referenties automatisch worden opgehaald van de Azure CLI. Als Azure CLI niet is geïnstalleerd, wordt u door de Maven-invoegtoepassing aangemeld met OAuth of apparaataanmelding. Raadpleeg indien nodig de details van [verificatie met Maven-invoegtoepassingen](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Als u de implementatie wilt configureren, voert u de opdracht maven uit bij de opdrachtprompt en gebruikt u de standaardconfiguraties door op **Enter** te drukken totdat u bij de prompt **Confirm (Y/N)** bent aangekomen. Druk hier op **'y'** om te bevestigen en de configuratie te voltooien. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
Een voorbeeldproces ziet er als volgt uit:

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

Open `pom.xml` om de bijgewerkte configuratie weer te geven.

```bash
code pom.xml
```

U kunt de configuraties voor App Service indien nodig rechtstreeks in uw pom-bestand wijzigen. Dit zijn een aantal algemene instellingen:

 Eigenschap | Vereist | Beschrijving | Versie
---|---|---|---
`<schemaVersion>` | false | Geef de versie van het configuratieschema op. Ondersteunde waarden zijn: `v1` of `v2`. | 1.5.2
`<resourceGroup>` | true | Azure-resourcegroep voor uw web-app. | 0.1.0+
`<appName>` | true | De naam is van uw web-app. | 0.1.0+
`<region>` | true | Hiermee geeft u de regio op waar uw web-app wordt gehost. De standaard waarde is **westeurope**. Alle geldige regio's staan in de sectie [Ondersteunde regio's](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | De prijscategorie voor uw web-app. De standaardwaarde is **P1V2**.| 0.1.0+
`<runtime>` | true | De configuratie van de runtime-omgeving. U kunt u de details [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) bekijken. | 0.1.0+
`<deployment>` | true | De implementatieconfiguratie. U kunt de details [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) bekijken. | 0.1.0+

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>De app implementeren

Implementeer uw Java-app in Azure met de volgende opdracht:

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, bladert u naar de geïmplementeerde toepassing met behulp van de volgende URL in uw webbrowser, bijvoorbeeld `http://<webapp>.azurewebsites.net`. 

![Voorbeeld-app uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service on Linux.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep uit de portal of door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met Azure SQL Database met Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor MySQL met Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor PostgreSQL met Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Java-app configureren](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD met Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Andere informatiebronnen voor Azure voor Java-ontwikkelaars](/java/azure/)

> [!div class="nextstepaction"]
> [Meer informatie over Maven-invoegtoepassingen voor Azure](https://github.com/microsoft/azure-maven-plugins)
