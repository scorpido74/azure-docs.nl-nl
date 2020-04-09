---
title: Referentieportaal voor app's | Azure
titleSuffix: Microsoft identity platform
description: Een beschrijving van de functies in de microsoft-app-registratieportal.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 910aaf84d3563d4410826d3c0bdfde3d2dfc75e3
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885630"
---
# <a name="app-registration-reference"></a>Naslaginformatie over app-registratie

Dit document bevat context en beschrijvingen van verschillende functies in de ervaring [met app-registraties](https://aka.ms/appregistrations) in de Azure-portal.

## <a name="my-applications-or-converged-applications"></a>Mijn toepassingen of geconvergeerde toepassingen

Deze lijst bevat al uw toepassingen die zijn geregistreerd voor gebruik met het Microsoft-identiteitsplatform (v2.0) eindpunt. Deze toepassingen hebben de mogelijkheid om gebruikers aan te melden met zowel persoonlijke Microsoft-accounts als werk-/schoolaccounts van Azure Active Directory. Zie het [v2.0-overzicht](active-directory-appmodel-v2-overview.md)voor meer informatie over het eindpunt van het identiteitsplatform. Deze toepassingen kunnen ook worden gebruikt om te `https://login.live.com`integreren met het eindpunt voor microsoft-accountverificatie.

## <a name="azure-ad-only-applications"></a>Alleen Azure AD-toepassingen

Deze lijst bevat al uw toepassingen die zijn geregistreerd voor gebruik met het Azure AD v1.0-eindpunt. Deze toepassingen hebben alleen de mogelijkheid om gebruikers aan te melden met werk-/schoolaccounts van Azure Active Directory. Deze lijst bevat toepassingen die zijn geregistreerd met de ervaring **met app-registraties** in de [Azure-portal.](https://portal.azure.com)

## <a name="live-sdk-applications"></a>Live SDK-toepassingen

Deze lijst bevat al uw toepassingen die zijn geregistreerd voor gebruik uitsluitend met Een Microsoft-account. Ze zijn niet ingeschakeld voor gebruik met Azure Active Directory. Hier vindt u alle toepassingen die eerder zijn geregistreerd `https://account.live.com/developers/applications`bij de MSA developer portal op . Alle functies die u `https://account.live.com/developers/applications` eerder hebt uitgevoerd, kunnen nu worden uitgevoerd in [app-registraties.](https://aka.ms/appregistrations)

## <a name="application-secrets"></a>Toepassingsgeheimen

Toepassingsgeheimen zijn referenties waarmee uw toepassing betrouwbare [clientverificatie](https://tools.ietf.org/html/rfc6749#section-2.3) kan uitvoeren met Azure AD. In OAuth & OpenID Connect wordt een toepassingsgeheim gewoonlijk aangeduid als een `client_secret`. In het v2.0-protocol moet elke toepassing die een beveiligingstoken `https` ontvangt op een adresseerbare locatie op het web (met behulp van een schema) een toepassingsgeheim gebruiken om zich te identificeren met Azure AD bij het inwisselen van dat beveiligingstoken. Bovendien wordt het elke native client die tokens op een apparaat ontvangt, verboden om een toepassingsgeheim te gebruiken om clientverificatie uit te voeren. Dit ontmoedigt de opslag van geheimen in onveilige omgevingen.

Elke app kan op elk moment twee geldige toepassingsgeheimen bevatten. Door twee geheimen te behouden, hebt u de mogelijkheid om periodieke sleutelrollover uit te voeren in de hele omgeving van uw toepassing. Zodra u het geheel van uw toepassing naar een nieuw geheim hebt gemigreerd, u het oude geheim verwijderen en een nieuw geheim inrichten.

Op dit moment zijn slechts twee soorten toepassingsgeheimen toegestaan in het app-registratieportaal. Als u **Nieuw wachtwoord genereren kiest,** wordt een gedeeld geheim gegenereerd en opgeslagen in het desbetreffende gegevensarchief, dat u in uw toepassing gebruiken. Als u **Nieuw sleutelpaar genereren kiest,** wordt een nieuw publiek/privésleutelpaar gemaakt dat kan worden gedownload en gebruikt voor clientverificatie naar Azure AD. Als u **de openbare sleutel uploadt,** u uw eigen openbare/private sleutelpaar gebruiken.
U moet een certificaat uploaden dat een openbare sleutel bevat.

## <a name="profile"></a>Profiel

Het profielgedeelte van de app-registratieportal kan worden gebruikt om de aanmeldingspagina voor uw toepassing aan te passen. Op dit moment u het toepassingslogo van de aanmeldingspagina, de URL van de servicevoorwaarden en de URL van de privacyverklaring wijzigen. Het logo moet een transparante afbeelding van 48 x 48 of 50 x 50 pixels hebben in een GIF-, PNG- of JPEG-bestand dat 15 KB of kleiner is. Probeer de waarden te wijzigen en de resulterende aanmeldingspagina te bekijken!

## <a name="live-sdk-support"></a>Ondersteuning voor live SDK

Wanneer u 'Live SDK Support' inschakelt, worden alle toepassingsgeheimen die u maakt, ingericht in zowel de gegevensarchieven van Azure AD als Microsoft Account. Hierdoor kan uw toepassing rechtstreeks integreren met de Microsoft Account-service (login.live.com). Als u een app rechtstreeks wilt bouwen met Microsoft-account (in tegenstelling tot het gebruik van het v2.0-eindpunt), moet u ervoor zorgen dat Live SDK-ondersteuning is ingeschakeld.

Het uitschakelen van Live SDK-ondersteuning zorgt ervoor dat het toepassingsgeheim alleen wordt geschreven in het Azure AD-gegevensarchief. Het Azure AD-gegevensarchief bevat bedrijfsvoorschriften waarmee het aan bepaalde normen kan voldoen, zoals FISMA-naleving. Als u Live SDK-ondersteuning inschakelt, voldoet uw toepassing mogelijk niet aan een aantal van deze standaarden.

Als u alleen van plan bent het v2.0-eindpunt te gebruiken, u live SDK-ondersteuning veilig uitschakelen.
