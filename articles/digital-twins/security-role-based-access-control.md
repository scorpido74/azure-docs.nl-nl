---
title: Inzicht in toegangsbeheer op basis van rollen - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over toegangsbeheer op basis van rollen en machtigingen voor beheer in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044917"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Op rollen gebaseerd toegangsbeheer in Azure Digital Twins

Azure Digital Twins maakt nauwkeurige toegangscontrole over specifieke gegevens, bronnen en acties in uw ruimtelijke grafiek mogelijk. Het doet dit door middel van gedetailleerde rol en toestemmingbeheer genaamd [role-based access control](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC bestaat uit _rollen_ en _rolopdrachten._ Rollen bepalen het niveau van machtigingen. Roltoewijzingen koppelen een rol aan een gebruiker of apparaat.

Met RBAC kan toestemming worden verleend voor:

- Een gebruiker.
- Een apparaat.
- Een servicedirecteur.
- Een door de gebruiker gedefinieerde functie.
- Alle gebruikers die tot een domein behoren.
- Een huurder.

De mate van toegang kan ook worden verfijnd.

RBAC is uniek omdat machtigingen worden overgenomen in de ruimtelijke grafiek.

## <a name="what-can-i-do-with-rbac"></a>Wat kan ik doen met op rollen gebaseerd toegangsbeheer?

Een ontwikkelaar kan RBAC gebruiken om:

- Geef een gebruiker de mogelijkheid om apparaten voor een heel gebouw te beheren, of alleen voor een specifieke kamer of vloer.
- Geef een beheerder globale toegang tot alle ruimtelijke grafiekknooppunten voor een hele grafiek of alleen voor een gedeelte van de grafiek.
- Geef een ondersteuningsspecialist leestoegang tot de grafiek, met uitzondering van toegangssleutels.
- Geef elk lid van een domein leestoegang tot alle grafiekobjecten.

## <a name="rbac-best-practices"></a>Aanbevolen procedures van RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Rollen

### <a name="role-definitions"></a>Roldefinities

Een roldefinitie is een verzameling machtigingen en andere kenmerken die een rol spelen. Een roldefinitie bevat de toegestane bewerkingen, waaronder *CREATE,* *READ*, *UPDATE*en *DELETE* die elk object met die rol kan uitvoeren. Het geeft ook aan op welke objecttypen de machtigingen van toepassing zijn.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Als u de volledige definities voor de vorige rollen wilt ophalen, zoekt u de API voor systeem/rollen op.
> Meer informatie door [Roltoewijzingen maken en beheren te](./security-create-manage-role-assignments.md#retrieve-all-roles)lezen.

### <a name="object-identifier-types"></a>Object-id-typen

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Meer informatie over het verlenen van machtigingen aan uw serviceprincipal door [Roltoewijzingen maken en beheren](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)te lezen.

In de volgende artikelen over referentiedocumentatie wordt beschreven:

- Hoe [query of de object-id voor een gebruiker](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Hoe [het object-id voor een serviceprincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)te verkrijgen .
- De [object-id ophalen voor een Azure AD-tenant](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Roltoewijzingen

Een Azure Digital Twins-roltoewijzing koppelt een object, zoals een gebruiker of een Azure AD-tenant, aan een rol en een spatie. Machtigingen worden verleend aan alle objecten die tot die ruimte behoren. De ruimte bevat de volledige ruimtelijke grafiek eronder.

Een gebruiker krijgt bijvoorbeeld een roltoewijzing `DeviceInstaller` met de rol voor het hoofdknooppunt van een ruimtelijke grafiek, die een gebouw vertegenwoordigt. De gebruiker kan vervolgens apparaten voor dat knooppunt en alle andere onderliggende ruimten in het gebouw lezen en bijwerken.

Als u machtigingen wilt verlenen aan een ontvanger, maakt u een roltoewijzing. Als u machtigingen wilt intrekken, verwijdert u de roltoewijzing.

>[!IMPORTANT]
> Meer informatie over roltoewijzingen door [roltoewijzingen maken en beheren te](./security-create-manage-role-assignments.md)lezen.

## <a name="next-steps"></a>Volgende stappen

- Lees [Roltoewijzingen](./security-create-manage-role-assignments.md)maken en beheren voor meer informatie over het maken en beheren van azure digital twins-roltoewijzingen.

- Lees meer over [RBAC voor Azure](https://docs.microsoft.com/azure/role-based-access-control/).
