---
title: Een kennis archief met Storage Explorer-Azure Search weer geven
description: Bekijk en analyseer een Azure Search kennis archief met de Storage Explorer van Azure Portal.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 9ec93bcd2309bb47b24983260c35726ac24b5e95
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265607"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Een kennis archief met Storage Explorer weer geven

> [!Note]
> Het kennis archief is in Preview en mag niet worden gebruikt in de productie omgeving. De [Azure Search rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>
In dit artikel leert u hoe u verbinding maakt en een kennis archief kunt verkennen met behulp van Storage Explorer in de Azure Portal. Zie [een kennis archief maken in azure Portal](knowledge-store-create-portal.md)voor het maken van het voor beeld van het kennis archief dat in dit scenario wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

+ Volg de stappen in [een kennis archief maken in azure Portal](knowledge-store-create-portal.md) om het voor beeld-kennis archief te maken dat wordt gebruikt in dit overzicht.

+ U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om het kennis archief te maken, samen met de toegangs sleutel van de Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Een kennis archief in Storage Explorer weer geven, bewerken en er een query op uitvoeren

1. Open in het Azure Portal het opslag account dat u hebt gebruikt om het kennis archief te maken.

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

Zie het volgende artikel voor meer informatie over het verbinden van dit kennis archief met Power BI.

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Zie het volgende artikel voor meer informatie over het maken van een kennis archief met behulp van de REST-Api's en postman.  

> [!div class="nextstepaction"]
> [Een kennis archief maken in REST](knowledge-store-howto.md)
