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
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a754873aeafe8d4e7b48d49647469874ff40f7e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875868"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Snelstartgids: aanmelden instellen voor een app met één pagina met Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) biedt cloudidentiteitsbeheer om uw toepassing, bedrijf en klanten te beschermen. Met Azure AD B2C zijn uw toepassingen in staat om zich met behulp van open-standaardprotocollen te verifiëren bij sociaalnetwerk- en Enterprise-accounts. In deze snelstart gebruikt u een toepassing met één pagina. Deze toepassing wordt gebruikt voor het aanmelden via een id-provider voor sociale netwerken en voor het aanroepen van een met Azure AD B2C beveiligde web-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Sociaal account van Facebook, Google of Microsoft
- Codevoorbeeld van GitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    U [het zip-archief downloaden](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of de repository klonen:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Start de server door de volgende opdrachten uit te voeren vanaf de Node.js-opdrachtprompt:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    De server die is gestart door *server.js* geeft de poort weer waarop deze luistert:

    ```console
    Listening on port 6420...
    ```

1. Blader naar de URL van de toepassing. Bijvoorbeeld `http://localhost:6420`.

    ![Voorbeeld-app voor toepassingen op één pagina die in de browser wordt weergegeven](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Aanmelden met uw account

1. Selecteer **Aanmelden** om het gebruikerstraject te starten.
1. Azure AD B2C presenteert een aanmeldingspagina voor een fictief bedrijf genaamd Fabrikam voor de voorbeeldwebtoepassing. Als u zich wilt aanmelden via een aanbieder van sociale identiteit, selecteert u de knop van de identiteitsprovider die u wilt gebruiken.

    ![Aanmeldings- of aanmeldingspagina met knoppen voor identiteitsprovider](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    U verifieert (logt in) met uw sociale accountgegevens en geeft de toepassing toestemming om informatie uit uw sociale account te lezen. Door toegang te verlenen, kan de toepassing profielgegevens van het sociaalnetwerkaccount ophalen, zoals uw naam en plaats.

1. Voltooi het aanmeldingsproces voor de id-provider.

## <a name="access-a-protected-api-resource"></a>Toegang tot een beveiligde API-resource

Selecteer **Call API** om uw weergavenaam terug te brengen uit de web-API als JSON-object.

![Voorbeeldtoepassing in browser met het web-API-antwoord](./media/quickstart-single-page-app/call-api-spa.png)

De voorbeeldtoepassing met één pagina bevat het toegangstoken van Azure AD in de aanvraag voor de beveiligde web-API-resource.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een voorbeeldtoepassing met één pagina gebruikt om:

- Meld u aan bij een aanbieder van sociale identiteit
- Een Azure AD B2C-gebruikersaccount maken (automatisch gemaakt bij aanmelding)
- Een web-API aanroepen die is beveiligd door Azure AD B2C

Aan de slag met het maken van uw eigen Azure AD B2C-tenant.

> [!div class="nextstepaction"]
> [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)
