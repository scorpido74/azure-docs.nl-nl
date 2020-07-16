---
title: Externe identiteiten vergelijken - Azure Active Directory | Microsoft Docs
description: Met externe Azure AD-identiteiten kunnen mensen buiten uw organisatie toegang krijgen tot uw apps en resources met hun eigen identiteit. Vergelijk oplossingen voor externe identiteiten, waaronder B2B-samenwerking Azure Active Directory en Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c45aae35c7c1cf2fbde436303a2ac000dfdd5ec
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339611"
---
# <a name="compare-solutions-for-external-identities-in-azure-active-directory"></a>Oplossingen voor externe identiteiten vergelijken in Azure Active Directory

Met externe identiteiten in Azure AD kunt u personen buiten uw organisatie toegang verlenen tot uw apps en resources, terwijl ze zich aanmelden met de identiteit van hun voorkeur. Uw partners, distributeurs, leveranciers en andere gastgebruikers kunnen 'hun eigen identiteiten meenemen'. Of ze nu deel uitmaken van Azure AD of een ander IT-beheerd systeem, of ze een niet-beheerde sociale identiteit als Google of Facebook hebben, ze kunnen hun eigen referenties gebruiken om zich aan te melden. De ID-provider beheert de identiteit van de externe gebruiker en beheert de toegang tot uw apps met Azure AD om uw resources te beschermen. 

## <a name="external-identities-scenarios"></a>Scenario's voor externe identiteiten

Externe identiteiten van Azure AD focussen minder op de relatie van een gebruiker met uw organisatie en meer op de manier waarop een individu zich wil aanmelden bij uw apps en resources. Binnen dit kader ondersteunt Azure AD diverse scenario's van business-to-business (B2B)-samenwerking tot het ontwikkelen van apps voor klanten en consumenten (business-to-consumer of B2C).

- **Deel apps met externe gebruikers (B2B-samenwerking)** . Nodig externe gebruikers in uw eigen tenant uit in als 'gastgebruikers' aan wie u machtigingen kunt toewijzen (voor autorisatie), terwijl ze hun bestaande referenties kunnen gebruiken (voor verificatie). Gebruikers melden zich aan bij de gedeelde resources met behulp van een eenvoudige uitnodiging en een inwisselproces met hun werkaccount, school-account of een e-mailaccount. En nu selfservice beschikbaar is voor het registreren van gebruikers (preview), kunt u ook een aanmeldingservaring bieden voor uw externe gebruikers via de toepassing die u wilt delen. U kunt instellingen voor de gebruikersstroom configureren om te bepalen hoe de gebruiker zich aanmeldt voor de toepassing en waarmee ze hun werkaccount, schoolaccount of een sociale identiteit kunnen gebruiken (zoals Google of Facebook) die ze willen gebruiken.  Zie de [Azure AD-B2B-documentatie](index.yml) voor meer informatie.

- **Ontwikkel apps die bedoeld zijn voor andere Azure AD-tenants (met één tenant of meerdere tenants)** . Bij het ontwikkelen van toepassingen voor Azure AD, kunt u gebruikers van één organisatie (één tenant) of gebruikers van elke organisatie die al een Azure AD-tenant heeft (toepassingen met meerdere tenants genoemd) als doel instellen. Deze toepassingen met meerdere tenants worden eenmalig in uw eigen Azure AD geregistreerd, maar kunnen vervolgens door elke Azure AD-gebruiker van elke organisatie worden gebruikt zonder dat u iets hoeft te doen.

