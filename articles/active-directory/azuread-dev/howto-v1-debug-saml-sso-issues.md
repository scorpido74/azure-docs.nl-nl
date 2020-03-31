---
title: Foutopsporing saml-gebaseerde aanmelding - Azure Active Directory | Microsoft Documenten
description: Foutopsporing op SAML-gebaseerde enkele aanmelding voor toepassingen in Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154845"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Foutopsporing op SAML-gebaseerde enkele aanmelding voor toepassingen in Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Meer informatie over het zoeken en oplossen [van afzonderlijke aanmeldingsproblemen](../manage-apps/what-is-single-sign-on.md) voor toepassingen in Azure Active Directory (Azure AD) die ondersteuning bieden voor [SAML (Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Voordat u begint

We raden u aan de [aanmeldingsextensie Mijn apps secure te](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)installeren. Deze browserextensie maakt het eenvoudig om de SAML-aanvraag en SAML-antwoordgegevens te verzamelen die u nodig hebt om problemen met eenmalige aanmelding op te lossen. In het geval u de extensie niet installeren, toont dit artikel u hoe u problemen oplossen, zowel met als zonder de geïnstalleerde extensie.

Als u de aangemeldsextensie Mijn apps Secure wilt downloaden en installeren, gebruikt u een van de volgende koppelingen.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Saml-gebaseerde aanmelding testen

