---
title: Toepassingen beheren met Azure Active Directory | Microsoft Docs
description: In dit artikel worden de voor delen beschreven van het integreren van Azure Active Directory met uw on-premises, Cloud-en SaaS-toepassingen.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77063319"
---
# <a name="application-management-with-azure-active-directory"></a>Toepassingsbeheer met Azure Active Directory

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop u uw toepassingen beheert door één identiteits systeem te bieden voor uw Cloud-en on-premises apps. U kunt uw SaaS-toepassingen (Software as a Service), on-premises toepassingen en LOB-apps (line-of-Business) toevoegen aan Azure AD. Gebruikers melden zich vervolgens eenmaal aan voor veilige en naadloze toegang tot deze toepassingen, samen met Office 365 en andere zakelijke toepassingen van micro soft. U kunt de beheer kosten verlagen door de [gebruikers inrichting te automatiseren](../app-provisioning/user-provisioning.md). U kunt ook multi-factor Authentication en beleid voor voorwaardelijke toegang gebruiken om toegang tot beveiligde toepassingen te bieden.

![Diagram waarin de apps worden weer gegeven die Federated zijn via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Waarom toepassingen beheren met een cloud-oplossing?

Organisaties hebben vaak honderden toepassingen waar gebruikers afhankelijk van zijn voor hun werk. Gebruikers hebben toegang tot deze toepassingen van groot aantal apparaten en -locaties. Elke dag worden nieuwe toepassingen toegevoegd, ontwikkeld en buiten gebruik gesteld. Met zoveel toepassingen en toegangs punten is het belang rijker dan ooit om een Cloud oplossing te gebruiken voor het beheren van gebruikers toegang tot alle toepassingen.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Welke typen toepassingen kan ik integreren met Azure AD?

Er zijn vier hoofd typen toepassingen die u aan uw **bedrijfs toepassingen** kunt toevoegen en beheren met Azure AD:

- **Azure AD Gallery-toepassingen** : Azure AD bevat een galerie met duizenden toepassingen die vooraf zijn geïntegreerd voor eenmalige aanmelding met Azure AD. Sommige toepassingen die worden gebruikt in uw organisatie, bevinden zich waarschijnlijk in de galerie. [Meer informatie over het plannen van uw app-integratie](plan-an-application-integration.md)of over het verkrijgen van gedetailleerde integratie stappen voor afzonderlijke apps in de [zelf studies voor SaaS-toepassingen](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **On-premises toepassingen met toepassings proxy** – met Azure AD-toepassingsproxy kunt u uw on-premises web-apps integreren met Azure AD ter ondersteuning van eenmalige aanmelding. Eind gebruikers hebben dan toegang tot uw on-premises web-apps op dezelfde manier als ze toegang hebben tot Office 365 en andere SaaS-apps. [Meer informatie over het gebruik van toepassings proxy en hoe deze werkt](what-is-application-proxy.md).

- **Aangepaste toepassingen** : bij het bouwen van uw eigen line-of-business-toepassingen kunt u ze integreren met Azure AD voor de ondersteuning van eenmalige aanmelding. Door uw toepassing te registreren bij Azure AD, hebt u controle over het verificatie beleid voor de toepassing. Zie [richt lijnen voor ontwikkel aars](developer-guidance-for-integrating-applications.md)voor meer informatie.

- **Niet-galerie toepassingen** : Breng uw eigen toepassingen. Ondersteuning voor eenmalige aanmelding voor andere apps door deze toe te voegen aan Azure AD. U kunt elke gewenste webkoppeling of een toepassing die een gebruikers naam en wachtwoord veld weergeeft, integreren met SAML-of OpenID Connect Connect-protocollen, of SCIM ondersteunen. Zie [eenmalige aanmelding configureren voor niet-galerie-apps](configure-single-sign-on-non-gallery-applications.md)voor meer informatie.

## <a name="manage-risk-with-conditional-access-policies"></a>Risico's beheren met beleid voor voorwaardelijke toegang

Koppelen van Azure AD eenmalige aanmelding (SSO) met [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) biedt een hoog beveiligings niveau voor het openen van toepassingen. Tot de beveiligings mogelijkheden behoren identiteits beveiliging op Cloud niveau, Toegangs beheer op basis van Risico's, systeem eigen multi-factor Authentication en beleid voor voorwaardelijke toegang. Dankzij deze mogelijkheden kunt een nauwkeurig beheer creëren op basis van toepassingen of van groepen die een hoger niveau van beveiliging nodig hebben.

## <a name="improve-productivity-with-single-sign-on"></a>Verhoog de productiviteit met eenmalige aanmelding

Eenmalige aanmelding (SSO) voor toepassingen en Office 365 biedt een superieure aanmeldingservaring voor bestaande gebruikers door verschillende aanmeldingen te elimineren. Er wordt een samenhangende gebruikersomgeving gecreëerd zonder afleiding door meerdere aanmeldschermen of de noodzaak om meerdere wachtwoorden te beheren. De bedrijfsgroep kan toegang beheren en goedkeuren via selfservice en dynamisch lidmaatschap. De zorg dat alleen de juiste personen in het bedrijf toegang hebben tot een toepassing verbetert de beveiliging van het identiteitssysteem.

SSO verbetert de beveiliging. *Zonder eenmalige aanmelding* moeten beheerders gebruikersaccounts maken en bijwerken voor elke afzonderlijke toepassing. Dat kost waardevolle tijd. Gebruikers moeten meerdere referenties bijhouden voor toegang tot hun toepassingen. Als gevolg hiervan schrijven gebruikers vaak hun wachtwoorden op of gebruiken andere oplossingen voor wachtwoordbeheer waarmee het beveiligingsrisico stijgt. [Meer informatie over eenmalige aanmelding](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Voeg governance en naleving toe

Met Azure AD, kunt u de toepassing-aanmeldingen bewaken met behulp van rapporten die gebruikmaken van hulpprogramma's voor beveiligingsincidenten en gebeurtenisbewaking (SIEM). U kunt de rapporten openen vanuit de portal of via API's. Programmatisch controleren wie toegang heeft tot uw toepassingen en toegang verwijderen van niet-actieve gebruikers via toegangsbeoordelingen.

## <a name="manage-costs"></a>Kosten beheren

Door te migreren naar Azure AD, kunt u kosten besparen en wordt het beheer van on-premises infrastructuur makkelijk. Azure AD biedt ook self-service tot toepassingen, en bespaart daarmee tijd voor beheerders en gebruikers. Eenmalige aanmelding elimineert de noodzaak van toepassingsspecifieke wachtwoorden. De mogelijkheid u slechts eenmaal te hoeven aanmelden, bespaart op kosten in verband met wachtwoordherstel voor toepassingen en wordt productieverlies voorkomen bij het ophalen van wachtwoorden.

## <a name="next-steps"></a>Volgende stappen

- [Wat is toepassingsproxy?](what-is-application-proxy.md)
- [Snelstartgids: een galerie toepassing toevoegen aan uw Azure AD-Tenant](add-application-portal.md)
