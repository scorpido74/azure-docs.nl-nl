---
title: Roltoewijzingen maken en beheren - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het maken en beheren van roltoewijzingen binnen Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110402"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Roltoewijzingen maken en beheren in Azure Digital Twins

Azure Digital Twins gebruikt op rollen gebaseerd toegangscontrole[(RBAC)](./security-role-based-access-control.md)om de toegang tot bronnen te beheren.

## <a name="role-assignments-overview"></a>Overzicht van roltoewijzingen

Elke roltoewijzing voldoet aan de volgende definitie:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

In de onderstaande tabel wordt elk kenmerk beschreven:

| Kenmerk | Name | Vereist | Type | Beschrijving |
| --- | --- | --- | --- | --- |
| rolId | Roldefinitie-id | Ja | Tekenreeks | De unieke ID van de gewenste rolopdracht. Zoek roldefinities en hun id door de systeem-API op te vragen of onderstaande controletabel op te vragen. |
| objectId | Object-id | Ja | Tekenreeks | Een Azure Active Directory ID, serviceprincipal object ID of domeinnaam. Wat of aan wie de roltoewijzing is toegewezen. De roltoewijzing moet worden opgemaakt op basis van het bijbehorende type. Voor `DomainName` het objectIdType moet objectId `“@”` beginnen met het teken. |
| objectIdType | Object-id-type | Ja | Tekenreeks | Het soort object-id dat wordt gebruikt. Zie **ondersteunde objectidtypes** hieronder. |
| path | Ruimtepad | Ja | Tekenreeks | Het volledige toegangspad `Space` naar het object. Een voorbeeld is `/{Guid}/{Guid}`. Als een id de roltoewijzing voor `"/"`de hele grafiek nodig heeft, geeft u op . Dit teken wijst de wortel aan, maar het gebruik ervan wordt afgeraden. Volg altijd het principe van het minste voorrecht. |
| tenantId | Tenant-id | Varieert | Tekenreeks | In de meeste gevallen een Azure Active Directory-tenant-id. Niet toegestaan `DeviceId` `TenantId` voor en ObjectIdTypes. Vereist `UserId` voor `ServicePrincipalId` en ObjectIdTypes. Optioneel voor het DomainName ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Ondersteunde roldefinitie-id's

Elke roltoewijzing associeert een roldefinitie met een entiteit in uw Azure Digital Twins-omgeving.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Ondersteunde object-id-typen

Voorheen werd het kenmerk **objectIdType** geïntroduceerd.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Taaktoewijzingsbewerkingen

Azure Digital Twins ondersteunt volledige *BEWERKINGen MAKEN,* *LEZEN*en *DELETE* voor roltoewijzingen. *UPDATE-bewerkingen* worden afgehandeld door roltoewijzingen toe te voegen, roltoewijzingen te verwijderen of de knooppunten [Spatial Intelligence Graph](./concepts-objectmodel-spatialgraph.md) te wijzigen waartoe roltoewijzingen toegang geven.

