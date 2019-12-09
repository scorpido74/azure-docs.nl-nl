---
title: Sorteer transformatie van gegevens stroom toewijzen
description: Trans formatie van gegevens Sorteer Azure Data Factory toewijzing
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930228"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Trans formaties Azure Data Factory gegevens stroom sorteren



![Instellingen sorteren](media/data-flow/sort.png "Sorteren")

Met de sorteer transformatie kunt u de binnenkomende rijen op de huidige gegevens stroom sorteren. De uitgaande rijen van de sorteer transformatie volgen daarna de regels die u hebt ingesteld. U kunt afzonderlijke kolommen kiezen en deze oplopend of DEC sorteren met behulp van de pijl-indicator naast elk veld. Als u de kolom wilt wijzigen voordat u de sortering toepast, klikt u op berekende kolommen om de expressie-editor te starten. Dit biedt de mogelijkheid om een expressie voor de sorteer bewerking te bouwen in plaats van eenvoudigweg een kolom voor de sortering toe te passen.

## <a name="case-insensitive"></a>Niet hoofdlettergevoelig
U kunt ' niet hoofdletter gevoelig ' inschakelen als u wilt negeren bij het sorteren van teken reeks-of tekst velden.

"Alleen sorteren in partities" maakt gebruik van Spark-gegevens partities. Door binnenkomende gegevens alleen in elke partitie te sorteren, kunnen gegevens stromen gepartitioneerde gegevens sorteren in plaats van de hele gegevens stroom te sorteren.

Elk van de sorteer voorwaarden in de sorteer transformatie kan opnieuw worden gerangschikt. Als u een kolom hoger in de sorteer volgorde wilt verplaatsen, bladert u de rij met de muis en verplaatst u deze hoger of lager in de Sorteer lijst.

Effecten partitioneren bij sorteren

ADF-gegevens stroom wordt uitgevoerd op big data Spark-clusters met gegevens die zijn verdeeld over meerdere knoop punten en partities. Het is belang rijk dat u er rekening mee houdt wanneer u uw gegevens stroom wilt ontwerpen als u afhankelijk van de sorteer transformatie een gegevens in dezelfde volg orde wilt opslaan. Als u ervoor kiest om uw gegevens in een volgende trans formatie opnieuw te partitioneren, kunt u uw Sorteer bewerking verliezen door de volg orde van de gegevens opnieuw in te delen.

## <a name="next-steps"></a>Volgende stappen

Na het sorteren kunt u de [geaggregeerde trans formatie](data-flow-aggregate.md) gebruiken
