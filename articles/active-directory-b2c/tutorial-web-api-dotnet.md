---
title: 'Zelfstudie: Toegang verlenen tot een ASP.NET web-API'
titleSuffix: Azure AD B2C
description: Zelfstudie over het gebruik van Active Directory B2C om een ASP.NET web-API te beveiligen en aan te roepen vanuit een ASP.NET-webtoepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 23531bd4c53dc2fc4851a1e4718fca0e9c3bfc1c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78187420"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Zelfstudie: Toegang verlenen tot een ASP.NET web-API met Azure Active Directory B2C

In deze zelfstudie ziet u hoe u een beveiligde web-API-bron in Azure Active Directory B2C (Azure AD B2C) aanroepen vanuit een webtoepassing ASP.NET.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voer de stappen en vereisten uit in [Zelfstudie: Verifiëren inschakelen in een webtoepassing met Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

Web-API-resources moeten worden geregistreerd in uw tenant voordat deze in staat zijn om beveiligde resourceaanvragen door clienttoepassingen die een toegangstoken aanbieden, kunnen accepteren en erop kunnen reageren.

Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen**en selecteer **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer **Ja**voor **Web-app/ web-API opnemen**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:44332`.
8. Voer voor **App-id-URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik **op Maken**.
10. Noteer op de eigenschappenpagina de toepassings-id die u gebruikt wanneer u de web-app configureert.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld *webapi1*.
1. Selecteer **onder Uri omleiden** **en**voer een eindpunt in waarin Azure AD B2C alle tokens die uw toepassing aanvraagt, moet retourneren. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:44332`.
1. Selecteer **Registreren**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

* * *

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. In deze zelfstudie gebruikt u bereiken om lees- en schrijfmachtigingen voor de web-API te definiëren.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen geven voor de API. In de vereiste zelfstudie hebt u een webtoepassing in Azure AD B2C gemaakt met de naam *webapp1*. Met deze toepassing kunt u de web-API aanroepen.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw toepassing is geregistreerd voor het aanroepen van de beveiligde web-API. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing te kunnen gebruiken. De toepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om uw Azure AD B2C-tenant te gebruiken. In deze zelfstudie gaat u een voorbeeld-web-API configureren. De voorbeeld-web-API bevindt zich in het project dat u hebt gedownload in de vereiste zelfstudie.

Er bevinden zich twee projecten in de voorbeeldoplossing:

* **TaskWebApp**: een lijst met taken maken en bewerken. Het voorbeeld gebruikt de gebruikersstroom voor **registratie of aanmelding** om gebruikers te registreren of aan te melden.
* **TaskService**: biedt ondersteuning voor het maken, lezen, bijwerken en verwijderen van takenlijstfunctionaliteit. De API wordt beveiligd door Azure AD B2C en wordt aangeroepen door TaskWebApp.

### <a name="configure-the-web-application"></a>De web-app configureren

1. Open de oplossing **B2C-WebAPI-DotNet** in Visual Studio.
1. Open **Web.config**in het **TaskWebApp-project** .
1. Als u de API lokaal wilt uitvoeren, moet u de localhost-instelling gebruiken voor **api:TaskServiceUrl**. Ga als volgt te werk om Web.config te wijzigen:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configureer de URI van de API. Dit is de URI die de web-app gebruikt om de API-aanvraag te maken. Configureer ook de machtigingen die zijn aangevraagd.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open **Web.config**in het **TaakService-project** .
1. Configureer de API om uw tenant te gebruiken.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Stel de client-id in op de toepassings-id van uw geregistreerde web-API-toepassing, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Werk de gebruikersstroominstelling bij met de naam van uw aanmeldings- en aanmeldingsgebruikersstroom, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configureer de scope-instelling op de instellingen die u in de portal hebt gemaakt.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U moet zowel project **TaskWebApp** als **TaskService** uitvoeren.

1. Klik in Solution Explorer met de rechtermuisknop op de oplossing en selecteer vervolgens **Opstartprojecten instellen...**.
1. Selecteer **Meerdere opstartprojecten**.
1. Wijzig de **actie** voor beide projecten in **Start**.
1. Klik op **OK** om de configuratie op te slaan.
1. Druk op **F5** om beide toepassingen uit te voeren. Elke toepassing wordt geopend in een eigen browservenster.
    * `https://localhost:44316/`is de webapplicatie.
    * `https://localhost:44332/` is de web-API.

1. Selecteer in de webtoepassing **aanmelden/ aanmelden** om u aan te melden bij de webtoepassing. Gebruik het account dat u eerder hebt gemaakt.
1. Nadat u zich hebt aangemeld, selecteert u **De lijst to-do** en maakt u een to-do-lijstitem.

Als u een takenlijstitem maakt, verzendt de webtoepassing een aanvraag naar de web-API om het takenlijstitem te maken. Uw beveiligde webtoepassing roept de web-API aan die is beveiligd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelfstudie: identiteitsproviders toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
