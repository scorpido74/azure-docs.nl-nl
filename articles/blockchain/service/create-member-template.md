---
title: Een lid van de Azure Block Chain-service maken met behulp van Azure Resource Manager-sjabloon
description: Meer informatie over hoe u een lid van de Azure Block Chain-service maakt met behulp van Azure Resource Manager-sjabloon.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: db5cabd82ebfed3b1de7659e4b9450ccee0ff001
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82115292"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Snelstartgids: een lid van de Azure Block Chain-service maken met behulp van een Azure Resource Manager sjabloon

In deze Quick Start implementeert u een nieuw Block Chain-lid en consortium in azure Block Chain service met behulp van een Azure Resource Manager sjabloon.

Een lid van de Azure Block Chain-service is een Block Chain-knoop punt in een particulier consortium Block chain-netwerk. Bij het inrichten van een lid kunt u een consortium netwerk maken of eraan toevoegen. U hebt ten minste één lid nodig voor een consortium netwerk. Het aantal Block Chain-leden dat nodig is voor deel nemers is afhankelijk van uw scenario. Consortium deelnemers kunnen een of meer Block Chain leden hebben of ze kunnen leden delen met andere deel nemers. Zie [Azure Block Chain Service consortium](consortium.md)voor meer informatie over consortiums.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/)start-sjablonen.

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Azure-resources die in de sjabloon zijn gedefinieerd:

* [**Micro soft. Block Chain/blockchainMembers**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling om u aan te melden bij Azure en een sjabloon te openen.

    [![Implementeren in Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Geef de instellingen voor het lid van de Azure Block Chain-service op.

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw service. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | Maak een nieuwe resource groep of kies een bestaande naam uit uw abonnement.
    Locatie | Kies een locatie om het lid te maken. Alle leden van het consortium moeten zich op dezelfde locatie bestaan. Beschik bare locaties voor de implementatie zijn *Europa West, Oost, southeastasia, Europa West, northeurope, westus2*en *japaneast*.
    De naam van lid | Kies een unieke naam voor het lid van de Azure Block Chain-service. De naam van het block Chain-lid mag alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet tussen 2 en 20 tekens lang zijn.
    Consortium naam | Voer een unieke naam in. Zie [Azure Block Chain Service consortium](consortium.md)voor meer informatie over consortiums.
    Wacht woord voor lid | Het wacht woord van het lid-account wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor uw lid wordt gemaakt. U gebruikt het wacht woord voor het account en het lid van het account voor het beheer van consortiums.
    SKU-laag | De prijs categorie voor uw nieuwe service. U kunt kiezen uit de lagen **Standard** en **Basic** . Gebruik de *basis* -laag voor het ontwikkelen, testen en testen van concepten. De *Standard* -laag gebruiken voor implementaties van productie cijfers. U moet ook de laag *standaard* gebruiken als u Block Chain Data Manager gebruikt of een groot aantal privé transacties verzendt. Het wijzigen van de prijs categorie tussen basis en standaard nadat het maken van een lid is niet ondersteund.
    SKU-naam | De knooppunt configuratie en de kosten voor uw nieuwe service.

1. Selecteer **aanschaffen** om de sjabloon te implementeren.

  De Azure Portal wordt hier gebruikt om de sjabloon te implementeren. U kunt ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie voor meer informatie over andere implementatie methoden [sjablonen implementeren](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt het block Chain-lid gebruiken dat u hebt gemaakt voor de volgende Snelstartgids of zelf studie. Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de resource groep te verwijderen die u hebt gemaakt voor de Snelstartgids.

De resourcegroep verwijderen:

1. Ga in het Azure Portal naar de **resource groep** in het navigatie deel venster links en selecteer de resource groep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Controleer het verwijderen door de naam van de resource groep in te voeren en **verwijderen**te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lid van de Azure Block Chain-service en een nieuw consortium geïmplementeerd. Probeer de volgende Snelstartgids om Azure Block Chain Development Kit te gebruiken voor Ethereum om te koppelen aan een lid van de Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om verbinding te maken met de Azure Block Chain-Service](connect-vscode.md)