---
title: Een Azure Blockchain Service-lid maken met azure resource manager-sjabloon
description: Meer informatie over het maken van een Azure Blockchain Service-lid met azure resource manager-sjabloon.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: 8fd5a1775a6a217ce9dd8c80439dd2c54f9cd727
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087595"
---
# <a name="create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Een Azure Blockchain Service-lid maken met behulp van een Azure Resource Manager-sjabloon

In deze quickstart implementeert u een nieuw blockchain-lid en consortium in Azure Blockchain Service met behulp van een Azure Resource Manager-sjabloon.

Een Azure Blockchain Service-lid is een blockchain-knooppunt in een blockchain-netwerk voor een privéconsortium. Wanneer u een lid indient, u een consortiumnetwerk maken of lid worden. U hebt ten minste één lid nodig voor een consortiumnetwerk. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortiumdeelnemers kunnen een of meer blockchain-leden hebben of leden delen met andere deelnemers. Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/)

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Azure-resources die in de sjabloon zijn gedefinieerd:

* [**Microsoft.Blockchain/blockchainLeden**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling om u aan te melden bij Azure en open een sjabloon.

    [![Implementeren in Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Geef de instellingen op voor het Azure Blockchain Service-lid.

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u voor uw service wilt gebruiken. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | Maak een nieuwe naam van de brongroep of kies een bestaande naam uit uw abonnement.
    Locatie | Kies een locatie om het lid te maken. Alle leden van het consortium moeten zich op dezelfde locatie bevinden. Beschikbare locaties voor de inzet zijn *West-Europa, Eastus, Southeastasia, West-Europa, Noord-Europa, Westus2*, en *Japaneast*.
    De naam van lid | Kies een unieke naam voor het Azure Blockchain Service-lid. De naam van het blockchain-lid kan alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet tussen de 2 en 20 tekens lang zijn.
    Consortiumnaam | Voer een unieke naam in. Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia.
    Lidmaatschapswachtwoord | Het wachtwoord van het ledenaccount wordt gebruikt om de privésleutel voor het Ethereum-account dat voor uw lid is gemaakt, te versleutelen. U gebruikt het accountaccount en het wachtwoord van het ledenaccount voor consortiumbeheer.
    Sku-laag | De prijscategorie voor uw nieuwe service. Kies tussen **standaard-** en **basislagen.** Gebruik de *basislaag* voor ontwikkeling, testen en proof of concepts. Gebruik de *standaardlaag* voor implementaties van productiekwaliteit. U moet ook de *standaardlaag* gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privétransacties verzendt. Het wijzigen van de prijslaag tussen basis en standaard na het maken van leden wordt niet ondersteund.
    Sku-naam | De knooppuntconfiguratie en kosten voor uw nieuwe service.

1. Selecteer **Kopen** om de sjabloon te implementeren.

  De Azure-portal wordt hier gebruikt om de sjabloon te implementeren. U ook de Azure PowerShell-, Azure CLI- en REST-API gebruiken. Zie [Sjablonen implementeren](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie over andere implementatiemethoden.

## <a name="clean-up-resources"></a>Resources opschonen

U het blockchain-lid gebruiken dat u hebt gemaakt voor de volgende quickstart of zelfstudie. Wanneer dit niet meer nodig is, u de resources verwijderen door de resourcegroep die u voor de quickstart hebt gemaakt, te verwijderen.

De resourcegroep verwijderen:

1. Navigeer in de Azure-portal naar **de groep Resource** in het linkernavigatiedeelvenster en selecteer de resourcegroep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Verwijder het verwijderen door de naam van de brongroep in te voeren en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Blockchain Service-lid en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om Azure Blockchain Development Kit voor Ethereum te gebruiken om te koppelen aan een Azure Blockchain Service-lid.

> [!div class="nextstepaction"]
> [Visual Studio-code gebruiken om verbinding te maken met Azure Blockchain-service](connect-vscode.md)