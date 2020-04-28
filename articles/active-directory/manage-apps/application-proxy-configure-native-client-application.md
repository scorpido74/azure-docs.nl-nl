---
title: Systeem eigen client-apps publiceren-Azure AD | Microsoft Docs
description: In dit artikel wordt beschreven hoe u systeem eigen client-apps kunt gebruiken om te communiceren met de Azure AD-toepassingsproxy-connector om veilige externe toegang tot uw on-premises apps te bieden.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77159281"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Systeem eigen client toepassingen inschakelen voor interactie met proxy toepassingen

U kunt Azure Active Directory (Azure AD)-toepassings proxy gebruiken om web-apps te publiceren, maar dit kan ook worden gebruikt voor het publiceren van systeem eigen client toepassingen die zijn geconfigureerd met de Azure AD-verificatie bibliotheek (ADAL). Systeem eigen client toepassingen verschillen van web-apps omdat ze zijn geïnstalleerd op een apparaat, terwijl web apps via een browser worden geopend.

Voor het ondersteunen van systeem eigen client toepassingen accepteert toepassings proxy Azure AD-uitgegeven tokens die in de header worden verzonden. De toepassings proxy service heeft de verificatie voor de gebruikers. In deze oplossing worden geen toepassings tokens gebruikt voor verificatie.

![Relatie tussen eind gebruikers, Azure AD en gepubliceerde toepassingen](./media/application-proxy-configure-native-client-application/richclientflow.png)

Voor het publiceren van systeem eigen toepassingen gebruikt u de Azure AD-verificatie bibliotheek, die zorgt voor verificatie en veel client omgevingen ondersteunt. Toepassings proxy past in het [scenario van de systeem eigen toepassing tot Web API](../azuread-dev/native-app.md).

Dit artikel begeleidt u stapsgewijs door de vier stappen voor het publiceren van een systeem eigen toepassing met toepassings proxy en de Azure AD-verificatie bibliotheek.

## <a name="step-1-publish-your-proxy-application"></a>Stap 1: uw proxy toepassing publiceren

Publiceer uw proxy toepassing, net zoals elke andere toepassing en wijs gebruikers toe om toegang te krijgen tot uw toepassing. Zie [toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md)voor meer informatie.

## <a name="step-2-register-your-native-application"></a>Stap 2: uw systeem eigen toepassing registreren

U moet uw toepassing nu als volgt registreren in azure AD:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). Het **dash board** voor het **Azure Active Directory-beheer centrum** wordt weer gegeven.
1. Selecteer **Azure Active Directory**in de zijbalk. De pagina overzicht van **Azure Active Directory** wordt weer gegeven.
1. Selecteer **app-registraties**in de terzijde van Azure AD-overzicht. De lijst met alle app-registraties wordt weer gegeven.
1. Selecteer **nieuwe registratie**. De pagina **een toepassing registreren** wordt weer gegeven.

   ![Een nieuwe app-registratie maken in de Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. Geef in de kop **naam** een door de gebruiker gerichte weergave naam op voor uw toepassing.
1. Selecteer onder de kop **ondersteunde account typen** een toegangs niveau met behulp van de volgende richt lijnen:

   - Als u alleen accounts wilt richten die intern zijn voor uw organisatie, selecteert u **alleen accounts in deze organisatie Directory**.
   - Als u alleen zakelijke of educatieve klanten wilt richten, selecteert u **accounts in een organisatorische Directory**.
   - Als u de breedste set met micro soft-identiteiten wilt richten, selecteert u **accounts in een organisatorische map en persoonlijke micro soft-accounts**.

1. Selecteer in de kop **omleidings-URI** de optie **open bare client (Mobile & bureau blad)** en typ vervolgens de omleidings-URI voor uw toepassing.
1. Selecteer en lees het **micro soft-platform beleid**en selecteer vervolgens **registreren**. Er wordt een overzichts pagina voor de nieuwe toepassings registratie gemaakt en weer gegeven.

Zie [toepassingen integreren met Azure Active Directory](../develop/quickstart-register-app.md)voor meer gedetailleerde informatie over het maken van een nieuwe toepassings registratie.

## <a name="step-3-grant-access-to-your-proxy-application"></a>Stap 3: toegang verlenen tot uw proxy toepassing

Nu u uw systeem eigen toepassing hebt geregistreerd, kunt u deze toegang verlenen tot andere toepassingen in uw directory, in dit geval voor toegang tot de proxy toepassing. De systeem eigen toepassing kan worden blootgesteld aan de proxy toepassing:

1. Selecteer in de zijbalk van de pagina nieuwe toepassings registratie de optie **API-machtigingen**. De pagina **API-machtigingen** voor de nieuwe toepassings registratie wordt weer gegeven.
1. Selecteer **een machtiging toevoegen**. De pagina **API-machtigingen voor aanvragen** wordt weer gegeven.
1. Selecteer onder de instelling **een API selecteren** de optie **api's mijn organisatie gebruikt**. Er wordt een lijst weer gegeven met de toepassingen in uw directory die Api's beschikbaar maken.
1. Typ in het zoekvak of blader naar de proxy toepassing die u in [stap 1: uw proxy](#step-1-publish-your-proxy-application)toepassing hebt gepubliceerd en selecteer vervolgens de proxy toepassing.
1. Selecteer in de sectie **Wat is het type machtigingen dat uw toepassing vereist?** het machtigings type. Als uw systeem eigen toepassing toegang moet hebben tot de proxy toepassings-API als de aangemelde gebruiker, kiest u **gedelegeerde machtigingen**.
1. Selecteer in de kop **machtigingen selecteren** de gewenste machtiging en selecteer vervolgens **machtigingen toevoegen**. De pagina **API-machtigingen** voor uw systeem eigen toepassing toont nu de API voor proxy toepassing en de machtiging die u hebt toegevoegd.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Stap 4: de Active Directory Authentication Library bewerken

Bewerk de systeem eigen toepassings code in de verificatie context van de Active Directory Authentication Library (ADAL) om de volgende tekst op te laten:

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

De vereiste informatie in de voorbeeld code vindt u als volgt in de Azure AD-portal:

| Info vereist | Hoe u het kunt vinden in de Azure AD-Portal |
| --- | --- |
| \<Tenant-ID> | **Azure Active Directory** > **Properties** > **Map-id** van Azure Active Directory-eigenschappen |
| \<De externe URL van de proxy-app> | **Bedrijfs toepassingen** > *uw proxy toepassings* > **proxy** > **externe URL** |
| \<App-ID van de systeem eigen app> | **Bedrijfs toepassingen** > *uw systeem eigen* > toepassings**Eigenschappen** > **toepassings-id** |
| \<Omleidings-URI van de systeem eigen app> | **Azure Active Directory** > **App registrations**app-registraties > de**omleidings-uri's** van*de systeem eigen toepassing* >  |
| \<API-URL-> voor proxy-apps | **Azure Active Directory** > **app-registraties****API permissions** > **API / PERMISSIONS NAME** *your native application* > uw eigen API-machtigingen voor toepassings-API voor de naam >  |

Nadat u de ADAL met deze para meters hebt bewerkt, kunnen uw gebruikers zich verifiëren bij native client toepassingen, zelfs wanneer ze zich buiten het bedrijfs netwerk bevinden.

## <a name="next-steps"></a>Volgende stappen

Zie [systeem eigen apps in azure Active Directory](../azuread-dev/native-app.md)voor meer informatie over de systeem eigen toepassings stroom.

Meer informatie over het instellen van [eenmalige aanmelding voor toepassingen in azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
