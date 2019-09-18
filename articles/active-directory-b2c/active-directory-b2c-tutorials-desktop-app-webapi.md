---
title: 'Zelfstudie: toegang verlenen aan een web-API van Node.js vanuit een desktop-app met behulp van Azure Active Directory B2C | Microsoft Docs'
description: Zelfstudie over het gebruik van Active Directory B2C om een Node.js web-API te beveiligen en aan te roepen vanuit een .NET-desktop-app.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 75469d4522cea2914e0f69d5aa1850e468cb0d50
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064840"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Zelfstudie: Toegang verlenen aan een web-API van Node.js vanuit een desktop-app met behulp van Azure Active Directory B2C

In deze zelf studie leert u hoe u een Azure Active Directory B2C (Azure AD B2C) beveiligde node. js-Web-API-resource aanroept vanuit een Windows Presentation Foundation-desktop-app (WPF).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen en de vereisten in [Zelfstudie: Verificatie van desktop-apps inschakelen met accounts met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

Web-API-resources moeten worden geregistreerd in uw tenant voordat deze in staat zijn om beveiligde resourceaanvragen door clienttoepassingen die een toegangstoken aanbieden, kunnen accepteren en erop kunnen reageren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan** **Ja**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:5000`.
8. Voer voor **App ID URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik op **Create**.
10. Noteer op de eigenschappenpagina de toepassings-ID die u gebruikt wanneer u de webtoepassing configureert.

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie gaat u lees- en schrijfmachtigingen definiëren voor de web-API.

1. Selecteer **Toepassingen** en vervolgens *webapi1*.
2. Selecteer **Gepubliceerde bereiken**.
3. Voer voor **Bereik** `Hello.Read` in en voor beschrijving `Read access to hello`.
4. Voer voor **Bereik** `Hello.Write` in en voor beschrijving `Write access to hello`.
5. Klik op **Opslaan**.

De gepubliceerde bereiken kunnen worden gebruikt om een client-appmachtiging te verlenen aan de web-API.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen geven voor de API. In de vereiste zelfstudie hebt u een webtoepassing in Azure AD B2C gemaakt met de naam *app1*. Met deze toepassing kunt u de web-API aanroepen.

1. Selecteer **Toepassingen** en vervolgens *nativeapp1*.
2. Selecteer **API-toegang** en vervolgens **Toevoegen**.
3. Selecteer *webapi1* in de vervolgkeuzelijst **API selecteren**.
4. Selecteer in de vervolgkeuzelijst **Bereiken selecteren** de bereiken **Hello.Read** en **Hello.Write** die u eerder hebt gedefinieerd.
5. Klik op **OK**.

Een gebruiker voert een verificatie uit met Azure AD B2C om de WPF-bureaubladtoepassing te kunnen gebruiken. De bureaubladtoepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Voorbeeld configureren

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om uw Azure AD B2C-tenant te gebruiken. In deze zelfstudie gaat u een Node.js web-app-voorbeeld configureren dat u kunt downloaden vanuit GitHub.

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Het Node.js web-API-voorbeeld maakt gebruik van de bibliotheek Passport.js zodat Azure AD B2C aanroepen naar de API kan beveiligen.

1. Open het `index.js`-bestand.
2. Configureer het voorbeeld met de registratiegegevens voor Azure AD B2C-tenant. Wijzig de volgende regels code:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Start een opdrachtprompt voor Node.js.
2. Ga naar de map met het Node.js-voorbeeld. Bijvoorbeeld `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Voer de volgende opdrachten uit:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>De desktop-app uitvoeren

1. Open de oplossing **active directory-b2c wpf** in Visual Studio.
2. Druk op **F5** om de desktop-app uit te voeren.
3. Meld u aan met het e-mailadres en het wachtwoord dat wordt gebruikt in de zelfstudie [Verificatie van gebruikers met Azure Active Directory B2C in een desktop-app](active-directory-b2c-tutorials-desktop-app.md).
4. Klik op de knop **API aanroepen**.

De desktop-app dient een aanvraag in bij de web-API en haalt van een antwoord op met de weergavenaam van de aangemelde gebruiker. Uw beveiligde web-app roept de beveiligde web-API in uw Azure AD B2C-tenant aan.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