[![Eindpunten voor roltoewijzing](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

De meegeleverde Swagger-referentiedocumentatie bevat meer informatie over alle beschikbare API-eindpunten, aanvraagbewerkingen en definities.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Machtigingen verlenen aan uw serviceprincipal

Het verlenen van machtigingen aan uw serviceprincipal is vaak een van de eerste stappen die u zult nemen bij het werken met Azure Digital Twins. Het houdt in:

1. U aanmeldt bij uw Azure-instantie via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) of [PowerShell.](https://docs.microsoft.com/powershell/azure/)
1. Het verkrijgen van uw service belangrijkste informatie.
1. Het toewijzen van de gewenste rol aan uw service principal.

Uw toepassings-id wordt aan u geleverd in Azure Active Directory. Lees voor meer informatie over het configureren en inrichten van een Azure Digital Twins in Active Directory de [Quickstart](./quickstart-view-occupancy-dotnet.md).

Zodra u de toepassings-id hebt, voert u een van de volgende opdrachten uit. In Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

In Powershell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Een gebruiker met de functie **Beheerder** kan vervolgens de rol Ruimtebeheerder aan een gebruiker toewijzen door een geverifieerd HTTP POST-verzoek aan de URL in te dienen:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Met de volgende JSON body:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Alle rollen ophalen

[![Systeemrollen](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Als u alle beschikbare rollen (roldefinities) wilt weergeven, dient u een geverifieerd HTTP GET-verzoek in om:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

Een geslaagde aanvraag retourneert een JSON-array met vermeldingen voor elke rol die kan worden toegewezen:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

### <a name="check-a-specific-role-assignment"></a>Een specifieke roltoewijzing controleren

Als u een specifieke roltoewijzing wilt controleren, dient u een geverifieerd HTTP GET-verzoek in om:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parameterwaarde** | **Vereist** |  **Type** |  **Beschrijving** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Tekenreeks |   De objectId voor het ObjectId-objectIdType. |
| YOUR_PATH | True | Tekenreeks |   Het gekozen pad om de toegang voor te controleren. |
| YOUR_ACCESS_TYPE |  True | Tekenreeks |   *Lezen,* *maken,* *bijwerken*of *verwijderen* |
| YOUR_RESOURCE_TYPE | True | Tekenreeks |  *Device*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *Endpoint*, *KeyStore*, *Matcher*, *Ontology*, *Report*, *RoleDefinition*, *Sensor*, *SensorExtendedProperty*, *Space* *, SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *System*, * UerDefinedFunction*, *User*, *UserBlobMetadata*, of *UserExtendedProperty* |

Een geslaagde aanvraag retourneert een booleaan `true` of `false` om aan te geven of het toegangstype is toegewezen aan de gebruiker voor het opgegeven pad en de opgegeven bron.

### <a name="get-role-assignments-by-path"></a>Roltoewijzingen op pad krijgen

Als u alle roltoewijzingen voor een pad wilt krijgen, dient u een geverifieerd HTTP GET-verzoek in om:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_PATH | Het volledige pad naar de ruimte |

Een geslaagde aanvraag retourneert een JSON-array met elke roltoewijzing die is gekoppeld aan de geselecteerde **padparameter:**

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Een machtiging intrekken

Als u een machtiging van een ontvanger wilt intrekken, verwijdert u de roltoewijzing door een geverifieerd HTTP DELETE-verzoek in te dienen:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | De **id** van de roltoewijzing die moet worden verwijderd |

Een succesvol DELETE-verzoek retourneert een antwoordstatus van 204. Controleer de verwijdering van de roltoewijzing door [te controleren of](#check-a-specific-role-assignment) de roltoewijzing nog steeds geldt.

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken

Als u een roltoewijzing wilt maken, dient u een geverifieerd HTTP-BERICHT-verzoek in op de URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Controleer of de JSON-body voldoet aan het volgende schema:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Een geslaagde aanvraag retourneert een reactiestatus van 201 samen met de **id** van de nieuw gemaakte roltoewijzing:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Configuratievoorbeelden

In de volgende voorbeelden wordt uitgelegd hoe u uw JSON-body configureren in verschillende veelvoorkomende roltoewijzingsscenario's.

* **Voorbeeld:** Een gebruiker heeft administratieve toegang nodig tot een vloer van een tenantruimte.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Voorbeeld:** Een toepassing voert testscenario's uit die bespottende apparaten en sensoren bespotten.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Voorbeeld:** Alle gebruikers die deel uitmaken van een domein krijgen leestoegang voor ruimten, sensoren en gebruikers. Deze toegang omvat de bijbehorende gerelateerde objecten.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Volgende stappen

- Als u Azure Digital Twins-functiegebaseerde toegangsbeheer wilt controleren, leest u [Role-base-access-control](./security-authenticating-apis.md).

- Lees [API-verificatie](./security-authenticating-apis.md)voor meer informatie over Azure Digital Twins API-verificatie.
