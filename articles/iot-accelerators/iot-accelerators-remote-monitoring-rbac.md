---
title: Toegangs beheer op afstand controleren-Azure | Microsoft Docs
description: Dit artikel bevat informatie over het configureren van op rollen gebaseerde toegangs beheer (RBAC) in de oplossings versneller voor externe controle
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 2774fc1374bf7fa3ed171258e8b1b51cfdb4b8b1
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612942"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Op rollen gebaseerde toegangs beheer configureren in de oplossings versneller voor externe controle

Dit artikel bevat informatie over het configureren van op rollen gebaseerde toegangs beheer in de oplossings versneller voor externe controle. Met toegangs beheer op basis van rollen kunt u de toegang voor afzonderlijke gebruikers beperken tot specifieke functies in de oplossing.

## <a name="default-settings"></a>Standaardinstellingen

Wanneer u de oplossing voor externe controle voor het eerst implementeert, zijn er twee rollen: **beheerder** en **alleen-lezen**.

Alle gebruikers in de rol **beheerder** hebben volledige toegang tot de oplossing, met inbegrip van de volgende machtigingen hieronder. Een gebruiker met de **alleen-lezen** rol heeft alleen toegang tot het weer geven van de oplossing.

| Machtiging            | Beheerder | Alleen-lezen |
|----------------       |-------|-----------|
| Oplossing weer geven         | Ja   | Ja       |
| Waarschuwingen bijwerken         | Ja   | Nee        |
| Waarschuwingen verwijderen         | Ja   | Nee        |
| Apparaten maken        | Ja   | Nee        |
| Apparaten bijwerken        | Ja   | Nee        |
| Apparaten verwijderen        | Ja   | Nee        |
| Apparaatgroepen maken  | Ja   | Nee        |
| Apparaatgroepen bijwerken  | Ja   | Nee        |
| Apparaatgroepen verwijderen  | Ja   | Nee        |
| Regels maken          | Ja   | Nee        |
| Update regels          | Ja   | Nee        |
| Regels verwijderen          | Ja   | Nee        |
| Taken maken           | Ja   | Nee        |
| SIM-beheer bijwerken | Ja   | Nee        |

De gebruiker die de oplossing heeft geïmplementeerd, krijgt standaard automatisch de rol **admin** toegewezen en is een Azure Active Directory eigenaar van de toepassing. Als eigenaar van een toepassing kunt u rollen toewijzen aan andere gebruikers via de Azure Portal. Als u wilt dat een andere gebruiker rollen toewijst in de oplossing, moeten deze ook worden ingesteld als eigenaar van een toepassing in de Azure Portal.

> [!NOTE]
> De gebruiker die de oplossing heeft geïmplementeerd, is de **enige persoon** die deze kan weer geven direct nadat deze is gemaakt. Als u anderen toegang wilt verlenen om de toepassing weer te geven als een alleen-lezen, beheerder of een aangepaste rol, raadpleegt u de onderstaande instructies bij gebruikers toevoegen of verwijderen.

## <a name="add-or-remove-users"></a>Gebruikers toevoegen of verwijderen

