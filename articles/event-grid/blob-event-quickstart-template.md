---
title: Blob-opslaggebeurtenissen naar webeindpunt verzenden - sjabloon
description: Gebruik Azure Event Grid en een Azure Resource Manager-sjabloon om blob-opslagaccount te maken en de gebeurtenissen ervan te abonneren. Stuur de evenementen naar een Webhook.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/27/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: subject-armqs
ms.openlocfilehash: 86dc7a4ed05ceae5c7a641ffef23bd75ec48ceea
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605523"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Blob-opslaggebeurtenissen routeren naar webeindpunt met azure resourcemanager-sjabloon

Azure Event Grid is een gebeurtenisservice voor de cloud. In dit artikel gebruikt u een Sjabloon azure resource beheer om een **Blob-opslagaccount** te maken, u te abonneren op gebeurtenissen voor die blob-opslag en een gebeurtenis te activeren om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van dit artikel stuurt u hier de gebeurtenissen echter naar een web-app die de berichten verzamelt en weergeeft.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

### <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich kunt abonneren op gebeurtenissen voor de Blob-opslag, moet u het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

    [Implementeren in Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

1. De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

   ![Nieuwe site weergeven](./media/blob-event-quickstart-portal/view-site.png)

## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Een opslagaccount maken met een Event Grid-abonnement

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)

[!code-json[<Azure Resource Manager template create Blob storage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

In de sjabloon worden twee Azure-resources gedefinieerd:

* [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)maak een Azure Storage-account.
* [**"Microsoft.Storage/storageAccounts/providers/eventSubscriptions:**](/azure/templates/microsoft.eventgrid/eventsubscriptions)maak een Azure Event Grid-abonnement voor het opslagaccount.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling om u aan te melden bij Azure en open een sjabloon. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Geef het **eindpunt**op: geef de URL `api/updates` van uw web-app op en voeg toe aan de URL van de startpagina.
3. Selecteer **Kopen** om de sjabloon te implementeren.

  De Azure-portal wordt hier gebruikt om de sjabloon te implementeren. U ook de Azure PowerShell-, Azure CLI- en REST-API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie over andere implementatiemethoden.

> [!NOTE]
> Meer sjabloonvoorbeelden voor Azure Event Grid vindt [u hier.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid)

## <a name="validate-the-deployment"></a>De implementatie valideren

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

![Een abonnementgebeurtenis weergeven](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd.

U activeert een gebeurtenis voor de Blob-opslag door een bestand te uploaden. Het bestand heeft geen specifieke inhoud nodig. In de artikelen wordt ervan uitgegaan dat u een bestand met de naam testfile.txt hebt, maar het bestand kan elke willekeurige naam hebben.

Wanneer u het bestand uploadt naar de Azure Blob-opslag, stuurt gebeurtenisraster een bericht naar het eindpunt dat u hebt geconfigureerd wanneer u zich abonneert. Het bericht is in de JSON-indeling en bevat een array met een of meer gebeurtenissen. In het volgende voorbeeld bevat het JSON-bericht een array met één gebeurtenis. Bekijk uw web-app en u ziet dat er een gebeurtenis Blob gemaakt is ontvangen.

![Resultaten weergeven](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, [verwijdert u de brongroep](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure Resource Manager-sjablonen:

* [Azure Resource Manager-documentatie](/azure/azure-resource-manager)
* [Resources definiëren in Azure Resource Manager-sjablonen](/azure/templates/)
* [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/)
* [Azure Event Grid-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
