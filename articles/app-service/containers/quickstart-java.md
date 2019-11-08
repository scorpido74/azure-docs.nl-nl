---
title: Een Java-Web-app maken op Linux-Azure App Service
description: In deze quickstart implementeert u in een paar minuten uw eerste Java-app (Hallo wereld) in Azure App Service in Linux.
keywords: Azure, app service, Web-app, Linux, Java, Maven, Quick Start
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 3b011d3d7dc881d44fdcafb29efacf9548866d7a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747729"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Quick Start: een Java-app maken op Azure App Service in Linux

[App Service in Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. In deze Quick start ziet u hoe u de [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) gebruikt met de [maven-invoeg toepassing voor Azure app service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) een WAR-bestand (Java Web Archive) op het Linux-besturings systeem implementeert.

> [!NOTE]
>
> Dit kan ook worden gedaan met populaire Ide's zoals IntelliJ en eclips. Bekijk onze vergelijk bare documenten op [Azure-Toolkit voor IntelliJ Quick](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) start of [Azure-Toolkit voor eclipse Quick](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)start.
>
![Voor beeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Een Java-app maken

Voer de volgende Maven opdracht uit in de Cloud Shell-prompt om een ​​nieuwe app met de naam `helloworld` te maken:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>De Maven-invoegtoepassing configureren

Het implementatie proces voor Azure App Service maakt gebruik van account referenties van de Azure CLI. [Meld u aan met de Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voordat u doorgaat.

```azurecli
az login
```

Vervolgens kunt u de implementatie configureren, de Maven-opdracht uitvoeren `mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config` in de opdracht prompt en de standaard configuraties gebruiken door op **Enter** te drukken totdat u de prompt **bevestigen (j/N)** krijgt en vervolgens op **y** drukt en de configuratie wordt uitgevoerd.

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
3. WILDFLY 14
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

 Eigenschap | Vereist | Beschrijving | Versie
---|---|---|---
`<schemaVersion>` | onwaar | Geef de versie van het configuratie schema op. Ondersteunde waarden zijn: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Azure-resource groep voor uw web-app. | 0.1.0 +
`<appName>` | true | De naam van uw web-app. | 0.1.0 +
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Hiermee geeft u de regio waar uw web-app wordt gehost. de standaard waarde is **Europa West**. Alle geldige regio's in de sectie [ondersteunde regio's](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) . | 0.1.0 +
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | onwaar | De prijs categorie voor uw web-app. De standaard waarde is **P1V2**.| 0.1.0 +
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | De configuratie van de runtime-omgeving, u kunt [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)de details zien. | 0.1.0 +
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | De implementatie configuratie, kunt u [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting)de details zien. | 0.1.0 +

## <a name="deploy-the-app"></a>De app implementeren

Implementeer uw Java-app in Azure met de volgende opdracht:

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, bladert u naar de geïmplementeerde toepassing met behulp van de volgende URL in uw webbrowser, bijvoorbeeld `http://<webapp>.azurewebsites.net`. 

![Voor beeld-app die wordt uitgevoerd in Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service on Linux.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Java Enter prise-app met PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Java-app configureren](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD met Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Andere bronnen van Azure voor Java-Ontwikkel aars](/java/azure/)
