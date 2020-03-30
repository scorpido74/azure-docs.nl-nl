---
title: Atlassian Jira/Confluence-beheerhandleiding - Azure Active Directory| Microsoft Documenten
description: Beheerhandleiding voor het gebruik van Atlassian Jira en Confluence met Azure Active Directory (Azure AD)..
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161209"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian Jira- en Confluence-beheerhandleiding voor Azure Active Directory

## <a name="overview"></a>Overzicht

Met de Plug-in Azure Active Directory (Azure AD) met eenmalige aanmelding (SSO) kunnen Microsoft Azure AD-klanten hun werk- of schoolaccount gebruiken om zich aan te melden bij producten op basis van Atlassian Jira en Confluence Server. Het implementeert SAML 2.0-gebaseerde SSO.

## <a name="how-it-works"></a>Hoe werkt het?

Wanneer gebruikers zich willen aanmelden bij de Atlassian Jira- of Confluence-toepassing, zien ze de knop **Inloggen met Azure AD** op de aanmeldingspagina. Wanneer ze deze selecteren, moeten ze zich aanmelden met de aanmeldingspagina van Azure AD-organisatie (dat wil zeggen hun werk- of schoolaccount).

Nadat de gebruikers zijn geverifieerd, moeten ze zich kunnen aanmelden bij de toepassing. Als ze al zijn geverifieerd met de ID en het wachtwoord voor hun werk- of schoolaccount, melden ze zich rechtstreeks aan bij de toepassing. 

Aanmelden werkt in Jira en Confluence. Als gebruikers zijn aangemeld bij de Jira-toepassing en Confluence wordt geopend in hetzelfde browservenster, hoeven ze de referenties voor de andere app niet te verstrekken. 

Gebruikers kunnen het Atlassian-product ook openen via Mijn apps onder het werk- of schoolaccount. Ze moeten worden aangemeld zonder te worden gevraagd om referenties.

> [!NOTE]
> Gebruikersinrichting gebeurt niet via de plug-in.

## <a name="audience"></a>Doelgroep

Jira- en Confluence-beheerders kunnen de plug-in gebruiken om SSO in te schakelen met Azure AD.

## <a name="assumptions"></a>Veronderstellingen

* Jira- en Confluence-exemplaren zijn HTTPS ingeschakeld.
* Gebruikers zijn al gemaakt in Jira of Confluence.
* Gebruikers hebben rollen toegewezen in Jira of Confluence.
* Beheerders hebben toegang tot informatie die nodig is om de plug-in te configureren.
* Jira of Confluence is ook buiten het bedrijfsnetwerk beschikbaar.
* De plug-in werkt alleen met de on-premises versie van Jira en Confluence.

## <a name="prerequisites"></a>Vereisten

Let op de volgende informatie voordat u de plug-in installeert:

* Jira en Confluence zijn geïnstalleerd op een Windows 64-bits versie.
* Jira- en Confluence-versies zijn HTTPS ingeschakeld.
* Jira en Confluence zijn beschikbaar op het internet.
* Admin referenties zijn op zijn plaats voor Jira en Confluence.
* Beheerdersreferenties zijn aanwezig voor Azure AD.
* WebSudo is uitgeschakeld in Jira en Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Ondersteunde versies van Jira en Confluence

De plug-in ondersteunt de volgende versies van Jira en Confluence:

* Jira Core en Software: 6.0 tot 7.12
* Jira Service Desk: 3.0.0 tot 3.5.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Samenloop: 5,0 tot 5,10
* Samenloop: 6.0.1
* Samenloop: 6.1.1
* Samenloop: 6.2.1
* Samenloop: 6.3.4
* Samenloop: 6.4.0
* Samenloop: 6.5.0
* Samenloop: 6.6.2
* Samenloop: 6.7.0
* Samenloop: 6.8.1
* Samenloop: 6.9.0
* Samenloop: 6.10.0
* Samenloop: 6.11.0
* Samenloop: 6.12.0

## <a name="installation"></a>Installeren

Voer de volgende stappen uit om de plug-in te installeren:

1. Meld je aan bij je Instantie Jira of Confluence als beheerder.

2. Ga naar de administratieconsole Jira/Confluence en selecteer **Add-ons**.

