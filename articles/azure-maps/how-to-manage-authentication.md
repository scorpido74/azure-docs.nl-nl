---
title: Verificatie beheren | Microsoft Azure kaarten
description: U kunt de Azure Portal gebruiken om verificatie te beheren in Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989197"
---
# <a name="manage-authentication-in-azure-maps"></a>Verificatie in Azure Maps beheren

Nadat u een Azure Maps-account hebt gemaakt, worden er een client-ID en-sleutels gemaakt ter ondersteuning van Azure Active Directory (Azure AD) en gedeelde sleutel verificatie.

## <a name="view-authentication-details"></a>Verificatie gegevens weer geven

Nadat het Azure Maps-account is gemaakt, worden de primaire en secundaire sleutels gegenereerd. Het is raadzaam om de primaire sleutel als abonnements sleutel te gebruiken wanneer u Azure Maps aanroept met behulp van [gedeelde sleutel verificatie](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Secundaire sleutel kan worden gebruikt in scenario's zoals het wijzigen van de toetstoewijzingen. Zie [verificatie met Azure Maps](https://aka.ms/amauth)voor meer informatie.

U kunt uw verificatie gegevens weer geven op de Azure Portal. Ga naar uw account en selecteer **verificatie** in het menu **instellingen** .

![Verificatie gegevens](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>Azure AD-app registratie configureren

1. Selecteer **Azure Active Directory** in de lijst met Azure-services in azure Portal.  Selecteer **app-registraties** en klik op **nieuwe registratie**.  Omruil. Voer een **naam**in, kies het **type ondersteunings account**en klik op **registreren**.  Als u al een geregistreerde app hebt, gaat u verder met stap 2. 

    ![App-registratie](./media/how-to-manage-authentication/app-registration.png)

    ![Details van app-registratie](./media/how-to-manage-authentication/app-create.png)

2. Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing onder **app-registraties**. Selecteer vervolgens **API-machtigingen**en selecteer vervolgens **een machtiging toevoegen**. Zoek en selecteer **Azure Maps** onder **api's mijn organisatie gebruikt**.

    ![API-machtigingen voor apps](./media/how-to-manage-authentication/app-permissions.png)

3. Controleer de **toegangs Azure Maps** en klik vervolgens op **machtigingen toevoegen**.

    ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

4. Voltooi stap a of b, afhankelijk van uw verificatie methode. 

    1. Als uw toepassing gebruikmaakt van verificatie op basis van gebruikers tokens met de Azure Maps Web-SDK, schakelt u `oauth2AllowImplicitFlow`in. Als u `oauth2AllowImplicitFlow`wilt inschakelen, stelt u deze in op True in het manifest gedeelte van de registratie van uw app. 
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    2. Als uw toepassing server/toepassings verificatie gebruikt, gaat u naar de Blade **certificaten & geheimen** op de pagina voor de registratie van uw app en maakt u een wacht woord door te klikken op **Nieuw client geheim** of een certificaat met een open bare sleutel naar de registratie van de app te uploaden. Als u een wacht woord maakt nadat u op **toevoegen**hebt geklikt, kopieert u het wacht woord voor later en slaat u het veilig op. U gebruikt dit wacht woord voor het verkrijgen van tokens van Azure AD.

       ![App-sleutels](./media/how-to-manage-authentication/app-keys.png)

       ![Sleutel toevoegen](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Op rollen gebaseerd toegangs beheer (RBAC) toekennen aan Azure Maps

Nadat u een Azure Maps-account hebt gekoppeld aan uw Azure AD-Tenant, kunt u toegangs beheer verlenen. U verleent toegangs beheer door een gebruiker, groep of toepassing toe te wijzen aan een of meer Azure Maps Access Control-rollen.

1. Ga naar uw **Azure Maps-account**. Selecteer **toegangs beheer (IAM)** en selecteer vervolgens **functie toewijzing**.

    ![RBAC toekennen](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Selecteer in het venster **roltoewijzing** onder **rol**de optie **Azure Maps datum lezer (preview)** . Onder **toegang toewijzen kunt u** **Azure AD-gebruiker,-groep of-Service-principe**selecteren. Selecteer de gebruiker of de toepassing. Selecteer **Opslaan**.

    ![Roltoewijzing toevoegen](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Beschik bare Azure Maps RBAC-rollen weer geven

Als u RBAC-rollen (Role-based Access Control) wilt weer geven die beschikbaar zijn voor Azure Maps, gaat u naar **toegangs beheer (IAM)** , selecteert u **rollen**en zoekt u vervolgens naar rollen die beginnen met **Azure Maps**. Deze rollen zijn de rollen, waaraan u toegang kunt verlenen.

![Beschik bare rollen weer geven](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC weer geven

RBAC biedt gedetailleerd toegangs beheer.

Ga naar **Access Control (IAM)** , selecteer **roltoewijzingen**en filter vervolgens op **Azure Maps**om gebruikers en apps weer te geven die zijn toegewezen RBAC voor Azure Maps.

![Gebruikers en apps weer geven die RBAC hebben verleend](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokens aanvragen voor Azure Maps

Nadat u uw app hebt geregistreerd en aan Azure Maps hebt gekoppeld, kunt u toegangs tokens aanvragen.

* Als uw toepassing gebruikmaakt van verificatie op basis van gebruikers tokens met de Azure Maps Web-SDK, moet u uw HTML-pagina configureren met de Azure Maps-client-ID en de Azure AD-App-ID.

* Als uw toepassing server/toepassings verificatie gebruikt, moet u een token aanvragen bij Azure AD token endpoint `https://login.microsoftonline.com` met de Azure AD-Resource-ID `https://atlas.microsoft.com/`, de Azure Maps client-ID, de Azure AD-App-ID en het Azure AD-App-registratie wachtwoord of-certificaat.

| Azure-omgeving   | Azure AD-token eindpunt | Azure-Resource-ID |
| --------------------|-------------------------|-------------------|
| Open bare Azure        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Zie [verificatie scenario's voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)voor meer informatie over het aanvragen van toegangs tokens van Azure AD, voor gebruikers en service-principals.


## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD en Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)voor meer informatie over Azure AD-verificatie en de Azure Maps Web-SDK.

Meer informatie over het weer geven van metrische gegevens over het API-gebruik voor uw Azure Maps-account:
> [!div class="nextstepaction"] 
> [Metrische gegevens over gebruik weer geven](how-to-view-api-usage.md)

Zie voor een lijst met voor beelden die laten zien hoe u Azure Active Directory (AAD) integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Voor beelden van Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
