---
title: Migreren naar verbindings monitor (preview) van Netwerkprestatiemeter
titleSuffix: Azure Network Watcher
description: Meer informatie over hoe u kunt migreren naar verbindings monitor (preview) van Netwerkprestatiemeter.
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
ms.openlocfilehash: dcbb82c1315e6150ddcfadbb52b2976447329b87
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441830"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migreren naar verbindings monitor (preview) van Netwerkprestatiemeter

U kunt tests migreren van Netwerkprestatiemeter (NPM) naar een nieuwe, verbeterde verbindings monitor (preview) met één klik en met een downtime van nul. Zie [verbindings monitor (preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)voor meer informatie over de voor delen.

>[!NOTE]
> Alleen tests van een service verbindings monitor kunnen worden gemigreerd naar de verbindings monitor (preview-versie).
>

## <a name="key-points-to-note"></a>Belangrijkste punten om te noteren

De migratie helpt de volgende resultaten te produceren:

* On-premises agents en Firewall instellingen werken op dezelfde locatie. Er zijn geen wijzigingen vereist. Log Analytics agents die zijn geïnstalleerd op virtuele machines van Azure, moeten worden vervangen door de Network Watcher extensie.
* Bestaande testen worden toegewezen aan de verbindings monitor (preview) > test groep > test-indeling. Als u **bewerken**selecteert, kunt u de eigenschappen van de nieuwe verbindings monitor (preview) bekijken en wijzigen, een sjabloon downloaden om wijzigingen aan te brengen en de sjabloon indienen via Azure Resource Manager.
* Agents verzenden gegevens naar de Log Analytics-werk ruimte en de metrieken.
* Gegevens bewaking:
   * **Gegevens in log Analytics**: vóór de migratie blijven de gegevens in de werk ruimte waarin NPM is geconfigureerd in de tabel NetworkMonitoring. Na de migratie worden de gegevens verplaatst naar de tabel NetworkMonitoring en de tabel ConnectionMonitor_CL in dezelfde werk ruimte. Nadat de tests zijn uitgeschakeld in NPM, worden de gegevens alleen opgeslagen in de tabel ConnectionMonitor_CL.
   * **Waarschuwingen op basis van Logboeken, Dash boards en integraties**: u moet de query's hand matig bewerken op basis van de nieuwe ConnectionMonitor_CL tabel. Zie [netwerk connectiviteit controleren met verbindings monitor (preview)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)als u de waarschuwingen in metrische gegevens opnieuw wilt maken.
    
## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat Network Watcher is ingeschakeld in uw abonnement en de regio van de Log Analytics-werk ruimte.
* Virtuele Azure-machines waarop Log Analytics-agents zijn geïnstalleerd, moeten worden ingeschakeld met de Network Watcher extensie.

## <a name="migrate-the-tests"></a>De tests migreren

Ga als volgt te werk om de tests te migreren van Netwerkprestatiemeter naar verbindings monitor (preview):

1. Selecteer in Network Watcher **verbindings monitor**en selecteer vervolgens het tabblad **tests migreren van NPM** . 

    ![Scherm opname van het deel venster ' tests migreren van NPM ' in Network Watcher | Verbindings monitor (preview-versie).](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1. Selecteer uw abonnement en werk ruimte in de vervolg keuzelijst en selecteer vervolgens de NPM-functie die u wilt migreren. Op dit moment kunt u alleen testen migreren vanuit de service verbindings monitor.  
1. Selecteer **importeren** om de tests te migreren.

Nadat de migratie is gestart, worden de volgende wijzigingen doorgevoerd: 
* Er wordt een nieuwe bron voor verbindings bewaking gemaakt.
   * Er wordt één verbindings monitor per regio en abonnement gemaakt. Voor testen met on-premises agents wordt de naam van de nieuwe verbindings monitor opgemaakt als `<workspaceName>_"on-premises"` . Voor testen met Azure-agents wordt de naam van de nieuwe verbindings monitor opgemaakt als `<workspaceName>_<Azure_region_name>` .
   * Bewakings gegevens worden nu opgeslagen in dezelfde Log Analytics werk ruimte waarin NPM is ingeschakeld, in een nieuwe tabel met de naam Connectionmonitor_CL. 
   * De test naam wordt getransporteerd als de naam van de test groep. De beschrijving van de test wordt niet gemigreerd.
   * De bron-en doel-eind punten worden gemaakt en gebruikt in de nieuwe test groep. Voor on-premises agents worden de eind punten opgemaakt als `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Als de migratie tests agents bevatten die niet worden uitgevoerd in azure, moet u de agents inschakelen en opnieuw migreren.
   * De doel poort en het probing-interval worden verplaatst naar een test configuratie met de naam *TC_ \<testname> * en *TC_ \<testname> _AppThresholds*. Het protocol wordt ingesteld op basis van de poort waarden. De drempel waarden voor geslaagde pogingen en andere optionele eigenschappen blijven leeg.
* NPM is niet uitgeschakeld, dus de gemigreerde tests kunnen gegevens blijven verzenden naar de NetworkMonitoring en ConnectionMonitor_CL tabellen. Deze aanpak zorgt ervoor dat bestaande waarschuwingen en integraties op basis van het logboek niet worden beïnvloed.
* De zojuist gemaakte verbindings monitor is zichtbaar in de verbindings monitor (preview).

Na de migratie, moet u het volgende doen:
* Schakel de tests in NPM hand matig uit. Totdat u dit doet, worden er nog steeds kosten in rekening gebracht. 
* Wanneer u NPM uitschakelt, moet u de waarschuwingen opnieuw maken in de tabel ConnectionMonitor_CL of metrische gegevens gebruiken. 
* Externe integraties migreren naar de ConnectionMonitor_CL tabel. Voor beelden van externe integraties zijn Dash boards in Power BI en Grafana en integraties met Security Information and Event Management-systemen (SIEM).


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over verbindings monitor (preview):
* [Migreren van verbindings monitor naar verbindings monitor (preview-versie)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* [Verbindings monitor (preview-versie) maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
