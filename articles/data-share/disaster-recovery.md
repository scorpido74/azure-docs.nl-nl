---
title: Herstel na nood geval voor Azure-gegevens share
description: Herstel na nood geval voor Azure-gegevens share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483178"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Herstel na nood geval voor Azure-gegevens share

In dit artikel wordt uitgelegd hoe u een nood herstel omgeving kunt configureren voor Azure-gegevens share. Storingen in azure Data Center zijn zeldzaam, maar kunnen een paar minuten tot uur duren. Data Center storingen kunnen leiden tot onderbrekingen in omgevingen die afhankelijk zijn van gegevens die worden gedeeld door de gegevens provider. Door de stappen te volgen die in dit artikel worden beschreven, kunnen gegevens providers gegevens blijven delen met hun eigen gegevens, in het geval van een storing in een Data Center voor de primaire regio die als host fungeert voor uw gegevens share. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Bedrijfs continuïteit voor Azure-gegevens delen bereiken

Voor de voor bereiding op een Data Center-storing kan de gegevens provider een gegevens share omgeving hebben die is ingericht in een secundaire regio. Er zijn metingen die kunnen worden uitgevoerd om te zorgen voor een soepele failover in het geval dat er een storing optreedt in een Data Center. 

Gegevens providers kunnen secundaire Azure-gegevens share resources inrichten in een extra regio. Deze gegevens share bronnen kunnen worden geconfigureerd om gegevens sets te bevatten die voor komen in de primaire gegevens share omgeving. Data-consumers kunnen worden toegevoegd aan de gegevens share bij het configureren van de DR-omgeving of worden toegevoegd op een later tijdstip (dat wil zeggen Als onderdeel van hand matige stappen voor failover).

Als de gegevens gebruikers een actief share-abonnement hebben in een secundaire omgeving die is ingericht voor DR-doel einden, kunnen ze het momentopname schema inschakelen als onderdeel van een failover. Als de gegevens gebruikers zich niet willen abonneren op een secundaire regio voor DR-doel einden, kunnen ze op een later tijdstip worden uitgenodigd voor de secundaire gegevens share. 

Gegevens gebruikers kunnen een actief share-abonnement hebben dat is inactief voor DR-doel einden, of gegevens providers kunnen ze op een later tijdstip toevoegen als onderdeel van hand matige procedures voor failover. 

## <a name="related-information"></a>Gerelateerde informatie

- [Bedrijfs continuïteit en herstel na nood gevallen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Hoge Beschik baarheid bouwen in uw BCDR-strategie](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.




