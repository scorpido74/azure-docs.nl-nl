---
title: Toegangsbeheer voor externe bewaking - Azure | Microsoft Documenten
description: In dit artikel vindt u informatie over hoe u op rollen gebaseerde toegangscontroles (RBAC) configureren in de versneller van de oplossing voor externe bewaking
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: b0c9699bccbb539c9617fac2f3296483139e7188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67203166"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Op rollen gebaseerde toegangscontroles configureren in de versneller van de oplossing voor externe bewaking

In dit artikel vindt u informatie over het configureren van op rollen gebaseerde toegangscontroles in de versneller van de oplossing voor externe bewaking. Met toegangsbesturingselementen op basis van rollen u de toegang voor individuele gebruikers beperken tot specifieke functies in de oplossing.

## <a name="default-settings"></a>Standaardinstellingen

Wanneer u de oplossing voor externe bewaking voor het eerst implementeert, zijn er twee rollen: **Beheerder** en **Alleen lezen**.

Elke gebruiker in de rol **Beheerder** heeft volledige toegang tot de oplossing, inclusief de volgende machtigingen hieronder. Een gebruiker in de rol **Alleen lezen** heeft alleen toegang tot de oplossing.

| Machtiging            | Beheerder | Alleen-lezen |
|----------------       |-------|-----------|
| Oplossing weergeven         | Ja   | Ja       |
| Alarmen bijwerken         | Ja   | Nee        |
| Alarmen verwijderen         | Ja   | Nee        |
| Apparaten maken        | Ja   | Nee        |
| Apparaten bijwerken        | Ja   | Nee        |
| Apparaten verwijderen        | Ja   | Nee        |
| Apparaatgroepen maken  | Ja   | Nee        |
| Apparaatgroepen bijwerken  | Ja   | Nee        |
| Apparaatgroepen verwijderen  | Ja   | Nee        |
| Regels maken          | Ja   | Nee        |
| Regels bijwerken          | Ja   | Nee        |
| Regels verwijderen          | Ja   | Nee        |
| Taken maken           | Ja   | Nee        |
| Sim-beheer bijwerken | Ja   | Nee        |

De gebruiker die de oplossing heeft geïmplementeerd, krijgt standaard automatisch de functie **Beheerder** toegewezen en is eigenaar van een Azure Active Directory-toepassings. Als eigenaar van een toepassing u rollen toewijzen aan andere gebruikers via de Azure-portal. Als u wilt dat een andere gebruiker rollen in de oplossing toewijst, moet deze ook worden ingesteld als eigenaar van de toepassing in de Azure-portal.

> [!NOTE]
> De gebruiker die de oplossing heeft geïmplementeerd, is de **enige persoon** die de oplossing direct na het maken kan bekijken. Als u anderen toegang wilt verlenen om de toepassing te bekijken als een alleen-lezen- of beheerder- of aangepaste rol, raadpleegt u de volgende aanwijzingen hieronder voor het toevoegen of verwijderen van gebruikers.

## <a name="add-or-remove-users"></a>Gebruikers toevoegen of verwijderen

