---
title: Toepassingen beheren met Azure Active Directory | Microsoft Docs
description: Dit artikel beschrijft de voordelen van het integreren van Azure Active Directory met uw on-premises, cloud- en SaaS-toepassingen.
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
ms.openlocfilehash: ee9a9d404e9a08d8b795a3d73907e84d25b73107
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739750"
---
# <a name="application-management-with-azure-active-directory"></a>Toepassingsbeheer met Azure Active Directory

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop u uw toepassingen beheert door één identiteitssysteem te bieden voor uw cloud- en on-premises apps. U kunt uw SaaS-toepassingen (Software as a Service), on-premises toepassingen en LOB-apps (Line-Of-Business) toevoegen aan Azure AD. Gebruikers melden zich vervolgens eenmalig aan voor veilige en naadloze toegang tot deze toepassingen, samen met Office 365 en andere zakelijke toepassingen van Microsoft. U kunt de administratieve kosten verlagen door het [inrichten van gebruikers te automatiseren](../app-provisioning/user-provisioning.md). U kunt ook meervoudige verificatie en beleid voor voorwaardelijke toegang gebruiken om op een veilige manier toegang te bieden tot toepassingen.

![Diagram waarin de apps worden weer gegeven die zijn gefedereerd via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Waarom toepassingen beheren met een cloud-oplossing?

Organisaties hebben vaak honderden toepassingen waar gebruikers afhankelijk van zijn voor hun werk. Gebruikers hebben toegang tot deze toepassingen van groot aantal apparaten en -locaties. Elke dag worden nieuwe toepassingen toegevoegd, ontwikkeld en buiten gebruik gesteld. Met zo veel toepassingen en toegangspunten is het belangrijker dan ooit om een op de cloud gebaseerde oplossing te gebruiken voor het beheren van gebruikerstoegang tot alle toepassingen.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Welke typen toepassingen kan ik integreren met Azure AD?

Er zijn vier hoofdtypen toepassingen die u kunt toevoegen aan **Bedrijfstoepassingen** en vervolgens kunt beheren met Azure AD:

- **Toepassingen uit galerie van Azure AD**: Azure AD bevat een galerie met duizenden toepassingen die vooraf zijn geïntegreerd voor eenmalige aanmelding met Azure AD. Sommige toepassingen die worden gebruikt in uw organisatie, bevinden zich waarschijnlijk in de galerie. [Lees meer over het plannen van uw app-integratie](plan-an-application-integration.md) of raadpleeg gedetailleerde integratiestappen voor afzonderlijke apps in de [zelfstudies voor SaaS-toepassingen](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **On-premises toepassingen met Application Proxy**: met Azure AD Application Proxy kunt u uw on-premises web-apps integreren met Azure AD om ondersteuning voor eenmalige aanmelding te bieden. Eindgebruikers hebben dan toegang tot on-premises web-apps op dezelfde manier als ze toegang hebben tot Office 365 en andere SaaS-apps. [Lees meer over het gebruik van Application Proxy en hoe dit werkt](what-is-application-proxy.md).

- **Aangepaste toepassingen**: wanneer u uw eigen LOB-toepassingen bouwt, kunt u deze integreren met Azure AD voor de ondersteuning van eenmalige aanmelding. Door uw toepassing te registreren bij Azure AD, hebt u controle over het verificatiebeleid voor de toepassing. Zie de [richtlijnen voor ontwikkelaars](developer-guidance-for-integrating-applications.md) voor meer informatie.

- **Toepassingen niet in galerie**: neem uw eigen toepassingen mee. Biedt ondersteuning voor eenmalige aanmelding voor andere apps door deze toe te voegen aan Azure AD. U kunt elke webkoppeling integreren, of elke toepassing die velden voor gebruikersnaam en wachtwoord weergeeft, SAML of OpenID Connect-protocollen ondersteunt, of SCIM ondersteunt. Zie [Wachtwoord voor eenmalige aanmelding configureren voor een toepassing buiten de galerie](configure-single-sign-on-non-gallery-applications.md) voor meer informatie.

## <a name="manage-risk-with-conditional-access-policies"></a>Risico beheersen met beleid voor voorwaardelijke toegang

Het koppelen van eenmalige aanmelding van Azure AD aan beleid voor [voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md) biedt een hoge mate van beveiliging voor toegang tot toepassingen. Mogelijkheden voor beveiliging zijn schaalbare cloud-identiteitsbeveiliging, risicogebaseerde meervoudige verificatie en beleid voor voorwaardelijke toegang. Dankzij deze mogelijkheden kunt een nauwkeurig beheer creëren op basis van toepassingen of van groepen die een hoger niveau van beveiliging nodig hebben.

## <a name="improve-productivity-with-single-sign-on"></a>Verhoog de productiviteit met eenmalige aanmelding

Eenmalige aanmelding (SSO) voor toepassingen en Office 365 biedt een superieure aanmeldingservaring voor bestaande gebruikers door verschillende aanmeldingen te elimineren. Er wordt een samenhangende gebruikersomgeving gecreëerd zonder afleiding door meerdere aanmeldschermen of de noodzaak om meerdere wachtwoorden te beheren. De bedrijfsgroep kan toegang beheren en goedkeuren via selfservice en dynamisch lidmaatschap. De zorg dat alleen de juiste personen in het bedrijf toegang hebben tot een toepassing verbetert de beveiliging van het identiteitssysteem.

SSO verbetert de beveiliging. *Zonder eenmalige aanmelding* moeten beheerders gebruikersaccounts maken en bijwerken voor elke afzonderlijke toepassing. Dat kost waardevolle tijd. Gebruikers moeten meerdere referenties bijhouden voor toegang tot hun toepassingen. Als gevolg hiervan schrijven gebruikers vaak hun wachtwoorden op of gebruiken andere oplossingen voor wachtwoordbeheer waarmee het beveiligingsrisico stijgt. [Lees meer over eenmalige aanmelding](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Voeg governance en naleving toe

Met Azure AD, kunt u de toepassing-aanmeldingen bewaken met behulp van rapporten die gebruikmaken van hulpprogramma's voor beveiligingsincidenten en gebeurtenisbewaking (SIEM). U kunt de rapporten openen vanuit de portal of via API's. Programmatisch controleren wie toegang heeft tot uw toepassingen en toegang verwijderen van niet-actieve gebruikers via toegangsbeoordelingen.

## <a name="manage-costs"></a>Kosten beheren

Door te migreren naar Azure AD, kunt u kosten besparen en wordt het beheer van on-premises infrastructuur makkelijk. Azure AD biedt ook self-service tot toepassingen, en bespaart daarmee tijd voor beheerders en gebruikers. Eenmalige aanmelding elimineert de noodzaak van toepassingsspecifieke wachtwoorden. De mogelijkheid u slechts eenmaal te hoeven aanmelden, bespaart op kosten in verband met wachtwoordherstel voor toepassingen en wordt productieverlies voorkomen bij het ophalen van wachtwoorden.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Application Proxy?](what-is-application-proxy.md)
- [Snelstart: Een galerietoepassing toevoegen aan uw Azure AD-tenant](add-application-portal.md)
