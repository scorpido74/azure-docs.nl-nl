---
title: 'Snelstart: aanmelden instellen voor een app van één pagina (SPA)'
titleSuffix: Azure AD B2C
description: Voer in deze Quickstart een voorbeeldtoepassing met één pagina uit waarmee Azure Active Directory B2C wordt gebruikt om aanmelding voor een account te bieden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5d4319f47530a91bcceb9b2dba94c6aa8e4c388
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78183887"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Snelstartgids: aanmelden instellen voor een app met één pagina met Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) biedt cloudidentiteitsbeheer om uw toepassing, bedrijf en klanten te beschermen. Met Azure AD B2C zijn uw toepassingen in staat om zich met behulp van open-standaardprotocollen te verifiëren bij sociaalnetwerk- en Enterprise-accounts. In deze snelstart gebruikt u een toepassing met één pagina. Deze toepassing wordt gebruikt voor het aanmelden via een id-provider voor sociale netwerken en voor het aanroepen van een met Azure AD B2C beveiligde web-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de **ASP.NET en web development** workload
- [Node.js](https://nodejs.org/en/download/)
- Sociaal account van Facebook, Google of Microsoft
- Codevoorbeeld van GitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    U [het zip-archief downloaden](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of de repository klonen:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Start de server door de volgende opdrachten uit te voeren vanaf de Node.js-opdrachtprompt:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js levert het poortnummer waarnaar de server op localhost luistert.

    ```
    Listening on port 6420...
    ```

2. Blader naar de URL van de toepassing. Bijvoorbeeld `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Aanmelden met uw account

1. Klik op **Aanmelden** om de werkstroom te starten.

    ![Voorbeeld-app voor toepassingen op één pagina die in de browser wordt weergegeven](./media/quickstart-single-page-app/sample-app-spa.png)

    In de voorbeeldtoepassing worden verschillende registratiemogelijkheden ondersteund. U kunt bijvoorbeeld een provider voor sociale identiteit gebruiken of een lokale account maken met behulp van een e-mailadres. Gebruik hiervoor een account van een sociale identiteitsprovider van Facebook, Google of Microsoft.

2. Azure AD B2C presenteert een aanmeldingspagina voor een fictief bedrijf genaamd Fabrikam voor de voorbeeldwebtoepassing. Klik op de knop van de id-provider voor sociale netwerken die u wilt gebruiken om u aan te melden via een id-provider voor sociale netwerken.

    ![Aanmeldings- of aanmeldingspagina met knoppen voor identiteitsprovider](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    U verifieert (logt in) met uw sociale accountgegevens en geeft de toepassing toestemming om informatie uit uw sociale account te lezen. Door toegang te verlenen, kan de toepassing profielgegevens van het sociaalnetwerkaccount ophalen, zoals uw naam en plaats.

3. Voltooi het aanmeldingsproces voor de id-provider.

## <a name="access-a-protected-api-resource"></a>Toegang tot een beveiligde API-resource

Klik op **Web-API aanroepen** om de weergavenaam van de web-API-aanroep als JSON-object te retourneren.

![Voorbeeldtoepassing in browser met het web-API-antwoord](./media/quickstart-single-page-app/call-api-spa.png)

De voorbeeldtoepassing met één pagina bevat het toegangstoken van Azure AD in de aanvraag voor de beveiligde web-API-resource.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw Azure AD B2C-tenant gebruiken voor andere snelstarts of zelfstudies voor Azure AD B2C. Als u deze niet meer nodig hebt, kunt u [uw Azure AD B2C-tenant verwijderen](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een voorbeeldtoepassing met één pagina gebruikt om:

* Inloggen met een aangepaste aanmeldingspagina
* Meld u aan bij een aanbieder van sociale identiteit
* Een Azure AD B2C-account maken
* Een web-API aanroepen die is beveiligd door Azure AD B2C

Aan de slag met het maken van uw eigen Azure AD B2C-tenant.

> [!div class="nextstepaction"]
> [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)
