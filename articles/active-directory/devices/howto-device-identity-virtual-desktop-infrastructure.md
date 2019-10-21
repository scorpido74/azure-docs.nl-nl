---
title: Apparaat-id en bureau blad-virtualisatie-Azure Active Directory
description: Meer informatie over hoe VDI en Azure AD-apparaat-id's samen kunnen worden gebruikt
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
ms.openlocfilehash: 1a1cba2c4572b2f898f631aefbbf316fae1195ac
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596359"
---
# <a name="device-identity-and-desktop-virtualization"></a>Apparaat-id en desktop-virtualisatie

Beheerders implementeren ook VDI-platforms (Virtual Desktop Infrastructure) die als host fungeren voor Windows-besturings systemen in hun organisaties. Beheerders implementeren VDI voor:

- Stroom lijn beheer.
- Verminder de kosten door consolidatie en gecentraliseering van resources.
- Lever eind gebruikers mobiliteit en de vrijheid om op elk gewenst moment toegang te krijgen tot virtuele Bureau bladen, vanaf elke locatie, op elk apparaat.

Er zijn twee primaire typen virtuele Bureau bladen:

- Permanent
- Niet-persistent

Permanente versies gebruiken een unieke bureaublad installatie kopie voor elke gebruiker of een groep gebruikers. Deze unieke Bureau bladen kunnen worden aangepast en opgeslagen voor toekomstig gebruik. 

Voor niet-permanente versies wordt gebruikgemaakt van een verzameling bureau bladen die gebruikers op een naar behoefte gebaseerde toegang hebben. Deze niet-permanente Bureau bladen worden teruggezet naar de oorspronkelijke staat nadat de gebruiker zich afmeldt.

In dit artikel worden de richt lijnen van micro soft behandeld voor beheerders met ondersteuning voor de apparaat-id en VDI. Zie het artikel [Wat is een apparaat-id](overview.md)? voor meer informatie over de identiteit van het apparaat.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Voordat u apparaat-id's configureert in azure AD voor uw VDI-omgeving, moet u vertrouwd raken met de ondersteunde scenario's. In de onderstaande tabel ziet u welke inrichtings scenario's worden ondersteund. Het inrichten in deze context impliceert dat een beheerder apparaat-id's op schaal kan configureren zonder dat hiervoor interactie van de eind gebruiker is vereist.

| Type apparaat-id | Identiteits infrastructuur | Windows-apparaten | VDI-platform versie | Ondersteund |
| --- | --- | --- | --- | --- |
| Hybride Azure AD-join | Federatie | Windows huidige * * * en Windows down level * * * * | Permanent | Ja |
|   |   |   | Niet-persistent | Ja |
|   | Beheerd * * | Windows huidige en Windows down level | Permanent | Ja |
|   |   | Downlevel Windows | Niet-persistent | Ja |
|   |   | Windows actueel | Niet-persistent | Nee |
| Toegevoegd aan Azure AD | Federatie | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
|   | Managed | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
| Geregistreerde Azure AD | Federatie | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
|   | Managed | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |

\* een infra structuur voor **federatieve** identiteiten is een omgeving met een id-provider, zoals AD FS of een andere IDP van derden.

\* \* een omgeving met **beheerde** identiteits infrastructuur vertegenwoordigt een omgeving met Azure AD als de ID-provider die is geïmplementeerd met een [hash-synchronisatie (PHS)](../hybrid/whatis-phs.md) of [Pass Through-verificatie (PTA)](../hybrid/how-to-connect-pta.md) met [ naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md).

\* \* \* **Windows huidige** apparaten vertegenwoordigen Windows 10, windows server 2016 en windows server 2019.

\* \* \* \* **Windows-apparaten op lagere niveaus** Windows 7, Windows 8,1, windows server 2008 R2, Windows Server 2012 en windows server 2012 R2 vertegenwoordigen. Zie [ondersteuning voor Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)voor meer informatie over ondersteuning voor Windows 7. Zie voor ondersteunings informatie over Windows Server 2008 R2 voor [bereiding op Windows server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Richt lijn van micro soft

Beheerders moeten verwijzen naar de volgende artikelen, op basis van hun identiteits infrastructuur, voor meer informatie over het configureren van hybride Azure AD-deelname.

- [Hybride Azure Active Directory-koppeling configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
- [Hybride Azure Active Directory-deelname configureren voor een beheerde omgeving](hybrid-azuread-join-managed-domains.md)

Als u afhankelijk bent van het hulp programma voor systeem voorbereiding (Sysprep. exe) en als u een installatie kopie van vóór Windows 10 1809 gebruikt, moet u ervoor zorgen dat de installatie kopie niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride Azure AD join.

Als u een moment opname van een virtuele machine (VM) vertrouwt om extra Vm's te maken, moet u ervoor zorgen dat de moment opname niet afkomstig is van een VM die al is geregistreerd bij Azure AD als hybride Azure AD-deelname.

Bij het implementeren van niet-persistente VDI, moeten IT-beheerders bijna de aandacht best Eden aan het beheer van verouderde apparaten in azure AD. Micro soft adviseert IT-beheerders de volgende instructies uit te voeren. Als u dit niet doet, zal uw directory veel verouderde Hybrid Azure AD-apparaten hebben die zijn geregistreerd bij het niet-permanente VDI-platform.

- Maak en gebruik een voor voegsel voor de weergave naam van de computer die het bureau blad als VDI-gebaseerd geeft.
- Implementeer de volgende opdrachten als onderdeel van het afmeldings script. Met deze opdrachten wordt een beste oproep naar Azure AD geactiveerd om het apparaat te verwijderen.
   - Voor Windows-huidige apparaten – dsregcmd. exe/Leave
   - Voor Windows-apparaten op lagere niveaus: autowerkplek. exe/Leave
- Definieer en implementeer het proces voor het [beheren van verouderde apparaten](manage-stale-devices.md).
   - Zodra u een strategie hebt voor het identificeren van uw niet-permanente hybride Azure AD-apparaten, kunt u zich op het opschonen van deze apparaten agressief maken om ervoor te zorgen dat uw Directory niet wordt gebruikt met veel verouderde apparaten.
 
## <a name="next-steps"></a>Volgende stappen

[Hybride Azure Active Directory-koppeling configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
