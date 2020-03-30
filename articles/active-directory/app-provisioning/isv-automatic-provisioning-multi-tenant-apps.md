---
title: Automatische gebruikersinrichting voor multitenanttoepassingen inschakelen - Azure AD
description: Een handleiding voor onafhankelijke softwareleveranciers voor het inschakelen van geautomatiseerde inrichting
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522390"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Automatische gebruikersinrichting inschakelen voor uw multi-tenanttoepassing

Automatische gebruikersinrichting is het proces van het automatiseren van het maken, onderhouden en verwijderen van gebruikersidentiteiten in doelsystemen zoals uw software-as-a-service-toepassingen.

## <a name="why-enable-automatic-user-provisioning"></a>Waarom automatische gebruikersinrichting inschakelen?

Toepassingen waarvoor een gebruikersrecord in de toepassing aanwezig is voordat de eerste aanmelding van een gebruiker vereist, vereisen gebruikersvoorziening. Er zijn voordelen voor u als dienstverlener, en voordelen voor uw klanten.

### <a name="benefits-to-you-as-the-service-provider"></a>Voordelen voor u als dienstverlener

* Verhoog de beveiliging van uw toepassing met behulp van het Microsoft-identiteitsplatform.

* Verminder de werkelijke en waargenomen moeite van de klant om uw toepassing over te nemen.