Als eigenaar van een Azure Active Directory-toepassings u de Azure-portal gebruiken om een gebruiker toe te voegen of te verwijderen aan een rol uit de oplossing voor externe bewaking. In de volgende stappen wordt de [Azure Active Directory-bedrijfstoepassing](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) gebruikt die is gemaakt toen u de oplossing voor externe bewaking hebt geïmplementeerd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Controleer of de [gebruiker zich in de map](../active-directory/fundamentals/add-users-azure-active-directory.md) bevindt die u gebruikt. U hebt de map gekozen die u wilt gebruiken toen u zich aanmeldde bij de [Microsoft Azure IoT Solution Accelerators-site.](https://www.azureiotsolutions.com/Accelerators) De naam van de map is zichtbaar in de rechterbovenhoek van de [pagina.](https://www.azureiotsolutions.com/Accelerators)

1. Zoek de **Enterprise-toepassing** voor uw oplossing in de Azure-portal. Eenmaal daar filtert u de lijst door **Toepassingstype** in te stellen op **Alle toepassingen.** Zoek naar uw toepassing op toepassingsnaam. De naam van de toepassing is de naam van uw oplossing voor externe bewaking. In de volgende screenshot, de oplossing en toepassing display namen zijn **contoso-rm4**.

    ![Bedrijfstoepassing](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Controleer of u eigenaar bent van de toepassing door op de toepassing te klikken en vervolgens op **Eigenaren**te klikken. In de volgende screenshot, **Contoso admin** is een eigenaar van de **contoso-rm4** applicatie:

    ![Eigenaren](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Als u geen eigenaar bent, moet u een bestaande eigenaar vragen om u aan de lijst toe te voegen. Alleen eigenaren kunnen toepassingsrollen zoals **Beheerder** of **Alleen lezen** toewijzen aan andere gebruikers.

1. Als u de lijst met gebruikers wilt zien die zijn toegewezen aan rollen in de toepassing, klikt u op **Gebruikers en groepen**.

1. Als u een gebruiker wilt toevoegen, klikt u op **+ Gebruiker toevoegen**en klikt u vervolgens op Gebruikers en **groepen, Niet geselecteerd** om een gebruiker in de map te selecteren.

1. Als u de gebruiker aan een rol wilt toewijzen, klikt u op **Rol selecteren, Niet geselecteerd** en kiest u de rol **Beheerder** of **Alleen lezen** voor de gebruiker. Klik **op Selecteren**en klik vervolgens op **Toewijzen**.

    ![Rol selecteren](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. De gebruiker heeft nu toegang tot de oplossing voor externe bewaking met de machtigingen die door de rol zijn gedefinieerd.

1. U gebruikers verwijderen uit de toepassing op de pagina **Gebruikers en groepen** in de portal.

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

De oplossing Voor externe bewaking bevat de rollen **Beheerder** en **Alleen-lezen** wanneer deze voor het eerst wordt geïmplementeerd. U aangepaste rollen toevoegen met verschillende sets machtigingen. Als u een aangepaste rol wilt definiëren, moet u het:

- Voeg een nieuwe rol toe aan de toepassing in de Azure-portal.
- Definieer een beleid voor de nieuwe rol in de microservice Verificatie en autorisatie.
- De webgebruikersinterface van de oplossing bijwerken.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Een aangepaste rol definiëren in de Azure-portal

In de volgende stappen wordt beschreven hoe u een rol toevoegt aan een toepassing in Azure Active Directory. In dit voorbeeld maakt u een nieuwe rol waarmee leden apparaten kunnen maken, bijwerken en verwijderen in de oplossing voor externe bewaking.

1. Zoek de **app-registratie** voor uw oplossing in de Azure-portal. De naam van de toepassing is de naam van uw oplossing voor externe bewaking. In de volgende screenshot, de oplossing en toepassing display namen zijn **contoso-rm4**.

    ![App-registratie](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Selecteer uw toepassing en klik op **Manifest**. U de twee bestaande [app-rollen](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) zien die voor de toepassing zijn gedefinieerd:

    ![Manifest weergeven](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Bewerk het manifest om een rol toe te voegen die ManageDevices wordt **genoemd,** zoals in het volgende fragment wordt weergegeven. U hebt een unieke tekenreeks nodig, zoals een GUID voor de nieuwe rol-ID. U een nieuwe GUID genereren met behulp van een service zoals de [Online GUID Generator:](https://www.guidgenerator.com/)

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Sla de wijzigingen op.

### <a name="define-a-policy-for-the-new-role"></a>Een beleid voor de nieuwe rol definiëren

Nadat u de rol aan de app in de Azure-portal hebt toegevoegd, moet u een beleid definiëren in [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) voor de rol waarin de machtigingen worden toegevoegd die nodig zijn om apparaten te beheren.

1. Kloon de [Remote Monitoring Microservices](https://github.com/Azure/remote-monitoring-services-dotnet) repository van GitHub naar uw lokale machine.

1. Bewerk het **bestand auth/Services/data/policies/roles.json** om het beleid voor de rol **ManageDevices** toe te voegen zoals weergegeven in het volgende fragment. De **id-** en **rolwaarden** moeten overeenkomen met de roldefinitie in het app-manifest uit de vorige sectie. Met de lijst met toegestane acties kan iemand in de rol **ManageDevices** apparaten maken, bijwerken en verwijderen die met de oplossing zijn verbonden:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Wanneer u klaar bent met het bewerken van het bestand **Services/data/policies/roles.json,** u de microservice Verificatie en autorisatie opnieuw herstellen naar uw oplossingsversneller.

### <a name="how-the-web-ui-enforces-permissions"></a>Hoe de webgebruikersinterface machtigingen afdwingt

De webgebruikersinterface gebruikt de [microservice Verificatie en autorisatie](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) om te bepalen welke acties een gebruiker mag uitvoeren en welke besturingselementen zichtbaar zijn in de gebruikersinterface. Als uw oplossing bijvoorbeeld **contoso-rm4**wordt genoemd, haalt de web-gebruikersinterface een lijst met toegestane acties voor de huidige gebruiker op door het volgende verzoek te verzenden:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Voor een gebruiker die **Apparaatbeheer** heet in de rol **ManageDevices,** bevat het antwoord de volgende JSON in de hoofdtekst:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

In het volgende fragment van [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) in de [webgebruikersinterface](https://github.com/Azure/pcs-remote-monitoring-webui/) ziet u hoe de machtigingen declaratief worden afgedwongen:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Zie [Beveiligde componenten voor](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md)meer informatie . U aanvullende machtigingen definiëren in het [bestand authModel.js.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js)

### <a name="how-the-microservices-enforce-permissions"></a>Hoe de microservices machtigingen afdwingen

De microservices controleren ook machtigingen om te beschermen tegen ongeautoriseerde API-aanvragen. Wanneer een microservice een API-aanvraag ontvangt, wordt het JWT-token gedecodeerd en gevalideerd om de gebruikers-id en machtigingen die zijn gekoppeld aan de rol van de gebruiker te krijgen.

In het volgende fragment van het [DevicesController.cs-bestand](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) in de [IoTHub Manager-microservice](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)wordt weergegeven hoe de machtigingen worden afgedwongen:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe op rollen gebaseerde toegangscontroles worden geïmplementeerd in de versneller van de oplossing voor externe bewaking.

Zie [Toegangsbesturingselementen configureren voor de Time Series Insights-verkenner](iot-accelerators-remote-monitoring-rbac-tsi.md) voor informatie over het beheren van toegang tot de Time Series Insights-verkenner in de accelerator voor oplossingen voor externe bewaking.

Zie [Remote Monitoring-architectuur](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor meer conceptuele informatie over de remote monitoring-oplossingsversneller

Zie [Een microservice aanpassen en opnieuw implementeren](iot-accelerators-microservices-example.md) voor meer informatie over het aanpassen van de oplossing voor externe bewaking
<!-- Next tutorials in the sequence -->