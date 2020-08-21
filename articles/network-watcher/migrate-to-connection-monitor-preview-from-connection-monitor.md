---
title: Migreren naar verbindings monitor (preview) vanuit verbindings monitor
titleSuffix: Azure Network Watcher
description: Meer informatie over hoe u kunt migreren naar verbindings monitor (preview) vanuit verbindings monitor.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701594"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migreren naar verbindings monitor (preview) vanuit verbindings monitor

U kunt bestaande verbindings monitors migreren naar de nieuwe en verbeterde verbindings monitor (preview) met één klik en met een downtime van nul. Als u meer wilt weten over de voor delen, kunt u [verbindings monitor (preview-versie)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview) lezen

## <a name="key-points-to-note"></a>Belangrijkste punten om te noteren

* Agents en Firewall instellingen werken zoals (geen aanraak vereist) 
* Bestaande verbindings monitors worden toegewezen aan de verbindings monitor (preview)-> test groep-> test indeling. Gebruikers kunnen klikken op *bewerken* voor het weer geven en wijzigen van eigenschappen van de nieuwe verbindings monitor en het downloaden van de sjabloon om wijzigingen aan te brengen in de verbindings monitor en deze via Azure Resource Manager te verzenden. 
* Virtuele Azure-machines met Network Watcher extensie verzenden gegevens naar zowel de werk ruimte als de metriek. Met verbindings monitors worden de gegevens beschikbaar gesteld via de nieuwe metrieken (ChecksFailedPercent (preview) en RoundTripTimeMs (preview)) in plaats van de stop verouderde metrische gegevens (ProbesFailedPercent en AverageRoundtripMs) 
* Bewakingsgegevens
    * Waarschuwingen: wordt gemigreerd naar nieuwe metrische gegevens als onderdeel van de migratie
    * Dash boards en integraties: u moet de set metrische gegevens hand matig bewerken. 
    
## <a name="prerequisites"></a>Vereisten

Als u een aangepaste werk ruimte gebruikt, moet u ervoor zorgen dat Network Watcher is ingeschakeld in het abonnement en de regio van de Log Analytics-werk ruimte 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Stappen voor het migreren van de verbindings monitor naar de verbindings monitor (preview-versie)

1. Klik op verbindings monitor, ga naar ' verbindings monitors migreren ' om de monitors van de verbinding te migreren van oudere naar nieuwere oplossingen.

    ![Scherm opname van het migreren van verbindings monitors met de preview-versie van verbindings monitor](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecteer monitors voor abonnementen en verbindingen en klik op ' geselecteerde items migreren '. Klik in een van de twee klikken op bestaande verbinding naar verbindings monitor migreren (preview-versie) 
1. U kunt de eigenschappen van de verbindings monitor aanpassen, de standaard werkruimte wijzigen, de sjabloon downloaden en de status van de migratie controleren. 
1. Zodra de migratie is gestart, gebeurt het volgende: 
    1. Azure Resource Manager bron wijzigingen in de nieuwere verbindings monitor
        1. De naam, de regio en het abonnement van de verbindings monitor blijven ongewijzigd. Daarom is er geen invloed op de resource-ID.
        1. Tenzij aangepast, wordt een standaard Log Analytics werkruimte gemaakt in de regio en het abonnement van de verbindings monitor. In deze werk ruimte wordt bewakings gegevens opgeslagen. Gegevens van de test resultaten worden ook opgeslagen in metrieken.
        1. Elke test wordt gemigreerd naar een test groep met de naam * defaultTestGroup *
        1.  De bron-en doel-eind punten worden gemaakt en gebruikt in de gemaakte test groep. Standaard namen zijn *defaultSourceEndpoint* en *defaultDestinationEndpoint*
        1. De doel poort en het probing-interval worden verplaatst naar de test configuratie met de naam *defaultTestConfiguration*. Het protocol is ingesteld op basis van de poort waarden. De drempel waarden voor geslaagde pogingen en andere optionele eigenschappen blijven leeg.
    1. Metrische waarschuwingen worden gemigreerd naar metrische waarschuwingen voor verbindings controle (preview). De metrische gegevens zijn anders <link to metric section in the doc> , dus de wijziging
    1. De gemigreerde verbindings monitors worden niet weer gegeven in de oudere oplossing voor verbindings controle. ze zijn nu alleen beschikbaar voor gebruik in de verbindings monitor (preview-versie)
    1. Externe integraties zoals Dash boards in Power BI, Grafana, integraties met SIEM-systemen, moeten rechtstreeks door de gebruiker worden gemigreerd. Dit is de enige hand matige stap die de gebruiker nodig heeft om zijn installatie te migreren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het migreren van Netwerkprestatiemeter naar verbindings monitor (preview-versie)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Meer informatie [over het maken van een verbindings monitor (preview) met behulp van Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
