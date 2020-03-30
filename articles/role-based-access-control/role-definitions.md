---
title: Roldefinities in RBAC voor Azure-resources begrijpen | Microsoft Documenten
description: Meer informatie over roldefinities in rbac (role-based access control) voor fijnkorrelig toegangsbeheer van Azure-resources.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: e4e4ac1b0a867130dd7b9e276db52e1ca1e72976
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062135"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Functiedefinities voor Azure-resources begrijpen

Als u probeert te begrijpen hoe een rol werkt of als u uw eigen [aangepaste rol voor Azure-resources maakt,](custom-roles.md)is het handig om te begrijpen hoe rollen worden gedefinieerd. In dit artikel worden de details van roldefinities beschreven en worden enkele voorbeelden gegeven.

## <a name="role-definition-structure"></a>Roldefinitiestructuur

Een *roldefinitie* is een verzameling machtigingen. Dit wordt soms gewoon een *rol* genoemd. Een roldefinitie beschijft de bewerkingen die kunnen worden uitgevoerd, zoals lezen, schrijven en verwijderen. Het kan ook een overzicht geven van de bewerkingen die niet kunnen worden uitgevoerd of bewerkingen met betrekking tot onderliggende gegevens. Een roldefinitie heeft de volgende structuur:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Bewerkingen worden opgegeven met tekenreeksen met de volgende indeling:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Het `{action}` gedeelte van een tekenreeks geeft het type bewerkingen aan dat u op een resourcetype uitvoeren. U ziet bijvoorbeeld de volgende subtekenreeksen in `{action}`:

| Subtekenreeks actie    | Beschrijving         |
| ------------------- | ------------------- |
| `*` | Het jokerteken geeft toegang tot alle bewerkingen die overeenkomen met de tekenreeks. |
| `read` | Hiermee u leesbewerkingen (GET) in- en uitlezen. |
| `write` | Hiermee u schrijfbewerkingen (PUT of PATCH) inmaken. |
| `action` | Hiermee u aangepaste bewerkingen uitvoeren, zoals virtuele machines opnieuw opstarten (POST). |
| `delete` | Hiermee u verwijderingsbewerkingen (Delete) inmaken. |

