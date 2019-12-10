---
title: Configuratie van sessies en eenmalige aanmelding
titleSuffix: Azure AD B2C
description: Sessie en configuratie van eenmalige aanmelding (SSO) in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950065"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configuratie van sessies en eenmalige aanmelding in Azure Active Directory B2C

Met deze functie beschikt u over een nauw keurige controle, [per gebruiker](active-directory-b2c-reference-policies.md), van:

- De levens duur van webtoepassings sessies die worden beheerd door Azure AD B2C.
- Gedrag van eenmalige aanmelding (SSO) voor meerdere apps en gebruikers stromen in uw Azure AD B2C-Tenant.

## <a name="session-behavior"></a>Gedrag van sessie

Azure AD B2C ondersteunt het [OpenID Connect Connect-verificatie protocol](active-directory-b2c-reference-oidc.md) voor het inschakelen van beveiligde aanmelding bij webtoepassingen. U kunt de volgende eigenschappen gebruiken voor het beheren van sessies van webtoepassingen:

- **Levens duur van de web-app (minuten)** : de levens duur van de Azure AD B2C's-sessie cookie die is opgeslagen in de browser van de gebruiker wanneer de verificatie is geslaagd.
    - Standaard instelling = 1440 minuten.
    - Mini maal (inclusief) = 15 minuten.
    - Maximum (inclusief) = 1440 minuten.
- **Time-out voor web-app-sessietime** -als deze schakel optie is ingesteld op **absoluut**, wordt de gebruiker na de periode die is opgegeven door de duur van de **Web-app-sessie (minuten)** , gedwongen deze te verifiëren. Als deze schakel optie is ingesteld op **Rolling** (de standaard instelling), blijft de gebruiker aangemeld zolang de gebruiker voortdurend actief is in uw webtoepassing.

De volgende use cases zijn ingeschakeld met behulp van deze eigenschappen:

- Voldoen aan de beveiligings-en nalevings vereisten van uw branche door de juiste levens duur van de webtoepassingsproxy in te stellen.
- Verificatie afdwingen na een bepaalde tijds periode tijdens de interactie van een gebruiker met een hoog beveiligings onderdeel van uw webtoepassing.

Deze instellingen zijn niet beschikbaar voor gebruikers stromen voor wacht woord opnieuw instellen.

## <a name="single-sign-on-sso-configuration"></a>Configuratie van eenmalige aanmelding (SSO)

Als u meerdere toepassingen en gebruikers stromen hebt in uw B2C-Tenant, kunt u gebruikers interacties voor hen beheren met de **configuratie-eigenschap voor eenmalige aanmelding** . U kunt de eigenschap instellen op een van de volgende instellingen:

- **Tenant** : dit is de standaard instelling. Met deze instelling kunnen meerdere toepassingen en gebruikers stromen in uw B2C-Tenant dezelfde gebruikers sessie delen. Wanneer een gebruiker zich bijvoorbeeld bij een toepassing aanmeldt, kan de gebruiker zich bij het openen van de app ook probleemloos aanmelden bij een andere, contoso-apotheek.
- **Toepassing** : met deze instelling kunt u een gebruikers sessie alleen voor een toepassing, onafhankelijk van andere toepassingen, onderhouden. Als u bijvoorbeeld wilt dat de gebruiker zich aanmeldt bij Contoso apotheek (met dezelfde referenties), zelfs als de gebruiker al is aangemeld bij Contoso-winkelen, een andere toepassing op dezelfde B2C-Tenant.
- **Beleid** : met deze instelling kunt u een gebruikers sessie alleen voor een gebruikers stroom onderhouden, onafhankelijk van de toepassingen die er gebruik van maken. Als de gebruiker zich al heeft aangemeld en een MFA-stap (multi factor Authentication) heeft voltooid, kan de gebruiker toegang krijgen tot hogere beveiligings onderdelen van meerdere toepassingen, zolang de sessie die aan de gebruikers stroom is gebonden, niet verloopt.
- **Uitgeschakeld** : deze instelling zorgt ervoor dat de gebruiker tijdens elke uitvoering van het beleid de volledige gebruikers stroom uitvoert.

Deze instellingen zijn niet beschikbaar voor gebruikers stromen voor wacht woord opnieuw instellen.

