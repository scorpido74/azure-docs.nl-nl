---
title: 'Quick Start: toegang blok keren als er een sessie risico wordt gedetecteerd met Azure Active Directory Identity Protection | Microsoft Docs'
description: In deze Quick Start leert u hoe u een beleid voor voorwaardelijke toegang voor het aanmelden van identiteits beveiliging van Azure Active Directory (Azure AD) kunt configureren om aanmeldingen te blok keren op basis van sessie Risico's.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5e5e26a27e13ba09ffcc97e0b2b0f1b37bc8bd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127703"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Quickstart: Toegang blok keren als er een sessie risico wordt gedetecteerd met Azure Active Directory Identity Protection  

Als u uw omgeving wilt beveiligen, wilt u wellicht verdachte gebruikers blok keren om zich aan te melden. Azure Active Directory (Azure AD) Identity Protection analyseert elke aanmelding en berekent de kans dat een aanmeldings poging niet is uitgevoerd door de rechtmatige eigenaar van een gebruikers account. De kans (laag, gemiddeld, hoog) wordt aangegeven in de vorm van een berekende waarde met de naam aanmeldings risico niveau. Door de aanmeldings risico voorwaarde in te stellen, kunt u een beleid voor voorwaardelijke toegang voor aanmeldings Risico's configureren om te reageren op specifieke risico niveaus. 

In deze Quick start ziet u hoe u een beleid voor voorwaardelijke toegang voor aanmeldings Risico's configureert waarmee een aanmelding wordt geblokkeerd wanneer een medium en een hoger aanmeldings risico niveau is gedetecteerd. 

![Beleid maken](./media/quickstart-sign-in-risk-policy/1004.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten 

Voor het voltooien van het scenario in deze zelfstudie hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium P2-editie** -Azure AD Identity Protection is een Azure AD Premium P2-functie. 
- **Identiteits beveiliging** : voor het scenario in deze Snelstartgids moet identiteits beveiliging zijn ingeschakeld. Als u niet weet hoe u identiteits beveiliging kunt inschakelen, raadpleegt u [Azure Active Directory Identity Protection](../identity-protection/enable.md)in te scha kelen.
- **Tor** -browser: de [Tor-Browser](https://www.torproject.org/projects/torbrowser.html.en) is ontworpen om u te helpen uw privacy online te bewaren. Identiteits beveiliging detecteert een aanmelding vanuit een Tor-browser als aanmeldingen **vanaf anonieme IP-adressen**, die een gemiddeld risico niveau hebben. Zie [Azure Active Directory-risico detectie](../reports-monitoring/concept-risk-events.md)voor meer informatie.  
- **Een test account met de naam Alain Charon** : als u niet weet hoe u een test account moet maken, raadpleegt u [een nieuwe gebruiker toevoegen](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Uw aanmelding testen 

Het doel van deze stap is om ervoor te zorgen dat uw test account toegang heeft tot uw Tenant met behulp van de Tor-browser.

**Uw aanmelding testen:**

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als **Alain Charon**.
2. Meld u af. 

## <a name="create-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang maken 

In het scenario in deze Snelstartgids wordt een aanmelding van een Tor-browser gebruikt om een gedetecteerde aanmeldingen te genereren op basis van een risico detectie voor **Anonieme IP-adressen** . Het risico niveau van deze risico detectie is gemiddeld. Als u wilt reageren op deze risico detectie, stelt u de risico voorwaarde voor aanmelden in op gemiddeld. 

In deze sectie wordt uitgelegd hoe u het vereiste beleid voor voorwaardelijke toegang voor aanmeldings Risico's maakt. Stel in uw beleid het volgende in:

|Instelling |Value|
|---     | --- |
| Gebruikers  | Alain Charon  |
| Voorwaarden | Aanmeldings risico, normaal en hoger |
| Besturingselementen | Toegang blokkeren |

![Beleid maken](./media/quickstart-sign-in-risk-policy/201.png)

**Het beleid voor voorwaardelijke toegang configureren:**

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als globale beheerder.
2. Ga naar de [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Klik op de pagina **Azure AD Identity Protection** in de sectie **configureren** op **risico beleid voor aanmelden**.
4. Klik op de pagina beleid in de sectie **toewijzingen** op **gebruikers**.
5. Klik op de pagina **gebruikers** op **gebruikers selecteren**.
6. Selecteer op de pagina **gebruikers selecteren** de optie **Alain Charon**en klik vervolgens op **selecteren**.
7. Klik op de pagina **gebruikers** op **gereed**. 
8. Klik op de pagina beleid in de sectie **toewijzingen** op **voor waarden**.
9. Klik op **aanmeldings risico**op de pagina **voor waarden** .
10. Selecteer op de pagina **aanmeldings risico** de optie **gemiddeld en hoger**en klik vervolgens op **selecteren**. 
11. Klik op **gereed**op de pagina **voor waarden** .
12. Klik op de pagina beleid in de sectie **besturings elementen** op **toegang**.
13. Klik op de pagina **toegang** op **toegang toestaan**, selecteer **multi-factor Authentication vereisen**en klik vervolgens op **selecteren**.
14. Klik op de pagina beleid op **Opslaan**.  

## <a name="test-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang testen

Als u uw beleid wilt testen, probeert u zich aan te melden bij uw [Azure Portal](https://portal.azure.com) als **Alan Charon** met behulp van de Tor-browser. Uw aanmeldings poging moet worden geblokkeerd door het beleid voor voorwaardelijke toegang.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de test gebruiker, de Tor-browser en schakelt u het beleid voor voorwaardelijke toegang voor aanmeldings Risico's uit:

- Als u niet weet hoe u een Azure AD-gebruiker kunt verwijderen, raadpleegt u [gebruikers toevoegen of verwijderen](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Zie [installatie ongedaan maken](https://tb-manual.torproject.org/uninstalling/)voor instructies voor het verwijderen van de Tor-browser.
