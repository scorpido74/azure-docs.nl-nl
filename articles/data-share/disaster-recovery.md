---
title: Herstel na noodgevallen voor Azure-gegevensshare
description: Herstel na noodgevallen voor Azure-gegevensshare
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483178"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Herstel na noodgevallen voor Azure-gegevensshare

In dit artikel bekijken we hoe u een noodherstelomgeving voor Azure Data Share configureren. Azure-datacenteronderbrekingen zijn zeldzaam, maar kunnen een paar minuten tot uren duren. Uitval van datacenters kan leiden tot verstoring van omgevingen die afhankelijk zijn van gegevens die worden gedeeld door de gegevensprovider. Door de stappen in dit artikel te volgen, kunnen gegevensproviders gegevens blijven delen met hun gegevensconsumenten in het geval van een datacenterstoring voor de primaire regio die uw gegevensaandeel host. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Bedrijfscontinuïteit voor Azure Data Share bereiken

Om voorbereid te zijn op een uitval van datacenters, kan de gegevensprovider een omgeving voor gegevensshare laten inrichten in een secundaire regio. Er zijn maatregelen die kunnen worden genomen die een soepele failover zal zorgen in het geval dat een datacenter storing optreedt. 

Gegevensproviders kunnen secundaire Azure Data Share-bronnen in een extra regio inrichten. Deze bronnen voor gegevensshare kunnen worden geconfigureerd om gegevenssets op te nemen die bestaan in de primaire omgeving voor gegevensshare. Gegevensconsumenten kunnen worden toegevoegd aan het gegevensaandeel bij het configureren van de DR-omgeving, of op een later tijdstip worden toegevoegd (d.w.z. als onderdeel van handmatige failoverstappen).

Als de gegevensconsumenten een actief aandelenabonnement hebben in een secundaire omgeving die is ingericht voor DR-doeleinden, kunnen ze het momentopnameschema inschakelen als onderdeel van een failover. Als de gegevens consumenten niet willen abonneren op een secundaire regio voor DR-doeleinden, kunnen ze worden uitgenodigd in de secundaire gegevens te delen op een later tijdstip. 

Gegevensconsumenten kunnen een actief aandelenabonnement hebben dat niet is inactief voor DR-doeleinden, of gegevensproviders kunnen ze op een later tijdstip toevoegen als onderdeel van handmatige failoverprocedures. 

## <a name="related-information"></a>Gerelateerde informatie

- [Bedrijfscontinuïteit en disaster recovery](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Hoge beschikbaarheid in uw BCDR-strategie bouwen](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Volgende stappen

Ga door met het [delen van uw gegevenszelfstudie](share-your-data.md) om te leren hoe u gegevens delen.




