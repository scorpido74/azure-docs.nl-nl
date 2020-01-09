---
title: Roltoewijzingen maken en beheren-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over het maken en beheren van roltoewijzingen binnen Azure Digital Apparaatdubbels.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/12/2019
ms.custom: seodec18
ms.openlocfilehash: 7eeaadc80a97a96e6effdfc9e5cc76c201998f3f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438059"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Roltoewijzingen in azure Digital Apparaatdubbels maken en beheren

Azure Digital Apparaatdubbels maakt gebruik van op rollen gebaseerd toegangs beheer ([RBAC](./security-role-based-access-control.md)) voor het beheren van de toegang tot bronnen.

## <a name="role-assignments-overview"></a>Overzicht roltoewijzingen

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

In de volgende tabel wordt elk kenmerk beschreven:

| Kenmerk | Name | Verplicht | Type | Beschrijving |
| --- | --- | --- | --- | --- |
| roleId | Roldefinitie-id | Ja | Tekenreeks | De unieke ID van de gewenste roltoewijzing. Zoek roldefinities en hun id door een query uit te zoeken naar de onderstaande systeem-API of-tabel. |
| object-id | Object-id | Ja | Tekenreeks | Een Azure Active Directory-ID, Service-Principal object-ID of domein naam. Waaraan de roltoewijzing is toegewezen. De roltoewijzing moet worden ingedeeld volgens het bijbehorende type. Voor de `DomainName` objectIdType moet objectId beginnen met het `“@”` teken. |
| objectIdType | Type object-id | Ja | Tekenreeks | Het soort object-id dat wordt gebruikt. Zie de **ondersteunde ObjectIdTypes** hieronder. |
| Pad | Pad naar Space | Ja | Tekenreeks | Het volledige pad naar het `Space`-object. Een voorbeeld is `/{Guid}/{Guid}`. Als een id de roltoewijzing voor de hele grafiek vereist, geeft u `"/"`op. Met dit teken wordt de hoofdmap aangeduid, maar het gebruik ervan wordt afgeraden. Volg altijd het principe van minimale bevoegdheden. |
| tenantId | Tenant-id | Varieert | Tekenreeks | In de meeste gevallen wordt de Tenant-ID van Azure Active Directory. Niet toegestaan voor `DeviceId` en `TenantId` ObjectIdTypes. Vereist voor `UserId` en `ServicePrincipalId` ObjectIdTypes. Optioneel voor de DomainName-ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Definitie-id's van ondersteunde rollen

Elke roltoewijzing koppelt een roldefinitie aan een entiteit in uw Azure Digital Apparaatdubbels-omgeving.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Ondersteunde object-id-typen

Voorheen werd het kenmerk **objectIdType** geïntroduceerd.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Roltoewijzings bewerkingen

Azure Digital Apparaatdubbels ondersteunt volledige bewerkingen voor *maken*, *lezen*en *verwijderen* voor roltoewijzingen. *Update* bewerkingen worden verwerkt door roltoewijzingen toe te voegen, roltoewijzingen te verwijderen of de knoop punten van de [ruimtelijke Intelligence-grafiek](./concepts-objectmodel-spatialgraph.md) te wijzigen waarvan de roltoewijzingen toegang krijgen.

