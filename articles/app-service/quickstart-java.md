---
title: 'Quickstart: Een Java web-app maken in Azure App Service'
description: Implementeer in enkele minuten uw eerste Java Hallo wereld in Azure App Service. De Maven-invoegtoepassing voor Azure Web Apps maakt het eenvoudig om Java-apps te implementeren.
keywords: azure, app service, web app, windows, linux, java, maven, quickstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182694"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Quickstart: Een Java web-app maken in Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.  In deze quickstart ziet u hoe u de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) met de [Maven-invoegtoepassing voor Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) gebruikt om een webarchiefbestand (WAR) voor Java te implementeren.

> [!NOTE]
> In dit artikel werken we alleen met Java-apps die verpakt zijn in WAR-bestanden. De invoegtoepassing biedt ook ondersteuning voor JAR-webtoepassingen. Ga naar [Een Java SE JAR-bestand implementeren in App Service in Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om dit uit te proberen.

> [!NOTE]
> Dit kan ook worden gedaan met populaire IDE's zoals IntelliJ en Eclipse. Bekijk onze vergelijkbare documenten via [Quickstart: Azure-toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) of [Quickstart: Azure-toolkit voor Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Voorbeeld-app uitgevoerd in Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

U kunt de onderstaande Maven-opdracht uitvoeren om de implementatie te configureren.
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
U wordt gevraagd om het volgende te selecteren: 
* **OS (standaardwaarde: `linux`)**
* **Java Version (standaardwaarde: `1.8`)**
* **Web Container (standaardwaarde: `tomcat 8.5`)** 
 
Zorg dat u **`2`** invoert om in de eerste stap **Windows** als het besturingssysteem (OS) te kiezen. De andere configuraties kunt u op de standaardwaarde laten staan door op **Enter** te drukken. Druk ten slotte op **`Y`** bij de prompt **Confirm (Y/N)** om de configuratie te voltooien.

Een voorbeeldproces ziet er als volgt uit:

```console
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
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

U wordt gevraagd om het volgende te selecteren: 
* **OS (standaardwaarde: `linux`)**
* **Java Version (standaardwaarde: `Java 8`)**
* **Web Container (standaardwaarde: `Tomcat 8.5`)** 

Alle configuraties kunt u op de standaardwaarde laten staan door op **Enter** te drukken. Druk ten slotte op **`Y`** bij de prompt **Confirm (Y/N)** om de configuratie te voltooien.
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
::: zone-end

U kunt de configuraties voor App Service indien nodig rechtstreeks in uw `pom.xml` wijzigen. Dit zijn een aantal algemene instellingen:

 Eigenschap | Vereist | Beschrijving | Versie
---|---|---|---
`<schemaVersion>` | false | Geef de versie van het configuratieschema op. Ondersteunde waarden zijn: `v1` of `v2`. | 1.5.2
`<resourceGroup>` | true | Azure-resourcegroep voor uw web-app. | 0.1.0+
`<appName>` | true | De naam is van uw web-app. | 0.1.0+
`<region>` | true | Hiermee geeft u de regio op waar uw web-app wordt gehost. De standaard waarde is **westeurope**. Alle geldige regio's staan in de sectie [Ondersteunde regio's](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | De prijscategorie voor uw web-app. De standaardwaarde is **P1V2**.| 0.1.0+
`<runtime>` | true | De configuratie van de runtime-omgeving. U kunt u de details [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) bekijken. | 0.1.0+
`<deployment>` | true | De implementatieconfiguratie. U kunt de details [hier](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) bekijken. | 0.1.0+

Let op de waarden voor `<appName>` en `<resourceGroup>`(`helloworld-1590394316693` en `helloworld-1590394316693-rg` in de demo) aangezien deze later worden gebruikt.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>De app implementeren

Bij het implementeren naar Azure App Service worden accountreferenties uit de Azure CLI gebruikt. [Meld u aan met Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voordat u doorgaat.

```azurecli
az login
```
Implementeer vervolgens uw Java-app in Azure met de volgende opdracht:

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, is uw toepassing beschikbaar op `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` in de demo). Open de URL met uw lokale webbrowser. U ziet dan het volgende als het goed is:

![Voorbeeld-app uitgevoerd in Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service.

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
> [Resources voor Azure voor Java-ontwikkelaars](/java/azure/)

> [!div class="nextstepaction"]
> [Java-app configureren](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD met Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Aangepast domein toewijzen](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Meer informatie over Maven-invoegtoepassingen voor Azure](https://github.com/microsoft/azure-maven-plugins)
