---
title: Gedrag van sessies configureren-Azure Active Directory B2C | Microsoft Docs
description: Het gedrag van sessies in Azure Active Directory B2C configureren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 249b9bb282024431d0ecd38c62d8d780602e6709
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229966"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Sessie gedrag in Azure Active Directory B2C configureren

Met deze functie beschikt u over een nauw keurige controle, [per gebruiker](user-flow-overview.md), van:

- De levens duur van webtoepassings sessies die worden beheerd door Azure AD B2C.
- Gedrag van eenmalige aanmelding (SSO) voor meerdere apps en gebruikers stromen in uw Azure AD B2C-Tenant.

Deze instellingen zijn niet beschikbaar voor gebruikers stromen voor wacht woord opnieuw instellen.

Azure AD B2C ondersteunt het [OpenID Connect Connect-verificatie protocol](openid-connect.md) voor het inschakelen van beveiligde aanmelding bij webtoepassingen. U kunt de volgende eigenschappen gebruiken voor het beheren van sessies van webtoepassingen:

## <a name="session-behavior-properties"></a>Eigenschappen van sessie gedrag

- **Levens duur van de web-app (minuten)** : de levens duur van de Azure AD B2C's-sessie cookie die is opgeslagen in de browser van de gebruiker wanneer de verificatie is geslaagd.
    - Standaard instelling = 1440 minuten.
    - Mini maal (inclusief) = 15 minuten.
    - Maximum (inclusief) = 1440 minuten.
- **Time-out voor web-app-sessietime** -als deze schakel optie is ingesteld op **absoluut**, wordt de gebruiker na de periode die is opgegeven door de duur van de **Web-app-sessie (minuten)** , gedwongen deze te verifiëren. Als deze schakel optie is ingesteld op **Rolling** (de standaard instelling), blijft de gebruiker aangemeld zolang de gebruiker voortdurend actief is in uw webtoepassing.
- **Configuratie van eenmalige aanmelding** Als u meerdere toepassingen en gebruikers stromen hebt in uw B2C-Tenant, kunt u gebruikers interacties voor hen beheren met de **configuratie-eigenschap voor eenmalige aanmelding** . U kunt de eigenschap instellen op een van de volgende instellingen:
    - **Tenant** : dit is de standaard instelling. Met deze instelling kunnen meerdere toepassingen en gebruikers stromen in uw B2C-Tenant dezelfde gebruikers sessie delen. Wanneer een gebruiker zich bijvoorbeeld bij een toepassing aanmeldt, kan de gebruiker zich bij het openen van de app ook probleemloos aanmelden bij een andere, contoso-apotheek.
    - **Toepassing** : met deze instelling kunt u een gebruikers sessie alleen voor een toepassing, onafhankelijk van andere toepassingen, onderhouden. Als u bijvoorbeeld wilt dat de gebruiker zich aanmeldt bij Contoso apotheek (met dezelfde referenties), zelfs als de gebruiker al is aangemeld bij Contoso-winkelen, een andere toepassing op dezelfde B2C-Tenant.
    - **Beleid** : met deze instelling kunt u een gebruikers sessie alleen voor een gebruikers stroom onderhouden, onafhankelijk van de toepassingen die er gebruik van maken. Als de gebruiker zich al heeft aangemeld en een MFA-stap (multi factor Authentication) heeft voltooid, kan de gebruiker toegang krijgen tot hogere beveiligings onderdelen van meerdere toepassingen, zolang de sessie die aan de gebruikers stroom is gebonden, niet verloopt.
    - **Uitgeschakeld** : deze instelling zorgt ervoor dat de gebruiker tijdens elke uitvoering van het beleid de volledige gebruikers stroom uitvoert.

De volgende use cases zijn ingeschakeld met behulp van deze eigenschappen:

- Voldoen aan de beveiligings-en nalevings vereisten van uw branche door de juiste levens duur van de webtoepassingsproxy in te stellen.
- Verificatie afdwingen na een bepaalde tijds periode tijdens de interactie van een gebruiker met een hoog beveiligings onderdeel van uw webtoepassing.

## <a name="configure-the-properties"></a>De eigenschappen configureren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)**.
5. Open de gebruikers stroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.
7. Configureer de duur van de **Web-app-sessie (minuten)**, **time-out voor de web-app sessie**, **configuratie van eenmalige aanmelding**en **vereisen een id-token in afmeldings aanvragen** , indien nodig.

    ![Instellingen voor de eigenschappen van sessie gedrag in de Azure Portal](./media/session-behavior/session-behavior.png)

8. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure AD B2C sessie](session-overview.md).