[eind punten voor roltoewijzing ![](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

De opgegeven Swagger-referentie documentatie bevat meer informatie over alle beschik bare API-eind punten, aanvraag bewerkingen en definities.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Machtigingen verlenen aan de Service-Principal

Het verlenen van machtigingen aan de Service-Principal is vaak een van de eerste stappen die u moet uitvoeren wanneer u met Azure Digital Apparaatdubbels werkt. Dit omvat:

1. Meld u aan bij uw Azure-exemplaar via [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) of [Power shell](https://docs.microsoft.com/powershell/azure/).
1. De gegevens van uw Service-Principal ophalen.
1. Wijs de gewenste rol toe aan uw service-principal.

Uw toepassings-ID wordt aan u verstrekt in Azure Active Directory. Lees de [Snelstartgids](./quickstart-view-occupancy-dotnet.md)voor meer informatie over het configureren en inrichten van een Azure Digital apparaatdubbels in Active Directory.

Wanneer u de toepassings-ID hebt, voert u een van de volgende opdrachten uit. In azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

In Power shell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Een gebruiker met de rol **admin** kan de rol van de ruimte beheerder vervolgens toewijzen aan een gebruiker door een geverifieerde HTTP POST-aanvraag naar de URL te maken:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Met de volgende JSON-hoofd tekst:

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

[![systeem rollen](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Als u alle beschik bare rollen (roldefinities) wilt weer geven, maakt u een geverifieerde HTTP GET-aanvraag naar:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Een geslaagde aanvraag retourneert een JSON-matrix met vermeldingen voor elke rol die kan worden toegewezen:

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

Als u een specifieke roltoewijzing wilt controleren, maakt u een geverifieerde HTTP GET-aanvraag naar:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parameter waarde** | **Vereist** |  **Type** |  **Beschrijving** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Waar | Tekenreeks |   De objectId voor de gebruikers-id objectIdType. |
| YOUR_PATH | Waar | Tekenreeks |   Het gekozen pad om de toegang voor te controleren. |
| YOUR_ACCESS_TYPE |  Waar | Tekenreeks |   *Lezen*, *maken*, *bijwerken*of *verwijderen* |
| YOUR_RESOURCE_TYPE | Waar | Tekenreeks |  *Apparaat*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *Extended type*, *eind punt* *, Keys*, *Matcher*, *Ontology*, *rapport*, *RoleDefinition*, *sensor*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *systeem*,  *UerDefinedFunction*, *User*, *UserBlobMetadata*of *UserExtendedProperty* |

Een geslaagde aanvraag retourneert een Booleaanse `true` of `false` om aan te geven of het toegangs type is toegewezen aan de gebruiker voor het gegeven pad en de opgegeven bron.

### <a name="get-role-assignments-by-path"></a>Roltoewijzingen ophalen op basis van pad

Als u alle roltoewijzingen voor een pad wilt ophalen, maakt u een geverifieerde HTTP GET-aanvraag naar:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_PATH | Het volledige pad naar de ruimte |

Een geslaagde aanvraag retourneert een JSON-matrix met elke roltoewijzing die is gekoppeld aan de para meter van het geselecteerde **pad** :

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

Als u een machtiging van een ontvanger wilt intrekken, verwijdert u de roltoewijzing door een geverifieerde HTTP-aanvraag te verwijderen:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | De **id** van de roltoewijzing die moet worden verwijderd |

Een geslaagde VERWIJDERings aanvraag retourneert een 204-antwoord status. Controleer het verwijderen van de roltoewijzing door te [controleren](#check-a-specific-role-assignment) of de roltoewijzing nog steeds aanwezig is.

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken

Als u een roltoewijzing wilt maken, maakt u een geverifieerde HTTP POST-aanvraag naar de URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Controleer of de JSON-hoofd tekst voldoet aan het volgende schema:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Een geslaagde aanvraag retourneert een 201-reactie status samen met de **id** van de zojuist gemaakte roltoewijzing:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Configuratievoorbeelden

De volgende voor beelden laten zien hoe u uw JSON-hoofd tekst kunt configureren in verschillende scenario's voor het toewijzen van rollen.

* **Voor beeld**: een gebruiker heeft beheerders toegang nodig tot een verdieping van een Tenant.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Voor beeld**: een toepassing voert test Scenario's uit voor het intrekken van apparaten en Sens oren.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Voor beeld**: alle gebruikers die deel uitmaken van een domein, krijgen lees toegang voor ruimten, Sens oren en gebruikers. Deze toegang bevat de bijbehorende gerelateerde objecten.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Volgende stappen

- Voor het controleren van de op rollen gebaseerde Azure Digital Apparaatdubbels-Access-Control, Lees [Role-Base-Access-Control](./security-authenticating-apis.md).

- Lees [API-verificatie](./security-authenticating-apis.md)voor meer informatie over de Azure Digital apparaatdubbels API-verificatie.
