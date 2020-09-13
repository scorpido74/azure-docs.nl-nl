---
title: Migreren naar verbindings monitor (preview) vanuit verbindings monitor
titleSuffix: Azure Network Watcher
description: Meer informatie over hoe u kunt migreren naar verbindings monitor (preview) vanuit verbindings monitor.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 05b42024529b8d9de3590488ecafbdf83283e007
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441813"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migreren naar verbindings monitor (preview) vanuit verbindings monitor

U kunt bestaande verbindings monitors migreren naar nieuwe, verbeterde verbindings controle (preview) met slechts enkele klikken en met een downtime van nul. Zie [verbindings monitor (preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)voor meer informatie over de voor delen.

## <a name="key-points-to-note"></a>Belangrijkste punten om te noteren

De migratie helpt de volgende resultaten te produceren:

* De agents en Firewall instellingen werken zoals. Er zijn geen wijzigingen vereist. 
* Bestaande verbindings monitors worden toegewezen aan de controle groep (preview) voor > test groepen > test-indeling. Als u **bewerken**selecteert, kunt u de eigenschappen van de nieuwe verbindings monitor weer geven en wijzigen, een sjabloon downloaden om wijzigingen aan te brengen in de verbindings monitor en deze verzenden via Azure Resource Manager. 
* Virtuele Azure-machines met de extensie Network Watcher verzenden gegevens naar zowel de werk ruimte als de metrieken. Met verbindings monitor worden de gegevens beschikbaar via de nieuwe metrieken (ChecksFailedPercent [Preview] en RoundTripTimeMs [Preview]) in plaats van de oude metrieken (ProbesFailedPercent en AverageRoundtripMs). 
* Gegevens bewaking:
   * **Waarschuwingen**: automatisch gemigreerd naar de nieuwe metrische gegevens.
   * **Dash boards en integraties**: vereisen hand matig bewerken van de metrische gegevens sets. 
    
## <a name="prerequisites"></a>Vereisten

Als u een aangepaste werk ruimte gebruikt, zorg er dan voor dat Network Watcher is ingeschakeld in uw abonnement en in de regio van uw Log Analytics-werk ruimte. 

## <a name="migrate-the-connection-monitors"></a>De verbindings monitors migreren

1. Als u de oudere verbindings monitors naar de nieuwere versie wilt migreren, selecteert u **verbindings monitor**en selecteert u vervolgens verbindings monitors **migreren**.

    ![Scherm opname van de migratie van verbindings monitors naar verbindings monitor (preview).](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecteer uw abonnement en de monitors voor de verbinding die u wilt migreren en selecteer vervolgens **migreren geselecteerd**. 

Met slechts enkele klikken hebt u de bestaande verbindings monitors gemigreerd naar de verbindings monitor (preview-versie). 

U kunt nu de eigenschappen van de verbindings monitor (preview-versie) aanpassen, de standaardwerk ruimte wijzigen, sjablonen downloaden en de migratie status controleren. 

Nadat de migratie is gestart, worden de volgende wijzigingen doorgevoerd: 
* De Azure Resource Manager bron wordt gewijzigd in de nieuwere verbindings monitor.
    * De naam, de regio en het abonnement van de verbindings monitor blijven ongewijzigd. De resource-ID wordt niet beïnvloed.
    * Tenzij de verbindings monitor is aangepast, wordt er een standaard Log Analytics werkruimte gemaakt in het abonnement en in de regio van de verbindings monitor. In deze werk ruimte worden bewakings gegevens opgeslagen. De gegevens van de test resultaten worden ook opgeslagen in de metrieken.
    * Elke test wordt gemigreerd naar een test groep met de naam *defaultTestGroup*.
    * De bron-en doel-eind punten worden gemaakt en gebruikt in de nieuwe test groep. De standaard namen zijn *defaultSourceEndpoint* en *defaultDestinationEndpoint*.
    * De doel poort en het probing-interval worden verplaatst naar een test configuratie met de naam *defaultTestConfiguration*. Het protocol wordt ingesteld op basis van de poort waarden. De drempel waarden voor geslaagde pogingen en andere optionele eigenschappen blijven leeg.
* Waarschuwingen voor metrische gegevens worden gemigreerd naar de metrische waarschuwingen voor verbindings controle (preview). De metrische gegevens zijn anders, dus de wijziging. Zie [netwerk connectiviteit controleren met verbindings monitor (preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)voor meer informatie.
* De gemigreerde verbindings monitors worden niet meer weer gegeven als de oudere oplossing van de verbindings monitor. Ze zijn nu alleen beschikbaar voor gebruik in verbindings monitor (preview-versie).
* Externe integraties, zoals Dash boards in Power BI en Grafana en integraties met Security Information and Event Management-systemen (SIEM), moeten hand matig worden gemigreerd. Dit is de enige hand matige stap die u moet uitvoeren om uw installatie te migreren.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over verbindings monitor (preview):
* [Migreren van Netwerkprestatiemeter naar verbindings monitor (preview-versie)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [Verbindings monitor (preview-versie) maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