* Verlaag uw kosten bij de integratie met meerdere identiteitsproviders (IdP's) voor automatische gebruikersinrichting door gebruik te maken van System for Cross-Domain Identity Management (SCIM)-gebaseerde inrichting.

* Verlaag de ondersteuningskosten door uitgebreide logboeken op te leveren om klanten te helpen problemen met het inrichten van gebruikers op te lossen.

* Vergroot de zichtbaarheid van uw toepassing in de [azure AD-app-galerie](https://azuremarketplace.microsoft.com/marketplace/apps).

* Krijg een geprioriteerd vermelding op de pagina App Tutorials.

### <a name="benefits-to-your-customers"></a>Voordelen voor uw klanten

* Verhoog de beveiliging door automatisch de toegang tot uw toepassing te verwijderen voor gebruikers die van rol veranderen of de organisatie aan uw toepassing overlaten.

* Vereenvoudig het gebruikersbeheer voor uw toepassing door menselijke fouten en repetitief werk in verband met handmatige inrichten te vermijden.

* Verlaag de kosten van het hosten en onderhouden van op maat ontwikkelde inrichtingsoplossingen.

## <a name="choose-a-provisioning-method"></a>Een inrichtingsmethode kiezen

Azure AD biedt verschillende integratiepaden om automatische gebruikersinrichting voor uw toepassing mogelijk te maken.

* De [Azure AD Provisioning Service](../app-provisioning/user-provisioning.md) beheert de inrichting en deprovisioning van gebruikers van Azure AD naar uw toepassing (uitgaande inrichting) en van uw toepassing naar Azure AD (inkomende inrichting). De service maakt verbinding met de SCIM-gebruikersbeheer-eindpunten (System for Cross-Domain Identity Management) die door uw toepassing worden geleverd.

* Wanneer u de [Microsoft Graph gebruikt,](https://docs.microsoft.com/graph/)beheert uw toepassing de inkomende en uitgaande inrichting van gebruikers en groepen van Azure AD naar uw toepassing door de Microsoft Graph-API op te vragen.

* De gebruikersinrichting van de gebruikersvoorziening van de beveiligingsbeweringsmelding just in time (SAML JIT) kan worden ingeschakeld als uw toepassing SAML voor federatie gebruikt. Het maakt gebruik van claiminformatie die in het SAML-token wordt verzonden naar gebruikers die indevoorziening worden verstrekt.

Als u wilt bepalen welke integratieoptie u voor uw toepassing wilt gebruiken, raadpleegt u de vergelijkingstabel op hoog niveau en raadpleegt u de meer gedetailleerde informatie over elke optie.

| Mogelijkheden ingeschakeld of verbeterd door automatische inrichting| Azure AD Provisioning Service (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Gebruikers- en groepsbeheer in Azure AD| √| √| Alleen voor de gebruiker |
| Gebruikers en groepen beheren die zijn gesynchroniseerd vanuit on-premises Active Directory| √*| √*| Alleen de gebruiker* |
| Toegang tot gegevens buiten gebruikers en groepen tijdens het inrichten van Toegang tot O365-gegevens (Teams, SharePoint, E-mail, Agenda, Documenten, enz.)| X+| √| X |
| Gebruikers maken, lezen en bijwerken op basis van bedrijfsregels| √| √| √ |
| Gebruikers verwijderen op basis van bedrijfsregels| √| √| X |
| Automatische gebruikersvoorziening beheren voor alle toepassingen vanuit de Azure-portal| √| X| √ |
| Meerdere identiteitsproviders ondersteunen| √| X| √ |
| Gastaccounts ondersteunen (B2B)| √| √| √ |
| Niet-ondernemingsrekeningen ondersteunen (B2C)| X| √| √ |

<sup>*</sup>– Azure AD Connect-installatie is vereist om gebruikers te synchroniseren van AD naar Azure AD.  
<sup>+</sup >– Het gebruik van SCIM voor provisioning sluit u niet uit om uw toepassing te integreren met MIcrosoft Graph voor andere doeleinden.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD Provisioning Service (SCIM)

De Azure AD-inrichtingsservices maken gebruik van [SCIM](https://aka.ms/SCIMOverview), een industriestandaard voor het inrichten van services die worden ondersteund door veel identiteitsproviders (IdPs) en toepassingen (bijvoorbeeld Slack, G Suite, Dropbox). We raden u aan de Azure AD-inrichtingsservice te gebruiken als u id-adressen naast Azure AD wilt ondersteunen, omdat elke SCIM-compatibele IdP verbinding kan maken met uw SCIM-eindpunt. Als u een eenvoudig /Gebruikerseindpunt bouwt, u inrichten inschakelen zonder dat u uw eigen synchronisatie-engine hoeft te onderhouden. 

Zie voor meer informatie over hoe de Azure AD Provisioning Service-gebruikers SCIM: 

* [Meer informatie over de SCIM-standaard](https://aka.ms/SCIMOverview)

* [Systeem voor cross-domain identity management (SCIM) gebruiken om gebruikers en groepen automatisch in te richten van Azure Active Directory naar toepassingen](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [De Azure AD SCIM-implementatie begrijpen](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph voor inrichting

Wanneer u Microsoft Graph gebruikt voor inrichting, hebt u toegang tot alle uitgebreide gebruikersgegevens die beschikbaar zijn in Grafiek. Naast de details van gebruikers en groepen u ook aanvullende informatie ophalen, zoals de rollen van de gebruiker, manager- en directe rapporten, eigendom en geregistreerde apparaten en honderden andere gegevensstukken die beschikbaar zijn in de [Microsoft Graph.](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) 

Meer dan 15 miljoen organisaties en 90% van de Fortune 500-bedrijven gebruiken Azure AD terwijl ze zich abonneren op Microsoft-cloudservices zoals Office 365, Microsoft Azure, Enterprise Mobility Suite of Microsoft 365. U Microsoft Graph gebruiken om uw app te integreren met administratieve werkstromen, zoals onboarding (en beëindiging van werknemers), profielonderhoud en meer. 

Meer informatie over het gebruik van Microsoft Graph voor inrichten:

* [Startpagina van Microsoft Graph](https://developer.microsoft.com/graph)

* [Overzicht van Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Overzicht van Microsoft Graph Auth](https://docs.microsoft.com/graph/auth/)

* [Aan de slag met Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>SAML JIT gebruiken voor inrichten

Als u gebruikers alleen wilt inrichten bij het eerste aanmelden bij uw toepassing en gebruikers niet automatisch hoeft te deprovisioneren, is SAML JIT een optie. Uw toepassing moet SAML 2.0 ondersteunen als federatieprotocol om SAML JIT te gebruiken.

SAML JIT gebruikt de claimgegevens in het SAML-token om gebruikersinformatie in de toepassing te maken en bij te werken. Klanten kunnen deze vereiste claims zo nodig configureren in de Azure AD-toepassing. Soms moet de JIT-inrichting worden ingeschakeld vanaf de toepassingszijde, zodat de klant deze functie kan gebruiken. SAML JIT is handig voor het maken en bijwerken van gebruikers, maar het kan de gebruikers in de toepassing niet verwijderen of deactiveren.

## <a name="next-steps"></a>Volgende stappen

* [Eén aanmelding inschakelen voor uw toepassing](../manage-apps/isv-sso-content.md)

* [Verzend uw toepassingsvermelding](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) en werk samen met Microsoft om documentatie op de site van Microsoft te maken.

* [Word lid van het Microsoft Partner Network (gratis) en maak uw go to-market-abonnement](https://partner.microsoft.com/en-us/explore/commercial).
