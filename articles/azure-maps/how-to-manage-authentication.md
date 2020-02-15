---
title: Verificatie beheren | Microsoft Azure kaarten
description: Gebruik de Azure Portal om verificatie te beheren in Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ef88f9794fcba8c702ee793044d5482912ec3d89
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210001"
---
# <a name="manage-authentication-in-azure-maps"></a>Verificatie in Azure Maps beheren

Nadat u een Azure Maps-account hebt gemaakt, worden er een client-ID en-sleutels gemaakt ter ondersteuning van de verificatie van Azure Active Directory (Azure AD) en de verificatie van de gedeelde sleutel.

## <a name="view-authentication-details"></a>Verificatie gegevens weer geven

Nadat u een Azure Maps account hebt gemaakt, worden de primaire en secundaire sleutels gegenereerd. U wordt aangeraden een primaire sleutel als abonnements sleutel te gebruiken wanneer u [gebruikmaakt van gedeelde sleutel verificatie om Azure Maps aan te roepen](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). U kunt een secundaire sleutel gebruiken in scenario's zoals wijzigingen in de Rolling sleutel. Zie [verificatie in azure Maps](https://aka.ms/amauth)voor meer informatie.

U kunt uw verificatie gegevens weer geven in de Azure Portal. In uw account selecteert u in het menu **instellingen** de optie **verificatie**.

![Verificatie gegevens](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Een Azure AD-App registreren en configureren

1. Selecteer in het Azure Portal in de lijst met Azure-Services **Azure Active Directory** > **app-registraties** een **nieuwe registratie**te > .  

    ![App-registratie](./media/how-to-manage-authentication/app-registration.png)

1. Als u uw app al hebt geregistreerd, gaat u verder met de volgende stap. Als u uw app nog niet hebt geregistreerd, voert u een **naam**in, kiest u een **type ondersteunings account**en selecteert u vervolgens **registreren**.  

    ![Details van app-registratie](./media/how-to-manage-authentication/app-create.png)

1. Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing. Selecteer vervolgens onder **app-registraties** **API-machtigingen** > **een machtiging toe te voegen**. Onder **api's die mijn organisatie gebruikt**, zoekt en selecteert u **Azure Maps**.

    ![API-machtigingen voor apps toevoegen](./media/how-to-manage-authentication/app-permissions.png)

1. Schakel het selectie vakje in naast **toegang Azure Maps**en selecteer vervolgens **machtigingen toevoegen**.

    ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

1. Voer een van de volgende stappen uit, afhankelijk van uw verificatie methode. 

    * Als uw toepassing gebruikmaakt van verificatie op basis van gebruikers tokens met de Azure Maps Web-SDK, schakelt u `oauth2AllowImplicitFlow`in. Als u dit wilt inschakelen, stelt u in de sectie **manifest** van de app-registratie `oauth2AllowImplicitFlow` in op waar. 
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    * Als uw toepassing gebruikmaakt van server-of toepassings verificatie, gaat u op de pagina app-registratie naar **certificaten & geheimen**. Upload vervolgens een certificaat met een open bare sleutel of maak een wacht woord door **Nieuw client geheim**te selecteren. 
    
       ![Een client geheim maken](./media/how-to-manage-authentication/app-keys.png)

        Als u een wacht woord maakt en u vervolgens **toevoegen**selecteert, kopieert u het wacht woord en slaat u het veilig op. U gebruikt dit wacht woord om tokens op te halen uit Azure AD.

       ![Een client geheim toevoegen](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Op rollen gebaseerd toegangs beheer toekennen aan Azure Maps

Nadat u een Azure Maps-account hebt gekoppeld aan uw Azure AD-Tenant, kunt u toegangs beheer verlenen. U verleent op *rollen gebaseerd toegangs beheer* (RBAC) door een gebruiker, groep of toepassing toe te wijzen aan een of meer Azure Maps Access Control-rollen. 

1. Ga naar uw **Azure Maps-account**. Selecteer **toegangs beheer (IAM)**  > **roltoewijzing**.

    ![RBAC toekennen](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Selecteer op **het tabblad roltoewijzingen onder** **rol**de optie **Azure Maps datum lezer (preview)** . Onder **toegang toewijzen aan**selecteert u **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer de gebruiker of de toepassing. Selecteer vervolgens **Opslaan**.

    ![Roltoewijzing toevoegen](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Beschik bare Azure Maps RBAC-rollen weer geven

Ga naar **toegangs beheer (IAM)** om RBAC-rollen weer te geven die beschikbaar zijn voor Azure Maps. Selecteer **rollen**en zoek vervolgens naar rollen die beginnen met *Azure Maps*. Deze Azure Maps rollen zijn de rollen waaraan u toegang kunt verlenen.

![Beschik bare rollen weer geven](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC weer geven

RBAC biedt gedetailleerd toegangs beheer.

Ga naar **Access Control (IAM)** om gebruikers en apps weer te geven die zijn toegewezen RBAC voor Azure Maps. Hier selecteert u **roltoewijzingen**en filtert u vervolgens op **Azure Maps**.

![Gebruikers en apps weer geven waaraan RBAC is toegewezen](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokens aanvragen voor Azure Maps

Nadat u uw app hebt geregistreerd en aan Azure Maps hebt gekoppeld, kunt u toegangs tokens aanvragen.

Als uw toepassing gebruikmaakt van verificatie op basis van gebruikers tokens met de Azure Maps Web-SDK, configureert u uw HTML-pagina met de Azure Maps-client-ID en de Azure AD-App-ID.

Als uw toepassing gebruikmaakt van server-of toepassings verificatie, vraagt u een token op bij het Azure AD-token eindpunt `https://login.microsoftonline.com`. Gebruik de volgende gegevens in uw aanvraag: 

* Resource-ID `https://atlas.microsoft.com/` Azure AD
* Client-ID Azure Maps
* Azure AD-App-ID
* Azure AD-App-registratie wachtwoord of-certificaat

| Azure-omgeving   | Azure AD-token eindpunt | Azure-Resource-ID |
| --------------------|-------------------------|-------------------|
| Open bare Azure-Cloud        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government Cloud   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Zie [verificatie scenario's voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)voor meer informatie over het aanvragen van toegangs tokens van Azure AD voor gebruikers en service-principals.


## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD en Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)voor meer informatie.

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"] 
> [Metrische gegevens over gebruik weer geven](how-to-view-api-usage.md)

Bekijk de voor beelden die laten zien hoe u Azure AD integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Voor beelden van Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
