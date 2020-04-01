---
title: Azure AD-verificatie configureren
description: Meer informatie over het configureren van Azure Active Directory-verificatie als identiteitsprovider voor uw App Service- of Azure-functie-app.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 4b42f0966288e4ee72b689ddce6313a41e91f13e
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438036"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Uw App-service- of Azure-functie-app configureren om Azure AD-aanmelding te gebruiken

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel ziet u hoe u Azure App Service of Azure-functies configureert om Azure Active Directory (Azure AD) als verificatieprovider te gebruiken.

> [!NOTE]
> Op dit moment wordt [Azure Active Directory v2.0](../active-directory/develop/v2-overview.md) (inclusief [MSAL)](../active-directory/develop/msal-overview.md)niet ondersteund voor Azure App Service en Azure Functions. Kijk terug voor updates.
>

Volg deze aanbevolen procedures bij het instellen van uw app en verificatie:

- Geef elke App Service-app zijn eigen machtigingen en toestemming.
- Configureer elke App Service-app met een eigen registratie.
- Vermijd het delen van machtigingen tussen omgevingen door afzonderlijke app-registraties te gebruiken voor afzonderlijke implementatiesleuven. Bij het testen van nieuwe code kan deze praktijk helpen voorkomen dat problemen van invloed zijn op de productie-app.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configureren met expresinstellingen

> [!NOTE]
> De **Express-optie** is niet beschikbaar voor overheidsclouds. 

1. Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app.
2. Selecteer verificatie / **autorisatie** > **aan**de linkerkant van de navigatie aan de linkerkant .
3. Selecteer **Azure Active Directory** > **Express**.

   Als u in plaats daarvan een bestaande app-registratie wilt kiezen:

   1. Kies **Bestaande AD-app**selecteren en klik vervolgens op **Azure AD-app**.
   2. Kies een bestaande app-registratie en klik op **OK**.

