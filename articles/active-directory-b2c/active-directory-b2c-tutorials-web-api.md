---
title: 'Zelf studie: toegang verlenen tot een ASP.NET-Web-API-Azure Active Directory B2C'
description: Zelfstudie over het gebruik van Active Directory B2C om een ASP.NET web-API te beveiligen en aan te roepen vanuit een ASP.NET-webtoepassing.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 87d46fad1c0a5494910a8218c4e40994fc140386
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103402"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Zelfstudie: Toegang verlenen aan een web-API van ASP.NET met behulp van Azure Active Directory B2C

In deze zelf studie wordt uitgelegd hoe u een beveiligde web-API-resource aanroept in Azure Active Directory B2C (Azure AD B2C) vanuit een ASP.NET-webtoepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen en de vereisten in de [zelfstudie: Verificatie inschakelen in een webtoepassing met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

Web-API-resources moeten worden geregistreerd in uw tenant voordat deze in staat zijn om beveiligde resourceaanvragen door clienttoepassingen die een toegangstoken aanbieden, kunnen accepteren en erop kunnen reageren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan** **Ja**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:44332`.
8. Voer voor **App ID URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik op **Create**.
10. Noteer op de eigenschappenpagina de toepassings-ID die u gebruikt wanneer u de webtoepassing configureert.

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. In deze zelfstudie gebruikt u bereiken om lees- en schrijfmachtigingen voor de web-API te definiëren.

1. Selecteer **Toepassingen** en vervolgens *webapi1*.
2. Selecteer **Gepubliceerde bereiken**.
3. Voer voor **Bereik** `Hello.Read` in en voor beschrijving `Read access to hello`.
4. Voer voor **Bereik** `Hello.Write` in en voor beschrijving `Write access to hello`.
5. Klik op **Opslaan**.

De gepubliceerde bereiken kunnen worden gebruikt om een clienttoepassingstoestemming te verlenen aan de web-API.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen verlenen tot de API. In de vereiste zelfstudie hebt u een webtoepassing in Azure AD B2C gemaakt met de naam *webapp1*. Met deze toepassing kunt u de web-API aanroepen.

1. Selecteer **Toepassingen** en vervolgens *webapp1*.
2. Selecteer **API-toegang**, en selecteer vervolgens **Toevoegen**.
3. Selecteer *webapi1* in de vervolgkeuzelijst **API selecteren**.
4. Selecteer in de vervolgkeuzelijst **Bereiken selecteren** de bereiken **Hello.Read** en **Hello.Write** die u eerder hebt gedefinieerd.
5. Klik op **OK**.

Uw toepassing is geregistreerd voor het aanroepen van de beveiligde web-API. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing te kunnen gebruiken. De toepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om uw Azure AD B2C-tenant te gebruiken. In deze zelfstudie gaat u een voorbeeld-web-API configureren. De voorbeeld-web-API bevindt zich in het project dat u hebt gedownload in de vereiste zelfstudie.

Er bevinden zich twee projecten in de voorbeeldoplossing:

Deze twee projecten bevinden zich in de voorbeeldoplossing:

* **TaskWebApp**: een lijst met taken maken en bewerken. Het voorbeeld gebruikt de gebruikersstroom voor **registratie of aanmelding** om gebruikers te registreren of aan te melden.
* **TaskService**: biedt ondersteuning voor het maken, lezen, bijwerken en verwijderen van takenlijstfunctionaliteit. De API wordt beveiligd door Azure AD B2C en wordt aangeroepen door TaskWebApp.

### <a name="configure-the-web-application"></a>De web-app configureren

1. Open de oplossing **B2C-WebAPI-DotNet** in Visual Studio.
1. Open **Web. config**in het project **project taskwebapp** .
1. Als u de API lokaal wilt uitvoeren, moet u de localhost-instelling gebruiken voor **api:TaskServiceUrl**. Ga als volgt te werk om Web.config te wijzigen:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configureer de URI van de API. Dit is de URI die de web-app gebruikt om de API-aanvraag te maken. Configureer ook de machtigingen die zijn aangevraagd.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open **Web. config**in het project **TaskService** .
1. Configureer de API om uw tenant te gebruiken.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Stel de client-ID in op de toepassings-ID van uw geregistreerde Web-API-toepassing, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Werk de gebruikers stroom instelling bij met de naam van uw registratie-en aanmeldings gebruikers stroom, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configureer de instellingen voor bereiken zodat deze overeenkomen met de scopes die u in de portal hebt gemaakt.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U moet zowel project **TaskWebApp** als **TaskService** uitvoeren.

1. Klik in Solution Explorer met de rechtermuisknop op de oplossing en selecteer vervolgens **Opstartprojecten instellen...** .
1. Selecteer **Meerdere opstartprojecten**.
1. Wijzig de **actie** voor beide projecten in **Start**.
1. Klik op **OK** om de configuratie op te slaan.
1. Druk op **F5** om beide toepassingen uit te voeren. Elke toepassing wordt in een eigen browser venster geopend.
    * `https://localhost:44316/`is de webtoepassing.
    * `https://localhost:44332/` is de web-API.

1. Selecteer in de webtoepassing **Aanmelden/aanmelden** om u aan te melden bij de webtoepassing. Gebruik het account dat u eerder hebt gemaakt.
1. Nadat u zich hebt aangemeld, selecteert **u taken lijst** en maakt u een taken lijst item.

Als u een takenlijstitem maakt, verzendt de webtoepassing een aanvraag naar de web-API om het takenlijstitem te maken. Uw beveiligde webtoepassing roept de Web-API aan die wordt beveiligd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
