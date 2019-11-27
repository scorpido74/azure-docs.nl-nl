---
title: Verificatie beheren in Azure Maps | Microsoft Docs
description: U kunt de Azure Portal gebruiken voor het beheren van verificatie in Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 057bd18c50d7074e8a88b8273bec766a306a3776
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484358"
---
# <a name="manage-authentication-in-azure-maps"></a>Verificatie in Azure Maps beheren

Nadat u een Azure Maps-account hebt gemaakt, worden er een client-ID en-sleutels gemaakt ter ondersteuning van Azure Active Directory (Azure AD) of de verificatie van de gedeelde sleutel.

## <a name="view-authentication-details"></a>Verificatie gegevens weer geven

U kunt uw verificatie gegevens weer geven op de Azure Portal. Ga naar uw account en selecteer **verificatie** in het menu **instellingen** .

![Verificatie gegevens](./media/how-to-manage-authentication/how-to-view-auth.png)

 Zie [verificatie met Azure Maps](https://aka.ms/amauth)voor meer informatie.


## <a name="set-up-azure-ad-app-registration"></a>Azure AD-App-registratie instellen

Nadat u een Azure Maps-account hebt gemaakt, moet u een koppeling tot stand brengen tussen uw Azure AD-Tenant en de Azure Maps-resource.

1. Ga naar de Azure AD-Blade en maak een app-registratie. Geef een naam op voor de registratie. Geef in het vak **aanmeld-URL** de start pagina van de web-app/API (bijvoorbeeld https:\//localhost/) op. Als u al een geregistreerde app hebt, gaat u naar stap 2.

    ![App-registratie](./media/how-to-manage-authentication/app-registration.png)

    ![Details van app-registratie](./media/how-to-manage-authentication/app-create.png)

2. Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing onder **app-registraties**en selecteert u vervolgens **instellingen**.  Selecteer **vereiste machtigingen**en selecteer vervolgens **toevoegen**. Zoek en selecteer **Azure Maps** onder **Selecteer een API**en selecteer vervolgens de knop **selecteren** .

    ![API-machtigingen voor apps](./media/how-to-manage-authentication/app-permissions.png)

3. Selecteer onder **machtigingen selecteren**de optie **toegang Azure Maps**en selecteer vervolgens de knop **selecteren** .

    ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

4. Voltooi stap a of b, afhankelijk van uw verificatie methode.

    1. Als uw toepassing gebruikmaakt van verificatie op basis van gebruikers tokens met de websdk van Azure Maps, schakelt u `oauthEnableImplicitFlow` in op waar in het manifest gedeelte van de detail pagina van de app-registratie.
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    2. Als uw toepassing server/toepassings verificatie gebruikt, gaat u naar de Blade **sleutels** in de app-registratie en maakt u een wacht woord of uploadt u een certificaat met een open bare sleutel naar de registratie van de app. Als u een wacht woord maakt, moet u na het selecteren van **Opslaan**het wacht woord kopiëren voor later en veilig opslaan. U gebruikt dit wacht woord voor het verkrijgen van tokens van Azure AD.

       ![App-sleutels](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>RBAC aan Azure Maps verlenen

Nadat u een Azure Maps-account hebt gekoppeld aan uw Azure AD-Tenant, kunt u toegangs beheer verlenen door een gebruiker, groep of toepassing toe te wijzen aan een of meer Azure Maps Access Control-rollen.

1. Ga naar **toegangs beheer (IAM)** , selecteer **roltoewijzingen**en selecteer **functie toewijzing toevoegen**.

    ![RBAC toekennen](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Selecteer in het venster **roltoewijzing toevoegen** onder **rol**de optie **Azure Maps datum lezer (preview)** . Onder **toegang toewijzen aan**selecteert u **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer onder **selecteren**de gebruiker of toepassing. Selecteer **Opslaan**.

    ![Roltoewijzing toevoegen](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Beschik bare Azure Maps RBAC-rollen weer geven

Als u RBAC-rollen (Role-based Access Control) wilt weer geven die beschikbaar zijn voor Azure Maps, gaat u naar **toegangs beheer (IAM)** , selecteert u **rollen**en zoekt u vervolgens naar rollen die beginnen met **Azure Maps**. Dit zijn de rollen waaraan u toegang kunt verlenen.

![Beschik bare rollen weer geven](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC weer geven

RBAC biedt gedetailleerd toegangs beheer.

Ga naar **Access Control (IAM)** , selecteer **roltoewijzingen**en filter vervolgens op **Azure Maps**om gebruikers en apps weer te geven die zijn toegewezen RBAC voor Azure Maps.

![Gebruikers en apps weer geven die RBAC hebben verleend](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokens aanvragen voor Azure Maps

Nadat u uw app hebt geregistreerd en aan Azure Maps hebt gekoppeld, kunt u toegangs tokens aanvragen.

* Als uw toepassing gebruikmaakt van verificatie op basis van gebruikers tokens met de Azure Maps Web-SDK, moet u uw HTML-pagina configureren met de Azure Maps-client-ID en de Azure AD-App-ID.

* Als uw toepassing server/toepassings verificatie gebruikt, moet u een token aanvragen bij Azure AD token endpoint `https://login.microsoftonline.com` met de Azure AD-Resource-ID `https://atlas.microsoft.com/`, de Azure Maps client-ID, de Azure AD-App-ID en het Azure AD-App-registratie wachtwoord of certificaat.

| Azure-omgeving   | Azure AD-token eindpunt | Azure-Resource-ID |
| --------------------|-------------------------|-------------------|
| Open bare Azure        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Zie [verificatie scenario's voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)voor meer informatie over het aanvragen van toegangs tokens van Azure AD voor gebruikers en service-principals.


## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD en Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)voor meer informatie over Azure AD-verificatie en de Azure Maps Web-SDK.

Meer informatie over het weer geven van metrische gegevens over het API-gebruik voor uw Azure Maps-account:
> [!div class="nextstepaction"] 
> [Metrische gegevens over gebruik weer geven](how-to-view-api-usage.md)

Zie voor een lijst met voor beelden die laten zien hoe u Azure Active Directory (AAD) integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Voor beelden van Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)