Als Azure Active Directory eigenaar van de toepassing kunt u de Azure Portal gebruiken om een gebruiker toe te voegen aan of te verwijderen uit de oplossing voor externe controle. In de volgende stappen wordt gebruikgemaakt van de [Azure Active Directory Enter prise-toepassing](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) die is gemaakt tijdens de implementatie van de oplossing voor externe controle.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Controleer of de [gebruiker zich in de map bevindt](../active-directory/fundamentals/add-users-azure-active-directory.md) die u gebruikt. U hebt gekozen voor de map die u wilt gebruiken wanneer u zich aanmeldt bij de [Microsoft Azure IOT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) -site. De mapnaam wordt weer gegeven in de rechter bovenhoek van de [pagina](https://www.azureiotsolutions.com/Accelerators).

1. Zoek de **bedrijfs toepassing** voor uw oplossing in de Azure Portal. Daarna kunt u de lijst filteren door **toepassings type** in te stellen op **alle toepassingen**. Zoek naar uw toepassing op toepassings naam. De naam van de toepassing is de naam van uw oplossing voor externe controle. In de volgende scherm afbeelding zijn de weergave namen van de oplossing en toepassing **Contoso-RM4**.

    ![Bedrijfs toepassing](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Controleer of u de eigenaar van de toepassing bent door te klikken op de toepassing en vervolgens te klikken op **eigen aren**. In de volgende scherm afbeelding is **Contoso-beheerder** een eigenaar van de toepassing **Contoso-RM4** :

    ![Eigenaren](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Als u geen eigenaar bent, moet u een bestaande eigenaar vragen om u toe te voegen aan de lijst. Alleen eigen aars kunnen toepassings rollen toewijzen, zoals **beheerder** of **alleen-lezen** aan andere gebruikers.

1. Klik op **gebruikers en groepen**voor een overzicht van de gebruikers die zijn toegewezen aan de rollen in de toepassing.

1. Als u een gebruiker wilt toevoegen, klikt u op **+ gebruiker toevoegen**en klikt u vervolgens op **gebruikers en groepen, geen geselecteerd** om een gebruiker te selecteren in de map.

1. Als u de gebruiker aan een rol wilt toewijzen, klikt u op **rol selecteren, geen geselecteerd** en kiest u de rol **beheerder** of **alleen-lezen** voor de gebruiker. Klik op **selecteren**en vervolgens op **toewijzen**.

    ![Rol selecteren](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. De gebruiker heeft nu toegang tot de oplossing voor externe controle met de machtigingen die zijn gedefinieerd door de rol.

1. U kunt gebruikers uit de toepassing verwijderen op de pagina **gebruikers en groepen** in de portal.

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

De oplossing voor controle op afstand bevat de rollen **beheerder** en **alleen lezen** wanneer deze voor het eerst wordt geïmplementeerd. U kunt aangepaste rollen toevoegen met verschillende sets machtigingen. Als u een aangepaste rol wilt definiëren, moet u het volgende doen:

- Voeg een nieuwe rol toe aan de toepassing in de Azure Portal.
- Definieer een beleid voor de nieuwe rol in de micro service verificatie en autorisatie.
- Update de Web-UI van de oplossing.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Een aangepaste rol definiëren in de Azure Portal

In de volgende stappen wordt beschreven hoe u een rol kunt toevoegen aan een toepassing in Azure Active Directory. In dit voor beeld maakt u een nieuwe rol die leden in staat stelt om apparaten te maken, bij te werken en te verwijderen in de oplossing voor controle op afstand.

1. Zoek de **app-registratie** voor uw oplossing in de Azure Portal. De naam van de toepassing is de naam van uw oplossing voor externe controle. In de volgende scherm afbeelding zijn de weergave namen van de oplossing en toepassing **Contoso-RM4**.

    ![App-registratie](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Selecteer uw toepassing en klik vervolgens op **manifest**. U kunt de twee bestaande [app-rollen](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) zien die zijn gedefinieerd voor de toepassing:

    ![Manifest weer geven](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Bewerk het manifest om een functie met de naam **ManageDevices** toe te voegen, zoals wordt weer gegeven in het volgende code fragment. U hebt een unieke teken reeks nodig, zoals een GUID voor de nieuwe functie-ID. U kunt een nieuwe GUID genereren met behulp van een service zoals de [online GUID-Generator](https://www.guidgenerator.com/):

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

Nadat u de rol aan de app hebt toegevoegd in de Azure Portal, moet u een beleid definiëren in [rollen. json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) voor de rol die de benodigde machtigingen voor het beheren van apparaten toewijst.

1. Kopieer de micro Services-opslag plaats voor [externe controle](https://github.com/Azure/remote-monitoring-services-dotnet) van github naar uw lokale computer.

1. Bewerk het bestand **auth/services/Data/policies/roles. json** om het beleid toe te voegen aan de **ManageDevices** -functie, zoals wordt weer gegeven in het volgende code fragment. De **id-** waarden en **rolinstellingen** moeten overeenkomen met de roldefinitie in het app-manifest uit de vorige sectie. Met de lijst met toegestane acties kan iemand in de **ManageDevices** -rol apparaten maken, bijwerken en verwijderen die zijn verbonden met de oplossing:

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

1. Wanneer u klaar bent met het bewerken van het bestand **Services/Data/policies/roles. json** , bouwt u de micro service voor verificatie en autorisatie opnieuw en implementeert u deze in uw oplossings versneller.

### <a name="how-the-web-ui-enforces-permissions"></a>Hoe de Web-UI machtigingen afdwingt

De Web-UI gebruikt de micro [service verificatie en autorisatie](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) om te bepalen welke acties een gebruiker mag ondernemen en welke besturings elementen zichtbaar zijn in de gebruikers interface. Als uw oplossing bijvoorbeeld **Contoso-RM4**heet, haalt de Web-UI een lijst met toegestane acties voor de huidige gebruiker op door de volgende aanvraag te verzenden:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Voor een gebruiker met de naam **Apparaatbeheer** in de rol **ManageDevices** bevat het antwoord de volgende json in de hoofd tekst:

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

In het volgende code fragment van [deviceDelete. js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) in de [Web-UI](https://github.com/Azure/pcs-remote-monitoring-webui/) ziet u hoe de machtigingen declaratief worden afgedwongen:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Zie [beveiligde onderdelen](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md)voor meer informatie. U kunt aanvullende machtigingen definiëren in het bestand [authModel. js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) .

### <a name="how-the-microservices-enforce-permissions"></a>De manier waarop de micro Services machtigingen afdwingen

De micro Services controleren ook de machtigingen om te beveiligen tegen ongeautoriseerde API-aanvragen. Wanneer een micro service een API-aanvraag ontvangt, wordt de JWT-token gedecodeerd en gevalideerd om de gebruikers-ID en de machtigingen voor de rol van de gebruiker op te halen.

In het volgende code fragment uit het [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) -bestand in de [IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)-micro service ziet u hoe de machtigingen worden afgedwongen:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe toegangs beheer op basis van rollen wordt geïmplementeerd in de oplossings versneller voor externe controle.

Zie [toegangs beheer configureren voor de time series Insights Explorer](iot-accelerators-remote-monitoring-rbac-tsi.md) voor informatie over het beheren van toegang tot de time series Insights Verkenner in de oplossing voor externe controle.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor meer conceptuele informatie over de oplossings versneller voor externe controle

Zie [een micro service aanpassen en opnieuw implementeren](iot-accelerators-microservices-example.md) voor meer informatie over het aanpassen van de oplossing voor controle op afstand.
<!-- Next tutorials in the sequence -->