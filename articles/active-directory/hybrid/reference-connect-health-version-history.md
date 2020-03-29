---
title: Versiegeschiedenis van Azure AD Connect Health
description: In dit document worden de releases voor Azure AD Connect Health beschreven en wat er in deze releases is opgenomen.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897032"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Versiegeschiedenis van status Azure AD Connect
Het Azure Active Directory-team werkt Azure AD Connect Health regelmatig bij met nieuwe functies en functionaliteit. In dit artikel worden de versies en functies weergegeven die zijn vrijgegeven.  

> [!NOTE]
> Verbindingsstatusmedewerkers worden automatisch bijgewerkt wanneer een nieuwe versie wordt uitgebracht. Controleer of de instellingen voor automatische upgrade zijn ingeschakeld via azure-portal.
>

Azure AD Connect Health for Sync is geïntegreerd met de installatie van Azure AD Connect. Lees meer over [azure AD Connect-releasedatum](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) Voor feedback over functies u stemmen op [het Voice-kanaal van gebruikersvan Connect Health](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>Juli 2019
**Agent-update**
* Azure AD Connect-statusagent voor AD FS (versie 3.1.59.0) 
   1. Tekstwijziging in TestWindowsTransport
   2. Wijzigingen voor het uploaden van AD FS RP
   
* Azure AD Connect-statusagent voor AD FS (versie 3.1.56.0) 
   1. TestWindowsTransport-test toevoegen en WsTrust-eindpuntencontroles verwijderen in CheckOffice365Endpoints-test
   2. Log OS en .NET-informatie
   3. De uploadgrootte van het RP-configuratiebericht verhogen tot 1 MB.
   4. Opgeloste fouten
   
* Azure AD Connect-statusagent voor AD DS (versie 3.1.56.0) 
   1. Log OS en .NET-informatie 
   2. Opgeloste fouten

## <a name="may-2019"></a>Mei 2019
**Agent Update:** 
* Azure AD Connect-statusagent voor AD FS (versie 3.1.51.0) 
   1. Bug fix om onderscheid te maken tussen meerdere aanmeldingen die dezelfde client-request-id delen.
   2. Bug fix te ontwente slechte gebruikersnaam / wachtwoord fouten op taal gelokaliseerde servers.   

## <a name="april-2019"></a>April 2019
**Agent Update:** 
* Azure AD Connect-statusagent voor AD FS (versie 3.1.46.0) 
   1. Dubbele SPN-waarschuwing voor ADFS oplossen

## <a name="march-2019"></a>Maart 2019
**Agent Update:** 
* Azure AD Connect-statusagent voor AD DS (versie 3.1.41.0)  
   1. .NET-versieverzameling
   2. Verbetering van de prestatietellerverzameling bij het missen van bepaalde categorieën
   3. Bug fix op het voorkomen van spawnen van meerdere Monitoring Agent instances

* Azure AD Connect-statusagent voor AD FS (versie 3.1.41.0) 
   1. Integratie en upgrade van AD FS-testscripts met ADFSToolBox
   2. .NET-versieverzameling implementeren
   3. Verbetering van de prestatietellerverzameling bij het missen van bepaalde categorieën
   4. Bug fix op het voorkomen van spawnen van meerdere Monitoring Agent instances


## <a name="november-2018"></a>November 2018
**Nieuwe GA-functies:** 
* Azure AD Connect Health for Sync - Dubbele fouten bij het synchroniseren van het kenmerk vanaf de portal diagnosticeren en herstellen

**Agent Update:** 
* Azure AD Connect-statusagent voor AD DS (versie 3.1.24.0) 
   1. Naleving en handhaving van het TLS-protocol (Transport Layer Security)
   2. Waarschuwingsruis voor globale catalogus verminderen
   3. Bugfixes voor statusagentregistratie

* Azure AD Connect-statusagent voor AD FS (versie 3.1.24.0)  
   1. Naleving en handhaving van het TLS-protocol (Transport Layer Security)
   2. Ondersteuning van Test-ADFSRequestToken voor gelokaliseerd besturingssysteem
   3. Probleem met probleem probleem met probleem met diagnostische agent EventHandler-vergrendeling opgelost
   4. Bugfixes voor statusagentregistratie

## <a name="august-2018"></a>Augustus 2018 
*  Azure AD Connect Health-agent voor synchronisatie (versie 3.1.7.0) uitgebracht met Azure AD Connect-versie 1.1.880.0    
   1. Hotfix voor [hoge CPU-probleem van monitoringagent met .NET Framework KB-releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juni 2018 
**Nieuwe preview-functies:** 
* Azure AD Connect Health for Sync - Dubbele fouten bij het synchroniseren van het kenmerk vanaf de portal diagnosticeren en herstellen 

**Agent Update:** 
* Azure AD Connect-statusagent voor AD DS (versie 3.1.7.0)    
  1. Hotfix voor [hoge CPU-probleem van monitoringagent met .NET Framework KB-releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect-statusagent voor AD FS (versie 3.1.7.0)  
  1. Hotfix voor [hoge CPU-probleem van monitoringagent met .NET Framework KB-releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Testresultaten fixes op ADFS Server 2016 secundaire server
   
* Azure AD Connect-statusagent voor AD FS (versie 3.1.2.0)  
  1. Hotfix voor agentgeheugenbeheer en gerelateerde waarschuwingen specifiek voor versie 3.0.244.0


## <a name="may-2018"></a>Mei 2018
**Agent Update:**
* Azure AD Connect-statusagent voor AD DS (versie 3.0.244.0)
  1. Verbetering van de privacy van agenten  
  2. Bugfixes en algemene verbeteringen

* Azure AD Connect-statusagent voor AD FS (versie 3.0.244.0)
  1. Agent Diagnostics Service en gerelateerde PowerShell-moduleverbeteringen
  2. Verbetering van de privacy van agenten  
  3. Bugfixes en algemene verbeteringen

* Azure AD Connect Health-agent voor synchronisatie (versie 3.0.164.0) uitgebracht met Azure AD Connect-versie 1.1.819.0 
  1. Verbetering van de privacy van agenten  
  2. Bugfixes en algemene verbeteringen


## <a name="march-2018"></a>Maart 2018
**Nieuwe preview-functies:**
* Azure AD Connect-status voor AD FS - Riskant IP-rapport en waarschuwing.

**Agent Update:**

* Azure AD Connect-statusagent voor AD DS (versie 3.0.176.0)
  1. Verbeteringen in de beschikbaarheid van agenten 
  2. Bugfixes en algemene verbeteringen
* Azure AD Connect-statusagent voor AD FS (versie 3.0.176.0)
  1. Verbeteringen in de beschikbaarheid van agenten 
  2. Bugfixes en algemene verbeteringen
* Azure AD Connect Health-agent voor synchronisatie (versie 3.0.129.0) uitgebracht met Azure AD Connect-versie 1.1.750.0  
  1. Verbeteringen in de beschikbaarheid van agenten 
  2. Bugfixes en algemene verbeteringen

## <a name="december-2017"></a>December 2017
**Agent Update:**

* Azure AD Connect-statusagent voor AD DS (versie 3.0.145.0)
  1. Verbeteringen in de beschikbaarheid van agenten 
  2. Nieuwe opdrachten voor het oplossen van problemen met een agent toegevoegd
  3. Bugfixes en algemene verbeteringen
* Azure AD Connect-statusagent voor AD FS (versie 3.0.145.0)
  1. Nieuwe opdrachten voor het oplossen van problemen met een agent toegevoegd
  2. Verbeteringen in de beschikbaarheid van agenten 
  3. Bugfixes en algemene verbeteringen
  
## <a name="october-2017"></a>Oktober 2017
**Agent Update:**

 * Azure AD Connect Health-agent voor synchronisatie (versie 3.0.129.0) uitgebracht met Azure AD Connect-versie 1.1.649.0
<br></br> Fixed a version compatibility issue between Azure AD Connect and Azure AD Connect Health Agent for Sync. Dit probleem heeft gevolgen voor klanten die Azure AD Connect in-place upgraden naar versie 1.1.647.0 uitvoeren, maar momenteel Health Agent-versie 3.0.127.0 hebben. Na de upgrade kan de statusagent geen statusgegevens meer over Azure AD Connect Synchronization Service verzenden naar Azure AD Health Service. Met deze oplossing wordt Health Agent-versie 3.0.129.0 geïnstalleerd tijdens de upgrade van Azure AD Connect. Health Agent-versie 3.0.129.0 heeft geen compatibiliteitsprobleem met Azure AD Connect-versie 1.1.649.0.

## <a name="july-2017"></a>Juli 2017
**Agent Update:**

* Azure AD Connect-statusagent voor AD DS (versie 3.0.68.0)
  1. Bugfixes en algemene verbeteringen
  2. Ondersteuning voor soevereine cloud
* Azure AD Connect-statusagent voor AD FS (versie 3.0.68.0)
  1. Bugfixes en algemene verbeteringen
  2. Ondersteuning voor soevereine cloud
* Azure AD Connect Health-agent voor synchronisatie (versie 3.0.68.0) uitgebracht met Azure AD Connect-versie 1.1.614.0
  1. Ondersteuning voor Microsoft Azure Government Cloud en Microsoft Cloud Germany

## <a name="april-2017"></a>April 2017      
**Agent Update:**

* Azure AD Connect-statusagent voor AD FS (versie 3.0.12.0)
  1. Bugfixes en algemene verbeteringen
* Azure AD Connect-statusagent voor AD DS (versie 3.0.12.0)
  1. Prestatiemeteritems uploaden verbeteringen
  2. Bugfixes en algemene verbeteringen

## <a name="october-2016"></a>Oktober 2016
**Agent Update:**

* Azure AD Connect-statusagent voor AD FS (versie 2.6.408.0)
* Verbeteringen in het detecteren van CLIENT IP-adressen in verificatieverzoeken
* Bugfixes met betrekking tot waarschuwingen
* Azure AD Connect-statusagent voor AD DS (versie 2.6.408.0)
* Bugfixes met betrekking tot waarschuwingen.
* Azure AD Connect Health-agent voor synchronisatie (versie 2.6.353.0) uitgebracht met Azure AD Connect-versie 1.1.281.0
* De vereiste gegevens voor de synchronisatiefoutrapporten verstrekken
* Bugfixes met betrekking tot waarschuwingen

**Nieuwe preview-functies:**

* Synchronisatiefoutrapporten voor Azure AD Connect

**Nieuwe functies:**

* Azure AD Connect Health for AD FS - IP-adres veld is beschikbaar in het rapport over top 50 gebruikers met een slechte gebruikersnaam / wachtwoord.

## <a name="july-2016"></a>Juli 2016
**Nieuwe preview-functies:**

* [Azure AD Connect-status voor AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Januari 2016
**Agent Update:**

* Azure AD Connect-statusagent voor AD FS (versie 2.6.91.1512)

**Nieuwe functies:**

* [Test connectivity Tool voor Azure AD Connect Health Agents](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Nieuwe functies:**

* Ondersteuning voor [toegangsbeheer op basis van rollen](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nieuwe preview-functies:**

* [Azure AD Connect-status voor synchronisatie](how-to-connect-health-sync.md).

**Vaste problemen:**

* Bug fixes voor fouten gezien tijdens agent registraties.

## <a name="september-2015"></a>September 2015
**Nieuwe functies:**

* Wachtwoordrapport voor verkeerde gebruikersnaam voor AD FS
* Ondersteuning voor het configureren van niet-geverifieerde HTTP-proxy
* Ondersteuning voor het configureren van agent op servercore
* Verbeteringen in waarschuwingen voor AD FS
* Verbeteringen in Azure AD Connect Health Agent voor AD FS voor connectiviteit en het uploaden van gegevens.

**Vaste problemen:**

* Bugfixes in usage insights voor AD FS-fouttypen.

## <a name="june-2015"></a>Juni 2015
**Eerste release van Azure AD Connect Health voor AD FS en AD FS Proxy.**

**Nieuwe functies:**

* Waarschuwingen voor het bewaken van AD FS- en AD FS-proxyservers met e-mailmeldingen.
* Eenvoudige toegang tot AD FS-topologie en -patronen in AD FS-prestatiemeteritems.
* Trend in succesvolle tokenaanvragen op AD FS-servers gegroepeerd op toepassingen, verificatiemethoden, netwerklocatie aanvragen enz.
* Trends in mislukte aanvraag op AD FS-servers gegroepeerd op toepassingen, fouttypen enz.
* Eenvoudiger agentimplementatie met azure AD Global Admin-referenties.  

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Uw on-premises identiteitsinfrastructuur en synchronisatieservices in de cloud controleren.](whatis-hybrid-identity-health.md)

