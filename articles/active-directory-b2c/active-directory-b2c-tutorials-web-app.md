---
title: Zelf studie-verificatie inschakelen in een web-app-Azure Active Directory B2C
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmelding voor een ASP.NET-webtoepassing.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42634aa86f210382adb1ae224c847a92d89109b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103308"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Zelfstudie: Verificatie inschakelen in een webtoepassing met behulp van Azure Active Directory B2C

In deze zelf studie wordt uitgelegd hoe u Azure Active Directory B2C (Azure AD B2C) gebruikt om u aan te melden en gebruikers te registreren in een ASP.NET-webtoepassing. Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De toepassing bijwerken in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Maak gebruikersstromen](tutorial-create-user-flows.md) om gebruikerservaringen in uw toepassing in te schakelen.
* Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de **ASP.net-en Web Development** -werk belasting.

## <a name="update-the-application"></a>De toepassing bijwerken

In de zelfstudie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing in Azure AD B2C toegevoegd. Om te communiceren met het voorbeeld in deze zelfstudie, moet u een omleidings-URI toevoegen aan de toepassing in Azure AD B2C.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL** `https://localhost:44316` toe.
1. Selecteer **Opslaan**.
1. Noteer op de eigenschappenpagina de toepassings-ID die u gebruikt wanneer u de webtoepassing configureert.
1. Selecteer **Sleutels**, selecteer **Sleutel genereren** en selecteer **Opslaan**. Noteer de sleutel die u gebruikt wanneer u de webtoepassing configureert.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. Het voorbeeld maakt gebruik van ASP.NET voor een eenvoudige takenlijst. In het voorbeeld worden [Microsoft OWIN-middlewareonderdelen](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/) gebruikt. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub. Zorg ervoor dat u het voorbeeldbestand uitpakt in een map en dat het aantal tekens van het pad minder is dan 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Deze twee projecten bevinden zich in de voorbeeldoplossing:

* **TaskWebApp**: een lijst met taken maken en bewerken. In het voor beeld wordt de gebruikers stroom **registreren of aanmelden** gebruikt om gebruikers te registreren en aan te melden.
* **TaskService**: biedt ondersteuning voor het maken, lezen, bijwerken en verwijderen van takenlijstfunctionaliteit. De API wordt beveiligd door Azure AD B2C en wordt aangeroepen door TaskWebApp.

U moet het voorbeeld wijzigen om de toepassing te gebruiken die geregistreerd staat in uw tenant, met inbegrip van de toepassings-ID en de sleutel die u eerder hebt genoteerd. U moet de gebruikersstromen die u hebt gemaakt ook configureren. Het voor beeld definieert de configuratie waarden als instellingen in het bestand *Web. config* .

Werk de instellingen in het bestand Web. config bij om met uw gebruikers stroom te werken:

1. Open de oplossing **B2C-WebAPI-DotNet** in Visual Studio.
1. Open in het project **TaskWebApp** het bestand **Web.config**.
    1. Werk de waarde van `ida:Tenant` en `ida:AadInstance` door de naam van de Azure AD B2C-Tenant die u hebt gemaakt. `fabrikamb2c` Vervang`contoso`bijvoorbeeld door.
    1. Vervang de waarde van `ida:ClientId` door de toepassings-id die u hebt vastgelegd.
    1. Vervang de waarde voor `ida:ClientSecret` door de sleutel die u hebt geregistreerd. U moet het client geheim coderen voordat u het toevoegt aan web. config.
    1. Vervang de waarde van `ida:SignUpSignInPolicyId` met `b2c_1_signupsignin1`.
    1. Vervang de waarde van `ida:EditProfilePolicyId` met `b2c_1_profileediting1`.
    1. Vervang de waarde van `ida:ResetPasswordPolicyId` met `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Klik in Solution Explorer met de rechtermuisknop op het project **TaskWebApp** en klik op **Instellen als opstartproject**.
1. Druk op **F5**. De standaardbrowser wordt gestart met het adres van de lokale site `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Selecteer **Aanmelden/aanmelden** om u aan te melden als een gebruiker van de toepassing. De gebruikersstroom **b2c_1_signupsignin1** wordt gebruikt.
1. Azure AD B2C toont een aanmeldingspagina met een koppeling voor registratie. Aangezien u nog geen account hebt, klikt u op **Nu registreren**. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.
1. Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![De registratie pagina die wordt weer gegeven als onderdeel van de werk stroom voor aanmelden/registreren](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Selecteer **maken** om een lokaal account te maken in de Azure AD B2C Tenant.

De gebruiker van de toepassing kan nu hun e-mail adres gebruiken om zich aan te melden en de webtoepassing te gebruiken.

De functie **taken lijst** werkt echter pas nadat u de volgende zelf studie in de reeks hebt voltooid, [zelf studie: Gebruik Azure AD B2C om een ASP.NET-web-](active-directory-b2c-tutorials-web-api.md)API te beveiligen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Bijwerken van de toepassing in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Ga nu verder met de volgende zelf studie om de functie **taken lijst** van de webtoepassing in te scha kelen. Hierin registreert u een web-API-toepassing in uw eigen Azure AD B2C Tenant en wijzigt u vervolgens het code voorbeeld om uw Tenant voor API-verificatie te gebruiken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Active Directory B2C gebruiken om een ASP.NET-Web-API te beveiligen >](active-directory-b2c-tutorials-web-api.md)
