---
title: Aan de slag met het integreren van Azure AD met apps | Microsoft Documenten
description: Dit artikel is een handleiding voor het integreren van Azure Active Directory (AD) met on-premises toepassingen en cloudtoepassingen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89b16a8479f8975d101b8a4e26dcb1885d9730bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063377"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Azure Active Directory integreren met handleiding voor toepassingen

In dit onderwerp wordt een overzicht van het proces voor het integreren van toepassingen met Azure Active Directory (AD). Elk van de onderstaande secties bevat een korte samenvatting van een meer gedetailleerd onderwerp, zodat u bepalen welke delen van deze handleiding voor de start voor u relevant zijn.

Zie [Volgende stappen](#next-steps)om diepgaande implementatieplannen te downloaden.

## <a name="take-inventory"></a>Inventariseren
Voordat u toepassingen integreert met Azure AD, is het belangrijk om te weten waar u bent en waar u naartoe wilt.  De volgende vragen zijn bedoeld om u te helpen nadenken over uw Azure AD-toepassingsintegratieproject.

### <a name="application-inventory"></a>Toepassingsinventaris
* Waar zijn al uw toepassingen? Van wie zijn ze?
* Wat voor soort authenticatie hebben uw toepassingen nodig?
* Wie heeft toegang nodig tot welke applicaties?
* Wilt u een nieuwe toepassing implementeren?
  * Gaat u het in eigen beheer bouwen en implementeren op een Azure-compute-exemplaar?
  * Gebruikt u er een die beschikbaar is in de Azure Application Gallery?

### <a name="user-and-group-inventory"></a>Gebruikers- en groepsvoorraad
* Waar bevinden uw gebruikersaccounts zich?
  * On-premises Active Directory
  * Azure AD
  * Binnen een aparte applicatiedatabase die u bezit
  * In niet-goedgekeurde aanvragen
  * Alle bovenstaande
* Welke machtigingen en roltoewijzingen hebben individuele gebruikers momenteel? Moet u hun toegang controleren of bent u er zeker van dat uw gebruikerstoegang en roltoewijzingen nu geschikt zijn?
* Zijn groepen al gevestigd in uw on-premises Active Directory?
  * Hoe zijn uw groepen georganiseerd?
  * Wie zijn de groepsleden?
  * Welke machtigingen/roltoewijzingen hebben de groepen momenteel?
* Moet u bestanden van gebruikers/groepen opschonen voordat u ze integreert?  (Dit is een vrij belangrijke vraag. Vuilnis in, vuilnis uit.)

### <a name="access-management-inventory"></a>Voorraad toegangsbeheer
* Hoe beheert u momenteel de toegang van gebruikers tot toepassingen? Moet dat veranderen?  Heeft u andere manieren overwogen om toegang te beheren, zoals bijvoorbeeld met [RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Wie heeft toegang nodig tot wat?

Misschien heb je niet de antwoorden op al deze vragen op voorhand, maar dat is oke.  Deze gids kan u helpen bij het beantwoorden van een aantal van deze vragen en het nemen van een aantal weloverwogen beslissingen.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Vind niet-goedgekeurde cloudtoepassingen met Cloud Discovery

Zoals hierboven vermeld, kunnen er toepassingen zijn die tot nu toe niet door uw organisatie zijn beheerd.  Als onderdeel van het voorraadproces is het mogelijk om niet-goedgekeurde cloudtoepassingen te vinden. Zie [Clouddetectie instellen](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Toepassingen integreren met Azure AD
In de volgende artikelen worden de verschillende manieren besproken waarop toepassingen worden geïntegreerd met Azure AD en worden enkele richtlijnen gegeven.

* [Bepalen welke Active Directory moet worden gebruikt](../fundamentals/active-directory-administer.md)
* [Toepassingen gebruiken in de Azure-toepassingsgalerie](what-is-single-sign-on.md)
* [Lijst met zelfstudies voor SaaS-toepassingen integreren](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Verificatietypen
Elk van uw toepassingen heeft mogelijk andere verificatievereisten. Met Azure AD kunnen ondertekeningscertificaten worden gebruikt met toepassingen die SAML 2.0- of WS-Federation-protocollen of OpenID Connect-protocollen gebruiken, evenals Wachtwoordmelding. Zie [Certificaten beheren voor federatie voor federatie van federaties voor federaties in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) en op basis van een enkel [aanmelding op basis van een wachtwoord voor](what-is-single-sign-on.md)gebruik met Azure AD .

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>SSO inschakelen met Azure AD-appproxy
Met Microsoft Azure AD Application Proxy u veilig, overal en op elk apparaat toegang bieden tot toepassingen in uw privénetwerk. Nadat u een proxyconnector voor toepassingen in uw omgeving hebt geïnstalleerd, kan deze eenvoudig worden geconfigureerd met Azure AD.

### <a name="integrating-custom-applications"></a>Aangepaste toepassingen integreren
Zie [Ontwikkelaars](../active-directory-applications-guiding-developers-for-lob-applications.md)begeleiden als u een nieuwe toepassing schrijft en ontwikkelaars wilt helpen bij het benutten van de kracht van Azure AD.

Zie ['Uw eigen app toevoegen' met Azure AD Selfservice SAML-configuratie](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)als u uw aangepaste toepassing wilt toevoegen aan de Azure Application Gallery.

## <a name="managing-access-to-applications"></a>Toegang tot toepassingen beheren
In de volgende artikelen worden beschreven hoe u de toegang tot toepassingen beheren nadat ze zijn geïntegreerd met Azure AD met Azure AD Connectors en Azure AD.

* [Toegang tot apps beheren met Azure AD](what-is-access-management.md)
* [Automatiseren met Azure AD-connectors](../app-provisioning/user-provisioning.md)
* [Gebruikers toewijzen aan een toepassing](../active-directory-applications-guiding-developers-assigning-users.md)
* [Groepen toewijzen aan een toepassing](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Accounts delen](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Volgende stappen
Voor uitgebreide informatie u Azure Active Directory-implementatieplannen downloaden van [GitHub.](https://aka.ms/deploymentplans) Voor galerietoepassingen u implementatieplannen downloaden voor één aanmelding, voorwaardelijke toegang en gebruikersvoorziening via de [Azure-portal.](https://portal.azure.com) 

Ga als volgende over een implementatieplan uit de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Enterprise-toepassingen** | Kies een**app-implementatieplan****Pick an App** | .

Geef feedback over implementatieplannen door de [enquête implementatieplan](https://aka.ms/DeploymentPlanFeedback)in te vullen.
