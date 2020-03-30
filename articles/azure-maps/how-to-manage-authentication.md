---
title: Verificatie beheren | Microsoft Azure Maps
description: Gebruik de Azure-portal om verificatie te beheren in Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335531"
---
# <a name="manage-authentication-in-azure-maps"></a>Verificatie beheren in Azure Maps

Nadat u een Azure Maps-account hebt gemaakt, worden een client-id en sleutels gemaakt ter ondersteuning van Azure Active Directory-verificatie (Azure AD) en verificatie van gedeelde sleutels.

## <a name="view-authentication-details"></a>Verificatiegegevens weergeven

Nadat u een Azure Maps-account hebt gemaakt, worden de primaire en secundaire sleutels gegenereerd. We raden u aan een primaire sleutel als abonnementssleutel te gebruiken wanneer u [verificatie van gedeelde sleutels gebruikt om Azure Maps te bellen.](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) U een secundaire sleutel gebruiken in scenario's zoals het doorrollen van belangrijke wijzigingen. Zie [Verificatie in Azure Maps](https://aka.ms/amauth)voor meer informatie.

U uw verificatiegegevens bekijken in de Azure-portal. Selecteer **verificatie**in uw account in het menu **Instellingen** .

![Verificatiegegevens](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Een Azure AD-app registreren en configureren

1. Selecteer in de Azure-portal in de lijst met Azure-services de optie **Azure Active Directory** > **App-registraties** > **Nieuwe registratie**.  

    ![App-registratie](./media/how-to-manage-authentication/app-registration.png)

1. Als u uw app al hebt geregistreerd, gaat u verder met de volgende stap. Als u uw app niet hebt geregistreerd, voert u een **naam**in, kiest u een **type Ondersteuningsaccount**en selecteert u **Register**.  

    ![Gegevens over app-registratie](./media/how-to-manage-authentication/app-create.png)

1. Als u gedelegeerde API-machtigingen wilt toewijzen aan Azure Maps, gaat u naar de toepassing. Selecteer vervolgens onder **App-registraties** **API-machtigingen** > **Voeg een machtiging toe.** Onder **API's die mijn organisatie gebruikt,** zoeken naar en selecteren **azure maps**.

    ![App API-machtigingen toevoegen](./media/how-to-manage-authentication/app-permissions.png)

1. Schakel het selectievakje naast **Access Azure Maps**in en selecteer Machtigingen **toevoegen**.

    ![App-API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

1. Voer een van de volgende stappen uit, afhankelijk van uw verificatiemethode. 

    * Als uw toepassing gebruikerstokenverificatie gebruikt met de Azure `oauth2AllowImplicitFlow`Maps Web SDK, schakelt u . Om dit in te schakelen, stel je `oauth2AllowImplicitFlow` in het gedeelte **Manifest** van je app-registratie in op true. 
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    * Als uw toepassing server- of toepassingsverificatie gebruikt, gaat u op uw pagina voor app-registratie naar **Certificaten & geheimen.** Upload vervolgens een certificaat met openbare sleutels of maak een wachtwoord door **Nieuw clientgeheim te**selecteren. 
    
       ![Een klantgeheim maken](./media/how-to-manage-authentication/app-keys.png)

        Als u een wachtwoord maakt, kopieert u het wachtwoord nadat u **Toevoegen**hebt geselecteerd, kopieert u het wachtwoord en slaat u het veilig op. U gebruikt dit wachtwoord om tokens van Azure AD te krijgen.

       ![Een clientgeheim toevoegen](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Toegangsbeheer op basis van rollen verlenen aan Azure Maps

Nadat u een Azure Maps-account hebt gekoppeld aan uw Azure AD-tenant, u toegangsbeheer verlenen. U verleent *rbac (role-based access control)* door een gebruiker, groep of toepassing toe te wijzen aan een of meer Azure Maps-toegangsbeheerrollen. 

1. Ga naar uw **Azure Maps-account**. Selecteer**Roltoewijzing** **toegangsbeheer (IAM).** > 

    ![Subsidie RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Selecteer op het tabblad **Toewijzingen van rollen** onder **Rol**de optie Azure Maps Date **Reader (Preview).** Selecteer **onder Toegang toewijzen tot**Azure **AD-gebruiker, groep of serviceprincipal**. Selecteer de gebruiker of toepassing. Selecteer vervolgens **Opslaan**.

    ![Roltoewijzing toevoegen](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Beschikbare RBAC-rollen voor Azure Maps weergeven

Als u RBAC-rollen wilt weergeven die beschikbaar zijn voor Azure Maps, gaat u naar **Toegangsbeheer (IAM).** Selecteer **Rollen**en zoek vervolgens naar rollen die beginnen met *Azure Maps.* Deze Azure Maps-rollen zijn de rollen waartoe u toegang verlenen.

![Beschikbare rollen weergeven](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Bekijk Azure Maps RBAC

RBAC biedt gedetailleerde toegangscontrole.

Als u gebruikers en apps wilt bekijken waaraan RBAC voor Azure Maps is toegekend, gaat u naar **Access Control (IAM).** Selecteer hier **Roltoewijzingen**en filter vervolgens op **Azure Maps**.

![Gebruikers en apps bekijken die RBAC hebben gekregen](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokens aanvragen voor Azure Maps

Nadat u uw app hebt geregistreerd en gekoppeld aan Azure Maps, u toegangstokens aanvragen.

Als uw toepassing gebruikerstokenverificatie gebruikt met de Azure Maps Web SDK, configureert u uw HTML-pagina met de Azure Maps-client-id en de Azure AD-app-id.

Als uw toepassing server- of toepassingsverificatie gebruikt, vraagt u `https://login.microsoftonline.com`een token aan bij het eindpunt van azure AD-token . Gebruik in uw verzoek de volgende gegevens: 

* Azure AD-bron-id`https://atlas.microsoft.com/`
* Azure Maps-client-id
* Azure AD-app-id
* Wachtwoord of certificaat voor registratie van Azure AD-app

| Azure-omgeving   | Eindpunt azure AD-token | Azure-bron-id |
| --------------------|-------------------------|-------------------|
| Azure public cloud        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government-cloud   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Zie [Verificatiescenario's voor Azure AD voor](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)meer informatie over het aanvragen van toegangstokens van Azure AD voor gebruikers en serviceprincipals.


## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD en Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)voor meer informatie.

Zoek de API-gebruiksstatistieken voor uw Azure Maps-account:
> [!div class="nextstepaction"] 
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)

Bekijk voorbeelden die laten zien hoe u Azure AD integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Azure AD-verificatievoorbeelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
