---
title: Atlassian Jira/confluence-beheer handleiding-Azure Active Directory | Microsoft Docs
description: Beheerders handleiding voor het gebruik van Atlassian Jira en confluence met Azure Active Directory (Azure AD)..
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161209"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian Jira en confluence-beheer handleiding voor Azure Active Directory

## <a name="overview"></a>Overzicht

Met de Azure Active Directory-invoeg toepassing (Azure AD) eenmalige aanmelding (SSO) kunnen Microsoft Azure AD klanten hun werk-of school account gebruiken om zich aan te melden bij Atlassian Jira en confluence server-gebaseerde producten. Het implementeert SSO op basis van SAML 2,0.

## <a name="how-it-works"></a>Het werkt als volgt

Wanneer gebruikers zich willen aanmelden bij de Atlassian Jira of confluence-toepassing, zien ze de knop **Aanmelden met Azure AD** op de aanmeldings pagina. Wanneer deze optie is geselecteerd, moeten ze zich aanmelden met behulp van de aanmeldings pagina van de Azure AD-organisatie (dat wil zeggen, hun werk-of school account).

Nadat de gebruikers zijn geverifieerd, moeten ze zich kunnen aanmelden bij de toepassing. Als ze al zijn geverifieerd met de ID en het wacht woord voor hun werk-of school account, kunnen ze zich rechtstreeks aanmelden bij de toepassing. 

Aanmelden werkt op Jira en confluence. Als gebruikers zijn aangemeld bij de Jira-toepassing en confluence in hetzelfde browser venster worden geopend, hoeven ze de referenties voor de andere app niet op te geven. 

Gebruikers kunnen via mijn apps onder het werk-of school account ook toegang krijgen tot het Atlassian-product. Ze moeten worden aangemeld zonder dat ze om referenties wordt gevraagd.

> [!NOTE]
> Gebruikers inrichten wordt niet uitgevoerd via de invoeg toepassing.

## <a name="audience"></a>Doelgroep

Jira-en confluence-beheerders kunnen de-invoeg toepassing gebruiken om SSO in te scha kelen met behulp van Azure AD.

## <a name="assumptions"></a>Veronderstellingen

* Voor Jira-en confluence-exemplaren is HTTPS ingeschakeld.
* Er zijn al gebruikers gemaakt in Jira of confluence.
* Voor gebruikers zijn rollen toegewezen in Jira of confluence.
* Beheerders hebben toegang tot de vereiste informatie voor het configureren van de invoeg toepassing.
* Jira of confluence is ook beschikbaar buiten het bedrijfs netwerk.
* De invoeg toepassing werkt alleen met de on-premises versie van Jira en confluence.

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende informatie voordat u de invoeg toepassing installeert:

* Jira en confluence zijn geïnstalleerd op een 64-bits versie van Windows.
* Voor Jira-en confluence-versies is HTTPS ingeschakeld.
* Jira en confluence zijn beschikbaar op internet.
* Er zijn beheerders referenties aanwezig voor Jira en confluence.
* Er zijn beheerders referenties voor Azure AD.
* WebSudo is uitgeschakeld in Jira en confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Ondersteunde versies van Jira en confluence

De invoeg toepassing ondersteunt de volgende versies van Jira en confluence:

* Jira core en software: 6,0 tot 7,12
* Jira Service Desk: 3.0.0 to 3.5.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5,0 tot 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Installatie

Als u de invoeg toepassing wilt installeren, voert u de volgende stappen uit:

1. Meld u als beheerder aan bij uw Jira-of confluence-instantie.

2. Ga naar de beheer console van Jira/confluence en selecteer **invoeg toepassingen**.

