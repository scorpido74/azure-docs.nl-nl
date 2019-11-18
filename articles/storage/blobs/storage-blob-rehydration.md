---
title: BLOB-gegevens worden opnieuw gehydrateerd op basis van de opslaglaag
description: U kunt de blobs uit archief opslag opnieuw gebruiken zodat u toegang hebt tot de gegevens.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113721"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-gegevens worden opnieuw gehydrateerd op basis van de opslaglaag

Terwijl een BLOB zich in de Access-laag Archive bevindt, wordt deze als offline beschouwd en kan deze niet worden gelezen of gewijzigd. De meta gegevens van de BLOB blijven online en beschikbaar, zodat u de BLOB en de bijbehorende eigenschappen kunt weer geven. Het lezen en wijzigen van BLOB-gegevens is alleen beschikbaar voor online lagen, zoals warme of koud. Er zijn twee opties voor het ophalen en openen van gegevens die zijn opgeslagen in de toegangs laag voor het archief.

1. [Een gearchiveerde BLOB opnieuw gehydrateerd naar een online-laag](#rehydrate-an-archived-blob-to-an-online-tier) -een archief-BLOB opnieuw laten worden gehydrateerd op warme of koud door de laag te wijzigen met de bewerking voor het instellen van de [BLOB-laag](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Een gearchiveerde BLOB naar een online-laag kopiëren](#copy-an-archived-blob-to-an-online-tier) : Maak een nieuwe kopie van een archief-blob met behulp van de [Kopieer bewerking BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Geef een andere blobnaam en een bestemmings laag op met de naam Hot of cool.

 Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](storage-blob-storage-tiers.md)voor meer informatie over lagen.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde BLOB opnieuw naar een online-laag gehydrateerd

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde BLOB kopiëren naar een online-laag

Als u de archief-BLOB niet opnieuw wilt laten worden gehydrateerd, kunt u ervoor kiezen om een [Kopieer-BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) bewerking uit te voeren. De oorspronkelijke BLOB blijft ongewijzigd in archief terwijl er een nieuwe BLOB wordt gemaakt in de online hot of cool-laag, zodat u kunt werken. In de bewerking BLOB kopiëren kunt u ook de optionele *x-MS-autohydrat-Priority-* eigenschap instellen op Standard of High (preview) om de prioriteit op te geven waarop u de BLOB-kopie wilt maken.

Archief-blobs kunnen alleen worden gekopieerd naar online doel lagen binnen hetzelfde opslag account. Het kopiëren van een archief-BLOB naar een andere archief-BLOB wordt niet ondersteund.

Het kopiëren van een BLOB uit het archief kan uren duren, afhankelijk van de geselecteerde opnieuw te maken prioriteit. Achter de schermen leest de bewerking **BLOB kopiëren** de bron-blob van het archief om een nieuwe online-Blob in de geselecteerde doellaag te maken. De nieuwe blob is mogelijk zichtbaar wanneer u blobs vermeldte, maar de gegevens zijn pas beschikbaar als de Lees bewerking van de blob van het bron archief is voltooid en de gegevens naar de nieuwe online-doel-BLOB zijn geschreven. De nieuwe blob is een onafhankelijke kopie en een wijziging of verwijdering hiervan heeft geen invloed op de bron archief-blob.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Reactiveren-blobs uit het archief in warme of coole lagen worden in rekening gebracht als Lees bewerkingen en gegevens ophalen. Het gebruik van high priority (preview) heeft hogere kosten voor bewerkingen en het ophalen van gegevens in vergelijking met de standaard prioriteit. Rehydratatie met hoge prioriteit worden weer gegeven als een afzonderlijk regel item op uw factuur. Als een aanvraag met een hoge prioriteit voor het retour neren van een archief-blob van een paar gigabytes meer dan vijf uur duurt, wordt niet het ophaal tarief met hoge prioriteit in rekening gebracht. Standaard tarieven voor het ophalen van zijn echter nog steeds van toepassing omdat de rehydratatie prioriteit heeft gegeven aan andere aanvragen.

Het kopiëren van blobs van Archive in warme of coole lagen wordt in rekening gebracht als Lees bewerkingen en ophalen van gegevens. Voor het maken van de nieuwe BLOB-kopie wordt een schrijf bewerking in rekening gebracht. Kosten voor vroegtijdig verwijderen zijn niet van toepassing wanneer u naar een online-BLOB kopieert, omdat de bron-BLOB ongewijzigd blijft in de laag van het archief. De kosten voor het ophalen van hoge prioriteit zijn van toepassing indien geselecteerd.

Blobs in de archief laag moeten mini maal 180 dagen worden opgeslagen. Als u gearchiveerde blobs verwijdert of reactiveren, worden de kosten 180 voor vroegtijdige verwijdering in rekening gebracht.

> [!NOTE]
> Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor blok-blobs en gegevens rehydratatie. Zie [prijs informatie voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/)voor meer informatie over de kosten voor uitgaande gegevens overdracht.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Blob Storage lagen](storage-blob-storage-tiers.md)
* [Prijzen voor de dynamische, statische en archieflaag controleren in Blob Storage- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
* [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
* [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
