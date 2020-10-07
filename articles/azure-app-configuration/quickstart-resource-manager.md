---
title: 'Quickstart: automatische VM-implementatie met Azure App Configuration'
description: In deze quickstart ziet u hoe u de Azure PowerShell-module en Azure Resource Manager-sjablonen kunt gebruiken om een Azure App Configuration-archief te implementeren. Gebruik vervolgens de waarden in het archief om een VM te implementeren.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88661466"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Quickstart: Geautomatiseerde VM-implementatie met App Configuration en Resource Manager-sjabloon (ARM-sjabloon)

Meer informatie over het gebruik van Azure Resource Manager-sjablonen en Azure PowerShell voor het implementeren van een Azure App Configuration-archief, het toevoegen van sleutelwaarden aan het archief en het gebruik van de sleutelwaarden in het archief voor het implementeren van een Azure-resource, zoals een virtuele machine van Azure in dit voorbeeld.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-templates"></a>De sjablonen controleren

De sjablonen die in deze snelstart worden gebruikt, komen uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Met de [eerste sjabloon](https://azure.microsoft.com/resources/templates/101-app-configuration-store/) maakt u een App Configuration-archief:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

Er is één Azure-resource gedefinieerd in de sjabloon:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): een App Configuration-archief maken.

Met de [tweede sjabloon](https://azure.microsoft.com/resources/templates/101-app-configuration/) wordt een virtuele machine gemaakt met behulp van de sleutelwaarden in het archief. Voor deze stap moet u sleutelwaarden toevoegen met behulp van de portal of Azure CLI.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>De sjablonen implementeren

### <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon wordt een resource voor een App Configuration-archief gemaakt.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden.

    - **abonnement**: selecteer het Azure-abonnement dat u hebt gebruikt om het App Configuration-archief te maken.
    - **Resourcegroep**: selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken, tenzij u een bestaande resourcegroep wilt gebruiken.
    - **Regio**: selecteer een locatie voor de resourcegroep.  Bijvoorbeeld **VS - Oost**.
    - **Naam van het Configuration-archief**: voer een nieuwe naam in voor het App Configuration-archief.
    - **Locatie**: geef de locatie op van het App Configuration-archief.  Gebruik de standaardwaarde.
    - **SKU-naam**: geef de SKU-naam op van het App Configuration-archief. Gebruik de standaardwaarde.

1. Selecteer **Controleren + maken**.
1. Controleer of **Validatie is geslaagd** wordt weergegeven op de pagina en selecteer vervolgens **Maken**.

Noteer de naam van de resourcegroep en de naam van het App Configuration-archief.  U hebt deze waarden nodig wanneer u de virtuele machine gaat implementeren
### <a name="add-vm-configuration-key-values"></a>Sleutelwaarden voor de VM-configuratie toevoegen

Nadat u een App Configuration-archief hebt gemaakt, kunt u Azure Portal of Azure CLI gebruiken om sleutelwaarden toe te voegen aan het archief.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer vervolgens naar het zojuist gemaakte App Configuration-archief.
1. Selecteer **Configuratieverkenner** in het menu links.
1. Selecteer **Maken** om de volgende sleutel-waardeparen toe te voegen:

   |Sleutel|Waarde|Label|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|sjabloon|
   |diskSizeGB|1023|sjabloon|

   Laat **Inhoudstype** leeg.

Raadpleeg [Werken met sleutelwaarden in een Azure App Configuration-archief](./scripts/cli-work-with-keys.md) voor meer informatie over Azure CLI.

### <a name="deploy-vm-using-stored-key-values"></a>Een VM implementeren met behulp van gearchiveerde sleutelwaarden

Nu u sleutelwaarden aan het archief hebt toegevoegd, kunt u een VM implementeren met behulp van een Azure Resource Manager-sjabloon. De sjabloon verwijst naar de sleutels **windowsOsVersion** en **diskSizeGB** die u hebt gemaakt.

> [!WARNING]
> ARM-sjablonen kunnen niet verwijzen naar sleutels in een App Configuration-archief waarvoor Private Link is ingeschakeld.

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon maakt u een virtuele machine met behulp van opgeslagen sleutelwaarden in het App Configuration-archief.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden.

    - **abonnement**: selecteer het Azure-abonnement dat u hebt gebruikt om de virtuele machine te maken.
    - **Resourcegroep**: geef dezelfde resourcegroep op als het App Configuration-archief of selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken.
    - **Regio**: selecteer een locatie voor de resourcegroep.  Bijvoorbeeld **VS - Oost**.
    - **Locatie**: geef de locatie van de virtuele machine op. gebruik de standaardwaarde.
    - **Gebruikersnaam van beheerder**: geef een gebruikersnaam op voor de beheerder van de virtuele machine.
    - **Beheerderswachtwoord**: geef een beheerderswachtwoord op voor de virtuele machine.
    - **Label domeinnaam**: geef een unieke domeinnaam op.
    - **Naam van het opslagaccount**: geef een unieke naam op voor een opslagaccount dat is gekoppeld aan de virtuele machine.
    - **Resourcegroep van het App Configuration-archief**: geef de resourcegroep op die het App Configuration-archief bevat.
    - **Naam van het App Configuration-archief**: geef de naam op van het Azure App Configuration-archief.
    - **VM SKU-sleutel**: geef **windowsOsVersion** op.  Dit is de naam van de sleutelwaarde die u hebt toegevoegd aan het archief.
    - **Sleutel schijfgrootte**: geef **diskSizeGB** op. Dit is de naam van de sleutelwaarde die u hebt toegevoegd aan het archief.

1. Selecteer **Controleren + maken**.
1. Controleer of **Validatie is geslaagd** wordt weergegeven op de pagina en selecteer vervolgens **Maken**.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer vervolgens naar de zojuist gemaakte virtuele machine.
1. Selecteer **Overzicht** in het menu links en controleer of de **SKU** **2019-Datacenter** is.
1. Selecteer **Schijven** in het menu links en controleer of de grootte van de gegevensschijf **2013** is.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, het App Configuration-archief en alle gerelateerde resources. Als u van plan bent om het App Configuration-archief of de virtuele machine in de toekomst te gebruiken, kunt u het verwijderen overslaan. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze quickstart zijn gemaakt door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een virtuele machine geïmplementeerd met behulp van een Azure Resource Manager-sjabloon en de sleutelwaarden van Azure App Configuration.

Ga door naar het volgende artikel voor meer informatie over het maken van andere toepassingen met Azure App Configuration:

> [!div class="nextstepaction"]
> [Quickstart: Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)
