---
title: Gebruikers die zijn gemarkeerd voor risico's in de Azure Active Directory-portal | Microsoft Documenten
description: Meer informatie over het beveiligingsrapport over gebruikers voor wie wordt aangegeven dat ze risico lopen in de Azure Active Directory-portal
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30d02c5484ea4cce2953eac6b1b7b26a17c142bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68989700"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Gebruikers herstellen voor wie wordt aangegeven dat ze risico lopen in de Azure Active Directory-portal

Met de beveiligingsrapporten in de Azure Active Directory (Azure AD) u de waarschijnlijkheid van gecompromitteerde gebruikersaccounts in uw omgeving meten. Een gebruiker voor wie wordt aangegeven dat deze risico loopt is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Microsoft spant zich in om uw omgevingen veilig te houden. Als onderdeel van deze inspanningen controleert Microsoft doorlopend activiteiten die ongebruikelijk zijn of consistent met bekende aanvalspatronen. 

Als ongebruikelijke activiteiten die erop wijzen dat er ongeautoriseerde toegang tot bepaalde accounts van uw gebruikers worden gedetecteerd, ontvangt u meldingen waarmee u actie ondernemen. Dit betekent niet dat de eigen systemen van Microsoft zijn gecompromitteerd.

## <a name="access-the-users-flagged-for-risk-report"></a>Gebruikers bekijken voor wie wordt aangegeven dat ze risico lopen

U gebruikers die zijn gemarkeerd op risico's bekijken via het [risicorapport van gebruikers](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) in de Azure-portal. Als u geen Azure AD hebt, u [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD)zich gratis aanmelden bij. 

Van de gebruikers die zijn gemarkeerd voor risicorapport, u de volgende acties voor elke gebruiker uitvoeren:

- Een tijdelijk wachtwoord genereren
- Vereisen dat gebruikers veilig hun wachtwoord opnieuw instellen de volgende keer dat ze zich aanmelden
- Het gebruikersrisico afwijzen zonder een herstelactie uit te voeren.

Zie [Gebruikers die zijn gemarkeerd voor risicobeveiligingsrapport voor](concept-user-at-risk.md)meer informatie .

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-abonnement voor Office 365-klanten

U uw Office 365-referenties ook gebruiken om toegang te krijgen tot het **Azure-beheercentrum.** Nadat u de toegang tot Azure AD hebt geactiveerd, wordt u doorgestuurd naar de Azure AD-portal. Bij een basisabonnement worden minder details in rapporten verstrekt. Aanvullende gegevens en analyses zijn verkrijgbaar voor abonnees van Azure Premium.

Ga als bedoeld als u de **gemarkeerde gebruikers voor risicorapporten** in het Microsoft 365-beheercentrum wilt openen:

1.  Selecteer **Beheercentra**in het navigatiemenu aan de linkerkant . 
2.  Selecteer **Azure AD**.
3.  Meld u aan bij **Azure Active Directory-beheercentrum**.
4.  Als boven aan de pagina met de tekst **Bekijk de nieuwe portal**een banner wordt weergegeven, selecteert u de koppeling.
4.  Selecteer **Azure Active Directory**in het navigatiemenu aan de linkerkant . 
5.  Selecteer in het navigatiedeelvenster **Gebruikers die zijn gemarkeerd voor risico's** in de sectie **Beveiliging.**

## <a name="remediation-actions"></a>Herstelacties

Onderneem de volgende acties om de betreffende accounts te herstellen en uw omgeving te beveiligen:

1.  [Valideer de juiste informatie](https://aka.ms/MFAValid) voor multi-factor authenticatie en self-service wachtwoord reset. 
2.  [Multifactorauthenticatie inschakelen](https://aka.ms/MFAuth) voor alle gebruikers. 
3.  Gebruik dit [herstelscript](https://aka.ms/remediate) voor elk account dat van invloed is, om automatisch de volgende stappen uit te voeren: 

    a. Reset het wachtwoord om het account te beveiligen en actieve sessies te doden.

    b. Verwijder gemachtigden voor een postvak.

    c. Schakel regels voor het doorsturen van e-mail voor externe domeinen uit.

    d. Verwijder de eigenschap voor het globaal doorsturen van e-mail voor het postvak uit.

    e. Schakel MFA voor het account van de gebruiker in.

    f. Stel de wachtwoordcomplexiteit voor het account in op Hoog.

    g. Schakel postvakcontrole in.

    h. Maak een controlelogboek voor de beheerder om te controleren.

4. Onderzoek uw Office 365-tenant en andere IT-infrastructuur, waaronder een controle van alle tenantinstellingen, gebruikersaccounts en de configuratie-instellingen per gebruiker op mogelijke wijzigingen. Controleer op de aanwezigheid van indicatoren voor persistentiemethoden, evenals indicatoren dat een indringer gebruik kan hebben gemaakt van een eerste aangrijpingspunt om VPN-referenties te verkrijgen, of toegang tot andere organisatieaccounts. 

5.  Als onderdeel van uw onderzoek moet u overwegen of u overheidsinstanties, inclusief rechtshandhaving, moet inlichten.

Daarnaast dient u:

- Lees en implementeer deze [richtlijnen voor het aanpakken van ongebruikelijke activiteiten](https://aka.ms/fixaccount). 
- [Schakel de auditpijplijn in](https://aka.ms/improvesecurity) om u te helpen de activiteit in uw tenant te analyseren. Zodra deze is voltooid, begint uw controlewinkel te vullen met activiteitslogboeken. Op dit moment u ook gebruikmaken van de [zoek- en onderzoeksbron](https://aka.ms/sccsearch)van het Security and Compliance Center. 
- Gebruik dit [script om postvakcontrole](https://aka.ms/mailboxaudit1) in te schakelen voor al uw accounts. 
- Bekijk voor alle postvakken de machtigingen voor gemachtigden en de regels voor het doorsturen van e-mail. U kunt dit [PowerShell-script](https://aka.ms/delegateforwardrules) gebruiken om deze taak uit te voeren. 

## <a name="next-steps"></a>Volgende stappen

* [Azure Active Directory-identiteitsbeveiliging](../active-directory-identityprotection.md)
* [Gebruikers voor wie wordt aangegeven dat ze risico lopen](concept-user-at-risk.md)
