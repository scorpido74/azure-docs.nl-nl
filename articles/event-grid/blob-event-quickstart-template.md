---
title: Blob Storage-gebeurtenissen verzenden naar web-eind punt-sjabloon
description: Gebruik Azure Event Grid en een Azure Resource Manager sjabloon voor het maken van een Blob Storage-account en Abonneer u op de gebeurtenissen. De gebeurtenissen verzenden naar een webhook.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/27/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: subject-armqs
ms.openlocfilehash: 343ed57c87ea6df5db4cde0978132af31419f905
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303338"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Blob Storage-gebeurtenissen naar een webeindpunt routeren met behulp van Azure Resource Manager sjabloon

Azure Event Grid is een gebeurtenisservice voor de cloud. In dit artikel gebruikt u een **Azure Resource Manager sjabloon** voor het maken van een Blob Storage-account, het abonneren op gebeurtenissen voor die Blob-opslag en het activeren van een gebeurtenis om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van dit artikel stuurt u hier de gebeurtenissen echter naar een web-app die de berichten verzamelt en weergeeft.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

### <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich kunt abonneren op gebeurtenissen voor de Blob-opslag, moet u het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

    [Implementeren naar Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

1. De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

   ![Nieuwe site weergeven](./media/blob-event-quickstart-portal/view-site.png)

## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Een opslag account maken met een Event Grid-abonnement

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)start-sjablonen.

[!code-json[<Azure Resource Manager template create Blob storage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Micro soft. Storage/Storage accounts**](/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken.
* [ **"Micro soft. Storage/Storage accounts/providers/eventSubscriptions**](/azure/templates/microsoft.eventgrid/eventsubscriptions): een Azure Event grid-abonnement maken voor het opslag account.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json"><img src="./media/blob-event-quickstart-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Geef het **eind punt**op: Geef de URL van uw web-app op en voeg `api/updates` toe aan de URL van de start pagina.
3. Selecteer **aanschaffen** om de sjabloon te implementeren.

  De Azure Portal wordt hier gebruikt om de sjabloon te implementeren. U kunt ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie voor meer informatie over andere implementatie methoden [sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> [Hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid)vindt u meer Azure Event grid sjabloon voorbeelden.

## <a name="validate-the-deployment"></a>De implementatie valideren

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

![Een abonnementgebeurtenis weergeven](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd.

U activeert een gebeurtenis voor de Blob-opslag door een bestand te uploaden. Het bestand heeft geen specifieke inhoud nodig. In de artikelen wordt ervan uitgegaan dat u een bestand met de naam testfile.txt hebt, maar het bestand kan elke willekeurige naam hebben.

Wanneer u het bestand uploadt naar de Azure Blob-opslag, stuurt Event Grid een bericht naar het eind punt dat u hebt geconfigureerd bij het abonneren. Het bericht bevindt zich in de JSON-indeling en bevat een matrix met een of meer gebeurtenissen. In het volgende voor beeld bevat het JSON-bericht een matrix met één gebeurtenis. Bekijk uw web-app en u ziet dat er een gebeurtenis Blob gemaakt is ontvangen.

![Resultaten weergeven](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, [verwijdert u de resource groep](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure Resource Manager sjablonen:

* [Documentatie over Azure Resource Manager](/azure/azure-resource-manager)
* [Resources definiëren in Azure Resource Manager sjablonen](/azure/templates/)
* [Sjablonen voor Azure Quick Start](https://azure.microsoft.com/resources/templates/)
* [Azure Event grid sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
