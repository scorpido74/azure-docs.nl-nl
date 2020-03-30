---
title: Problemen met voorwaardelijke toegang oplossen met het gereedschap Wat als - Azure Active Directory
description: Waar u vinden welke voorwaardene voorwaarden beleid werden toegepast en waarom
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175806"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Probleemoplossing voorwaardelijke toegang met het gereedschap Wat als

Het [gereedschap Wat als](what-if-tool.md) in Voorwaardelijke toegang is krachtig wanneer u probeert te begrijpen waarom een beleid al dan niet is toegepast op een gebruiker in een specifieke omstandigheid of als een beleid in een bekende status van toepassing zou zijn.

Het gereedschap Wat als bevindt zich in de **Azure-portal** > **Azure Active Directory** > **voorwaardelijke toegang tot** > **wat als**.

![Voorwaardelijke toegang wat als gereedschap bij standaardstatus](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Het gereedschap Wat als evalueert momenteel het beleid niet in de modus Alleen rapporteren.

## <a name="gathering-information"></a>Informatie verzamelen

Met het gereedschap Wat als hoeft alleen een **gebruiker** aan de slag te gaan. 

De volgende aanvullende informatie is optioneel, maar zal helpen om de ruimte voor specifieke gevallen te verkleinen.

* Cloud-apps of acties
* IP-adres 
* Land
* Apparaatplatform
* Client-apps (voorbeeld)
* Apparaatstatus (voorbeeld) 
* Aanmeldingsrisico

Deze informatie kan worden verzameld van de gebruiker, het apparaat of het Azure AD-aanmeldingslogboek.

## <a name="generating-results"></a>Resultaten genereren

Voer de criteria in die in de vorige sectie zijn verzameld en selecteer **Wat als u** een lijst met resultaten wilt genereren. 

Op elk gewenst moment u **Opnieuw instellen** selecteren om de invoer van criteria te wissen en terug te keren naar de standaardstatus.

## <a name="evaluating-results"></a>Resultaten evalueren

### <a name="policies-that-will-apply"></a>Beleid dat van toepassing is

In deze lijst wordt weergegeven welk beleid voor voorwaardelijke toegang van toepassing is, afhankelijk van de voorwaarden. De lijst bevat zowel de subsidie- als sessiebesturingselementen die van toepassing zijn. Voorbeelden hiervan zijn het vereisen van multi-factor authenticatie om toegang te krijgen tot een specifieke toepassing.

### <a name="policies-that-will-not-apply"></a>Beleid dat niet van toepassing is

In deze lijst wordt beleid voor voorwaardelijke toegang weergegeven dat niet van toepassing is als de voorwaarden van toepassing zijn. De lijst bevat alle beleidsregels en de reden waarom ze niet van toepassing zijn. Voorbeelden hiervan zijn gebruikers en groepen die mogelijk van een beleid zijn uitgesloten.

## <a name="use-case"></a>Gebruiksvoorbeeld

Veel organisaties maken beleid op basis van netwerklocaties, waardoor vertrouwde locaties worden toegestaan en locaties worden geblokkeerd waar geen toegang mag plaatsvinden.

Om te valideren dat een configuratie op de juiste manier is gemaakt, kan een beheerder het gereedschap Wat als gebruiken om toegang na te bootsen, vanaf een locatie die moet worden toegestaan en vanaf een locatie die moet worden geweigerd.

![Wat als-gereedschap met resultaten met Bloktoegang](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

In dit geval zou de gebruiker worden geblokkeerd van de toegang tot een cloud-app op hun reis naar Noord-Korea als Contoso heeft geblokkeerd toegang vanaf die locatie.

Deze test kan worden uitgebreid met andere gegevenspunten om het bereik te verkleinen.

## <a name="next-steps"></a>Volgende stappen

* [Wat is voorwaardelijke toegang?](overview.md)
* [Wat is Azure Active Directory Identity Protection](../identity-protection/overview-v2.md)
* [Wat is een apparaat-id?](../devices/overview.md)
* [Hoe werkt het - Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
