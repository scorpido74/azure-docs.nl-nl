---
title: Migreren naar verbindings monitor (preview) van Netwerkprestatiemeter
titleSuffix: Azure Network Watcher
description: Meer informatie over hoe u kunt migreren naar verbindings monitor (preview) van Netwerkprestatiemeter.
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701593"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migreren naar verbindings monitor (preview) van Netwerkprestatiemeter

U kunt tests van Netwerkprestatiemeter naar de nieuwe en verbeterde verbindings monitor (preview) migreren met één klik en met een downtime van nul. Als u meer wilt weten over de voor delen, kunt u [verbindings monitor (preview-versie)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview) lezen

>[!NOTE]
> Alleen tests van een service verbindings monitor kunnen worden gemigreerd naar de verbindings monitor (preview-versie).
>

## <a name="key-points-to-note"></a>Belangrijkste punten om te noteren

* On-premises agents en Firewall instellingen werken zoals is. Er zijn geen wijzigingen vereist. Log Analytics agents die zijn geïnstalleerd op Azure Virtual Machines moeten worden vervangen door Network Watcher extensie
* Bestaande testen worden toegewezen aan de verbindings monitor (preview)-> test groep-> test indeling. Gebruikers kunnen klikken op *bewerken* voor het weer geven en wijzigen van eigenschappen van de nieuwe verbindings monitor en het downloaden van de sjabloon om wijzigingen aan te brengen in de verbindings monitor en deze via Azure Resource Manager te verzenden.
* Agents verzenden gegevens naar beide-Log Analytics werk ruimte en metrieken.
* Bewakingsgegevens
    * Gegevens in Log Analytics: alle vooraf gemigreerde gegevens blijven in de werk ruimte waarin NPM is geconfigureerd in de tabel NetworkMonitoring. Na de migratie worden gegevens verplaatst naar de tabel NetworkMonitoring en de tabel ConnectionMonitor_CL in dezelfde werk ruimte. Wanneer tests zijn uitgeschakeld in NPM, worden de gegevens alleen opgeslagen in ConnectionMonitor_CL tabel
    * Waarschuwingen, Dash boards en integraties op basis van Logboeken: u moet de query's hand matig bewerken op basis van de nieuwe tabel ConnectionMonitor_CL. U kunt de waarschuwingen ook opnieuw maken in metrische gegevens met behulp van deze koppeling. De mogelijkheid om logboeken op basis van waarschuwingen in NetworkMonitoring-tabel automatisch te migreren naar metrieke waarschuwingen wanneer een deel van de migratie binnenkort beschikbaar is
    
## <a name="prerequisites"></a>Vereisten

*   Zorg ervoor dat Network Watcher is ingeschakeld in het abonnement en de regio van de Log Analytics-werk ruimte
*   Virtuele Azure-machines waarop log Analytics-agents zijn geïnstalleerd, moeten worden ingeschakeld met Network Watcher extensie

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Stappen voor het migreren van tests van Netwerkprestatiemeter naar verbindings monitor (preview-versie)

1.  Klik op verbindings monitor, ga naar ' tests migreren van NPM ' om testen naar verbindings monitor te migreren (preview-versie)

    ![Scherm afbeelding van migratie tests van NPM naar de preview-versie van de verbindings monitor](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Selecteer abonnement, werk ruimte en de NPM-functie die u wilt migreren. Op dit moment kunt u alleen testen van de controle van de service connectiviteit migreren.  
1.  Klik op importeren om tests te migreren
1.  Zodra de migratie is gestart, gebeurt het volgende: 
    1. Er wordt een nieuwe bron voor verbindings controle gemaakt
        1. Er wordt één verbindings monitor per regio en abonnement gemaakt. Voor testen met on-premises agents is de naam van de nieuwe verbindings monitor de notatie <workspaceName> _on-premises. Voor testen met Azure-agents, de naam van de nieuwe verbindings monitor <workspaceName> is de indeling_<Azure_region_name>
        1. Bewakings gegevens worden nu opgeslagen in dezelfde Log Analytics werk ruimte waarin NPM is ingeschakeld, in een nieuwe tabel met de naam Connectionmonitor_CL tabel. 
        1. De test naam wordt getransporteerd naar de naam van de test groep. De test beschrijving wordt niet gemigreerd.
        1. De bron-en doel-eind punten worden gemaakt en gebruikt in de gemaakte test groep. Voor on-premises agents worden de eind punten aangeduid met de notatie <workspaceName> _"eind punt"_ <FQDN of on-premises machine> . Als de migratie tests agents in azure bevatten, moet u de agents inschakelen en de migratie opnieuw uitvoeren.
        1. De doel poort en het probing-interval worden verplaatst naar de test configuratie, namelijk "TC"_ <testname> "en" TC "_ <testname> _" AppThresholds ". Het protocol is ingesteld op basis van de poort waarden. De drempel waarden voor geslaagde pogingen en andere optionele eigenschappen blijven leeg.
    1. NPM is niet uitgeschakeld. Daarom blijven gemigreerde testen gegevens verzenden naar de tabel NetworkMonitoring en de tabel ConnectionMonitor_CL. Deze stap zorgt ervoor dat de bestaande waarschuwingen en integraties op basis van het logboek niet worden beïnvloed. Waarschuwingen op basis van Logboeken in NetworkMonitoring-tabel naar metrische gegevens worden automatisch gemigreerd als onderdeel van de migratie wordt binnenkort beschikbaar.
    1. Nieuw gemaakte verbindings monitor wordt weer gegeven in verbindings monitor (preview-versie)
1.  Na de migratie moet u de tests in NPM hand matig uitschakelen. Totdat u dat doet, worden er nog steeds kosten in rekening gebracht. Zorg ervoor dat u tijdens het uitschakelen van NPM uw waarschuwingen opnieuw maakt in ConnectionMonitor_CL tabel of metrische gegevens gebruikt. Zorg er ook voor dat externe integraties, zoals Dash boards in Power BI, Grafana, integraties met SIEM-systemen, moeten worden gemigreerd naar ConnectionMonitor_CL tabel


## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het migreren van verbindings monitor naar verbindings monitor (preview-versie)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Meer informatie [over het maken van een verbindings monitor (preview) met behulp van Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
