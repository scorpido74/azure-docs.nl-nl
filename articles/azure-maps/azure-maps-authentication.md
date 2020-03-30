---
title: Authenticatiemethoden | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over Azure Active Directory (Azure AD) en verificatie van gedeelde sleutels. Beide worden gebruikt voor Microsoft Azure Maps-services. Meer informatie over het downloaden van Azure Maps-abonnementen.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335707"
---
# <a name="authentication-with-azure-maps"></a>Verificatie met Azure Maps

Azure Maps ondersteunt twee manieren om aanvragen te verifiëren: verificatie van gedeelde sleutels en Azure Active Directory-verificatie. In dit artikel worden deze verificatiemethoden uitgelegd om uw implementatie van Azure Maps-services te begeleiden.

> [!NOTE]
> Om de veilige communicatie met Azure Maps te verbeteren, ondersteunen we nu TLS (Transport Layer Security) 1.2 en ondersteunen we de ondersteuning voor TLS 1.0 en 1.1. Om onderbrekingen van de service te voorkomen, **werkt u uw servers en toepassingen bij om TLS 1.2 vóór 2 april 2020 te gebruiken.**  Als u tls 1.x momenteel gebruikt, evalueert u de gereedheid van TLS 1.2 en ontwikkelt u een migratieplan met de tests die zijn beschreven in [Het oplossen van het TLS 1.0-probleem.](https://docs.microsoft.com/security/solving-tls1-problem)

## <a name="shared-key-authentication"></a>Verificatie van gedeelde sleutels

 Primaire en secundaire sleutels worden gegenereerd nadat het Azure Maps-account is gemaakt. U wordt aangemoedigd om de primaire sleutel als abonnementssleutel te gebruiken wanneer u Azure Maps belt met verificatie van gedeelde sleutels. Verificatie met gedeelde sleutel geeft een sleutel die door een Azure Maps-account wordt gegenereerd, door aan een Azure Maps-service. Voeg voor elk verzoek aan Azure Maps-services de *abonnementssleutel* als parameter toe aan de URL. De secundaire sleutel kan worden gebruikt in scenario's zoals het rollen van belangrijke wijzigingen.  

Zie [Verificatie beheren](https://aka.ms/amauthdetails)voor informatie over het weergeven van uw sleutels in de Azure-portal.

> [!Tip]
> U wordt aangeraden de sleutels regelmatig opnieuw te genereren. U bent voorzien van twee toetsen, zodat u verbindingen met de ene toets onderhouden terwijl u de andere regenereert. Wanneer u uw sleutels regenereert, moet u alle toepassingen, die toegang hebben tot uw account, bijwerken met de nieuwe sleutels.

## <a name="authentication-with-azure-active-directory-preview"></a>Verificatie met Azure Active Directory (preview-versie)

Azure Maps biedt nu aanvragen verificatie voor Azure Maps-services met Azure [Active Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Azure AD biedt verificatie op basis van identiteit, inclusief [rbac (Role-based access control).](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC wordt gebruikt om gebruikersniveau, groepsniveau of toepassingsniveau toegang te verlenen tot Azure Maps-bronnen. In de volgende secties worden concepten en onderdelen van Azure Maps-integratie met Azure AD besproken.

## <a name="authentication-with-oauth-access-tokens"></a>Verificatie met OAuth-toegangstokens

Azure Maps accepteert **OAuth 2.0-toegangstokens** voor Azure AD-tenants die zijn gekoppeld aan een Azure-abonnement dat een Azure Maps-account bevat. Azure Maps accepteert ook tokens voor:

* Azure AD-gebruikers
* Partnertoepassingen die machtigingen gebruiken die door gebruikers zijn gedelegeerd
* Beheerde identiteiten voor Azure-resources

Azure Maps genereert een *unieke id (client-id)* voor elk Azure Maps-account. U tokens aanvragen bij Azure AD wanneer u deze client-id combineert met extra parameters. Als u een token wilt aanvragen, geeft u de waarden op in de volgende tabel op basis van uw Azure-omgeving.

| Azure-omgeving   | Eindpunt azure AD-token |
| --------------------|-------------------------|
| Openbare Azure-peering        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Zie [Verificatie beheren in Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)voor meer informatie over het configureren van Azure AD en het aanvragen van tokens voor Azure Maps.

Zie Wat is verificatie voor algemene informatie over het aanvragen van tokens van Azure [AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Azure Map-bronnen aanvragen met OAuth-tokens

Nadat Azure AD een token heeft ontvangen, verzendt Azure Maps een aanvraag met de volgende set vereiste aanvraagkoppen:

| Aanvraagheader    |    Waarde    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorisatie     | Bearer-token eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`is de GUID op basis van Azure Maps-account die wordt weergegeven op de verificatiepagina van Azure Maps.

Hier volgt een voorbeeld van een Azure Maps-routeaanvraag die een OAuth-token gebruikt:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Zie [Verificatiegegevens weergeven](https://aka.ms/amauthdetails)voor informatie over het weergeven van uw client-id.

## <a name="control-access-with-rbac"></a>Toegang beheren met RBAC

Gebruik RBAC in Azure AD om de toegang tot beveiligde bronnen te beheren. Stel uw Azure Maps-account in en registreer uw Azure Maps Azure AD-tenant. Azure Maps ondersteunt leestoegangsbeheer voor afzonderlijke Azure AD-gebruikers, -groepen, -toepassingen, Azure-bronnen en Azure-services via beheerde identiteiten voor Azure-bronnen. Op de portalpagina azure maps u RBAC instellen voor de door u gekozen rollen.

![Azure Maps-gegevenslezer (voorbeeld)](./media/azure-maps-authentication/concept.png)

Zie [RBAC configureren voor Azure Maps voor](https://aka.ms/amrbac)informatie over het weergeven van uw RBAC-instellingen.

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Beheerde identiteiten voor Azure-resources en Azure Maps

[Beheerde identiteiten voor Azure-bronnen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) bieden Azure-services een automatisch beheerde identiteit, die kan worden geautoriseerd om toegang te krijgen tot Azure Maps-services. Enkele voorbeelden van beheerde identiteiten, zoals: Azure App Service, Azure Functions en Azure Virtual Machines.

## <a name="next-steps"></a>Volgende stappen

* Zie [Verificatie beheren in Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)voor meer informatie over het verifiëren van een toepassing met Azure AD en Azure Maps.

* Zie Azure [Maps Map Control gebruiken](https://aka.ms/amaadmc)voor meer informatie over het verifiëren van het Azure Maps Map Control en Azure AD.
