---
title: Een kennis archief (preview) weer geven met Storage Explorer
titleSuffix: Azure Cognitive Search
description: Bekijk en analyseer een Azure Cognitive Search Knowledge Store met de Storage Explorer van Azure Portal. Het kennis archief is momenteel beschikbaar als open bare preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2df05cf20ef51b2d5ca866f22bd9450dd6acaf
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406557"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Een kennis archief met Storage Explorer weer geven

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

In dit artikel leert u hoe u verbinding kunt maken met een kennis archief en hoe u deze kunt verkennen met behulp van Storage Explorer in de Azure Portal.

## <a name="prerequisites"></a>Vereisten

+ Volg de stappen in [een kennis archief maken in azure Portal](knowledge-store-create-portal.md) of [maak een Azure Cognitive Search Knowledge Store door rest te gebruiken](knowledge-store-create-rest.md) om het voor beeld van een kennis archief te maken dat wordt gebruikt in dit overzicht.

+ U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om het kennis archief te maken, samen met de toegangs sleutel van de Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Een kennis archief in Storage Explorer weer geven, bewerken en er een query op uitvoeren

1. Open in het Azure Portal [het opslag account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) dat u hebt gebruikt om het kennis archief te maken.

1. Klik op **Storage Explorer**in het linkernavigatievenster van het opslag account.

1. Vouw de lijst **tabellen** uit om een lijst met Azure-tabel prognoses weer te geven die zijn gemaakt tijdens het uitvoeren van de wizard **gegevens importeren** in uw hotel voor beeld van de voorbeeld gegevens.

Selecteer een wille keurige tabel om de verrijkte gegevens weer te geven, met inbegrip van belang rijke sentiment-scores, breedte-en breedte graad en meer.

   ![Tabellen in Storage Explorer weer geven](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Tabellen in Storage Explorer weer geven")

Klik op **bewerken**als u het gegevens type voor tabel waarden wilt wijzigen of afzonderlijke waarden in de tabel wilt wijzigen. Wanneer u het gegevens type voor een kolom in één tabelrij wijzigt, wordt deze toegepast op alle rijen.

   ![Tabel in Storage Explorer bewerken](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Tabel in Storage Explorer bewerken")

Als u query's wilt uitvoeren, klikt u op **query** op de opdracht balk en voert u de voor waarden in.  

   ![Query tabel in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Query tabel in Storage Explorer")

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Verbind dit kennis archief met Power BI voor een diepere analyse of ga door met de code met behulp van de REST API en postman om een ander kennis archief te maken.

> [!div class="nextstepaction"]
> [Verbinding maken met Power BI](knowledge-store-connect-power-bi.md)
> [een Knowledge Store in rest](knowledge-store-howto.md)
