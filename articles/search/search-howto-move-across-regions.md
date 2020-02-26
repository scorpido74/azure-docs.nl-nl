---
title: Uw service resource verplaatsen tussen regio's
titleSuffix: Azure Cognitive Search
description: In dit artikel wordt uitgelegd hoe u uw Azure Cognitive Search-resources verplaatst van de ene regio naar de andere in de Azure-Cloud.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599299"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Verplaats uw Azure Cognitive Search-service naar een andere Azure-regio

Als u uw Azure cognitieve service-account wilt verplaatsen van de ene regio naar een andere, maakt u een export sjabloon om uw abonnement (en) te verplaatsen. Nadat u uw abonnement hebt verplaatst, moet u uw gegevens verplaatsen en uw service opnieuw maken.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een sjabloon exporteren.
> * Wijzig de sjabloon: het toevoegen van de doel regio, de namen van de zoek-en opslag accounts.
> * Implementeer de sjabloon voor het maken van de nieuwe zoek-en opslag accounts.
> * Controleer de status van uw service in de nieuwe regio
> * Resources opschonen in de bron regio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt worden ondersteund in de doel regio.

- Zorg ervoor dat uw abonnement white list is voor de doel regio voor preview-functies. Zie voor meer informatie over preview-functies [kennis winkels](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incrementele verrijking](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)en [persoonlijk eind punt](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Beoordeling en planning

Wanneer u uw zoek service naar de nieuwe regio verplaatst, moet u [uw gegevens naar de nieuwe opslag service verplaatsen](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) en vervolgens uw indexen, vaardig heden en kennis winkels opnieuw samen stellen. U moet de huidige instellingen vastleggen en json-bestanden kopiëren om het opnieuw samen stellen van uw service gemakkelijker en sneller te maken.

## <a name="moving-your-search-services-resources"></a>De resources van uw zoek service verplaatsen

Als u wilt beginnen, gaat u een resource manager-sjabloon exporteren en wijzigen.

### <a name="export-a-template"></a>Een sjabloon exporteren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Ga naar de pagina van de resource groep.

> [!div class="mx-imgBorder"]
> ![pagina voor beeld van een resource groep](./media/search-move-resource/export-template-sample.png)

3. Selecteer **Alle resources**.

3. Selecteer in het navigatie menu links **sjabloon exporteren**.

4. Kies **downloaden** op de pagina **sjabloon exporteren** .

5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze.

Het zip-bestand bevat de. json-bestanden waaruit de sjabloon en scripts bestaan voor het implementeren van de sjabloon.

### <a name="modify-the-template"></a>De sjabloon aanpassen

U gaat de sjabloon wijzigen door de namen en regio's van de zoek-en opslag accounts te wijzigen. De namen moeten voldoen aan de regels voor elke naam Conventie van service en regio. 

Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **centrale amerikaanse** = **centralus**.

1. Selecteer in Azure Portal **Een resource maken**.

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie**. Druk vervolgens op **ENTER**.

3. Selecteer **Sjabloonimplementatie**.

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **bestand laden**en volg de instructies voor het laden van het **sjabloon. json** -bestand dat u in de vorige sectie hebt gedownload en uitgepakt.

7. Geef in het bestand **Template. json** de doel-en opslag accounts een naam door de standaard waarde van de namen van de zoek-en opslag account in te stellen. 

8. Bewerk de eigenschap **Location** in het bestand **Template. json** in de doel regio voor uw zoek-en opslag Services. In dit voor beeld wordt de doel regio ingesteld op `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
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

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Sla het bestand **Template. json** op.

2. Voer de eigenschaps waarden in of Selecteer deze:

- **Subscription**: selecteer een Azure-abonnement.

- **Resourcegroep**: selecteer **Nieuwe maken** en geef de resourcegroep een naam.

- **Locatie**: Selecteer een Azure-locatie.

3. Klik op het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden** en klik vervolgens op de knop **aankoop selecteren** .

## <a name="verifying-your-services-status-in-new-region"></a>De status van uw Services verifiëren in de nieuwe regio

Als u de verplaatsing wilt controleren, opent u de nieuwe resource groep en worden uw services weer gegeven in de nieuwe regio.

Als u uw gegevens van de bron regio naar de doel regio wilt verplaatsen, raadpleegt u de richt lijnen in dit artikel voor [het verplaatsen van uw gegevens naar het nieuwe opslag account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Resources in de oorspronkelijke regio opschonen

Als u de wijzigingen wilt door voeren en de verplaatsing van uw service account wilt volt ooien, verwijdert u het bron service account.

## <a name="next-steps"></a>Volgende stappen

[Een index maken](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Een vaardig heden maken](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Een kennis archief maken](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

