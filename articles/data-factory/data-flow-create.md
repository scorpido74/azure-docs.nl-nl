---
title: Een kaartgegevensstroom maken
description: Een gegevensstroom voor azure datafactory-toewijzingmaken maken
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416513"
---
# <a name="create-azure-data-factory-data-flow"></a>Gegevensstroom voor Azure Data Factory maken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Het toewijzen van gegevensstromen in ADF biedt een manier om gegevens op schaal te transformeren zonder dat er codering nodig is. U een taak voor gegevenstransformatie in de gegevensstroomontwerper ontwerpen door een reeks transformaties te maken. Begin met een willekeurig aantal brontransformaties, gevolgd door stappen voor gegevenstransformatie. Vul vervolgens uw gegevensstroom in met gootsteen om uw resultaten in een bestemming te landen.

Ga aan de slag door eerst een nieuwe V2-gegevensfabriek te maken vanuit de Azure-portal. Nadat u uw nieuwe fabriek hebt gemaakt, klikt u op de tegel 'Auteur & monitor' om de gebruikersinterface van de gegevensfabriek te starten.

![Opties voor gegevensstroom](media/data-flow/v2portal.png "maken van gegevensstromen")

Zodra u zich in de gebruikersinterface van de gegevensfabriek bevindt, u voorbeeldgegevensstromen gebruiken. De voorbeelden zijn beschikbaar in de ADF-sjabloongalerie. Maak in ADF 'Pijplijn van sjabloon' en selecteer de categorie Gegevensstroom in de sjabloongalerie.

![Opties voor gegevensstroom](media/data-flow/template.png "maken van gegevensstromen")

U wordt gevraagd uw Azure Blob Storage-accountgegevens in te voeren.

![Opties voor gegevensstroom](media/data-flow/template2.png "gegevensstroom maken 2")

[De gegevens die voor deze monsters worden gebruikt, zijn hier te vinden.](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata) Download de voorbeeldgegevens en sla de bestanden op in uw Azure Blob-opslagaccounts, zodat u de voorbeelden uitvoeren.

## <a name="create-new-data-flow"></a>Nieuwe gegevensstroom maken

Gebruik de knop Resource maken in de ADF-gebruikersinterface om gegevensstromen te maken.

![Opties voor gegevensstroom](media/data-flow/newresource.png "Nieuwe bron")

## <a name="next-steps"></a>Volgende stappen

Begin met het bouwen van uw gegevenstransformatie met een [brontransformatie.](data-flow-source.md)
