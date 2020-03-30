---
title: 'Azure AD Connect: msExchUserHoldPolicies en cloudMsExchUserHoldPolicies | Microsoft Documenten'
description: In dit onderwerp worden kenmerkgedrag van de kenmerken msExchUserHoldPolicies en cloudMsExchUserHoldPolicies beschreven
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213081"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies en cloudMsExchUserHoldPolicies
In het volgende referentiedocument worden deze kenmerken beschreven die door Exchange worden gebruikt en de juiste manier om de standaardsynchronisatieregels te bewerken.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Wat zijn msExchUserHoldPolicies en cloudMsExchUserHoldPolicies?
Er zijn twee soorten [holden](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) beschikbaar voor een Exchange Server: Litigation Hold en In-Place Hold. Wanneer Proceswachting is ingeschakeld, worden alle items in de wachtstand geplaatst.  Een in-place hold wordt gebruikt om alleen die items te behouden die voldoen aan de criteria van een zoekopdracht die u hebt gedefinieerd met het gereedschap In-Place eDiscovery.

Met de kenmerken MsExchUserHoldPolcies en cloudMsExchUserHoldPolicies kunnen on-premises AD en Azure AD bepalen welke gebruikers onder een blokkering staan, afhankelijk van of ze on-premises Exchange of Exchange on-line gebruiken.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies-synchronisatiestroom
Standaard wordt MsExchUserHoldPolcies gesynchroniseerd door Azure AD Connect rechtstreeks met het kenmerk msExchUserHoldPolicies in de metaverse en vervolgens met het kenmerk msExchUserHoldPolices in Azure AD

In de volgende tabellen wordt de stroom beschreven:

Binnenkomend vanuit on-premises Active Directory:

|Active Directory-kenmerk|Kenmerknaam|Stroomtype|Metaverse attribuut|Synchronisatieregel|
|-----|-----|-----|-----|-----|
|On-premises Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|Binnenkomen vanaf AD - User Exchange|

Uitgaannaar Azure AD:

|Metaverse attribuut|Kenmerknaam|Stroomtype|Azure AD-kenmerk|Synchronisatieregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>synchronisatiestroom van cloudMsExchUserHoldPolicies
Standaard cloudMsExchUserHoldPolicies wordt door Azure AD Connect rechtstreeks gesynchroniseerd met het kenmerk cloudMsExchUserHoldPolicies in de metaverse. Als msExchUserHoldPolices niet null is in de metaverse, is het kenmerk vervolgens uitgestroomd naar Active Directory.

In de volgende tabellen wordt de stroom beschreven:

Binnenkomend vanuit Azure AD:

|Active Directory-kenmerk|Kenmerknaam|Stroomtype|Metaverse attribuut|Synchronisatieregel|
|-----|-----|-----|-----|-----|
|On-premises Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|In van AAD - User Exchange|

Uitgaande naar on-premises Active Directory:

|Metaverse attribuut|Kenmerknaam|Stroomtype|Azure AD-kenmerk|Synchronisatieregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|ALS(NIET NULL)|msExchUserHoldPolicies|Out naar AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informatie over het kenmerkgedrag
Het msExchangeUserHoldPolicies is één autoriteitskenmerk.  Er kan één autoriteitskenmerk worden ingesteld op een object (in dit geval gebruikersobject) in de on-premises directory of in de cloudmap.  De regels start van autoriteit dicteren, dat als het kenmerk is gesynchroniseerd vanuit on-premises, azure AD dit kenmerk niet mag bijwerken.

Om gebruikers in staat te stellen een wachtbeleid in te stellen voor een gebruikersobject in de cloud, wordt het kenmerk cloudMSExchangeUserHoldPolicies gebruikt. Dit kenmerk wordt gebruikt omdat Azure AD msExchangeUserHoldPolicies niet rechtstreeks kan instellen op basis van de hierboven beschreven regels.  Dit kenmerk wordt vervolgens gesynchroniseerd met de on-premises directory als het msExchangeUserHoldPolicies niet null is en de huidige waarde van msExchangeUserHoldPolicies vervangt.

Onder bepaalde omstandigheden, bijvoorbeeld, als beide werden veranderd on-premises en in Azure op hetzelfde moment, kan dit leiden tot een aantal problemen.  

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