3. Down load de [micro soft SAML SSO-invoeg toepassing voor Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ micro soft [SAML SSO-invoeg toepassing voor confluence](https://www.microsoft.com/download/details.aspx?id=56503)in het micro soft Download centrum.

   De juiste versie van de invoeg toepassing wordt weer gegeven in de zoek resultaten.

4. Selecteer de invoeg toepassing en de universele invoeg toepassing Manager (UPM) wordt geïnstalleerd.

Nadat de invoeg toepassing is geïnstalleerd, wordt deze weer gegeven in de sectie door de **gebruiker geïnstalleerde** invoeg toepassingen van **invoeg toepassingen beheren**.

## <a name="plug-in-configuration"></a>Configuratie van invoeg toepassing

Voordat u begint met het gebruik van de invoeg toepassing, moet u deze configureren. Selecteer de invoeg toepassing, selecteer de knop **configureren** en geef de configuratie details op.

In de volgende afbeelding ziet u het configuratie scherm in zowel Jira als confluence:

![Configuratie scherm voor de invoeg toepassing](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Meta gegevens-URL**: de URL voor het ophalen van federatieve meta gegevens uit Azure AD.

* **Id's**: de URL die door Azure AD wordt gebruikt om de bron van de aanvraag te valideren. Het wordt toegewezen aan het **id** -element in azure AD. Deze URL wordt door de invoeg toepassing automatisch afgeleid van https:// *\<domein: poort >* /.

* **Antwoord-URL**: de antwoord-URL in uw ID-provider (IDP) die de SAML-aanmelding initieert. Het wordt toegewezen aan het **antwoord-URL** -element in azure AD. Deze URL wordt door de invoeg toepassing automatisch afgeleid van https:// *\<domein: poort >* /plugins/servlet/SAML/auth.

* **Aanmeldings-URL**: de AANMELDINGS-URL in uw IDP waarmee de SAML-aanmelding wordt gestart. Het wordt toegewezen aan het **aanmeldings** element in azure AD. Deze URL wordt door de invoeg toepassing automatisch afgeleid van https:// *\<domein: poort >* /plugins/servlet/SAML/auth.

* **IDP-entiteit-id**: de entiteit-id die uw IDP gebruikt. Dit vak wordt ingevuld wanneer de meta gegevens-URL is opgelost.

* **Aanmeldings-URL**: de AANMELDINGS-URL van uw IDP. Dit vak wordt ingevuld vanuit Azure AD wanneer de meta gegevens-URL is opgelost.

* **Afmeldings-URL**: de afmeldings-URL van uw IDP. Dit vak wordt ingevuld vanuit Azure AD wanneer de meta gegevens-URL is opgelost.

* **X. 509-certificaat**: het x. 509-certificaat van uw IDP. Dit vak wordt ingevuld vanuit Azure AD wanneer de meta gegevens-URL is opgelost.

* **Naam van de aanmeldings knop**: de naam van de aanmeldings knop die uw organisatie gebruikers wil laten zien op de aanmeldings pagina.

* **Gebruikers-ID-locaties van SAML**: de locatie waar de gebruikers-id van Jira of confluence wordt verwacht in het SAML-antwoord. Het kan worden **NameID** of de naam van een aangepast kenmerk.

* **Kenmerk naam**: de naam van het kenmerk waarin de gebruikers-id wordt verwacht.

* **Home realm Discovery inschakelen**: de selectie die moet worden uitgevoerd als het bedrijf gebruikmaakt van Active Directory Federation Services (AD FS)-aanmelding.

* **Domein naam**: de domein naam als aanmelding is AD FS op basis van.

* **Eén afmelden inschakelen**: de selectie die moet worden uitgevoerd als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij Jira of confluence.

## <a name="troubleshooting"></a>Problemen oplossen

* **U krijgt meerdere certificaat fouten**: Meld u aan bij Azure AD en verwijder de meerdere certificaten die beschikbaar zijn voor de app. Zorg ervoor dat er slechts één certificaat aanwezig is.

* **Een certificaat verloopt binnenkort in azure AD**: invoeg toepassingen zorgen ervoor dat de automatische rollover van het certificaat wordt toegepast. Wanneer een certificaat bijna verloopt, moet een nieuw certificaat worden gemarkeerd als actief en moeten ongebruikte certificaten worden verwijderd. Wanneer een gebruiker zich probeert aan te melden bij Jira in dit scenario, wordt het nieuwe certificaat door de invoeg toepassing opgehaald en opgeslagen.

* **U wilt WebSudo uitschakelen (de beveiligde beheer sessie uitschakelen)** :

  * Voor Jira zijn beveiligde beheerders sessies (dat wil zeggen, wacht woord bevestigen vóór toegang tot beheer functies) standaard ingeschakeld. Als u deze mogelijkheid in uw Jira-exemplaar wilt verwijderen, geeft u de volgende regel op in het bestand Jira-config. Properties: `ira.websudo.is.disabled = true`

  * Volg voor confluence de stappen op de [ondersteunings site van confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Velden die moeten worden ingevuld door de meta gegevens-URL, worden niet ingevuld**:

  * Controleer of de URL juist is. Controleer of u de juiste Tenant en App-ID hebt toegewezen.

  * Voer de URL in een browser in en controleer of u de federatieve meta gegevens-XML ontvangt.

* **Er is een interne server fout opgetreden**: Raadpleeg de logboeken in de logboekmap van de installatie. Als u de fout melding krijgt wanneer de gebruiker zich probeert aan te melden met behulp van Azure AD SSO, kunt u de logboeken delen met het ondersteunings team.

* De **fout ' gebruikers-id is niet gevonden ' wordt weer gegeven wanneer de gebruiker zich probeert aan te melden**: Maak de gebruikers-id in Jira of confluence.

* Er is **een fout ' app niet gevonden ' in azure AD**: Raadpleeg of de juiste URL is toegewezen aan de app in azure AD.

* **U hebt ondersteuning nodig**: Neem contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Het team reageert binnen 24-48 werk uren.

  U kunt ook een ondersteunings ticket met micro soft genereren via het Azure Portal kanaal.

## <a name="plug-in-faq"></a>Veelgestelde vragen over invoeg toepassingen

Raadpleeg de veelgestelde vragen als u een query hebt over deze invoeg toepassing.

### <a name="what-does-the-plug-in-do"></a>Wat doet de invoeg toepassing?

De invoeg toepassing biedt ondersteuning voor eenmalige aanmelding (SSO) voor Atlassian Jira (waaronder Jira core, Jira software, Jira Service Desk) en confluence on-premises software. De invoeg toepassing werkt met Azure Active Directory (Azure AD) als een id-provider (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Welke Atlassian-producten werken met de invoeg toepassing?

De invoeg toepassing werkt met on-premises versies van Jira en confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Werkt de invoeg toepassing in Cloud versies?

Nee. De invoeg toepassing biedt alleen ondersteuning voor on-premises versies van Jira en confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Welke versies van Jira en confluence bieden ondersteuning voor de invoeg toepassing?

De invoeg toepassing ondersteunt de volgende versies:

* Jira core en software: 6,0 tot 7,12
* Jira Service Desk: 3.0.0 to 3.5.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5,0 tot 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Is de invoeg toepassing gratis of betaald?

Het is een gratis invoeg toepassing.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Moet ik Jira of confluence opnieuw starten nadat ik de invoeg toepassing heb geïmplementeerd?

Opnieuw opstarten is niet vereist. U kunt de invoeg toepassing onmiddellijk gaan gebruiken.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hoe kan ik ondersteuning voor de invoeg toepassing krijgen?

U kunt contact met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) bereiken voor de ondersteuning die nodig is voor deze invoeg toepassing. Het team reageert binnen 24-48 werk uren.

U kunt ook een ondersteunings ticket met micro soft genereren via het Azure Portal kanaal.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Werkt de invoeg toepassing op een Mac-of Ubuntu-installatie van Jira en confluence?

We hebben de invoeg toepassing alleen getest op 64-bits Windows Server-installaties van Jira en confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Werkt de invoeg toepassing met een andere id dan Azure AD?

Nee. Het werkt alleen met Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Welke versie van SAML werkt met de invoeg toepassing?

Het werkt met SAML 2,0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Wordt de gebruiker door de invoeg toepassing ingericht?

Nee. De invoeg toepassing biedt alleen op SAML 2,0 gebaseerde SSO. De gebruiker moet in de toepassing worden ingericht voordat de aanmelding wordt gedaan.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Ondersteunt de invoeg toepassing cluster versies van Jira en confluence?

Nee. De invoeg toepassing werkt met on-premises versies van Jira en confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Werkt de invoeg toepassing met HTTP-versies van Jira en confluence?

Nee. De invoeg toepassing werkt alleen met installaties met HTTPS-functionaliteit.