Ga als belangrijkste aanmelding voor SAML tussen Azure AD en een doeltoepassing:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als globale beheerder of andere beheerder die gemachtigd is om toepassingen te beheren.
1. Selecteer in het linkerblad **Azure Active Directory**en selecteer vervolgens **Enterprise-toepassingen**. 
1. Selecteer in de lijst met bedrijfstoepassingen de toepassing waarvoor u één aanmelding wilt testen en selecteer vervolgens **Eenmalig aanmelden**.
1. Als u de saml-gebaseerde testervaring voor één aanmelding wilt openen, gaat u naar **Eenmalige aanmelding testen** (stap 5). Als de **knop Testen** grijs is, moet u eerst de vereiste kenmerken invullen en opslaan in de sectie **BasisSAML-configuratie.**
1. Gebruik in het **enkelaanmeldingsblad testen** uw bedrijfsreferenties om u aan te melden bij de doeltoepassing. U zich aanmelden als de huidige gebruiker of als een andere gebruiker. Als u zich aanmeldt als een andere gebruiker, wordt u door een prompt gevraagd om te verifiëren.

    ![Schermafbeelding van de SAML SSO-pagina voor testen](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Als u bent aangemeld, is de test geslaagd. In dit geval heeft Azure AD een SAML-antwoordtoken aan de toepassing uitgegeven. De toepassing gebruikte het SAML-token om u met succes aan te melden.

Als er een fout optreedt op de aanmeldingspagina van het bedrijf of de pagina van de toepassing, gebruikt u een van de volgende secties om de fout op te lossen.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Een aanmeldingsfout oplossen op de aanmeldingspagina van uw bedrijf

Wanneer u zich probeert aan te melden, ziet u mogelijk een fout op uw aanmeldingspagina van uw bedrijf die vergelijkbaar is met het volgende voorbeeld.

![Voorbeeld van een fout op de aanmeldingspagina van het bedrijf](./media/howto-v1-debug-saml-sso-issues/error.png)

Als u deze fout wilt opsporen, hebt u het foutbericht en het SAML-verzoek nodig. Met de aanmeldingsextensie Mijn apps secure verzamelt deze informatie automatisch deze informatie en worden de oplossingsrichtlijnen weergegeven op Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>De aanmeldingsfout oplossen met de aangemeldsextensie Mijn apps secure

1. Wanneer er een fout optreedt, wordt u door de extensie omgeleid naar het **azure AD-testblad voor één aanmelding.**
1. Selecteer in het **enkeltekenblad testen** de optie Het **SAML-verzoek downloaden**.
1. U ziet specifieke oplossingsrichtlijnen op basis van de fout en de waarden in de SAML-aanvraag.
1. U ziet de knop **Fixit om** de configuratie in Azure AD automatisch bij te werken om het probleem op te lossen. Als u deze knop niet ziet, is het aanmeldingsprobleem niet te wijten aan een verkeerde configuratie op Azure AD.

Als er geen oplossing is voorzien voor de aanmeldingsfout, raden we u aan het tekstvak voor feedback te gebruiken om ons hiervan op de hoogte te stellen.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>De fout oplossen zonder de aanmeldingsextensie Mijn apps secure te installeren

1. Kopieer het foutbericht rechtsonder op de pagina. Het foutbericht bevat:
    - Een Correlatie-ID en tijdstempel. Deze waarden zijn belangrijk wanneer u een ondersteuningsaanvraag maakt bij Microsoft, omdat ze de technici helpen uw probleem te identificeren en een nauwkeurige oplossing voor uw probleem te bieden.
    - Een verklaring waarin de oorzaak van het probleem wordt geïdentificeerd.
1. Ga terug naar Azure AD en zoek het **mes voor één aanmelding testen.**
1. Plak het foutbericht in het tekstvak boven **Richtlijnen voor resolutie**weergeven.
1. Klik **op Richtlijnen voor resolutie opvragen** om stappen weer te geven voor het oplossen van het probleem. De richtlijnen vereisen mogelijk informatie van het SAML-verzoek of het SAML-antwoord. Als u de aanmeldingsextensie Mijn apps secure niet gebruikt, hebt u mogelijk een tool zoals [Fiddler](https://www.telerik.com/fiddler) nodig om het SAML-verzoek en -antwoord op te halen.
1. Controleer of de bestemming in de SAML-aanvraag overeenkomt met de SAML Single Sign-On Service URL verkregen uit Azure AD.
1. Controleer of de uitgever in het SAML-verzoek dezelfde id is die u hebt geconfigureerd voor de toepassing in Azure AD. Azure AD gebruikt de uitgever om een toepassing in uw map te vinden.
1. Verificatie De URL van AssertionConsumerService is de plaats waar de toepassing het SAML-token van Azure AD verwacht te ontvangen. U deze waarde configureren in Azure AD, maar het is niet verplicht als deze deel uitmaakt van de SAML-aanvraag.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Een aanmeldingsfout op de toepassingspagina oplossen

U zich met succes aanmelden en vervolgens een fout zien op de pagina van de toepassing. Dit gebeurt wanneer Azure AD een token aan de toepassing heeft uitgegeven, maar de toepassing het antwoord niet accepteert.

Voer de volgende stappen uit om de fout op te lossen:

1. Als de toepassing zich in de Azure AD-galerie bevindt, controleert u of u alle stappen hebt gevolgd voor de integratie van de toepassing met Azure AD. Zie de [lijst met zelfinzetertutorials voor saas-toepassingen](../saas-apps/tutorial-list.md)om de integratie-instructies voor uw toepassing te vinden.
1. Haal het SAML-antwoord op.
    - Als de aangemeldsextensie Mijn apps secure is geïnstalleerd, klikt u in het **aanmeldingsblad Voor één aanmelding van mijn** apps op het **SAML-antwoord downloaden**.
    - Als de extensie niet is geïnstalleerd, gebruikt u een gereedschap zoals [Fiddler](https://www.telerik.com/fiddler) om het SAML-antwoord op te halen.
1. Let op deze elementen in het SAML-antwoordtoken:
   - Unieke id van de gebruiker van de waarde en notatie van NameID
   - Claims uitgegeven in het token
   - Certificaat dat wordt gebruikt om het token te ondertekenen.

     Zie [Enkelteken-saml-protocol](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over het SAML-antwoord .

1. Nu u het SAML-antwoord hebt beoordeeld, raadpleegt u [Fout op de pagina van een toepassing nadat u zich hebt aanmelden](../manage-apps/application-sign-in-problem-application-error.md) voor richtlijnen voor het oplossen van het probleem. 
1. Als u zich nog steeds niet met succes aanmelden, u de leverancier van de toepassing vragen wat er ontbreekt in het SAML-antwoord.

## <a name="next-steps"></a>Volgende stappen

Nu één aanmelding werkt voor uw toepassing, u [het inrichten en de-provisioning van gebruikers automatiseren naar SaaS-toepassingen](../manage-apps/user-provisioning.md) of [aan de slag gaan met voorwaardelijke toegang.](../conditional-access/app-based-conditional-access.md)