3. Selecteer **OK** om de App Service-app te registreren in Azure Active Directory. Er wordt een nieuwe app-registratie gemaakt.
   
    ![Expresinstellingen in Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Optioneel) App Service biedt standaard verificatie, maar beperkt de geautoriseerde toegang tot uw site-inhoud en API's niet. U moet gebruikers autoriseren in uw app-code. Als u de toegang tot apps alleen wilt beperken tot gebruikers die zijn geverifieerd door Azure Active Directory, stelt u **Actie in die moet worden uitgevoerd wanneer het verzoek niet is geverifieerd** om zich aan te melden bij Azure Active **Directory.** Wanneer u deze functionaliteit instelt, moet uw app worden geverifieerd. Het leidt ook alle niet-geverifieerde naar Azure Active Directory voor verificatie.

    > [!CAUTION]
    > Het beperken van de toegang op deze manier is van toepassing op alle oproepen naar uw app, wat mogelijk niet wenselijk is voor apps met een openbaar beschikbare startpagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kan **het toestaan van anonieme verzoeken (geen actie)** de voorkeur hebben, waarbij de app zelf handmatig begint met inloggen. Zie [Verificatiestroom](overview-authentication-authorization.md#authentication-flow)voor meer informatie .
5. Selecteer **Opslaan**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configureren met geavanceerde instellingen

U app-instellingen handmatig configureren als u een app-registratie wilt gebruiken vanuit een andere Azure AD-tenant. Ga als volgt te werk om deze aangepaste configuratie te voltooien:

1. Maak een registratie in Azure AD.
2. Geef een aantal registratiegegevens op aan App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Een app-registratie maken in Azure AD voor uw App Service-app

U hebt de volgende informatie nodig wanneer u uw App Service-app configureert:

- Client-id
- Tenant-id
- Clientgeheim (optioneel)
- Uri van toepassings-id

Voer de volgende stappen uit:

1. Meld u aan bij de [Azure-portal,]zoek naar en selecteer **App Services**en selecteer vervolgens uw app. Let op de **URL**van uw app . U gebruikt het om de registratie van uw Azure Active Directory-app te configureren.
1. Selecteer **Azure Active Directory** > **App-registraties** > **Nieuwe registratie**.
1. Voer **op de** pagina Een aanvraag registreren een **naam** in voor uw app-registratie.
1. Selecteer **web** en type `<app-url>/.auth/login/aad/callback`in Redirect **URI**. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Selecteer **Maken**.
1. Nadat de app-registratie is gemaakt, kopieert u de **id van toepassing (client)** en de **id directory (tenant)** voor later.
1. Selecteer **Verificatie**. Schakel **onder Impliciete toekenning** **ID-tokens** in om Aanmeldingen van OpenID Connect-gebruikers van App Service toe te staan.
1. (Optioneel) Selecteer **Branding**. Voer in **de URL**van de startpagina de URL van uw App Service-app in en selecteer **Opslaan**.
1. Selecteer **Een API-set** > **blootleggen**. Plak de URL van uw App Service-app en selecteer **Opslaan**.

   > [!NOTE]
   > Deze waarde is de **Application ID URI** van de app-registratie. Als uw web-app toegang tot een API in de cloud vereist, hebt u de **Application ID URI** van de web-app nodig wanneer u de cloud App Service-bron configureert. U dit bijvoorbeeld gebruiken als u wilt dat de cloudservice expliciet toegang verleent tot de web-app.

1. Selecteer **Een bereik toevoegen**.
   1. Voer **in de naam Scope** *user_impersonation*in .
   1. Voer in de tekstvakken de naam en beschrijving van het toestemmingsbereik in die gebruikers op de toestemmingspagina moeten zien. Voer bijvoorbeeld *Toegang tot mijn app*in . 
   1. Selecteer **Bereik toevoegen**.
1. (Optioneel) Als u een clientgeheim wilt maken, selecteert u **Certificaten & geheimen** > **Nieuwe clientgeheime** > **toevoegen**. Kopieer de geheime waarde van de client die op de pagina wordt weergegeven. Het zal niet meer getoond worden.
1. (Optioneel) Als u meerdere **antwoord-URL's wilt toevoegen,** selecteert u **Verificatie**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Azure Active Directory inschakelen in uw App Service-app

1. Zoek in de [Azure-portal]naar en selecteer **App Services**en selecteer vervolgens uw app. 
1. Selecteer in het linkerdeelvenster onder **Instellingen**de optie **Verificatie / Autorisatie** > **aan**.
1. (Optioneel) Standaard biedt App Service-verificatie niet-geverifieerde toegang tot uw app. Als u gebruikersverificatie wilt afdwingen, stelt u **Actie in om actie uit te voeren wanneer het verzoek niet is geverifieerd** om zich aan te melden bij Azure Active **Directory**.
1. Selecteer Azure **Active Directory**onder **Verificatieproviders**.
1. Selecteer in **de beheermodus** **Geavanceerde** verificatie van appservice en configureer volgens de volgende tabel:

    |Veld|Beschrijving|
    |-|-|
    |Client-id| Gebruik de **toepassings-id (client)** van de app-registratie. |
    |Url van uitgever| * \<Tenant-id-id* `https://login.microsoftonline.com/<tenant-id>`>gebruiken en vervangen door de **directory -id (tenant)** van de app-registratie. Deze waarde wordt gebruikt om gebruikers om te leiden naar de juiste Azure AD-tenant en om de juiste metagegevens te downloaden om bijvoorbeeld de juiste tokenondertekeningssleutels en claimwaarde voor tokenuitgevende instellingen te bepalen. |
    |Clientgeheim (optioneel)| Gebruik het clientgeheim dat u hebt gegenereerd in de app-registratie.|
    |Toegestane tokendoelgroepen| Als dit een cloud- of server-app is en u verificatietokens van een web-app wilt toestaan, voegt u hier de **Toepassings-ID URI** van de web-app toe. De geconfigureerde **client-id** wordt *altijd* impliciet beschouwd als een toegestane doelgroep. |

2. Selecteer **OK**en selecteer **Opslaan**.

U Azure Active Directory nu gebruiken voor verificatie in uw App Service-app.

## <a name="configure-a-native-client-application"></a>Een native clienttoepassing configureren

U native clients registreren om verificatie toe te staan voor web-API's die in uw app worden gehost met behulp van een clientbibliotheek, zoals de **Active Directory Authentication Library.**

1. Selecteer active > **directory-app-registraties** > in de [Azure-portal]**nieuwe registratie**. **Active Directory**
1. Voer **op de** pagina Een aanvraag registreren een **naam** in voor uw app-registratie.
1. Selecteer **in Redirect URI**de optie Openbare client `<app-url>/.auth/login/aad/callback` **(mobiel & desktop)** en typ de URL . Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Voor een Microsoft Store-toepassing gebruikt u het [pakket SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) als uri- in plaats daarvan.
1. Selecteer **Maken**.
1. Nadat de app-registratie is gemaakt, kopieert u de waarde van de id van **toepassing (client**).
1. **API-machtigingen** > **toevoegen Voeg een machtiging** > **Mijn API's toe**.
1. Selecteer de app-registratie die u eerder hebt gemaakt voor uw App Service-app. Als u de app-registratie niet ziet, controleert u of u het **user_impersonation-bereik** hebt toegevoegd in [Een app-registratie maken in Azure AD voor uw App Service-app](#register).
1. Selecteer **user_impersonation**en selecteer **Machtigingen toevoegen**.

U hebt nu een native clienttoepassing geconfigureerd die namens een gebruiker toegang heeft tot uw App Service-app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
