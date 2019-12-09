---
title: Een toewijzings gegevens stroom maken
description: Een gegevens stroom voor Azure Data Factory toewijzing maken
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 2eb455ba6fa40538bfa03018be47232066036c23
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930432"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory gegevens stroom maken



Het toewijzen van gegevens stromen in ADF biedt een manier om gegevens op schaal te transformeren zonder dat hiervoor benodigde code hoeft te worden uitgevoerd. U kunt een gegevens transformatie taak ontwerpen in de ontwerp functie voor gegevens stromen door een reeks trans formaties te maken. Begin met een wille keurig aantal bron transformaties gevolgd door de stappen voor gegevens transformatie. Voltooi vervolgens uw gegevens stroom met Sink om uw resultaten in een bestemming te brengen.

Ga aan de slag door eerst een nieuwe v2-Data Factory te maken op basis van de Azure Portal. Nadat u de nieuwe fabriek hebt gemaakt, klikt u op de tegel & controle maken om de Data Factory-gebruikers interface te starten.

![Opties voor gegevens stroom](media/data-flow/v2portal.png "gegevens stroom maken")

Zodra u zich in de Data Factory-gebruikers interface bevindt, kunt u voorbeeld gegevens stromen gebruiken. De voor beelden zijn beschikbaar in de galerie met ADF-sjablonen. Maak in ADF "pijp lijn van sjabloon" en selecteer de categorie gegevens stroom in de sjabloon galerie.

![Opties voor gegevens stroom](media/data-flow/template.png "gegevens stroom maken")

U wordt gevraagd om uw Azure Blob Storage-account gegevens in te voeren.

![Opties voor gegevens stroom](media/data-flow/template2.png "gegevens stroom maken 2")

[De gegevens die voor deze steek proeven worden gebruikt, kunt u hier vinden](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Down load de voorbeeld gegevens en sla de bestanden op in de Azure Blob Storage-accounts, zodat u de voor beelden kunt uitvoeren.

## <a name="create-new-data-flow"></a>Nieuwe gegevens stroom maken

Gebruik de knop voor het maken van een resource plus teken in de ADF-gebruikers interface om gegevens stromen te maken.

![Opties voor gegevens stroom](media/data-flow/newresource.png "Nieuwe resource")

## <a name="next-steps"></a>Volgende stappen

Begin met het bouwen van uw gegevens transformatie met een [bron transformatie](data-flow-source.md).
