---
title: Een kennisarchief (voorbeeld) weergeven met Storage Explorer
titleSuffix: Azure Cognitive Search
description: Een Azure Cognitive Search-kennisarchief weergeven en analyseren met de Storage Explorer van de Azure-portal. kennisop te slaan is momenteel in openbare preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754061"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Een kenniswinkel weergeven met Storage Explorer

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

In dit artikel leert u bijvoorbeeld hoe u verbinding maken met een kennisarchief en een kennisarchief verkennen met Storage Explorer in de Azure-portal.

## <a name="prerequisites"></a>Vereisten

+ Volg de stappen in [Een kennisarchief maken in azure-portal](knowledge-store-create-portal.md) om de voorbeeldkenniswinkel te maken die in deze walkthrough wordt gebruikt.

+ U hebt ook de naam nodig van het Azure-opslagaccount dat u hebt gebruikt om de kenniswinkel te maken, samen met de toegangssleutel van de Azure-portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Een kennisarchief weergeven, bewerken en opvragen in Storage Explorer

1. Open in de Azure-portal [het opslagaccount](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) dat u hebt gebruikt om de kenniswinkel te maken.

1. Klik in het linkernavigatiedeelvenster van het opslagaccount op **Storage Explorer**.

1. Vouw de lijst **TABELLEN** uit om een lijst met Azure-tabelprojecties weer te geven die zijn gemaakt toen u de wizard **Gegevens importeren** op de voorbeeldgegevens van uw hotelbeoordelingen gebruikte.

Selecteer een tabel om de verrijkte gegevens weer te geven, inclusief belangrijke zinnen en sentimentscores.

   ![Tabellen weergeven in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Tabellen weergeven in Storage Explorer")

Als u het gegevenstype voor een tabelwaarde wilt wijzigen of afzonderlijke waarden in de tabel wilt wijzigen, klikt u op **Bewerken**. Wanneer u het gegevenstype voor een kolom in één tabelrij wijzigt, wordt deze op alle rijen toegepast.

   ![Tabel bewerken in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Tabel bewerken in Storage Explorer")

Als u query's wilt uitvoeren, klikt u op **Query** op de opdrachtbalk en voert u de voorwaarden in.  

   ![Querytabel in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Querytabel in Storage Explorer")

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Verbind deze kenniswinkel met Power BI voor een diepere analyse of ga verder met code, met behulp van de REST API en Postman om een andere kenniswinkel te maken.

> [!div class="nextstepaction"]
> [Maak verbinding met Power BI](knowledge-store-connect-power-bi.md)
> [Maak een kenniswinkel in REST](knowledge-store-create-rest.md)
