---
title: Toepassingen beheren met Azure Active Directory | Microsoft Docs
description: In dit artikel worden de voordelen beschreven van de integratie van Azure Active Directory met uw on-premises cloud- en SaaS-toepassingen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca73fac06649f801461e53130a67aa9ec0ad0d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063319"
---
# <a name="application-management-with-azure-active-directory"></a>Toepassingsbeheer met Azure Active Directory

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop u uw toepassingen beheert door één identiteitssysteem te bieden voor uw cloud- en on-premises apps. U uw software as a service (SaaS)-toepassingen, on-premises toepassingen en LOB-apps (Line of Business) toevoegen aan Azure AD. Vervolgens melden gebruikers zich één keer aan om veilig en naadloos toegang te krijgen tot deze toepassingen, samen met Office 365 en andere zakelijke toepassingen van Microsoft. U de administratieve kosten verlagen door [de inrichting van de gebruiker te automatiseren.](../app-provisioning/user-provisioning.md) U ook multi-factor authenticatie en voorwaardelijke toegangsbeleidsregels gebruiken om veilige toegang tot toepassingen te bieden.

![Diagram met apps die worden gefedereerd via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Waarom toepassingen beheren met een cloud-oplossing?

Organisaties hebben vaak honderden toepassingen waar gebruikers afhankelijk van zijn voor hun werk. Gebruikers hebben toegang tot deze toepassingen van groot aantal apparaten en -locaties. Elke dag worden nieuwe toepassingen toegevoegd, ontwikkeld en buiten gebruik gesteld. Met zoveel toepassingen en toegangspunten is het belangrijker dan ooit om een cloudgebaseerde oplossing te gebruiken om gebruikerstoegang tot alle toepassingen te beheren.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Welke soorten toepassingen kan ik integreren met Azure AD?

Er zijn vier belangrijke typen toepassingen die u aan uw **Enterprise-toepassingen** toevoegen en beheren met Azure AD:

- **Azure AD Gallery-toepassingen** – Azure AD heeft een galerie met duizenden toepassingen die vooraf zijn geïntegreerd voor eenmalige aanmelding met Azure AD. Sommige toepassingen die worden gebruikt in uw organisatie, bevinden zich waarschijnlijk in de galerie. [Lees meer over het plannen van uw app-integratie](plan-an-application-integration.md)of ontvang gedetailleerde integratiestappen voor afzonderlijke apps in de zelfstudies van de [SaaS-toepassing.](https://docs.microsoft.com/azure/active-directory/saas-apps/)

- **On-premises toepassingen met Application Proxy** : met Azure AD Application Proxy u uw on-premises webapps integreren met Azure AD om eenmalige aanmelding te ondersteunen. Vervolgens hebben eindgebruikers toegang tot uw on-premises webapps op dezelfde manier als ze toegang hebben tot Office 365 en andere SaaS-apps. [Meer informatie over waarom u Application Proxy gebruiken en hoe deze werkt.](what-is-application-proxy.md)

- **Op maat gemaakte toepassingen** – Wanneer u uw eigen bedrijfsproductlijn bouwt, u deze integreren met Azure AD om eenmalige aanmelding te ondersteunen. Door uw toepassing te registreren bij Azure AD, hebt u controle over het verificatiebeleid voor de toepassing. Zie [richtlijnen voor ontwikkelaars voor](developer-guidance-for-integrating-applications.md)meer informatie.

- **Niet-Gallery toepassingen** – Breng uw eigen applicaties! Ondersteuning voor eenmalige aanmelding voor andere apps door ze toe te voegen aan Azure AD. U elke gewenste weblink integreren, of elke toepassing die een gebruikersnaam en wachtwoordveld weergeeft, SAML- of OpenID Connect-protocollen ondersteunt of SCIM ondersteunt. Zie [Eenmalige aanmelding configureren voor niet-galerie-apps voor](configure-single-sign-on-non-gallery-applications.md)meer informatie .

## <a name="manage-risk-with-conditional-access-policies"></a>Risico's beheren met beleid voor voorwaardelijke toegang

Het koppelen van Azure AD single sign-on (SSO) met [Voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) biedt hoge beveiligingsniveaus voor toegang tot toepassingen. Beveiligingsmogelijkheden omvatten identiteitsbescherming op cloudschaal, op risico's gebaseerde toegangscontrole, native multi-factor authenticatie en beleid voor voorwaardelijke toegang. Dankzij deze mogelijkheden kunt een nauwkeurig beheer creëren op basis van toepassingen of van groepen die een hoger niveau van beveiliging nodig hebben.

## <a name="improve-productivity-with-single-sign-on"></a>Verhoog de productiviteit met eenmalige aanmelding

Eenmalige aanmelding (SSO) voor toepassingen en Office 365 biedt een superieure aanmeldingservaring voor bestaande gebruikers door verschillende aanmeldingen te elimineren. Er wordt een samenhangende gebruikersomgeving gecreëerd zonder afleiding door meerdere aanmeldschermen of de noodzaak om meerdere wachtwoorden te beheren. De bedrijfsgroep kan toegang beheren en goedkeuren via selfservice en dynamisch lidmaatschap. De zorg dat alleen de juiste personen in het bedrijf toegang hebben tot een toepassing verbetert de beveiliging van het identiteitssysteem.

SSO verbetert de beveiliging. *Zonder eenmalige aanmelding* moeten beheerders gebruikersaccounts maken en bijwerken voor elke afzonderlijke toepassing. Dat kost waardevolle tijd. Gebruikers moeten meerdere referenties bijhouden voor toegang tot hun toepassingen. Als gevolg hiervan schrijven gebruikers vaak hun wachtwoorden op of gebruiken andere oplossingen voor wachtwoordbeheer waarmee het beveiligingsrisico stijgt. [Lees meer over eenmalige aanmelding](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Voeg governance en naleving toe

Met Azure AD, kunt u de toepassing-aanmeldingen bewaken met behulp van rapporten die gebruikmaken van hulpprogramma's voor beveiligingsincidenten en gebeurtenisbewaking (SIEM). U kunt de rapporten openen vanuit de portal of via API's. Programmatisch controleren wie toegang heeft tot uw toepassingen en toegang verwijderen van niet-actieve gebruikers via toegangsbeoordelingen.

## <a name="manage-costs"></a>Kosten beheren

Door te migreren naar Azure AD, kunt u kosten besparen en wordt het beheer van on-premises infrastructuur makkelijk. Azure AD biedt ook self-service tot toepassingen, en bespaart daarmee tijd voor beheerders en gebruikers. Eenmalige aanmelding elimineert de noodzaak van toepassingsspecifieke wachtwoorden. De mogelijkheid u slechts eenmaal te hoeven aanmelden, bespaart op kosten in verband met wachtwoordherstel voor toepassingen en wordt productieverlies voorkomen bij het ophalen van wachtwoorden.

## <a name="next-steps"></a>Volgende stappen

- [Wat is toepassingsproxy?](what-is-application-proxy.md)
- [Snelstart: een galerietoepassing toevoegen aan uw Azure AD-tenant](add-application-portal.md)
