---
title: Een App Service-app maken met behulp van een Azure Resource Manager-sjabloon
description: Maak binnen enkele seconden uw eerste app voor Azure App Service met behulp van een Azure Resource Manager-sjabloon. Dit is een van de vele manieren om in App Service te implementeren.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653266"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Een App Service-app maken met behulp van een Azure Resource Manager-sjabloon

Aan de slag met [Azure App Service](overview.md) door een app te implementeren in de cloud met behulp van een Azure Resource Manager-sjabloon en de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in Cloud Shell. Omdat u een gratis servicelaag van App Service gebruikt, zijn er geen kosten verbonden aan het voltooien van deze quickstart.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Een Azure App Service-app maken

### <a name="review-the-template"></a>De sjabloon controleren

::: zone pivot="platform-windows"
De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://github.com/Azure/azure-quickstart-templates/). Hiermee wordt een App Service-plan en een App Service-app in Windows geïmplementeerd. De sjabloon is compatibel met .NET Core, .NET Framework, PHP, Node.js en statische HTML-apps. Raadpleeg [Java-app maken](app-service-web-get-started-java.md) voor Java. 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): een App Service-plan maken.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): een App Service-app maken.

Deze sjabloon bevat verschillende parameters die voor het gemak vooraf zijn gedefinieerd. Zie de onderstaande tabel voor de standaardwaarden van de parameters en de bijbehorende beschrijvingen:

| Parameters | Type     | Standaardwaarde                | Beschrijving |
|------------|---------|------------------------------|-------------|
| webAppName | tekenreeks  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Naam van app |
| location   | tekenreeks  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | App-regio |
| sku        | tekenreeks  | "F1"                         | Exemplaargrootte (F1 = gratis laag) |
| language   | tekenreeks  | ".net"                       | Stack met programmeertalen (.net, php, node, html) |
| helloWorld | booleaans | False                        | True = Hello World-app implementeren |
| repoUrl    | tekenreeks  | / ` ~ " ( ) ;                          | Externe Git-opslagplaats (optioneel) |
::: zone-end
::: zone pivot="platform-linux"
De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://github.com/Azure/azure-quickstart-templates/). Hiermee wordt een App Service-plan en een App Service-app in Linux geïmplementeerd. De sjabloon is compatibel met alle ondersteunde programmeertalen in App Service.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): een App Service-plan maken.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): een App Service-app maken.

Deze sjabloon bevat verschillende parameters die voor het gemak vooraf zijn gedefinieerd. Zie de onderstaande tabel voor de standaardwaarden van de parameters en de bijbehorende beschrijvingen:

| Parameters | Type     | Standaardwaarde                | Beschrijving |
|------------|---------|------------------------------|-------------|
| webAppName | tekenreeks  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Naam van app |
| location   | tekenreeks  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | App-regio |
| sku        | tekenreeks  | "F1"                         | Exemplaargrootte (F1 = gratis laag) |
| linuxFxVersion   | tekenreeks  | DOTNETCORE&#124;3.0        | Stack met programmeertalen &#124; versie |
| repoUrl    | tekenreeks  | / ` ~ " ( ) ;                          | Externe Git-opslagplaats (optioneel) |

---
::: zone-end


### <a name="deploy-the-template"></a>De sjabloon implementeren

Hier wordt de Azure CLI gebruikt om de sjabloon te implementeren. U kunt ook Azure Portal, Azure PowerShell, de REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden. 

Met de volgende code maakt u een resourcegroep, een App Service-plan en een web-app. Er zijn een standaardresourcegroep, een App Service-plan en een locatie voor u ingesteld. Vervang `<app-name>` door een globaal unieke app-naam (geldige tekens zijn `a-z`, `0-9` en `-`).

::: zone pivot="platform-windows"
Voer de volgende code uit om een .NET Framework-app in Windows te implementeren.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Als u een andere taalstack wilt implementeren, werkt u `linuxFxVersion` bij met de juiste waarden. Voorbeelden worden hieronder weergegeven. Als u huidige versies wilt weergeven, voert u de volgende opdracht in de Cloud Shell uit: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Taal    | Voorbeeld:                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> U vindt [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites) meer App Service-sjabloonvoorbeelden.


## <a name="validate-the-deployment"></a>De implementatie valideren

Blader naar `http://<app_name>.azurewebsites.net/` en controleer of deze is gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

[Verwijder de resourcegroep](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group) als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>Implementeren vanuit lokale Git

> [!div class="nextstepaction"]
>ASP.NET Core met SQL Database

> [!div class="nextstepaction"]
>Python met Postgres

> [!div class="nextstepaction"]
>PHP met MySQL

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure SQL-database met Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
>Aangepast domein toewijzen