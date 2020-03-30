---
title: Native client-apps publiceren - Azure AD | Microsoft Documenten
description: Hiermee u informatie geven over hoe u native client-apps communiceren met Azure AD Application Proxy Connector om veilige externe toegang tot uw on-premises apps te bieden.
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
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159281"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Native clienttoepassingen inschakelen om te communiceren met proxytoepassingen

U Azure Active Directory (Azure AD) Application Proxy gebruiken om web-apps te publiceren, maar het kan ook worden gebruikt om native clienttoepassingen te publiceren die zijn geconfigureerd met de Azure AD-verificatiebibliotheek (ADAL). Native clienttoepassingen verschillen van web-apps omdat ze op een apparaat zijn geïnstalleerd, terwijl web-apps worden geopend via een browser.

Als u native clienttoepassingen wilt ondersteunen, accepteert Application Proxy door Azure AD uitgegeven tokens die in de koptekst worden verzonden. De Application Proxy-service doet de verificatie voor de gebruikers. Deze oplossing gebruikt geen toepassingstokens voor verificatie.

![Relatie tussen eindgebruikers, Azure AD en gepubliceerde toepassingen](./media/application-proxy-configure-native-client-application/richclientflow.png)

Als u native toepassingen wilt publiceren, gebruikt u de Azure AD-verificatiebibliotheek, die zorgt voor verificatie en veel clientomgevingen ondersteunt. Application Proxy past in het [Native Application to Web API-scenario.](../azuread-dev/native-app.md)

In dit artikel vindt u de vier stappen om een native toepassing te publiceren met Application Proxy en de Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Stap 1: Uw proxy-toepassing publiceren

Publiceer uw proxy-toepassing zoals u zou doen met elke andere toepassing en gebruikers toewijzen om toegang te krijgen tot uw toepassing. Zie [Toepassingen publiceren met application proxy voor](application-proxy-add-on-premises-application.md)meer informatie.

## <a name="step-2-register-your-native-application"></a>Stap 2: Registreer uw native applicatie

U moet uw toepassing nu als volgt registreren in Azure AD:

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/). Het **dashboard** voor het **Azure Active Directory-beheercentrum** wordt weergegeven.
1. Selecteer Azure Active **Directory**op de zijbalk . De **overzichtspagina van Azure Active Directory** wordt weergegeven.
1. Selecteer **app-registraties**in de zijbalk van het Azure AD-overzicht . De lijst met alle app-registraties wordt weergegeven.
1. Selecteer **Nieuwe registratie**. De **pagina Een aanvraag registreren** wordt weergegeven.

   ![Een nieuwe app-registratie maken in de Azure-portal](./media/application-proxy-configure-native-client-application/create.png)

1. Geef in de kop **Naam** een door de gebruiker gerichte weergavenaam voor uw toepassing op.
1. Selecteer onder de kop **Ondersteunde accounttypen** een toegangsniveau met behulp van deze richtlijnen:

   - Als u alleen accounts wilt targeten die intern zijn voor uw organisatie, selecteert u **Alleen Accounts in deze organisatiemap**.
   - Als u alleen zakelijke of educatieve klanten wilt targeten, selecteert u **Accounts in een organisatiemap.**
   - Als u de breedste set Microsoft-identiteiten wilt targeten, selecteert u **Accounts in een organisatiemap en persoonlijke Microsoft-accounts.**

1. Selecteer in de kop **URI-omleiding** de optie **Openbare client (mobiel &-bureaublad)** en typ vervolgens de omleidingsURI voor uw toepassing.
1. Selecteer en lees het **Microsoft Platform-beleid**en selecteer **Vervolgens Registreren**. Er wordt een overzichtspagina voor de nieuwe toepassingsregistratie gemaakt en weergegeven.

Zie Toepassingen integreren met Azure Active [Directory](../develop/quickstart-register-app.md)voor meer gedetailleerde informatie over het maken van een nieuwe toepassingsregistratie.

## <a name="step-3-grant-access-to-your-proxy-application"></a>Stap 3: Toegang verlenen tot uw proxy-toepassing

Nu u uw native toepassing hebt geregistreerd, u deze toegang geven tot andere toepassingen in uw directory, in dit geval om toegang te krijgen tot de proxy-toepassing. Ga als bedoeld als u de oorspronkelijke toepassing wilt laten zien aan de proxytoepassing:

1. Selecteer **API-machtigingen**in de zijbalk van de nieuwe pagina met toepassingsregistratie . De pagina **API-machtigingen** voor de nieuwe toepassingsregistratie wordt weergegeven.
1. Selecteer **Een machtiging toevoegen**. De pagina **Api-machtigingen aanvragen** wordt weergegeven.
1. Selecteer onder de **API-instelling selecteren** de optie **API's die mijn organisatie gebruikt**. Er verschijnt een lijst met de toepassingen in uw map die API's blootleggen.
1. Typ in het zoekvak of scrolt om de proxy-toepassing te vinden die u hebt gepubliceerd in [stap 1: Publiceer uw proxy-toepassing](#step-1-publish-your-proxy-application)en selecteer vervolgens de proxytoepassing.
1. Selecteer in het type machtigingen dat **uw toepassing vereist?** Als uw native toepassing toegang moet krijgen tot de API voor proxy-toepassingen als de aangemelde gebruiker, kiest **u Gedelegeerde machtigingen**.
1. Selecteer in de kop **Machtigingen selecteren** de gewenste machtiging en selecteer **Machtigingen toevoegen**. Op de pagina **API-machtigingen** voor uw native toepassing worden nu de proxy-toepassings- en machtigings-API weergegeven die u hebt toegevoegd.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Stap 4: De Active Directory-verificatiebibliotheek bewerken

Bewerk de native toepassingscode in de verificatiecontext van de Active Directory Authentication Library (ADAL) om de volgende tekst op te nemen:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

De vereiste informatie in de voorbeeldcode vindt u als volgt in de Azure AD-portal:

| Informatie vereist | Hoe vind je het in de Azure AD-portal |
| --- | --- |
| \<Tenant-id> | **Directory-id van Azure Active Directory** > **Properties** > **Directory ID** |
| \<Externe url van proxy-app> | **Enterprise-toepassingen** > *uw proxy-toepassingsproxy* > **Application proxy** > **Externe url** |
| \<App-id van de native app> | **Bedrijfstoepassingen** > *uw native application* > **Properties** > Application**ID** |
| \<Uri van de native app-> omleiden | **Azure Active Directory** > **App-registraties** > *uw native toepassing* > **Omleiding URI's** |
| \<Url-> proxy-app-api-> | **Azure Active Directory** > **App registratiet** > *uw API* > voor native application**API-machtigingen** > **/ NAAM VAN MACHTIGINGEN** |

Nadat u de ADAL met deze parameters hebt bewerkt, kunnen uw gebruikers zich verifiëren voor native clienttoepassingen, zelfs wanneer ze zich buiten het bedrijfsnetwerk bevinden.

## <a name="next-steps"></a>Volgende stappen

Zie [Native-apps in Azure Active Directory](../azuread-dev/native-app.md)voor meer informatie over de native toepassingsstroom.

Meer informatie over het instellen [van één aanmelding voor toepassingen in Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
