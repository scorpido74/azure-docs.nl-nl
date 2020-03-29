---
title: 'Azure AD Connect: verificatie voor doorgeefbewerking - Zo werkt het | Microsoft Documenten'
description: In dit artikel wordt beschreven hoe Azure Active Directory Pass-Through Authentication werkt
services: active-directory
keywords: Azure AD Connect Pass-Through Authentication, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347772"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory Pass-Through-verificatie: technische diepe duik
Dit artikel is een overzicht van hoe Azure Active Directory (Azure AD) Pass-through Authentication werkt. Zie het artikel [Security deep dive](how-to-connect-pta-security-deep-dive.md) voor diepgaande technische en beveiligingsinformatie.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hoe werkt Azure Active Directory Pass-through Authentication?

>[!NOTE]
>Als voorwaarde voor pass-through-verificatie om te werken, moeten gebruikers worden ingericht in Azure AD vanuit on-premises Active Directory met Azure AD Connect. Pass-through Authentication is niet van toepassing op cloudgebruikers.

Wanneer een gebruiker zich probeert aan te melden bij een toepassing die is beveiligd door Azure AD en als verificatie via de pas is ingeschakeld op de tenant, worden de volgende stappen uitgevoerd:

1. De gebruiker probeert toegang te krijgen tot een toepassing, bijvoorbeeld [Outlook Web App](https://outlook.office365.com/owa/).
2. Als de gebruiker nog niet is aangemeld, wordt de gebruiker doorgestuurd naar de **aanmeldingspagina van Azure AD-gebruiker.**
3. De gebruiker voert zijn gebruikersnaam in op de aanmeldingspagina van Azure AD en selecteert vervolgens de knop **Volgende.**
4. De gebruiker voert zijn wachtwoord in op de aanmeldingspagina van Azure AD en selecteert vervolgens de knop **Aanmelden.**
5. Azure AD plaatst bij ontvangst van het verzoek om u aan te melden de gebruikersnaam en het wachtwoord (versleuteld met behulp van de openbare sleutel van de verificatiegemachtigden) in een wachtrij.
6. Een on-premises verificatieagent haalt de gebruikersnaam en het versleutelde wachtwoord uit de wachtrij. Houd er rekening mee dat de agent niet vaak enquêtes voor verzoeken uit de wachtrij, maar haalt aanvragen via een vooraf ingestelde permanente verbinding.
7. De agent decodeert het wachtwoord met behulp van de persoonlijke sleutel.
8. De agent valideert de gebruikersnaam en het wachtwoord ten opzichte van Active Directory met behulp van standaard Windows API's, wat een vergelijkbaar mechanisme is als wat AD FS (Active Directory Federation Services) gebruikt. De gebruikersnaam kan de on-premises standaardgebruikersnaam zijn, meestal `userPrincipalName`of een ander `Alternate ID`kenmerk dat is geconfigureerd in Azure AD Connect (bekend als ).
9. De on-premises Active Directory-domeincontroller (DC) evalueert de aanvraag en retourneert de juiste reactie (succes, fout, wachtwoord verlopen of gebruiker die is vergrendeld) naar de agent.
10. De verificatieagent retourneert dit antwoord op zijn beurt terug naar Azure AD.
11. Azure AD evalueert het antwoord en reageert naar gelang van het geval op de gebruiker. Azure AD meldt de gebruiker bijvoorbeeld onmiddellijk in of vraagt om Azure Multi-Factor Authentication.
12. Als de aanmelding van de gebruiker succesvol is, heeft de gebruiker toegang tot de toepassing.

In het volgende diagram worden alle onderdelen en de betrokken stappen geïllustreerd:

![Pass-through-verificatie](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Volgende stappen
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): Ontdek welke scenario's worden ondersteund en welke niet.
- [Snel aan de slag:](how-to-connect-pta-quick-start.md)aan de slag met Azure AD Pass-through Authentication.
- [Migreren van AD FS naar Pass-through Authentication](https://aka.ms/adfstoPTADP) - Een gedetailleerde handleiding om te migreren van AD FS (of andere federatietechnologieën) naar Pass-through Authentication.
- [Smart Lockout:](../authentication/howto-password-smart-lockout.md)Configureer de Smart Lockout-mogelijkheid op uw tenant om gebruikersaccounts te beschermen.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Vind antwoorden op veelgestelde vragen.
- [Problemen oplossen:](tshoot-connect-pass-through-authentication.md)meer informatie over het oplossen van veelvoorkomende problemen met de functie Doorgeefverificatie.
- [Security Deep Dive:](how-to-connect-pta-security-deep-dive.md)Krijg diepgaande technische informatie over de pass-through authenticatie functie.
- [Azure AD Seamless SSO:](how-to-connect-sso.md)Meer informatie over deze aanvullende functie.
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)gebruik het Azure Active Directory Forum om nieuwe functieaanvragen in te dienen.