Hier is de [roldefinitie van medewerker](built-in-roles.md#contributor) in JSON-indeling. De jokerbewerking (`*`) onder `Actions` geeft aan dat de principal die aan deze rol is toegewezen alle acties kan uitvoeren, of met andere woorden, alles kan beheren. Dit omvat acties die in de toekomst zijn gedefinieerd, wanneer Azure nieuwe resourcetypen toevoegt. De bewerkingen onder `NotActions` worden afgetrokken van `Actions`. In het geval van de rol van [Lezer](built-in-roles.md#contributor) verwijdert `NotActions` de mogelijkheid van deze rol om de toegang tot resources te beheren en ook toegang tot resources toe te wijzen.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Beheer en gegevensactiviteiten

Op rollen gebaseerde toegangscontrole voor beheerbewerkingen wordt opgegeven in de `Actions` eigenschappen en `NotActions` eigenschappen van een roldefinitie. Hier volgen enkele voorbeelden van beheerbewerkingen in Azure:

- Toegang tot een opslagaccount beheren
- Een blobcontainer maken, bijwerken of verwijderen
- Een resourcegroep en al zijn bronnen verwijderen

Beheertoegang wordt niet overgenomen naar uw gegevens, mits de containerverificatiemethode is ingesteld op 'Azure AD-gebruikersaccount' en niet op 'Toegangssleutel'. Deze scheiding voorkomt dat`*`rollen met jokertekens ( ) onbeperkte toegang hebben tot uw gegevens. Als een gebruiker bijvoorbeeld een [Reader-rol](built-in-roles.md#reader) bij een abonnement heeft, kan hij het opslagaccount bekijken, maar standaard kunnen ze de onderliggende gegevens niet bekijken.

Voorheen werd op rollen gebaseerd toegangscontroleniet gebruikt voor gegevensbewerkingen. De autorisatie voor gegevensbewerkingen varieerde per resourceprovider. Hetzelfde op rollen gebaseerde toegangscontroleautorisatiemodel dat wordt gebruikt voor beheerbewerkingen, is uitgebreid tot gegevensbewerkingen.

Om gegevensbewerkingen te ondersteunen, zijn nieuwe gegevenseigenschappen toegevoegd aan de roldefinitiestructuur. Gegevensbewerkingen worden opgegeven in de eigenschappen `DataActions` en `NotDataActions`. Door deze gegevenseigenschappen toe te voegen, wordt de scheiding tussen beheer en gegevens gehandhaafd. Hiermee voorkomt u dat huidige roltoewijzingen met jokertekens (`*`) plotseling toegang hebben tot gegevens. Hier volgen enkele gegevensbewerkingen die kunnen worden opgegeven in `DataActions` en `NotDataActions`:

- Een lijst met blobs in een container lezen
- Een opslag-blob in een container schrijven
- Een bericht in een wachtrij verwijderen

Hier vindt u de roldefinitie van Storage Blob `Actions` Data `DataActions` [Reader,](built-in-roles.md#storage-blob-data-reader) die bewerkingen in zowel de eigenschappen als de eigenschappen bevat. Met deze rol u de blobcontainer en ook de onderliggende blobgegevens lezen.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Alleen gegevensbewerkingen kunnen worden toegevoegd aan de eigenschappen `DataActions` en `NotDataActions`. Resourceproviders bepalen welke bewerkingen gegevensbewerkingen zijn door de `isDataAction` eigenschap in te stellen op `true`. Zie Resourceprovideroperations als `isDataAction` u `true`een lijst wilt zien met de bewerkingen waar deze zich [bevindt.](resource-provider-operations.md) Rollen die geen gegevensbewerkinghebben, hoeven `DataActions` `NotDataActions` niet te beschikken en eigenschappen binnen de roldefinitie.

Autorisatie voor alle API-aanroepen voor beheerbewerking wordt beheerd door Azure Resource Manager. Autorisatie voor API-aanroepen voor gegevensbewerking wordt verwerkt door een resourceprovider of Azure Resource Manager.

### <a name="data-operations-example"></a>Voorbeeld van gegevensbewerkingen

Om beter te begrijpen hoe beheer- en gegevensbewerkingen werken, moeten we een specifiek voorbeeld overwegen. Alice heeft de rol [Eigenaar](built-in-roles.md#owner) toegewezen gekregen op het abonnementsbereik. Bob heeft de rol [Opslagblob-gegevensbijdrager](built-in-roles.md#storage-blob-data-contributor) toegewezen gekregen bij een opslagaccountbereik. In het volgende diagram ziet u dit voorbeeld.

![Rolgebaseerde toegangscontrole is uitgebreid om zowel beheer als gegevensactiviteiten te ondersteunen](./media/role-definitions/rbac-management-data.png)

De rol [Eigenaar](built-in-roles.md#owner) voor Alice en de rol [van opslagblobgegevensbijdrager](built-in-roles.md#storage-blob-data-contributor) voor Bob hebben de volgende acties:

Eigenaar

&nbsp;&nbsp;&nbsp;&nbsp;Acties<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Opslagblob-gegevensbijdrager

&nbsp;&nbsp;&nbsp;&nbsp;Acties<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Gegevensacties<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Aangezien Alice een`*`wildcard ( ) actie op een abonnement bereik heeft, hun machtigingen erven naar beneden om hen in staat stellen om alle beheeracties uit te voeren. Alice kan containers lezen, schrijven en verwijderen. Alice kan echter geen gegevensbewerkingen uitvoeren zonder extra stappen te ondernemen. Alice kan bijvoorbeeld standaard de blobs in een container niet lezen. Om de blobs te lezen, moet Alice de opslagtoegangssleutels ophalen en gebruiken om toegang te krijgen tot de blobs.

De machtigingen van Bob zijn `Actions` beperkt `DataActions` tot alleen de machtigingen en opgegeven in de rol [Opslagblobgegevensbijdrager.](built-in-roles.md#storage-blob-data-contributor) Op basis van de rol kan Bob zowel beheer- als databewerkingen uitvoeren. Bob kan bijvoorbeeld containers in het opgegeven opslagaccount lezen, schrijven en verwijderen en kan de blobs ook lezen, schrijven en verwijderen.

Zie de [beveiligingshandleiding azure storage](../storage/blobs/security-recommendations.md)voor meer informatie over beheer en beveiliging van gegevensvliegtuigen voor opslag.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Welke tools ondersteunen het gebruik van RBAC voor gegevensbewerkingen?

Als u gegevensbewerkingen wilt weergeven en ermee wilt werken, moet u over de juiste versies van de hulpprogramma's of SDK's beschikken:

| Hulpprogramma  | Versie  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 of hoger |
| [Azure-CLI](/cli/azure/install-azure-cli) | 2.0.30 of hoger |
| [Azure voor .NET](/dotnet/azure/) | 2.8.0-preview of hoger |
| [Azure SDK voor Go](/azure/go/azure-sdk-go-install) | 15.0.0 of hoger |
| [Azure voor Java](/java/azure/) | 1.9.0 of hoger |
| [Azure voor Python](/azure/python/) | 0.40.0 of hoger |
| [Azure-SDK voor Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 of hoger |

Als u de gegevensbewerkingen in de REST-API wilt weergeven en gebruiken, moet u de parameter **api-versie** instellen op de volgende versie of hoger:

- 2018-07-01

## <a name="actions"></a>Acties

De `Actions` machtiging geeft de beheerbewerkingen op die de rol toestaat om te worden uitgevoerd. Het is een verzameling van bewerkingen die securable bewerkingen van Azure-resourceproviders identificeren. Hier volgen enkele voorbeelden van beheerbewerkingen `Actions`die kunnen worden gebruikt in .

> [!div class="mx-tableFixed"]
> | Tekenreeks bewerking    | Beschrijving         |
> | ------------------- | ------------------- |
> | `*/read` | Verleent toegang tot leesbewerkingen voor alle resourcetypen van alle Azure-bronproviders.|
> | `Microsoft.Compute/*` | Verleent toegang tot alle bewerkingen voor alle resourcetypen in de Microsoft.Compute-resourceprovider.|
> | `Microsoft.Network/*/read` | Verleent toegang tot leesbewerkingen voor alle resourcetypen in de Microsoft.Network-bronprovider.|
> | `Microsoft.Compute/virtualMachines/*` | Verleent toegang tot alle bewerkingen van virtuele machines en de typen onderliggende bronnen.|
> | `microsoft.web/sites/restart/Action` | Verleent toegang tot het opnieuw opstarten van een web-app.|

## <a name="notactions"></a>NotActions

De `NotActions` machtiging geeft de beheerbewerkingen op `Actions`die zijn uitgesloten van de toegestane . Gebruik `NotActions` de machtiging als de set bewerkingen die u wilt toestaan, gemakkelijker kan worden gedefinieerd door beperkte bewerkingen uit te sluiten. De toegang die wordt verleend door een rol (effectieve `NotActions` machtigingen) `Actions` wordt berekend door de bewerkingen af te trekken van de bewerkingen.

> [!NOTE]
> Als een gebruiker een rol krijgt toegewezen `NotActions`die een bewerking uitsluit in , en een tweede rol krijgt toegewezen die toegang geeft tot dezelfde bewerking, mag de gebruiker die bewerking uitvoeren. `NotActions`is geen weigeringsregel – het is gewoon een handige manier om een set toegestane bewerkingen te maken wanneer specifieke bewerkingen moeten worden uitgesloten.
>

## <a name="dataactions"></a>Gegevensacties

De `DataActions` machtiging geeft de gegevensbewerkingen op die de rol toestaat om te worden uitgevoerd naar uw gegevens binnen dat object. Als een gebruiker bijvoorbeeld blobgegevenstoegang tot een opslagaccount heeft gelezen, kan deze de blobs in dat opslagaccount lezen. Hier volgen enkele voorbeelden van gegevensbewerkingen `DataActions`die kunnen worden gebruikt in .

> [!div class="mx-tableFixed"]
> | Tekenreeks bewerking    | Beschrijving         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Retourneert een blob of een lijst met blobs. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Geeft als resultaat het resultaat van het schrijven van een blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Retourneert een bericht. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Retourneert een bericht of het resultaat van het schrijven of verwijderen van een bericht. |

## <a name="notdataactions"></a>NietGegevensacties

De `NotDataActions` machtiging geeft de gegevensbewerkingen op `DataActions`die zijn uitgesloten van de toegestane . De toegang die wordt verleend door een rol (effectieve `NotDataActions` machtigingen) `DataActions` wordt berekend door de bewerkingen af te trekken van de bewerkingen. Elke resourceprovider biedt zijn respectievelijke set API's om gegevensbewerkingen uit te voeren.

> [!NOTE]
> Als een gebruiker een rol krijgt toegewezen `NotDataActions`die een gegevensbewerking uitsluit in , en een tweede rol krijgt toegewezen die toegang geeft tot dezelfde gegevensbewerking, mag de gebruiker die gegevensbewerking uitvoeren. `NotDataActions`is geen weigeringsregel – het is gewoon een handige manier om een set toegestane gegevensbewerkingen te maken wanneer specifieke gegevensbewerkingen moeten worden uitgesloten.
>

## <a name="assignablescopes"></a>AssignableScopes

De `AssignableScopes` eigenschap geeft de scopes (beheergroepen, abonnementen of resourcegroepen) op die deze roldefinitie beschikbaar hebben. U de rol alleen beschikbaar maken voor toewijzing in de beheergroepen, abonnementen of resourcegroepen die deze vereisen. U moet ten minste één beheergroep, abonnement of resourcegroep gebruiken.

Ingebouwde rollen `AssignableScopes` zijn ingesteld op`"/"`het hoofdbereik ( ). De hoofdmap geeft aan dat de rol beschikbaar is voor toewijzing in alle scopes. Voorbeelden van geldige toewijsbare scopes zijn:

> [!div class="mx-tableFixed"]
> | Rol is beschikbaar voor toewijzing | Voorbeeld |
> |----------|---------|
> | Eén abonnement | `"/subscriptions/{subscriptionId1}"` |
> | Twee abonnementen | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Netwerkbrongroep | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Eén managementgroep | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Managementgroep en een abonnement | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Alle scopes (geldt alleen voor ingebouwde rollen) | `"/"` |

Zie Aangepaste `AssignableScopes` rollen voor [Azure-resources voor](custom-roles.md)informatie over aangepaste rollen.

## <a name="next-steps"></a>Volgende stappen

* [Ingebouwde rollen voor Azure-resources](built-in-roles.md)
* [Aangepaste rollen voor Azure-resources](custom-roles.md)
* [Azure Resource Manager-resourceproviderbewerkingen](resource-provider-operations.md)
