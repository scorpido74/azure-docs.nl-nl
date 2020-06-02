---
title: Module dubbele JSON-schema-Azure
description: In dit onderwerp wordt het module dubbele JSON-schema van live video Analytics op IoT Edge beschreven.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a342c59b35c7ebb4b6021163da76bdd3e0d449c3
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266811"
---
# <a name="module-twin-json-schema"></a>Module dubbele JSON-schema

Apparaatdubbels zijn JSON-documenten die status informatie van een apparaat opslaan, inclusief meta gegevens, configuraties en voor waarden. Azure IoT Hub onderhoudt een apparaatdubbel voor elk apparaat dat u verbindt met IoT Hub. Zie voor gedetailleerde uitleg [begrijpen en apparaatdubbels module gebruiken in IOT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)

In dit onderwerp wordt het module dubbele JSON-schema van live video Analytics op IoT Edge beschreven.

> [!NOTE]
> Als u toegang wilt hebben tot Media Services resources en de Media Services-API, moet u eerst worden geverifieerd. Zie [toegang tot de Azure Media Services-API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api)voor meer informatie.

## <a name="module-twin-properties"></a>Dubbele eigenschappen van module

Met live video Analytics op IoT Edge worden de volgende twee eigenschappen van de module weer gegeven. 

|Eigenschap |Vereist |Dynamisch |Beschrijving |
|---|---|---|---|
|applicationDataDirectory |Yes |No |Pad naar een gekoppeld volume voor permanente configuratie. |
|azureMediaServicesArmId |Yes |No |Unieke id voor het beheer van Azure-resources voor het Media Services-account.|
|aadTenantId |Yes |No |Azure AD-Tenant-ID van klant.|
|aadServicePrincipalAppId |Ja |Ja |Klant heeft Azure AD AppId gemaakt.|
|aadServicePrincipalCertificate |Klikt<sup>*</sup>  |Yes |Klant heeft Azure AD AppId-certificaat gemaakt.|
|aadServicePrincipalPassword |Klikt<sup>*</sup>  |Yes |Klant heeft het Azure AD-toepassings-id-wacht woord gemaakt.|
|aadEndpoint |Nee |Nee |Cloud-specifiek Azure AD-eind punt. <br/>Prijs`https://login.microsoftonline.com` |
|aadResourceId |Nee |Nee |Cloud-specifieke Azure AD-doel groep/Resource-ID <br/>Prijs`https://management.core.windows.net/` |
|armEndpoint |Nee |Nee |Cloud-specifiek Azure resource Manage-eind punt. <br/>Prijs`https://management.azure.com/` |
|diagnosticsLevel |No |Yes |Uitgebreidere gebeurtenissen: <br/>Informatie & # x02758; Waarschuwing & # x02758; Fout & # x02758; Kritiek & # x02758; Geen |
|diagnosticsEventsOutputName |No |Yes |Hub-uitvoer voor diagnostische gebeurtenissen. <br/>(Leeg betekent dat er geen diagnostische gegevens worden gepubliceerd)|
|operationalEventsOutputName|No|Yes|Hub-uitvoer voor operationele gebeurtenissen.<br/>(Leeg betekent dat operationele gebeurtenissen niet worden gepubliceerd)
|logLevel|No|Yes|Een van de volgende producten: <br/>& # x000B7; Uitgebreide<br/>& # x000B7; Gegevens (standaard)<br/>& # x000B7; Waarschuwing<br/>& # x000B7; Optreedt<br/>& # x000B7; Geen|
|logCategories|No|Yes|Een door komma's gescheiden lijst met de volgende items: toepassing, MediaPipeline, gebeurtenissen <br/>Standaard: toepassing, gebeurtenissen|
|debugLogsDirectory|No|Yes|Map voor logboeken voor fout opsporing. Als er logboeken worden gegenereerd als er geen logboeken voor fout opsporing aanwezig zijn, worden deze uitgeschakeld.

<sup>*</sup>U moet een Service-Principal-certificaat of-wacht woord opgeven. 

Dynamische eigenschappen kunnen worden bijgewerkt zonder de module opnieuw op te starten. U kunt de waarden voor een aantal van deze eigenschappen verkrijgen door de instructie in het artikel [toegang tot Media Services-API verkrijgen te](../latest/access-api-cli-how-to.md) volgen. 

Zie het artikel over [bewaking en logboek registratie](monitoring-logging.md) voor meer informatie over de rol van de optionele Diagnostische instellingen.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Volgende stappen

[Directe methoden](direct-methods.md)
