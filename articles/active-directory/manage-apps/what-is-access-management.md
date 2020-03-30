---
title: Toegang tot apps beheren met Azure AD | Microsoft Documenten
description: Beschrijft hoe Azure Active Directory organisaties in staat stelt om de apps op te geven waartoe elke gebruiker toegang heeft.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409063"
---
# <a name="managing-access-to-apps"></a>Toegang tot apps beheren

Doorlopend toegangsbeheer, gebruiksevaluatie en rapportage blijven een uitdaging nadat een app is geïntegreerd in het identiteitssysteem van uw organisatie. In veel gevallen moeten IT-beheerders of helpdesk een voortdurende actieve rol spelen bij het beheren van de toegang tot uw apps. Soms wordt de opdracht uitgevoerd door een algemeen of divisie-IT-team. Vaak is de toewijzingsbeslissing bedoeld om te worden gedelegeerd aan de zakelijke beslisser, waarbij hun goedkeuring vereist is voordat IT de opdracht maakt.  Andere organisaties investeren in integratie met een bestaand geautomatiseerd identiteits- en toegangsbeheersysteem, zoals Role-Based Access Control (RBAC) of Attribute-Based Access Control (ABAC). Zowel de integratie als de regelontwikkeling zijn meestal gespecialiseerd en duur. Monitoring of rapportage over een van beide management benaderingen is zijn eigen afzonderlijke, kostbare en complexe investeringen.

## <a name="how-does-azure-active-directory-help"></a>Hoe helpt Azure Active Directory?

