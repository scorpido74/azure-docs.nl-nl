---
title: Een toepassing met één pagina beveiligen met aanmelding door gebruikers
titleSuffix: Azure Maps
description: Een toepassing met één pagina configureren die ondersteuning biedt voor eenmalige aanmelding van Azure AD met Azure Maps Web-SDK.
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 307f1ea8d98aec8594dd4f666d4e18922122b0f6
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988567"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Een toepassing met één pagina beveiligen met aanmelding door gebruikers

De volgende hand leiding heeft betrekking op een toepassing die wordt gehost op een inhouds server of minimale webserver afhankelijkheden heeft. De toepassing biedt beveiligde resources die alleen zijn beveiligd voor Azure AD-gebruikers. Het doel van het scenario is de webtoepassing in te scha kelen om te verifiëren bij Azure AD en Azure Maps REST Api's namens de gebruiker aan te roepen.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Een toepassings registratie maken in azure AD

Maak de webtoepassing in azure AD voor gebruikers om zich aan te melden. De webtoepassing delegeert gebruikers toegang tot Azure Maps REST-Api's.

1. Selecteer in het Azure Portal in de lijst met Azure-Services **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistratie](./media/how-to-manage-authentication/app-registration.png)

2. Voer een **naam**in, kies een **type ondersteunings account**en geef een OMleidings-URI op die de URL vertegenwoordigt die door Azure AD wordt uitgegeven en de URL waar het kaart besturings element wordt gehost. Zie [Azure Maps Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)-voor beelden voor een gedetailleerd voor beeld. Selecteer vervolgens **Registreren**.  

3. Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing. Selecteer vervolgens onder **app-registraties**de optie **API-machtigingen**  >  **een machtiging toevoegen**. Onder **api's die mijn organisatie gebruikt**, zoekt en selecteert u **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![API-machtigingen voor apps toevoegen](./media/how-to-manage-authentication/app-permissions.png)

4. Schakel het selectie vakje in naast **toegang Azure Maps**en selecteer vervolgens **machtigingen toevoegen**.

    > [!div class="mx-imgBorder"]
    > ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

5. Inschakelen `oauth2AllowImplicitFlow` . Als u dit wilt inschakelen, stelt u in het **manifest** gedeelte van de app-registratie in `oauth2AllowImplicitFlow` op `true` .

6. Kopieer de Azure AD-App-ID en de Azure AD-Tenant-ID uit de app-registratie die u wilt gebruiken in de Web-SDK. Voeg de Azure AD-App-registratie Details en de `x-ms-client-id` van de Azure map-account toe aan de Web-SDK.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Configureer het toegangs beheer op basis van Azure-rollen voor gebruikers of groepen. Zie de [volgende secties om RBAC in te scha kelen](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over een toepassings scenario met één pagina:
> [!div class="nextstepaction"]
> [Toepassing met één pagina](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"]
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)

Bekijk de voor beelden die laten zien hoe u Azure AD integreert met Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-voor beelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
