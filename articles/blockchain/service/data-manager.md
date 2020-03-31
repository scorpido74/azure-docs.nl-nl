---
title: Wat is Blockchain Data Manager voor Azure Blockchain Service
description: Blockchain Data Manager om blockchain-gegevens vast te leggen, te transformeren en levert aan Event Grid-onderwerpen.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209440"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Wat is Block Chain Data Manager voor Azure Blockchain Service?

Blockchain Data Manager legt, transformeert en levert azure Blockchain Service-transactiegegevens aan Azure Event Grid-onderwerpen die betrouwbare en schaalbare blockchain-grootboekintegratie met Azure-services bieden.

In de meeste enterprise blockchain-scenario's is een blockchain-grootboek een onderdeel van een oplossing. Als u bijvoorbeeld een actief van de ene entiteit naar de andere wilt overbrengen, hebt u een mechanisme nodig voor het indienen van de transactie. U hebt dan een mechanisme nodig voor het lezen van grootboekgegevens om ervoor te zorgen dat de transactie is gebeurd, is geaccepteerd en de resulterende statuswijzigingen vervolgens worden geïntegreerd met uw end-to-end-oplossing. In dit voorbeeld u, als u een slim contract schrijft om activa over te dragen, Blockchain Data Manager gebruiken om off-chain applicaties en datastores te integreren. Voor het voorbeeld van assettransfer worden gebeurtenissen en wijzigingen in de eigendomsstatus geleverd door Blockchain Data Manager via Event Grid. U vervolgens meerdere mogelijke gebeurtenishandlers voor Event Grid gebruiken om blockchain-gegevens off-chain op te slaan of in realtime te reageren op wijzigingen in de status.

Blockchain Data Manager voert drie belangrijke functies uit: vastleggen, transformeren en leveren.

![Blockchain Data Manager-functies](./media/data-manager/functions.png)

## <a name="capture"></a>Capture

Elk Blockchain Data Manager-exemplaar maakt verbinding met één transactieknooppunt van Azure Blockchain Service-leden. Alleen gebruikers met toegang tot het transactieknooppunt kunnen een verbinding maken die een goede toegangscontrole tot klantgegevens waarborgt. Een Blockchain Data Manager-exemplaar legt op betrouwbare wijze alle ruwe blok- en ruwe transactiegegevens van het transactieknooppunt vast en kan worden geschaald om bedrijfsworkloads te ondersteunen.

## <a name="transform"></a>Transformeren

U Blockchain Data Manager gebruiken om de status van gebeurtenis en eigendom te decoderen door slimme contracttoepassingen te configureren binnen Blockchain Data Manager. Als u een slim contract wilt toevoegen, verstrekt u het contract ABI en bytecode. Blockchain Data Manager gebruikt de slimme contractartefacten om contractadressen te decoderen en te ontdekken. Na het toevoegen van de blockchain-toepassing aan de instantie, ontdekt Blockchain Data Manager dynamisch het slimme contractadres wanneer het slimme contract wordt geïmplementeerd naar het consortium en verzendt gedecodeerde gebeurtenis- en eigendomsstatus naar geconfigureerde bestemmingen.

## <a name="deliver"></a>Aanbieden

Blockchain Data Manager ondersteunt meerdere uitgaande netwerkverbindingen voor een evenementraster voor een bepaald Blockchain Data Manager-exemplaar. U blockchain-gegevens naar één bestemming verzenden of blockchain-gegevens naar meerdere bestemmingen verzenden. Met Blockchain Data Manager u een schaalbare oplossing voor het publiceren van gegevens op basis van gebeurtenissen bouwen voor elke blockchain-implementatie.

## <a name="configuration-options"></a>Configuratie-opties

U Blockchain Data Manager configureren om aan de behoeften van uw oplossing te voldoen. U bijvoorbeeld invoorzien:

* Eén Blockchain Data Manager-exemplaar voor een Azure Blockchain Service-lid.
* Een Blockchain Data Manager-exemplaar per Azure Blockchain Service-transactieknooppunt. Privétransactieknooppunten kunnen bijvoorbeeld hun eigen Blockchain Data Manager-exemplaar hebben om de vertrouwelijkheid te behouden.
* Een instantie Blockchain Data Manager kan meerdere uitvoerverbindingen ondersteunen. Eén Blockchain Data Manager-exemplaar kan worden gebruikt om alle integratiepunten voor het publiceren van gegevens voor een Azure Blockchain Service-lid te beheren.

## <a name="next-steps"></a>Volgende stappen

Probeer [een Blockchain Data Manager-exemplaar te maken](data-manager-portal.md) voor een Azure Blockchain Service-lid.
