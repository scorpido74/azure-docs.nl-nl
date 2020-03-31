---
title: Sessiegedrag configureren - Azure Active Directory B2C | Microsoft Documenten
description: Sessiegedrag configureren in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186808"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Sessiegedrag configureren in Azure Active Directory B2C

Deze functie geeft u fijnkorrelige controle, op een [per-gebruiker flow basis](user-flow-overview.md)van:

- Levensduur van webtoepassingssessies die worden beheerd door Azure AD B2C.
- SSO-gedrag (Single Sign-on) in meerdere apps en gebruikersstromen in uw Azure AD B2C-tenant.

Deze instellingen zijn niet beschikbaar voor gebruikersstromen voor het opnieuw instellen van wachtwoorden.

Azure AD B2C ondersteunt het [OpenID Connect-verificatieprotocol](openid-connect.md) voor het inschakelen van veilige aanmelding bij webtoepassingen. U de volgende eigenschappen gebruiken om webtoepassingssessies te beheren:

## <a name="session-behavior-properties"></a>Eigenschappen van sessiegedrag

- **Levensduur van de web-app-sessie (minuten)** - De levensduur van de sessiecookie van Azure AD B2C die is opgeslagen in de browser van de gebruiker bij succesvolle verificatie.
    - Standaard = 1440 minuten.
    - Minimum (inclusief) = 15 minuten.
    - Maximum (inclusief) = 1440 minuten.
- **Time-out van web-apps -** Als deze switch is ingesteld op **Absoluut,** wordt de gebruiker gedwongen om opnieuw te verifiëren nadat de periode die is opgegeven door de levensduur van de **web-app-sessie (minuten)** verstrijkt. Als deze switch is ingesteld op **Rollen** (de standaardinstelling), blijft de gebruiker aangemeld zolang de gebruiker voortdurend actief is in uw webtoepassing.
- **Configuratie met één aanmelding** Als u meerdere toepassingen en gebruikersstromen in uw B2C-tenant hebt, u gebruikersinteracties in deze gebruikers beheren met de eigenschap **Configuratie voor één aanmelding.** U de eigenschap instellen op een van de volgende instellingen:
    - **Tenant** - Deze instelling is de standaardinstelling. Met deze instelling kunnen meerdere toepassingen en gebruikersstromen in uw B2C-tenant dezelfde gebruikerssessie delen. Bijvoorbeeld, zodra een gebruiker zich aanmeldt bij een toepassing, kan de gebruiker zich ook naadloos aanmelden bij een andere, Contoso Pharmacy, bij toegang tot een toepassing.
    - **Toepassing** - Met deze instelling u een gebruikerssessie exclusief voor een toepassing onderhouden, onafhankelijk van andere toepassingen. Als u bijvoorbeeld wilt dat de gebruiker zich aanmeldt bij Contoso Pharmacy (met dezelfde referenties), zelfs als de gebruiker al is aangemeld bij Contoso Shopping, een andere toepassing op dezelfde B2C-tenant.
    - **Beleid** - Met deze instelling u een gebruikerssessie exclusief voor een gebruikersstroom onderhouden, onafhankelijk van de toepassingen die deze gebruiken. Als de gebruiker zich bijvoorbeeld al heeft aangemeld en een MFA-stap (Multi factor Authentication) heeft voltooid, kan de gebruiker toegang krijgen tot delen met een hogere beveiliging van meerdere toepassingen, zolang de sessie die is gekoppeld aan de gebruikersstroom niet verloopt.
    - **Uitgeschakeld** - Deze instelling dwingt de gebruiker om de volledige gebruikersstroom uit te voeren bij elke uitvoering van het beleid.

De volgende use cases zijn ingeschakeld met behulp van deze eigenschappen:

- Voldoe aan de beveiligings- en nalevingsvereisten van uw branche door de juiste levensduur van de webtoepassingssessie in te stellen.
- Verificatie afdwingen na een bepaalde periode tijdens de interactie van een gebruiker met een hoogbeveiligd onderdeel van uw webtoepassing.

## <a name="configure-the-properties"></a>De eigenschappen configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD B2C-tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Gebruikersstromen (beleidsregels)**.
5. Open de gebruikersstroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.
7. Configureer **de sessielevensduur van de web-app (minuten),** **een time-out van de web-app-sessie,** **een malige aanmeldingsconfiguratie**en **Een ID-token vereisen in afmeldverzoeken** als dat nodig is.

    ![Eigenschappeninstellingen voor sessiegedrag in de Azure-portal](./media/session-behavior/session-behavior.png)

8. Klik op **Opslaan**.
