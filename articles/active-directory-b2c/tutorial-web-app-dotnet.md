---
title: 'Zelfstudie: Verificatie inschakelen in een webtoepassing'
titleSuffix: Azure AD B2C
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmelding voor een ASP.NET-webtoepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183313"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Zelfstudie: Verificatie inschakelen in een webtoepassing met Azure Active Directory B2C

In deze zelfstudie ziet u hoe u Azure Active Directory B2C (Azure AD B2C) gebruiken om gebruikers aan te melden en aan te melden in een webtoepassing ASP.NET. Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bijwerken van de toepassing in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Maak gebruikersstromen](tutorial-create-user-flows.md) om gebruikerservaringen in uw toepassing in te schakelen.
* Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de **ASP.NET en webdevelopment** workload.

## <a name="update-the-application-registration"></a>De registratie van de toepassing bijwerken

In de zelfstudie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing geregistreerd in Azure AD B2C. Als u de communicatie met het voorbeeld in deze zelfstudie wilt inschakelen, moet u een omleidingsURI toevoegen en een clientgeheim (sleutel) maken voor de geregistreerde toepassing.

### <a name="add-a-redirect-uri-reply-url"></a>Een omleidingsURI toevoegen (antwoord-URL)

U de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)** gebruiken om de toepassing bij te werken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`https://localhost:44316` toe.
1. Selecteer **Opslaan**.
1. Noteer op de pagina Eigenschappen de toepassings-id voor gebruik in een latere stap wanneer u de webtoepassing configureert.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)**, selecteer het tabblad **Bezeten toepassingen** en selecteer vervolgens de *webapp1-toepassing.*
1. Selecteer **Verificatie**en selecteer **Vervolgens Probeer de nieuwe ervaring uit** (indien weergegeven).
1. Selecteer **onder Web**de koppeling URI **toevoegen,** voer `https://localhost:44316`enter en selecteer **Opslaan**.
1. Selecteer **Overzicht**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap wanneer u de webtoepassing configureert.

* * *

### <a name="create-a-client-secret"></a>Een klantgeheim maken

Maak vervolgens een klantgeheim voor de geregistreerde webtoepassing. Het voorbeeld van de webtoepassingscode gebruikt dit om zijn identiteit te bewijzen bij het aanvragen van tokens.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. Het voorbeeld maakt gebruik van ASP.NET voor een eenvoudige takenlijst. In het voorbeeld worden [Microsoft OWIN-middlewareonderdelen](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/) gebruikt. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub. Zorg ervoor dat u het voorbeeldbestand uitpakt in een map en dat het aantal tekens van het pad minder is dan 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Deze twee projecten bevinden zich in de voorbeeldoplossing:

* **TaskWebApp**: een lijst met taken maken en bewerken. Het voorbeeld gebruikt de **aanmeldings- of aanmeldingsstroom** om gebruikers aan te melden en in te loggen.
* **TaskService**: biedt ondersteuning voor het maken, lezen, bijwerken en verwijderen van takenlijstfunctionaliteit. De API wordt beveiligd door Azure AD B2C en wordt aangeroepen door TaskWebApp.

U moet het voorbeeld wijzigen om de toepassing te gebruiken die geregistreerd staat in uw tenant, met inbegrip van de toepassings-ID en de sleutel die u eerder hebt genoteerd. U moet de gebruikersstromen die u hebt gemaakt ook configureren. In het voorbeeld worden de configuratiewaarden gedefinieerd als instellingen in het *web.config-bestand.*

Werk de instellingen in het web.config-bestand bij om met uw gebruikersstroom te werken:

1. Open de oplossing **B2C-WebAPI-DotNet** in Visual Studio.
1. Open in het project **TaskWebApp** het bestand **Web.config**.
    1. Werk de `ida:Tenant` waarde `ida:AadInstance` bij van en met de naam van de Azure AD B2C-tenant die u hebt gemaakt. Vervang bijvoorbeeld `fabrikamb2c` door `contoso`.
    1. Vervang de `ida:ClientId` waarde van de toepassings-id die u hebt geregistreerd.
    1. Vervang de waarde voor `ida:ClientSecret` door de sleutel die u hebt geregistreerd. Als het clientgeheim vooraf gedefinieerde XML-entiteiten bevat,`<`bijvoorbeeld minder`>`dan (`&`), groter dan`"`( ), ampersand ( ) of dubbele aanhalingsteken ( ), moet u aan die tekens ontsnappen door XML-codering van het clientgeheim voordat u het toevoegt aan uw Web.config.
    1. Vervang de `ida:SignUpSignInPolicyId` waarde `b2c_1_signupsignin1`van door .
    1. Vervang de `ida:EditProfilePolicyId` waarde `b2c_1_profileediting1`van door .
    1. Vervang de `ida:ResetPasswordPolicyId` waarde `b2c_1_passwordreset1`van door .

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Klik in Solution Explorer met de rechtermuisknop op het project **TaskWebApp** en klik op **Instellen als opstartproject**.
1. Druk op **F5**. De standaardbrowser wordt gestart met het adres van de lokale site `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Selecteer **Aanmelden / Aanmelden** om u aan te melden als gebruiker van de toepassing. De gebruikersstroom **b2c_1_signupsignin1** wordt gebruikt.
1. Azure AD B2C toont een aanmeldingspagina met een koppeling voor registratie. Aangezien u nog geen account hebt, klikt u op **Nu registreren**. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.
1. Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![Aanmeldingspagina weergegeven als onderdeel van de aanmeldings-/aanmeldingswerkstroom](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Selecteer **Maken** om een lokaal account te maken in de Azure AD B2C-tenant.

De gebruiker van de toepassing kan nu zijn e-mailadres gebruiken om in te loggen en de webapplicatie te gebruiken.

De functie **Taaklijst** werkt echter pas als u de volgende zelfstudie in de reeks, [Zelfstudie: Azure AD B2C,](tutorial-web-api-dotnet.md)hebt voltooid om een ASP.NET web-API te beschermen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Bijwerken van de toepassing in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Ga nu naar de volgende zelfstudie om de functie **To-Do List** van de webtoepassing in te schakelen. Hierin registreert u een web-API-toepassing in uw eigen Azure AD B2C-tenant en wijzigt u vervolgens het codevoorbeeld om uw tenant te gebruiken voor API-verificatie.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Active Directory B2C gebruiken om een ASP.NET web-API->te beschermen](tutorial-web-api-dotnet.md)
