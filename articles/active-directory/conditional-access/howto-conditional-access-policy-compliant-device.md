---
title: 'Voorwaardelijke toegang: compatibele apparaten vereisen-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken om compatibele apparaten te vereisen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d19cc39147608dafc4576f4c8bad0533ad1b32d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521911"
---
# <a name="conditional-access-require-compliant-devices"></a>Voorwaardelijke toegang: compatibele apparaten vereisen

Organisaties die Microsoft Intune hebben geïmplementeerd, kunnen de informatie gebruiken die wordt geretourneerd door hun apparaten om apparaten te identificeren die voldoen aan de nalevings vereisten, zoals:

* Een pincode vereisen om te ontgrendelen
* Apparaatversleuteling vereisen
* Een minimum-of maximum versie van het besturings systeem vereisen
* Vereisen dat een apparaat niet is opengebroken of geroot

Deze informatie over beleids naleving wordt doorgestuurd naar Azure AD, waarbij voorwaardelijke toegang kan besluiten om toegang tot resources toe te kennen of te blok keren. Meer informatie over nalevings beleid voor apparaten vindt u in het artikel, [regels instellen op apparaten om toegang tot resources in uw organisatie toe te staan met intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om te vereisen dat apparaten die toegang hebben tot resources, worden gemarkeerd als compatibel met het intune-nalevings beleid van uw organisatie.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer onder **Cloud-apps of acties** > **bevatten** **alle Cloud-apps**.
   1. Als u specifieke toepassingen van uw beleid moet uitsluiten, kunt u ze kiezen op het tabblad **uitsluiten** onder **Selecteer uitgesloten Cloud-apps** en kiest u **selecteren**.
   1. Selecteer **Done**.
1. Selecteer in **toegangs beheer** > **toekenning toestaan** **dat apparaat moet worden gemarkeerd als compatibel**.
   1. Kies **Selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

### <a name="known-behavior"></a>Bekend gedrag

In Windows 7, iOS, Android, macOS en sommige webbrowsers van derden identificeert het apparaat met behulp van een client certificaat dat is ingericht op het moment dat het apparaat wordt geregistreerd bij Azure AD. Wanneer een gebruiker zich voor het eerst aanmeldt via de browser, wordt de gebruiker gevraagd het certificaat te selecteren. De eind gebruiker moet dit certificaat selecteren om de browser te kunnen blijven gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Nalevings beleid voor apparaten werkt met Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
