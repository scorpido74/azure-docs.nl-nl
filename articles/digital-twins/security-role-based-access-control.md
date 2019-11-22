---
title: Meer informatie over op rollen gebaseerd toegangs beheer-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over op rollen gebaseerd toegangs beheer en het beheren van machtigingen in azure Digital Apparaatdubbels.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: efa19cdd1dd0827fd0e88f533b94f69bd7b99ca9
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307237"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Op rollen gebaseerd toegangs beheer in azure Digital Apparaatdubbels

Met Azure Digital Apparaatdubbels hebt u nauw keurige toegangs controle over specifieke gegevens, resources en acties in uw ruimtelijke grafiek. Dit doet u door het beheer [van op rollen gebaseerd toegangs beheer](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC bestaat uit _rollen_ en _roltoewijzingen_. Rollen bepalen het machtigings niveau. Roltoewijzingen koppelen een rol aan een gebruiker of apparaat.

Met RBAC kunt u machtigingen verlenen aan:

- Een gebruiker.
- Een apparaat.
- Een service-principal.
- Een door de gebruiker gedefinieerde functie.
- Alle gebruikers die tot een domein behoren.
- Een Tenant.

De mate van toegang kan ook worden verfijnd.

RBAC is uniek in die machtigingen worden overgenomen in de ruimtelijke grafiek.

## <a name="what-can-i-do-with-rbac"></a>Wat kan ik doen met op rollen gebaseerd toegangsbeheer?

Een ontwikkelaar kan RBAC gebruiken voor het volgende:

- Geef een gebruiker de mogelijkheid om apparaten te beheren voor een geheel gebouw of alleen voor een specifieke kamer of basis.
- Verleen een beheerder algemene toegang tot alle knoop punten met ruimtelijke grafieken voor een hele grafiek of alleen voor een sectie van de grafiek.
- Verleen een ondersteunings specialist Lees toegang tot de grafiek, met uitzonde ring van toegangs sleutels.
- Ken elk lid van een domein Lees toegang tot alle Graph-objecten.

## <a name="rbac-best-practices"></a>Aanbevolen procedures voor RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Rollen

### <a name="role-definitions"></a>Roldefinities

Een roldefinitie is een verzameling machtigingen en andere kenmerken die een rol vormen. Met een roldefinitie worden de toegestane bewerkingen vermeld, waaronder *maken*, *lezen*, *bijwerken*en *verwijderen* , die een wille keurig object met die rol kan uitvoeren. Ook wordt aangegeven op welke object typen de machtigingen van toepassing zijn.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Als u de volledige definities voor de vorige rollen wilt ophalen, moet u een query uitvoeren op de systeem/rollen-API.
> Lees meer informatie door [roltoewijzingen te maken en te beheren](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Object-id-typen

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Meer informatie over het verlenen van machtigingen aan de Service-Principal door [het maken en beheren van roltoewijzingen](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

In de volgende naslag documentatie artikelen wordt beschreven:

- Informatie over [het opvragen van query's of de object-id van een gebruiker](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- [De object-id van een Service-Principal ophalen](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- [De object-id ophalen voor een Azure AD-Tenant](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Roltoewijzingen

Een functie toewijzing van Azure Digital Apparaatdubbels koppelt een object, zoals een gebruiker of een Azure AD-Tenant, met een rol en een ruimte. Machtigingen worden verleend aan alle objecten die bij die ruimte horen. De ruimte bevat de volledige ruimtelijke grafiek daaronder.

Een gebruiker krijgt bijvoorbeeld een roltoewijzing met de rol `DeviceInstaller` voor het hoofd knooppunt van een ruimtelijke grafiek, die een gebouw vertegenwoordigt. De gebruiker kan vervolgens apparaten voor dat knoop punt en alle andere onderliggende ruimten in het gebouw lezen en bijwerken.

Als u machtigingen wilt verlenen aan een ontvanger, maakt u een roltoewijzing. Als u machtigingen wilt intrekken, verwijdert u de roltoewijzing.

>[!IMPORTANT]
> Lees meer over roltoewijzingen door [het maken en beheren van roltoewijzingen](./security-create-manage-role-assignments.md)te lezen.

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie over het maken en beheren van Azure Digital Apparaatdubbels-roltoewijzingen de [functie toewijzingen maken en beheren](./security-create-manage-role-assignments.md).

- Lees meer over [RBAC voor Azure](https://docs.microsoft.com/azure/role-based-access-control/).
