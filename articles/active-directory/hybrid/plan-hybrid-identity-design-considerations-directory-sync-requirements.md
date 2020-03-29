---
title: Hybride identiteitsontwerp - vereisten voor het synchroniseren van directory's Azure | Microsoft Documenten
description: Bepaal welke vereisten nodig zijn voor het synchroniseren van alle gebruikers tussen on=premises en cloud voor de onderneming.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381158"
---
# <a name="determine-directory-synchronization-requirements"></a>Adreslijstsynchronisatievereisten bepalen
Synchronisatie is alles over het verstrekken van gebruikers een identiteit in de cloud op basis van hun on-premises identiteit. Of ze nu wel of niet gesynchroniseerde account gebruiken voor authenticatie of federatieve verificatie, de gebruikers moeten nog steeds een identiteit in de cloud hebben.  Deze identiteit moet periodiek worden gehandhaafd en bijgewerkt.  De updates kunnen vele vormen aannemen, van titelwijzigingen tot wachtwoordwijzigingen.  

Begin met het evalueren van de on-premises identiteitsoplossing en gebruikersvereisten van organisaties. Deze evaluatie is belangrijk om de technische vereisten te definiëren voor de manier waarop gebruikersidentiteiten worden gemaakt en onderhouden in de cloud.  Voor een meerderheid van de organisaties is Active Directory on-premises en is het de on-premises directory waargebruikers vanaf worden gesynchroniseerd, maar in sommige gevallen zal dit niet het geval zijn.  

Zorg ervoor dat u de volgende vragen beantwoordt:

* Heeft u een AD-forest, meerdere of geen?
  
  * Naar hoeveel Azure AD-mappen wordt u gesynchroniseerd?
    
    1. Gebruik je filteren?
    2. Zijn er meerdere Azure AD Connect-servers gepland?
* Heeft u momenteel een synchronisatietool on-premises?
  
  * Zo ja, doen uw gebruikers als gebruikers een virtuele directory / integratie van identiteiten?
* Heeft u een andere directory on-premises die u wilt synchroniseren (bijvoorbeeld LDAP Directory, HR-database, enz.)?
  * Ga je galsync doen?
  * Wat is de huidige status van UPN's in uw organisatie? 
  * Heeft u een andere directory die gebruikers verifiëren tegen?
  * Gebruikt uw bedrijf Microsoft Exchange?
    * Zijn ze van plan om een hybride uitwisseling inzet?

Nu u een idee hebt over uw synchronisatievereisten, moet u bepalen welk gereedschap de juiste is om aan deze vereisten te voldoen.  Microsoft biedt verschillende tools om directory-integratie en synchronisatie te bereiken.  Zie de [vergelijkingstabel voor integratietools](plan-hybrid-identity-design-considerations-tools-comparison.md) voor hybride identiteit voor directory-integratie voor meer informatie. 

Nu u uw synchronisatievereisten en de tool hebt die dit voor uw bedrijf zal bereiken, moet u de toepassingen evalueren die deze directoryservices gebruiken. Deze evaluatie is belangrijk om de technische vereisten te definiëren om deze toepassingen in de cloud te integreren. Zorg ervoor dat u de volgende vragen beantwoordt:

* Worden deze toepassingen verplaatst naar de cloud en wordt de directory gebruikt?
* Zijn er speciale kenmerken die moeten worden gesynchroniseerd met de cloud, zodat deze toepassingen ze met succes kunnen gebruiken?
* Moeten deze applicaties opnieuw worden geschreven om te profiteren van cloud auth?
* Blijven deze toepassingen on-premises leven terwijl gebruikers er toegang toe hebben via de cloudidentiteit?

U moet ook de beveiligingsvereisten en beperkingen van de adreslijstsynchronisatie bepalen. Deze evaluatie is belangrijk om een lijst te krijgen van de vereisten die nodig zijn om de identiteit van de gebruiker in de cloud te creëren en te behouden. Zorg ervoor dat u de volgende vragen beantwoordt:

* Waar bevindt zich de synchronisatieserver?
* Zal het domein worden samengevoegd?
* Bevindt de server zich op een beperkt netwerk achter een firewall, zoals een DMZ?
  * u de vereiste firewallpoorten openen om synchronisatie te ondersteunen?
* Heeft u een noodherstelplan voor de synchronisatieserver?
* Heeft u een account met de juiste machtigingen voor alle forests waarmee u wilt synchroniseren?
  * Als uw bedrijf het antwoord op deze vraag niet weet, bekijkt u de sectie 'Machtigingen voor wachtwoordsynchronisatie' in het artikel [Installeer de Azure Active Directory Sync Service](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) en bepaalt u of u al een account met deze machtigingen hebt of dat u er een moet maken.
* Als u mutli-forest sync is de sync server in staat om naar elk bos?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Bepaal incident response eisen](plan-hybrid-identity-design-considerations-incident-response-requirements.md) zal gaan over de beschikbare opties. Door deze vragen te hebben beantwoord, selecteert u welke optie het beste bij uw bedrijfsbehoeften past.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor meervoudige verificatie bepalen](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

