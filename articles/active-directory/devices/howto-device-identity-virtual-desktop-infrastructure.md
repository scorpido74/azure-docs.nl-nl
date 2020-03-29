---
title: Apparaatidentiteit en desktopvirtualisatie - Azure Active Directory
description: Meer informatie over hoe VDI- en Azure AD-apparaatidentiteiten samen kunnen worden gebruikt
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900376"
---
# <a name="device-identity-and-desktop-virtualization"></a>Apparaatidentiteit en desktopvirtualisatie

Beheerders implementeren vaak VDI-platforms (Virtual Desktop Infrastructure) die Windows-besturingssystemen hosten in hun organisaties. Beheerders implementeren VDI naar:

- Stroomlijn het beheer.
- Verlaag de kosten door consolidatie en centralisatie van middelen.
- Lever mobiliteit van eindgebruikers en de vrijheid om altijd en overal toegang te krijgen tot virtuele desktops, op elk apparaat.

Er zijn twee primaire typen virtuele bureaubladen:

- Permanent
- Niet-persistent

Permanente versies gebruiken een unieke bureaubladafbeelding voor elke gebruiker of een groep gebruikers. Deze unieke desktops kunnen worden aangepast en opgeslagen voor toekomstig gebruik. 

Niet-permanente versies maken gebruik van een verzameling desktops die gebruikers naar behoefte kunnen openen. Deze niet-permanente desktops worden teruggezet naar hun oorspronkelijke staat nadat de gebruiker zich heeft afmeldt.

Dit artikel gaat over de richtlijnen van Microsoft voor beheerders over ondersteuning voor apparaatidentiteit en VDI. Zie het artikel Wat is [een apparaatidentiteit](overview.md)voor meer informatie over de identiteit van het apparaat.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Voordat u apparaatidentiteiten configureert in Azure AD voor uw VDI-omgeving, moet u vertrouwd raken met de ondersteunde scenario's. De onderstaande tabel illustreert welke inrichtingsscenario's worden ondersteund. Inrichten in deze context houdt in dat een beheerder apparaatidentiteiten op schaal kan configureren zonder dat er interactie tussen eindgebruikers nodig is.

| Apparaatidentiteitstype | Identiteitsinfrastructuur | Windows-apparaten | VDI-platformversie | Ondersteund |
| --- | --- | --- | --- | --- |
| lid is van hybride Azure Active Directory | Gefedereerd* | Windows huidige*** en Windows down-level**** | Permanent | Ja |
|   |   | Windows actueel | Niet-persistent | Nee |
|   |   | Downlevel Windows | Niet-persistent | Ja |
|   | Beheerd** | Windows huidige en Windows down-level | Permanent | Ja |
|   |   | Windows actueel | Niet-persistent | Nee |
|   |   | Downlevel Windows | Niet-persistent | Ja |
| Neemt deel aan Azure AD | Federatief | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
|   | Beheerd | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
| Azure AD-geregistreerd | Federatief | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
|   | Beheerd | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |

\*Een **Federated** identity infrastructure-omgeving vertegenwoordigt een omgeving met een identiteitsprovider zoals AD FS of andere IDP van derden.

\*\*Een **beheerde** identiteitsinfrastructuuromgeving vertegenwoordigt een omgeving met Azure AD als de identiteitsprovider die is geïmplementeerd met [een wachtwoordhashsync (PHS)](../hybrid/whatis-phs.md) of [pass-through-verificatie (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze enkele aanmelding.](../hybrid/how-to-connect-sso.md)

\*\*\***Windows huidige** apparaten vertegenwoordigen Windows 10, Windows Server 2016 en Windows Server 2019.

\*\*\*\***Windows down-level apparaten** vertegenwoordigen Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2. Zie Ondersteuning voor Windows [7 loopt af voor](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)ondersteuningsinformatie over Windows 7. Zie Voorbereiden op het einde van de [ondersteuning van Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008)R2 voor ondersteuningsinformatie op Windows Server 2008.

## <a name="microsofts-guidance"></a>Richtlijnen van Microsoft

Beheerders moeten verwijzen naar de volgende artikelen, op basis van hun identiteitsinfrastructuur, om te leren hoe u hybride Azure AD-join configureren.

- [Hybride Azure Active Directory join configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
- [Hybride Azure Active Directory join configureren voor beheerde omgeving](hybrid-azuread-join-managed-domains.md)

Als u vertrouwt op het systeemvoorbereidingshulpprogramma (sysprep.exe) en als u een afbeelding van voor Windows 10 1809 gebruikt voor installatie, moet u ervoor zorgen dat deze afbeelding niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride Azure AD is toegetreden.

Als u een vm-momentopname (Virtual Machine) gebruikt om extra VM's te maken, moet u ervoor zorgen dat momentopname niet afkomstig is van een vm die al is geregistreerd bij Azure AD, omdat hybride Azure AD lid wordt.

Bij het implementeren van niet-permanente VDI moeten IT-beheerders veel aandacht besteden aan het beheren van verouderde apparaten in Azure AD. Microsoft raadt IT-beheerders aan onderstaande richtlijnen te implementeren. Als u dit niet doet, wordt in uw directory veel verouderde Hybride Azure AD-apparaten weergegeven die zijn geregistreerd vanaf uw niet-permanente VDI-platform.

- Maak en gebruik een voorvoegsel voor de weergavenaam van de computer die het bureaublad aangeeft als VDI-gebaseerd.
- Implementeer de volgende opdracht als onderdeel van het afmeldscript. Met deze opdracht wordt een oproep naar Azure AD in het beste geval geactiveerd om het apparaat te verwijderen.
   - Voor Windows down-level apparaten - autoworkplace.exe / leave
- Definieer en implementeer het proces voor [het beheren van verouderde apparaten.](manage-stale-devices.md)
   - Zodra u een strategie hebt om uw niet-permanente hybride Azure AD-apparaten te identificeren, u agressiever zijn bij het opschonen van deze apparaten om ervoor te zorgen dat uw directory niet wordt verbruikt met veel verouderde apparaten.
 
## <a name="next-steps"></a>Volgende stappen

[Hybride Azure Active Directory-join configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