3. Download vanuit het Microsoft Downloadcentrum de [Microsoft SAML SSO-plug-in voor Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO-plug-in voor confluence.](https://www.microsoft.com/download/details.aspx?id=56503)

   De juiste versie van de plug-in wordt weergegeven in de zoekresultaten.

4. Selecteer de plug-in en de Universal Plug-in Manager (UPM) installeert deze.

Nadat de plug-in is geïnstalleerd, wordt deze weergegeven in het gedeelte **Gebruikersgeïnstalleerde invoegtoepassingen** van **Add-ons beheren.**

## <a name="plug-in-configuration"></a>Invoegconfiguratie

Voordat u de plug-in gaat gebruiken, moet u deze configureren. Selecteer de plug-in, selecteer de knop **Configureren** en geef de configuratiegegevens op.

De volgende afbeelding toont het configuratiescherm in zowel Jira als Confluence:

![Configuratiescherm voor invoeging](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL met ametagegevens:** de URL om federatiemetagegevens van Azure AD te krijgen.

* **Id's:** de URL die Azure AD gebruikt om de bron van de aanvraag te valideren. Het wordt toegewezen aan het **id-element** in Azure AD. De plug-in ontleent deze URL automatisch als https://*\<domein:poort>*/.

* **Url van antwoord:** de url van het antwoord in uw identiteitsprovider (IdP) waarmee de SAML-aanmelding wordt gestart. Het wordt toegewezen aan het **element URL beantwoorden** in Azure AD. De plug-in ontleent deze URL automatisch als https://*\<domein:poort>*/plugins/servlet/saml/auth.

* **Aanmeldings-URL**: de aanmeldings-URL in uw IdP die de SAML-aanmelding initieert. Het wordt toegewezen aan het element **Aanmelden** in Azure AD. De plug-in ontleent deze URL automatisch als https://*\<domein:poort>*/plugins/servlet/saml/auth.

* **IdP-entiteits-id:** de entiteits-id die uw IdP gebruikt. Dit vak wordt ingevuld wanneer de URL met metagegevens is opgelost.

* **Aanmeldings-URL**: De aanmeldings-URL van uw IdP. Dit vak wordt ingevuld vanuit Azure AD wanneer de URL met ametjes is opgelost.

* **Uitlog-URL**: De uitlog-URL van uw IdP. Dit vak wordt ingevuld vanuit Azure AD wanneer de URL met ametjes is opgelost.

* **X.509 Certificaat**: Het X.509-certificaat van uw IdP. Dit vak wordt ingevuld vanuit Azure AD wanneer de URL met ametjes is opgelost.

* **Naam van de aanmeldingsknop**: de naam van de aanmeldingsknop die uw organisatie gebruikers wil laten zien op de aanmeldingspagina.

* **SAML User ID Locaties:** De locatie waar de Jira of Confluence user ID wordt verwacht in de SAML-respons. Het kan in **NameID** of in een aangepaste attribuutnaam zijn.

* **Kenmerknaam:** de naam van het kenmerk waar de gebruikersnaam wordt verwacht.

* **Home Realm Discovery inschakelen:** de selectie die moet worden gemaakt als het bedrijf op Active Directory Federation Services (AD FS)-gebaseerde aanmelding wordt gebruikt.

* **Domeinnaam:** De domeinnaam als aanmelden ad FS is gebaseerd.

* **Eén afmelding inschakelen:** de selectie die u wilt maken als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij Jira of Confluence.

## <a name="troubleshooting"></a>Problemen oplossen

* **U krijgt meerdere certificaatfouten:** Meld u aan bij Azure AD en verwijder de meerdere certificaten die beschikbaar zijn voor de app. Zorg ervoor dat er slechts één certificaat aanwezig is.

* **Een certificaat verloopt bijna in Azure AD:** Add-ons zorgen voor automatische rollover van het certificaat. Wanneer een certificaat bijna verloopt, moet een nieuw certificaat actief worden gemarkeerd en moeten ongebruikte certificaten worden verwijderd. Wanneer een gebruiker zich in dit scenario bij Jira probeert aan te melden, haalt en slaat de plug-in het nieuwe certificaat op.

* **U wilt WebSudo uitschakelen (de beveiligde administratorsessie uitschakelen):**

  * Voor Jira zijn beveiligde beheerderssessies (dat wil zeggen wachtwoordbevestiging voordat ze toegang krijgen tot beheerfuncties) standaard ingeschakeld. Als u deze mogelijkheid in uw Jira-instantie wilt verwijderen, geeft u de volgende regel op in het bestand jira-config.properties:`ira.websudo.is.disabled = true`

  * Volg voor Confluence de stappen op de [ondersteuningssite van Confluence.](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)

* **Velden die worden verondersteld te worden ingevuld door de URL van metagegevens worden niet ingevuld:**

  * Controleer of de URL juist is. Controleer of u de juiste tenant en app-id in kaart hebt gebracht.

  * Voer de URL in een browser in en kijk of u de xml metagegevens van de federatie ontvangt.

* **Er is een interne serverfout:** Controleer de logboeken in de logboekmap van de installatie. Als u de fout oploopt wanneer de gebruiker zich probeert aan te melden met Azure AD SSO, u de logboeken delen met het ondersteuningsteam.

* **Er is een foutmelding 'Gebruikersnaam niet gevonden' wanneer de gebruiker zich probeert aan te melden:** De gebruikersnaam maken in Jira of Confluence.

* **Er is een fout 'App niet gevonden' in Azure AD:** kijk of de juiste URL is toegewezen aan de app in Azure AD.

* **Ondersteuning nodig:** Neem contact op met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Het team reageert in 24-48 kantooruren.

  U ook een ondersteuningsticket met Microsoft verhogen via het Azure-portalkanaal.

## <a name="plug-in-faq"></a>Veelgestelde vragen over plug-in

Raadpleeg hieronder veelgestelde vragen als u vragen heeft over deze plug-in.

### <a name="what-does-the-plug-in-do"></a>Wat doet de plug-in?

De plug-in biedt single sign-on (SSO) mogelijkheden voor Atlassian Jira (inclusief Jira Core, Jira Software, Jira Service Desk) en Confluence on-premises software. De plug-in werkt met Azure Active Directory (Azure AD) als identiteitsprovider (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Met welke Atlassian-producten werkt de plug-in?

De plug-in werkt met on-premises versies van Jira en Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Werkt de plug-in op cloudversies?

Nee. De plug-in ondersteunt alleen on-premises versies van Jira en Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Welke versies van Jira en Confluence ondersteunt de plug-in?

De plug-in ondersteunt deze versies:

* Jira Core en Software: 6.0 tot 7.12
* Jira Service Desk: 3.0.0 tot 3.5.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Samenloop: 5,0 tot 5,10
* Samenloop: 6.0.1
* Samenloop: 6.1.1
* Samenloop: 6.2.1
* Samenloop: 6.3.4
* Samenloop: 6.4.0
* Samenloop: 6.5.0
* Samenloop: 6.6.2
* Samenloop: 6.7.0
* Samenloop: 6.8.1
* Samenloop: 6.9.0
* Samenloop: 6.10.0
* Samenloop: 6.11.0
* Samenloop: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Is de plug-in gratis of betaald?

Het is een gratis add-on.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Moet ik Jira of Confluence opnieuw opstarten nadat ik de plug-in heb geïmplementeerd?

Een herstart is niet vereist. U de plug-in direct gaan gebruiken.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hoe krijg ik ondersteuning voor de plug-in?

U contact opnemen met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) voor alle ondersteuning die nodig is voor deze plug-in. Het team reageert in 24-48 kantooruren.

U ook een ondersteuningsticket met Microsoft verhogen via het Azure-portalkanaal.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Zou de plug-in werken op een Mac of Ubuntu installatie van Jira en Confluence?

We hebben de plug-in alleen getest op 64-bits Windows Server-installaties van Jira en Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Werkt de plug-in met andere id's dan Azure AD?

Nee. Het werkt alleen met Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Met welke versie van SAML werkt de plug-in?

Het werkt met SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Doet de plug-in user provisioning?

Nee. De plug-in biedt alleen SAML 2.0-gebaseerde SSO. De gebruiker moet in de toepassing worden opgenomen voordat de SSO-aanmelding wordt ingediend.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Ondersteunt de plug-in clusterversies van Jira en Confluence?

Nee. De plug-in werkt met on-premises versies van Jira en Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Werkt de plug-in met HTTP-versies van Jira en Confluence?

Nee. De plug-in werkt alleen met HTTPS-installaties.
