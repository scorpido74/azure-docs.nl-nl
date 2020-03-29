---
title: Overzicht van bedrijfscontinuïteit
titleSuffix: Microsoft Genomics
description: In dit overzicht worden de mogelijkheden beschreven die Microsoft Genomics biedt voor bedrijfscontinuïteit en disaster recovery.
keywords: bedrijfscontinuïteit, disaster recovery
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72249182"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Overzicht van de bedrijfscontinuïteit met Microsoft Genomics
In dit overzicht worden de mogelijkheden beschreven die Microsoft Genomics biedt voor bedrijfscontinuïteit en disaster recovery. Meer informatie over opties voor het herstellen van storende gebeurtenissen, zoals een azure-regiostoring, die gegevensverlies kan veroorzaken. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics-functies die de bedrijfscontinuïteit ondersteunen 
Hoewel zeldzaam, kan een Azure-datacenter een storing hebben, waardoor een bedrijfsonderbreking kan optreden die een paar minuten tot een paar uur kan duren. Wanneer er een storing optreedt, mislukken alle taken die momenteel in het datacenter worden uitgevoerd en worden taken niet automatisch opnieuw verzonden naar een secundaire regio. 

* Een optie is om te wachten tot het datacenter weer online komt wanneer de datacenterstoring voorbij is. Als de storing kort is, detecteert de Microsoft Genomics-service automatisch de mislukte taken en wordt de werkstroom automatisch opnieuw gestart.

* Een andere optie is om de werkstroom proactief in te dienen in een ander gegevensgebied. Microsoft Genomics implementeert instanties in verschillende [Azure-regio's](https://azure.microsoft.com/regions/services/)en elke regiospecifieke instantie is onafhankelijk. Als een van de Microsoft Genomics-exemplaren een lokale regiofout ondervindt, blijven andere regio's met instanties van Microsoft Genomics taken verwerken. Deze overdracht naar een alternatieve regio staat onder controle van de gebruiker en is op elk moment beschikbaar.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Microsoft Genomics-workflows handmatig mislukken naar een andere regio
Als er een regionale datacenterstoring optreedt, u ervoor kiezen om Microsoft Genomics-workflows in te dienen in een secundaire regio, op basis van uw individuele vereisten voor gegevenssoevereiniteit en bedrijfscontinuïteit. Als u Microsoft Genomics-workflows handmatig wilt failoveren, gebruikt u een andere regiospecifieke. Genomics-account en dien de taak in met de juiste regiospecifieke Genomics- en opslagaccountreferenties.

In het bijzonder moet u:
* Maak een Genomics-account in de secundaire regio met behulp van de Azure-portal. 
* Migreer uw invoergegevens naar een opslagaccount in het secundaire gebied en stel een uitvoermap in het secundaire gebied in.
* Verzend de werkstroom in het secundaire gebied.

Wanneer het oorspronkelijke gebied is hersteld, migreert de Microsoft Genomics-service de gegevens van het secundaire gebied niet terug naar de oorspronkelijke regio. U ervoor kiezen om de invoer- en uitvoerbestanden van het secundaire gebied terug te verplaatsen naar het oorspronkelijke gebied.  Als u ervoor kiest om hun gegevens te verplaatsen, is dit buiten de Genomics-service en alle kosten in verband met de gegevensverplaatsing zouden uw verantwoordelijkheid zijn. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Voorbereiden op een mogelijke regio-specifieke storing
Als u zich zorgen maakt over sneller herstel in het geval van een uitval van datacenters, u een paar stappen nemen om de tijd te verkorten die nodig is om uw Microsoft Genomics-workflows handmatig opnieuw in te dienen bij een secundaire regio:

* Een geschikte secundaire regio identificeren en proactief een Genomics-account aanmaken in die regio
* Dupliceer uw gegevens in het primaire en secundaire gebied, zodat uw gegevens onmiddellijk beschikbaar zijn in de secundaire regio. Dit kan handmatig worden gedaan of met behulp van de [geo-redundante opslagfunctie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) die beschikbaar is in Azure-opslag. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel leert u over uw opties voor bedrijfscontinuïteit en herstel na noodgevallen bij het gebruik van de Microsoft Genomics-service. Zie Technische richtlijnen voor azure tolerantie voor meer informatie over bedrijfscontinuïteit en disaster recovery binnen Azure in het [algemeen.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 