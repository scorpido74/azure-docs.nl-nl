---
title: Azure Functions runtime-versies instellen
description: Azure Functions ondersteunt meerdere versies van de runtime. Meer informatie over het opgeven van de runtime versie van een functie-app die wordt gehost in Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77151952"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions runtime-versies instellen

Een functie-app wordt uitgevoerd op een specifieke versie van de Azure Functions runtime. Er zijn drie hoofd versies: [1. x, 2. x en 3. x](functions-versions.md). Functie-apps worden standaard gemaakt in versie 2. x van de runtime. In dit artikel wordt uitgelegd hoe u een functie-app in azure kunt configureren om uit te voeren op de versie die u kiest. Zie voor meer informatie over het configureren van een lokale ontwikkel omgeving voor een specifieke versie [code en testen Azure functions lokaal](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Automatische en hand matige versie-updates

Met Azure Functions kunt u een specifieke versie van de runtime richten met behulp van de `FUNCTIONS_EXTENSION_VERSION` toepassings instelling in een functie-app. De functie-app wordt op de opgegeven primaire versie bewaard totdat u expliciet naar een nieuwe versie gaat verplaatsen.

Als u alleen de primaire versie opgeeft, wordt de functie-app automatisch bijgewerkt naar nieuwe secundaire versies van de runtime wanneer deze beschikbaar komen. Nieuwe secundaire versies mogen geen belang rijke wijzigingen introduceren. Als u een secundaire versie opgeeft (bijvoorbeeld ' 2.0.12345 '), wordt de functie-app vastgemaakt aan die specifieke versie totdat u deze expliciet wijzigt.

> [!NOTE]
> Als u vastmaakt aan een specifieke versie van Azure Functions en vervolgens probeert te publiceren naar Azure met Visual Studio, wordt er een dialoog venster weer gegeven waarin u wordt gevraagd om bij te werken naar de nieuwste versie of de publicatie te annuleren. U kunt dit voor komen door de `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` eigenschap in het bestand toe te voegen `.csproj` .

Wanneer een nieuwe versie openbaar beschikbaar is, kunt u met een prompt in de portal naar de gewenste versie gaan. Nadat u naar een nieuwe versie hebt verplaatst, kunt u de `FUNCTIONS_EXTENSION_VERSION` toepassings instelling altijd gebruiken om terug te gaan naar een vorige versie.

In de volgende tabel ziet `FUNCTIONS_EXTENSION_VERSION` u de waarden voor elke primaire versie om automatische updates in te scha kelen:

| Primaire versie | `FUNCTIONS_EXTENSION_VERSION`Value |
| ------------- | ----------------------------------- |
| controleert  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Een wijziging in de runtime versie leidt ertoe dat een functie-app opnieuw wordt gestart.

## <a name="view-and-update-the-current-runtime-version"></a>De huidige runtime versie weer geven en bijwerken

U kunt de runtime versie die wordt gebruikt door uw functie-app wijzigen. U kunt de runtime-versie alleen wijzigen voordat u functies in uw functie-app hebt gemaakt. 

> [!IMPORTANT]
> Hoewel de runtime versie wordt bepaald door de `FUNCTIONS_EXTENSION_VERSION` instelling, moet u deze wijziging aanbrengen in de Azure Portal en niet door de instelling rechtstreeks te wijzigen. Dit komt doordat de portal uw wijzigingen valideert en andere gerelateerde wijzigingen indien nodig maakt.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Met behulp van de Azure Portal kunt u de runtime versie niet wijzigen voor een functie-app die al functies bevat.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Uit de Azure CLI

U kunt ook de `FUNCTIONS_EXTENSION_VERSION` van de Azure cli weer geven en instellen.

>[!NOTE]
>Omdat andere instellingen van invloed kunnen zijn op de runtime versie, moet u de versie in de portal wijzigen. In de portal worden de andere nood zakelijke updates automatisch uitgevoerd, zoals Node.js versie en runtime stack, wanneer u runtime versies wijzigt.  

Bekijk met behulp van de Azure CLI de huidige runtime versie met de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Vervang in deze code door `<function_app>` de naam van uw functie-app. Vervang ook door `<my_resource_group>` de naam van de resource groep voor uw functie-app. 

U ziet de `FUNCTIONS_EXTENSION_VERSION` in de volgende uitvoer, die is afgekapt voor duidelijkheid:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

U kunt de `FUNCTIONS_EXTENSION_VERSION` instelling in de functie-app bijwerken met de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Vervang door `<function_app>` de naam van uw functie-app. Vervang ook door `<my_resource_group>` de naam van de resource groep voor uw functie-app. Vervang ook door `<version>` een geldige versie van de 1. x-runtime of `~2` voor versie 2. x.

U kunt deze opdracht uitvoeren vanuit de [Azure Cloud shell](../cloud-shell/overview.md) door deze in het voor gaande code voorbeeld **te kiezen.** U kunt de [Azure cli ook lokaal](/cli/azure/install-azure-cli) gebruiken om deze opdracht uit te voeren nadat u [AZ login](/cli/azure/reference-index#az-login) hebt uitgevoerd om u aan te melden.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De 2,0-runtime in uw lokale ontwikkel omgeving richten](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zie de release opmerkingen voor runtime versies](https://github.com/Azure/azure-webjobs-sdk-script/releases)
