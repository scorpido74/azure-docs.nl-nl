---
title: Uw service resource verplaatsen tussen regio's
titleSuffix: Azure Cognitive Search
description: In dit artikel wordt uitgelegd hoe u uw Azure Cognitive Search-resources verplaatst van de ene regio naar de andere in de Azure-Cloud.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80246299"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Verplaats uw Azure Cognitive Search-service naar een andere Azure-regio

Af en toe vragen klanten om een zoek service te verplaatsen naar een andere regio. Er is momenteel geen ingebouwd mechanisme of hulp programma beschikbaar voor deze taak, maar in dit artikel krijgt u inzicht in de hand matige stappen om hetzelfde resultaat te bereiken.

> [!NOTE]
> In de Azure Portal hebben alle services een opdracht **sjabloon exporteren** . In het geval van Azure Cognitive Search, wordt met deze opdracht een basis definitie van een service (naam, locatie, laag, replica en aantal partities) gemaakt, maar wordt de inhoud van uw service niet herkend en worden er geen sleutels, rollen of Logboeken uitgevoerd. Hoewel de opdracht bestaat, is het niet raadzaam om deze te gebruiken voor het verplaatsen van een zoek service.

## <a name="guidance-for-moving-a-service"></a>Richt lijnen voor het verplaatsen van een service

1. Identificeer afhankelijkheden en gerelateerde services om inzicht te krijgen in de volledige impact van het verplaatsen van een service. voor het geval moet u meer dan alleen Azure Cognitive Search.

   Azure Storage wordt gebruikt voor logboek registratie, het maken van een kennis archief en is een veelgebruikte externe gegevens bron voor AI-verrijking en indexering. Cognitive Services is een afhankelijkheid in AI-verrijking. Zowel Cognitive Services als uw zoek service moeten zich in dezelfde regio bevinden als u gebruikmaakt van AI-verrijking.

1. Maak een inventarisatie van alle objecten op de service, zodat u weet wat u moet verplaatsen: indexen, synoniemen, Indexeer functies, gegevens bronnen, vaardig heden. Als u logboek registratie hebt ingeschakeld, maakt en archiveert u rapporten die u mogelijk nodig hebt voor een historische record.

1. Controleer de prijzen en beschik baarheid in de nieuwe regio om de beschik baarheid van Azure Cognitive Search plus alle gerelateerde services in de nieuwe regio te garanderen. De meeste functies zijn beschikbaar in alle regio's, maar sommige preview-functies hebben beperkte Beschik baarheid.

1. Maak een service in de nieuwe regio en publiceer opnieuw vanuit de bron code alle bestaande indexen, synoniemen, Indexeer functies, gegevens bronnen en vaardig heden. Houd er rekening mee dat service namen uniek moeten zijn, zodat u de bestaande naam niet opnieuw kunt gebruiken. Controleer elke vaardig heden om te zien of verbindingen met Cognitive Services nog steeds geldig zijn in termen van dezelfde-regio vereiste. Als er ook kennis archieven worden gemaakt, controleert u de verbindings reeksen voor Azure Storage als u een andere service gebruikt.

1. Herlaad indexen en kennis winkels, indien van toepassing. U gebruikt toepassings code om JSON-gegevens naar een index te pushen of om Indexeer functies opnieuw uit te voeren om documenten uit externe bronnen te halen. 

1. Schakel logboek registratie in en als u deze gebruikt, maakt u de beveiligings rollen opnieuw.

1. Update client toepassingen en test suites voor het gebruik van de nieuwe service naam en API-sleutels en test alle toepassingen.

1. Verwijder de oude service zodra de nieuwe service volledig getest en operationeel is.

## <a name="next-steps"></a>Volgende stappen

U kunt de volgende koppelingen gebruiken om meer informatie te vinden bij het uitvoeren van de hierboven beschreven stappen.

+ [Prijzen en regio's van Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Een laag kiezen](search-sku-tier.md)
+ [Een zoek service maken](search-create-service-portal.md)
+ [Zoek documenten laden](search-what-is-data-import.md)
+ [Logboekregistratie inschakelen](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->