---
title: 'Azure AD Connect: msExchUserHoldPolicies en cloudMsExchUserHoldPolicies | Microsoft Docs'
description: In dit onderwerp wordt het kenmerk gedrag van de kenmerken msExchUserHoldPolicies en cloudMsExchUserHoldPolicies beschreven
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
ms.openlocfilehash: e4950906e016b9e1915d18ecacc1edcfda8b4d09
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279394"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies en cloudMsExchUserHoldPolicies
In het volgende referentie document worden deze kenmerken beschreven die worden gebruikt door Exchange en de juiste manier om de standaard synchronisatie regels te bewerken.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Wat zijn msExchUserHoldPolicies en cloudMsExchUserHoldPolicies?
Er zijn twee soorten [bewaringen](/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) beschikbaar voor een Exchange-Server: in-place bewaring. Als er een afwachting is ingeschakeld, worden alle items in de wachtrij geplaatst.  Een in-place bewaring wordt gebruikt om alleen die items te bewaren die voldoen aan de criteria van een zoek opdracht die u hebt gedefinieerd met behulp van de in-place eDiscovery-tool.

Met de MsExchUserHoldPolcies-en cloudMsExchUserHoldPolicies-kenmerken kunnen lokale AD-en Azure AD-locaties bepalen welke gebruikers zich in een wacht ruimte bevinden, afhankelijk van of ze on-premises Exchange of Exchange Online gebruiken.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies-synchronisatie stroom
Standaard wordt MsExchUserHoldPolcies gesynchroniseerd door Azure AD Connect rechtstreeks naar het kenmerk msExchUserHoldPolicies in de tekst en vervolgens naar het kenmerk msExchUserHoldPolices in azure AD

In de volgende tabellen wordt de stroom beschreven:

Inkomend van on-premises Active Directory:

|Active Directory kenmerk|Kenmerk naam|Stroom type|Omgekeerd kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|On-premises Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|In van de uitwisseling van AD-gebruikers|

Uitgaand naar Azure AD:

|Omgekeerd kenmerk|Kenmerk naam|Stroom type|Azure AD-kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Naar AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies-synchronisatie stroom
Standaard wordt cloudMsExchUserHoldPolicies gesynchroniseerd door Azure AD Connect rechtstreeks naar het kenmerk cloudMsExchUserHoldPolicies in de tekst. Als msExchUserHoldPolices niet null is in de tekst, wordt het kenmerk gestroomd naar Active Directory.

In de volgende tabellen wordt de stroom beschreven:

Binnenkomend vanuit Azure AD:

|Active Directory kenmerk|Kenmerk naam|Stroom type|Omgekeerd kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|On-premises Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|In van AAD-gebruikers uitwisseling|

Uitgaand naar on-premises Active Directory:

|Omgekeerd kenmerk|Kenmerk naam|Stroom type|Azure AD-kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Naar AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informatie over het kenmerk gedrag
De msExchangeUserHoldPolicies is één instantie kenmerk.  Een kenmerk met één instantie kan worden ingesteld voor een object (in dit geval gebruikers object) in de on-premises map of in de Cloud Directory.  Het begin van de Autoriteits regels bepaalt dat als het kenmerk vanuit on-premises is gesynchroniseerd, dit kenmerk niet door Azure AD kan worden bijgewerkt.

Het kenmerk cloudMSExchangeUserHoldPolicies wordt gebruikt om gebruikers toe te staan een blokkerings beleid in te stellen voor een gebruikers object in de Cloud. Dit kenmerk wordt gebruikt omdat Azure AD msExchangeUserHoldPolicies niet rechtstreeks kan instellen op basis van de hierboven beschreven regels.  Dit kenmerk wordt vervolgens opnieuw gesynchroniseerd naar de on-premises Directory als de msExchangeUserHoldPolicies niet null is en de huidige waarde van msExchangeUserHoldPolicies vervangen.

Onder bepaalde omstandigheden, bijvoorbeeld als beide on-premises en in azure op hetzelfde moment zijn gewijzigd, kan dit een aantal problemen veroorzaken.  

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).