Azure AD ondersteunt uitgebreid toegangsbeheer voor geconfigureerde toepassingen, zodat organisaties eenvoudig het juiste toegangsbeleid kunnen bereiken, variërend van automatische, op kenmerken gebaseerde toewijzing (ABAC- of RBAC-scenario's) via delegeren en beheerdersbeheer. Met Azure AD u eenvoudig complexe beleidsregels realiseren, meerdere beheermodellen voor één toepassing combineren en zelfs beheerregels voor toepassingen met dezelfde doelgroepen hergebruiken.

Met Azure AD is de rapportage over gebruik en toewijzing volledig geïntegreerd, zodat beheerders eenvoudig kunnen rapporteren over toewijzingsstatus, toewijzingsfouten en zelfs gebruik.

### <a name="assigning-users-and-groups-to-an-app"></a>Gebruikers en groepen toewijzen aan een app

De toepassingstoewijzing van Azure AD is gericht op twee primaire toewijzingsmodi:

* **Individuele opdracht** Een IT-beheerder met machtigingen voor globale beheerders van directory's kan afzonderlijke gebruikersaccounts selecteren en deze toegang verlenen tot de toepassing.

* **Groepstoewijzing (vereist Azure AD Premium P1 of P2)** Een IT-beheerder met machtigingen voor directory Globale beheerders kan een groep aan de toepassing toewijzen. De toegang van specifieke gebruikers wordt bepaald door of ze lid zijn van de groep op het moment dat ze toegang proberen te krijgen tot de toepassing. Met andere woorden, een beheerder kan effectief een toewijzingsregel maken met de vermelding "elk huidig lid van de toegewezen groep heeft toegang tot de toepassing". Met deze toewijzingsoptie kunnen beheerders profiteren van een van de azure AD-groepsbeheeropties, waaronder dynamische groepen op basis van [kenmerken,](../fundamentals/active-directory-groups-create-azure-portal.md)externe systeemgroepen (bijvoorbeeld on-premises Active Directory of Workday) of door de beheerder beheerde of zelfbeheerde groepen. Eén groep kan eenvoudig aan meerdere apps worden toegewezen, zodat toepassingen met toewijzingsaffiniteit toewijzingsregels kunnen delen, waardoor de algehele complexiteit van het beheer wordt verminderd. Houd er rekening mee dat geneste groepslidmaatschappen op dit moment niet worden ondersteund voor groepstoewijzing aan toepassingen.

Met behulp van deze twee toewijzingsmodi kunnen beheerders elke gewenste aanpak voor toewijzingsbeheer bereiken.

### <a name="requiring-user-assignment-for-an-app"></a>Gebruikerstoewijzing vereisen voor een app

Bij bepaalde typen toepassingen u [ervoor zorgen dat gebruikers aan de toepassing worden toegewezen.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) Door dit te doen, voorkomt u dat iedereen zich aanmeldt, behalve de gebruikers die u expliciet aan de toepassing toewijst. De volgende typen toepassingen ondersteunen deze optie:

* Toepassingen die zijn geconfigureerd voor federatieve single sign-on (SSO) met SAML-gebaseerde verificatie
* Toepassingsproxytoepassingen die Azure Active Directory Pre-Authentication gebruiken
* Toepassingen die zijn gebouwd op het Azure AD-toepassingsplatform en die OAuth 2.0 / OpenID Connect-verificatie gebruiken nadat een gebruiker of beheerder toestemming heeft gegeven voor die toepassing. Bepaalde bedrijfstoepassingen bieden extra controle over wie zich mag aanmelden.

Wanneer gebruikerstoewijzing *niet vereist*is, zien niet-toegewezen gebruikers de app niet op hun deelvenster Mijn apps, maar kunnen ze zich wel aanmelden bij de toepassing zelf (ook wel SP-geïnitieerde aanmelding genoemd) of ze kunnen de URL voor **gebruikerstoegang** gebruiken op de pagina **Eigenschappen** van de toepassing (ook bekend als idp-geïnitieerde aanmelding).

Voor sommige toepassingen is de optie om gebruikerstoewijzing vereisen niet beschikbaar in de eigenschappen van de toepassing. In deze gevallen u PowerShell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen op de serviceprincipal.

### <a name="determining-the-user-experience-for-accessing-apps"></a>De gebruikerservaring bepalen voor toegang tot apps

Azure AD biedt [verschillende aanpasbare manieren om toepassingen](end-user-experiences.md) te implementeren voor eindgebruikers in uw organisatie:

* Toegangspaneel voor Azure AD Mijn apps
* Startpictogram voor Office 365-toepassingen
* Directe aanmelding bij federatieve apps (service-pr)
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

U bepalen of gebruikers die zijn toegewezen aan een bedrijfsapp deze kunnen zien in het toegangspaneel en het startprogramma voor Office 365-toepassingen.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Voorbeeld: Complexe toepassingstoewijzing met Azure AD
Overweeg een toepassing zoals Salesforce. In veel organisaties wordt Salesforce voornamelijk gebruikt door de marketing- en salesteams. Vaak hebben leden van het marketingteam een zeer bevoorrechte toegang tot Salesforce, terwijl leden van het verkoopteam beperkte toegang hebben. In veel gevallen heeft een brede populatie van informatiewerkers de toegang tot de toepassing beperkt. Uitzonderingen op deze regels maken de zaken ingewikkelder. Het is vaak het voorrecht van de marketing- of salesleiderschapsteams om een gebruiker toegang te verlenen of hun rol onafhankelijk van deze algemene regels te wijzigen.

Met Azure AD kunnen toepassingen zoals Salesforce vooraf worden geconfigureerd voor single sign-on (SSO) en geautomatiseerde inrichting. Zodra de toepassing is geconfigureerd, kan een beheerder de eenmalige actie uitvoeren om de juiste groepen te maken en toe te wijzen. In dit voorbeeld kan een beheerder de volgende toewijzingen uitvoeren:

* [Dynamische groepen](../fundamentals/active-directory-groups-create-azure-portal.md) kunnen worden gedefinieerd om automatisch alle leden van de marketing- en verkoopteams te vertegenwoordigen met behulp van kenmerken zoals afdeling of rol:
  
  * Alle leden van marketinggroepen zouden worden toegewezen aan de rol 'marketing' in Salesforce
  * Alle leden van verkoopteamgroepen worden toegewezen aan de rol 'verkoop' in Salesforce. Een verdere verfijning kan meerdere groepen gebruiken die regionale verkoopteams vertegenwoordigen die zijn toegewezen aan verschillende Salesforce-rollen.

* Om het uitzonderingsmechanisme in te schakelen, kan voor elke rol een selfservicegroep worden gemaakt. De groep 'Salesforce-marketinguitzondering' kan bijvoorbeeld worden gemaakt als een selfservicegroep. De groep kan worden toegewezen aan de Marketingrol van Salesforce en het marketingleiderschapsteam kan eigenaar worden. Leden van het marketingleiderschapsteam kunnen gebruikers toevoegen of verwijderen, een joinbeleid instellen of zelfs verzoeken van individuele gebruikers om lid te worden goedkeuren of weigeren. Dit mechanisme wordt ondersteund door middel van een informatiewerker passende ervaring die geen gespecialiseerde opleiding voor eigenaren of leden vereist.

In dit geval worden alle toegewezen gebruikers automatisch ingericht voor Salesforce, omdat ze aan verschillende groepen worden toegevoegd, hun roltoewijzing zou worden bijgewerkt in Salesforce. Gebruikers zouden Salesforce kunnen ontdekken en openen via het Microsoft-toepassingstoegangspaneel, Office-webclients of zelfs door naar hun organisatorische Salesforce-inlogpagina te navigeren. Beheerders kunnen de gebruiks- en toewijzingsstatus eenvoudig bekijken met Azure AD-rapportage.

Beheerders kunnen [Azure AD Conditional Access](../active-directory-conditional-access-azure-portal.md) gebruiken om toegangsbeleid voor specifieke rollen in te stellen. Dit beleid kan omvatten of toegang is toegestaan buiten de bedrijfsomgeving en zelfs Multi-Factor Authenticatie of apparaatvereisten om toegang te krijgen in verschillende gevallen.

## <a name="access-to-microsoft-applications"></a>Toegang tot Microsoft-toepassingen

Microsoft-toepassingen (zoals Office 365 Exchange, SharePoint, Yammer, enz.) worden een beetje anders toegewezen en beheerd dan SaaS-toepassingen van derden of andere toepassingen die u integreert met Azure AD voor éénmalige aanmelding.

Er zijn drie belangrijke manieren waarop een gebruiker toegang kan krijgen tot een door Microsoft gepubliceerde toepassing.

- Voor toepassingen in de Office 365 of andere betaalde suites krijgen gebruikers toegang via **licentietoewijzing,** hetzij rechtstreeks tot hun gebruikersaccount, hetzij via een groep met behulp van onze groepsgebaseerde licentietoewijzingsmogelijkheid.
- Voor toepassingen die Microsoft of een derde partij vrij publiceert voor iedereen om te gebruiken, kunnen gebruikers toegang krijgen via [toestemming van de gebruiker.](configure-user-consent.md) Dit betekent dat ze zich aanmelden bij de toepassing met hun Azure AD Work- of School-account en deze toegang geven tot een beperkte set gegevens op hun account.
- Voor toepassingen die Microsoft of een derde partij vrij publiceert voor iedereen om te gebruiken, kunnen gebruikers ook toegang krijgen via [toestemming van de beheerder.](manage-consent-requests.md) Dit betekent dat een beheerder heeft bepaald dat de toepassing kan worden gebruikt door iedereen in de organisatie, zodat ze zich aanmelden bij de toepassing met een Global Administrator-account en toegang verlenen aan iedereen in de organisatie.

Sommige toepassingen combineren deze methoden. Bepaalde Microsoft-toepassingen maken bijvoorbeeld deel uit van een Office 365-abonnement, maar vereisen nog steeds toestemming.

Gebruikers hebben toegang tot Office 365-toepassingen via hun Office 365-portals. U Office 365-toepassingen ook weergeven of verbergen in het toegangspaneel mijn apps met de [zichtbaarheidsschakelaar van Office 365](hide-application-from-user-portal.md) in de **gebruikersinstellingen**van uw map. 

Net als bij bedrijfsapps u [gebruikers toewijzen](assign-user-or-group-access-portal.md) aan bepaalde Microsoft-toepassingen via de Azure-portal of, als de portaloptie niet beschikbaar is, met PowerShell.

## <a name="next-steps"></a>Volgende stappen
* [Apps beveiligen met voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md)
* [Selfservice group management/SSAA](../users-groups-roles/groups-self-service-management.md)