- **White label-apps ontwikkelen voor consumenten en klanten (Azure AD B2C)** . Als u een bedrijf of ontwikkelaar bent die klantgerichte apps maakt, kunt u schalen naar consumenten, klanten of burgers door gebruik te maken van een Azure AD B2C. Ontwikkelaars kunnen Azure AD als het volledig functionele identiteitssysteem gebruiken voor hun toepassing, zodat klanten zich kunnen aanmelden met een identiteit die ze al hebt gemaakt (zoals Facebook of Gmail). Met Azure AD B2C kunt u volledig aanpassen en beheren hoe klanten zich registreren en aanmelden en hoe zij hun profielen beheren wanneer ze uw toepassingen gebruiken. Zie de [Azure AD-B2C-documentatie](https://docs.microsoft.com/azure/active-directory-b2c/) voor meer informatie.

De onderstaande tabel bevat een gedetailleerde vergelijking van de verschillende scenario's die mogelijk zijn met externe Azure AD-identiteiten.

| Toepassingen voor meerdere tenants  | Samenwerking van externe gebruikers (B2B) | Apps voor consumenten of klanten (B2C)  |
| ---- | --- | --- |
| Primair scenario: Software-as-a-Service voor ondernemingen (SaaS) | Primair scenario: Samenwerking met Microsoft-toepassingen (Microsoft 365, Teams,...) of uw eigen samenwerkingssoftware.  | Primair scenario: Transactionele toepassingen die gebruikmaken van aangepaste ontwikkelde toepassingen.   |
| Bedoeld voor: Organisaties die een groot aantal zakelijke klanten van software willen voorzien.    | Bedoeld voor: Organisaties die gebruikers van een partnerorganisatie willen kunnen verifiëren, ongeacht de id-provider.    | Bedoeld voor: Klanten van uw mobiele apps en web-apps uitnodigen, zoals personen, institutionele of organisatie-klanten in een Azure AD-map, gescheiden van de map van uw eigen organisatie. |
| Ondersteunde identiteiten: Werknemers met Azure AD-accounts. | Ondersteunde identiteiten: werknemers met werk- of schoolaccounts, partners met werk- of schoolaccounts of een e-mailadres. Binnenkort wordt directe federatie ondersteund.      | Ondersteunde identiteiten: consumentgebruikers met lokale toepassingen onder accounts (een e-adres of gebruikersnaam) of een ondersteund sociale id met directe federatie.       |
| Externe gebruikers worden beheerd in hun eigen map, geïsoleerd van de map waarin de toepassing is geregistreerd.    | Externe gebruikers worden beheerd in dezelfde map als werknemers, maar met een speciale aantekening. Ze kunnen op dezelfde manier als werknemers worden beheerd, kunnen worden toegevoegd aan dezelfde groepen, enzovoort.    | Externe gebruikers worden beheerd in de toepassingsmap. Ze worden afzonderlijk beheerd vanaf de werknemers- en partnermap van de organisatie (indien van toepassing).  |
| Eenmalige aanmelding: SSO voor alle Azure AD verbonden apps wordt ondersteund.          | Eenmalige aanmelding: SSO voor alle Azure AD verbonden apps wordt ondersteund. U kunt bijvoorbeeld toegang bieden voor Microsoft 365 of on-premises apps en andere SaaS-apps, zoals Salesforce of Workday.    | Eenmalige aanmelding: SSO voor apps van klanten in de Azure AD B2C-tenants wordt ondersteund. SSO bij Microsoft 365 of andere SaaS-apps van Microsoft wordt niet ondersteund.    |
| Levenscyclus van de klant: Beheerd door de thuisorganisatie van de gebruiker.      | Levenscyclus van de partner: beheerd door de host/uitnodigende organisatie.    | Levenscyclus van de klant: self-service of beheerd door de toepassing.      |
| Beveiligingsbeleid en naleving: Beheerd door de host/uitnodigende organisatie (bijvoorbeeld met [Beleid voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).           | Beveiligingsbeleid en naleving: Beheerd door de host/uitnodigende organisatie (bijvoorbeeld met [Beleid voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)). | Beveiligingsbeleid en naleving: beheerd door de toepassing.        |
| Branding: Merk van host/uitnodigende organisatie wordt gebruikt.   | Branding: Merk van host/uitnodigende organisatie wordt gebruikt.    | Branding: beheerd door de toepassing. Doorgaans het merkproduct, terwijl de organisatie vervaagt in de achtergrond.   |
| Meer informatie: [Identiteitsbeheer in multitenant-toepassingen](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [Instructiegids](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Meer informatie: [Blogpost](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentatie](what-is-b2b.md)                   | Meer informatie: [Productpagina](https://azure.microsoft.com/services/active-directory-b2c/), [Documentatie](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Beveilig en beheer klanten en partners buiten de grenzen van uw organisatie met externe Azure AD-identiteiten.

### <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Over Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
