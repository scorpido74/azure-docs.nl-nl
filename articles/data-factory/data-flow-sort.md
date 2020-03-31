---
title: Sorteertransformatie van gegevensstroom toewijzen
description: Sorteertransformatie voor het sorteren van azure-gegevensfabriek
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930228"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory Data Flow Sorteertransformaties



![Sorteerinstellingen](media/data-flow/sort.png "Sorteren")

Met de transformatie Sorteren u de binnenkomende rijen in de huidige gegevensstroom sorteren. De uitgaande rijen van de sorteertransformatie volgen vervolgens de bestelregels die u instelt. U afzonderlijke kolommen kiezen en ze ASC of DEC sorteren met behulp van de pijlindicator naast elk veld. Als u de kolom moet wijzigen voordat u de sortering toepast, klikt u op 'Berekende kolommen' om de expressieeditor te starten. Dit biedt de mogelijkheid om een expressie voor de sorteerbewerking te bouwen in plaats van alleen een kolom voor de sortering toe te passen.

## <a name="case-insensitive"></a>Niet hoofdlettergevoelig
U 'Hoofdletter ongevoelig' inschakelen als u de aanvraag wilt negeren bij het sorteren van tekenreeks- of tekstvelden.

"Sort Only Within Partitions" maakt gebruik van Spark-gegevenspartitionering. Door binnenkomende gegevens alleen binnen elke partitie te sorteren, kunnen gegevensstromen partitiegegevens sorteren in plaats van hele gegevensstroom te sorteren.

Elk van de sorteervoorwaarden in de sorteertransformatie kan opnieuw worden gerangschikt. Dus als u een kolom hoger in de sorteerprioriteit moet verplaatsen, pak dan die rij met de muis en beweegt deze hoger of lager in de sorteerlijst.

Partitie-effecten op Sorteren

ADF Data Flow wordt uitgevoerd op big data Spark-clusters met gegevens verdeeld over meerdere knooppunten en partities. Het is belangrijk om hiermee rekening te houden bij het ontwerpen van uw gegevensstroom als u afhankelijk bent van de transformatie Sorteren om gegevens in dezelfde volgorde te bewaren. Als u ervoor kiest om uw gegevens opnieuw te verdelen in een volgende transformatie, u uw sortering verliezen als gevolg van die herschikking van gegevens.

## <a name="next-steps"></a>Volgende stappen

Na het sorteren u de [aggregaattransformatie](data-flow-aggregate.md) gebruiken
