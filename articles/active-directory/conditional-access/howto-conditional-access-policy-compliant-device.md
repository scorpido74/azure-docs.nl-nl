---
title: Voorwaardelijke toegang - Compatibele apparaten vereisen - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om compatibele apparaten te vereisen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295207"
---
# <a name="conditional-access-require-compliant-devices"></a>Voorwaardelijke toegang: apparaten die compatibel zijn vereisen

Organisaties die Microsoft Intune hebben geïmplementeerd, kunnen de vanaf hun apparaten geretourneerde informatie gebruiken om apparaten te identificeren die voldoen aan nalevingsvereisten, zoals:

* Een pincode nodig hebben om te ontgrendelen
* Apparaatversleuteling vereisen
* Een minimale of maximale versie van het besturingssysteem vereisen
* Het vereisen van een apparaat is niet jailbroken of geworteld

Deze nalevingsgegevens worden doorgestuurd naar Azure AD, waar Voorwaardelijke toegang beslissingen kan nemen om toegang tot bronnen te verlenen of te blokkeren. Meer informatie over het nalevingsbeleid van apparaten vindt u in het artikel, [Regels instellen op apparaten om toegang te geven tot bronnen in uw organisatie met Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

Met de volgende stappen u een beleid voor voorwaardelijke toegang maken om te eisen dat apparaten die toegang krijgen tot resources worden gemarkeerd als compatibel met het intune-nalevingsbeleid van uw organisatie.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies de noodtoegangs- of breakglas-accounts van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer **Onder Cloud-apps of acties** > **Omvatten**alle **cloud-apps**.
   1. Als u specifieke toepassingen van uw beleid moet uitsluiten, u deze kiezen op het tabblad **Uitsluiten** onder **Uitgesloten cloud-apps selecteren** en **Selecteren**kiezen.
   1. Selecteer **Done**.
1. Stel **Configureren** in op **Ja**en selecteer **Gereed**onder **Apps** > **voor client (voorbeeld)** instellen .
1. Selecteer **onder Access controls** > **Grant**de optie Apparaat vereisen dat als compatibel moet **zijn gemarkeerd**.
   1. Kies **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

> [!NOTE]
> U uw nieuwe apparaten inschrijven op Intune, zelfs als u Apparaat vereisen selecteert **dat is gemarkeerd als compatibel** voor alle **gebruikers** en **Alle cloud-apps** met de bovenstaande stappen. **Vereisen dat het apparaat moet worden gemarkeerd als compatibel** besturingselement, blokkeert de inschrijving van Intune niet. 

### <a name="known-behavior"></a>Bekend gedrag

Op Windows 7, iOS, Android, macOS en sommige webbrowsers van derden identificeert Azure AD het apparaat met behulp van een clientcertificaat dat is ingericht wanneer het apparaat is geregistreerd bij Azure AD. Wanneer een gebruiker zich voor het eerst aanmeldt via de browser wordt de gebruiker gevraagd het certificaat te selecteren. De eindgebruiker moet dit certificaat selecteren voordat hij de browser kan blijven gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Apparaatnalevingsbeleid werkt met Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
