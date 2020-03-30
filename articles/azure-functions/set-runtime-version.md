---
title: Azure-versies voor runtime targeten
description: Azure Functions ondersteunt meerdere versies van de runtime. Meer informatie over het opgeven van de runtime-versie van een functie-app die wordt gehost in Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151952"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure-versies voor runtime targeten

Een functie-app wordt uitgevoerd op een specifieke versie van de runtime van Azure Functions. Er zijn drie belangrijke versies: [1.x, 2.x en 3.x](functions-versions.md). Functie-apps worden standaard gemaakt in versie 2.x van de runtime. In dit artikel wordt uitgelegd hoe u een functie-app in Azure configureert om uit te voeren op de versie die u kiest. Zie [Azure-functies lokaal](functions-run-local.md)coderen en testen voor informatie over het configureren van een lokale ontwikkelomgeving voor een specifieke versie.

## <a name="automatic-and-manual-version-updates"></a>Automatische en handmatige versie-updates

Met Azure Functions u een specifieke versie `FUNCTIONS_EXTENSION_VERSION` van de runtime targeten met behulp van de toepassingsinstelling in een functie-app. De functie-app wordt op de opgegeven hoofdversie bewaard totdat u er expliciet voor kiest om naar een nieuwe versie te gaan.

Als u alleen de hoofdversie opgeeft, wordt de functie-app automatisch bijgewerkt naar nieuwe secundaire versies van de runtime wanneer deze beschikbaar zijn. Nieuwe secundaire versies mogen geen baanbrekende wijzigingen introduceren. Als u een secundaire versie opgeeft (bijvoorbeeld '2.0.12345'), wordt de functie-app vastgemaakt aan die specifieke versie totdat u deze expliciet wijzigt.

> [!NOTE]
> Als u vastmaakt aan een specifieke versie van Azure-functies en vervolgens probeert te publiceren naar Azure met Behulp van Visual Studio, verschijnt er een dialoogvenster waarin u wordt gevraagd om bij te werken naar de nieuwste versie of de publicatie te annuleren. Om dit te `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` voorkomen, `.csproj` voegt u de eigenschap in uw bestand toe.

Wanneer een nieuwe versie openbaar beschikbaar is, geeft een prompt in de portal u de kans om over te gaan naar die versie. Nadat u naar een nieuwe versie `FUNCTIONS_EXTENSION_VERSION` bent verhuisd, u altijd de toepassingsinstelling gebruiken om terug te gaan naar een vorige versie.

In de volgende `FUNCTIONS_EXTENSION_VERSION` tabel worden de waarden voor elke hoofdversie weergegeven om automatische updates in te schakelen:

| Belangrijke versie | `FUNCTIONS_EXTENSION_VERSION`Waarde |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Een wijziging in de runtime-versie zorgt ervoor dat een functie-app opnieuw wordt opgestart.

## <a name="view-and-update-the-current-runtime-version"></a>De huidige runtime-versie weergeven en bijwerken

U de runtime-versie wijzigen die wordt gebruikt door uw functie-app. Vanwege het potentieel van het verbreken van wijzigingen, u de runtime-versie alleen wijzigen voordat u functies in uw functie-app hebt gemaakt. 

> [!IMPORTANT]
> Hoewel de runtime-versie `FUNCTIONS_EXTENSION_VERSION` wordt bepaald door de instelling, moet u deze wijziging aanbrengen in de Azure-portal en niet door de instelling rechtstreeks te wijzigen. Dit komt omdat de portal uw wijzigingen valideert en indien nodig andere gerelateerde wijzigingen aanbrengt.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Met de Azure-portal u de runtime-versie voor een functie-app die al functies bevat, niet wijzigen.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Vanuit de Azure CLI

U de `FUNCTIONS_EXTENSION_VERSION` azure cli ook bekijken en instellen.

>[!NOTE]
>Omdat andere instellingen kunnen worden beïnvloed door de runtime-versie, moet u de versie in de portal wijzigen. De portal maakt automatisch de andere benodigde updates, zoals Node.js-versie en runtime stack, wanneer u runtime-versies wijzigt.  

Bekijk met de Azure CLI de huidige runtime-versie met de opdracht [az-functieapp config-appsettings.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Vervang `<function_app>` in deze code de naam van uw functie-app. Vervang `<my_resource_group>` ook de naam van de resourcegroep voor uw functie-app. 

U ziet `FUNCTIONS_EXTENSION_VERSION` de volgende uitvoer, die is afgekapt voor duidelijkheid:

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

Je `FUNCTIONS_EXTENSION_VERSION` de instelling in de functie-app bijwerken met de opdracht [az-functieapp config appsettings.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Vervang `<function_app>` door de naam van uw functie-app. Vervang `<my_resource_group>` ook de naam van de resourcegroep voor uw functie-app. Vervang ook `<version>` een geldige versie van de 1.x runtime of `~2` voor versie 2.x.

U deze opdracht uitvoeren vanuit de [Azure Cloud Shell](../cloud-shell/overview.md) door Try it te kiezen in **het** vorige codevoorbeeld. U de [Azure CLI](/cli/azure/install-azure-cli) ook lokaal gebruiken om deze opdracht uit te voeren nadat u [az-aanmelding](/cli/azure/reference-index#az-login) hebt uitgevoerd om u aan te melden.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Target de 2.0 runtime in uw lokale ontwikkelingsomgeving](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zie Notities vrijgeven voor runtime-versies](https://github.com/Azure/azure-webjobs-sdk-script/releases)
