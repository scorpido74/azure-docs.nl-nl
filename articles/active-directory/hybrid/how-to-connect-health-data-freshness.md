---
title: Azure AD Connect-status - Statusservicegegevens zijn niet up-to-date | Microsoft Documenten
description: In dit document wordt de oorzaak beschreven van de waarschuwing 'Gegevens van de gezondheidsservice is niet up-to-date' en hoe u dit oplossen.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897179"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Gegevens van de gezondheidsdienst zijn niet up-to-date

## <a name="overview"></a>Overzicht

De agents op de on-premises machines die Azure AD Connect Health controleert, uploaden periodiek gegevens naar de Azure AD Connect Health Service. Als de service geen gegevens van een agent ontvangt, wordt de informatie die de portal presenteert verouderd. Om het probleem te benadrukken, zal de service verhogen de **health service gegevens is niet up-to-date** alert. Deze waarschuwing wordt gegenereerd wanneer de service in de afgelopen twee uur geen volledige gegevens heeft ontvangen.  

- De waarschuwing voor de **status waarschuwing** wordt geactiveerd als de gezondheidsservice in de afgelopen twee uur slechts **gedeeltelijke** gegevenstypen heeft ontvangen die vanaf de server zijn verzonden. De waarschuwing statuswaarschuwing leidt niet tot e-mailmeldingen voor geconfigureerde ontvangers. 
- De foutmelding **wordt** geactiveerd als de statusservice in de afgelopen twee uur geen gegevenstypen van de server heeft ontvangen. De foutstatuswaarschuwing activeert e-mailmeldingen naar geconfigureerde ontvangers.

De service krijgt de gegevens van agents die op de on-premises machines draaien, afhankelijk van het servicetype. In de volgende tabel worden de agents weergegeven die op de machine worden uitgevoerd, wat ze doen en de gegevenstypen die de service genereert. In sommige gevallen zijn er meerdere diensten betrokken bij het proces, dus elk van hen kan de boosdoener zijn. 

## <a name="understanding-the-alert"></a>Inzicht in de waarschuwing

Het blad **Waarschuwingsdetails** geeft aan wanneer de waarschuwing is opgetreden en voor het laatst is gedetecteerd. Een achtergrondproces dat elke twee uur wordt uitgevoerd, genereert en evalueert de waarschuwing opnieuw. In het volgende voorbeeld vond de eerste waarschuwing plaats op 03/10 om 09:59 uur. De waarschuwing bestond nog steeds op 03/12 om 10:00 uur toen de waarschuwing opnieuw werd geëvalueerd. Het blad vermeldt ook de tijd dat de Health Service voor het laatst een bepaald gegevenstype heeft ontvangen. 
 
 ![Azure AD Connect-statuswaarschuwingsgegevens](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
In de volgende tabel worden servicetypen toegewezen aan overeenkomstige vereiste gegevenstypen:

| Servicetype | Agent (Windows-servicenaam) | Doel | Gegenereerd gegevenstype  |
| --- | --- | --- | --- |  
| Azure AD Connect (Synchroniseren) | Azure AD Connect Health Sync Insights-service | Aad Connect-specifieke informatie verzamelen (connectors, synchronisatieregels, enz.) | - AadSyncService-synchronisatieregels <br />  - AadSyncService-connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResultaten <br /> - AadSyncService-ServiceConfiguraties <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring-service | Aad Connect-specifieke perf-tellers, ETW-sporen, bestanden verzamelen | Prestatiemeterteller |
| AD DS | Azure AD Connect Health AD DS Insights-service | Synthetische tests uitvoeren, topologie-informatie verzamelen, replicatiemetagegevens |  - Voegt-TopologyInfo-Json toe <br /> - Common-TestData-Json (maakt de testresultaten)   | 
|  | Azure AD Connect Health AD DS Monitoring-service | Verzamel ADDS-specifieke perf tellers, ETW traces, bestanden | - Prestatiemeterteller  <br /> - Common-TestData-Json (uploadt de testresultaten)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics-service | Synthetische tests uitvoeren | TestResult (maakt de testresultaten) | 
| | Azure AD Connect Health AD FS Insights-service  | ADFS-gebruiksstatistieken verzamelen | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring-service | ADFS-specifieke perf-tellers verzamelen, ETW-sporen, bestanden | TestResultaat (uploadt de testresultaten) |

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing 

Hieronder vindt u de stappen die nodig zijn om het probleem te diagnosticeren. De eerste is een set basiscontroles die gemeenschappelijk zijn voor alle servicetypen. 

> [!IMPORTANT] 
> Deze waarschuwing volgt het beleid voor [het bewaren van gegevens van](reference-connect-health-user-privacy.md#data-retention-policy) Connect Health

* Zorg ervoor dat de nieuwste versies van de agents zijn geïnstalleerd. [Releasegeschiedenis](reference-connect-health-version-history.md)weergeven . 
* Controleer of azure AD Connect Health Agents-services op de machine **worden uitgevoerd.** Connect Health voor AD FS moet bijvoorbeeld drie services hebben.
  ![Azure AD Connect Health verifiëren](./media/how-to-connect-health-agent-install/install5.png)

* Zorg ervoor dat je er overheen gaat en voldoet aan de [vereistensectie.](how-to-connect-health-agent-install.md#requirements)
* Gebruik [testconnectiviteitstool](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) om verbindingsproblemen te ontdekken.
* Als u een HTTP-proxy hebt, voert u de [volgende configuratiestappen uit](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Volgende stappen
Als een van de bovenstaande stappen een probleem heeft geïdentificeerd, moet u dit oplossen en wachten tot de waarschuwing is opgelost. Het waarschuwingsachtergrondproces wordt elke 2 uur uitgevoerd, dus het duurt maximaal 2 uur om de waarschuwing op te lossen. 

* [Azure AD Connect-beleid voor het bewaren van gegevens](reference-connect-health-user-privacy.md#data-retention-policy)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
