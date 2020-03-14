---
title: Functie definities in RBAC voor Azure-resources begrijpen | Microsoft Docs
description: Meer informatie over roldefinities in op rollen gebaseerd toegangs beheer (RBAC) voor het verfijnen van toegang tot Azure-resources.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 3ff4b2cb6a59a35dc6da4748a7c7fbb4758a4fcf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283223"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Functie definities voor Azure-resources begrijpen

Als u wilt weten hoe een rol werkt of als u uw eigen [aangepaste rol voor Azure-resources](custom-roles.md)maakt, is het handig om te begrijpen hoe rollen worden gedefinieerd. In dit artikel worden de details van roldefinities beschreven en vindt u enkele voor beelden.

## <a name="role-definition-structure"></a>Roldefinitie structuur

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

Bewerkingen worden opgegeven met teken reeksen die de volgende indeling hebben:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Het `{action}` gedeelte van een bewerkings reeks geeft u het type bewerkingen op dat u kunt uitvoeren op een resource type. U ziet bijvoorbeeld de volgende subtekenreeksen in `{action}`:

| Subtekenreeks van actie    | Beschrijving         |
| ------------------- | ------------------- |
| `*` | Het Joker teken verleent toegang tot alle bewerkingen die overeenkomen met de teken reeks. |
| `read` | Hiermee schakelt u lees bewerkingen in (GET). |
| `write` | Hiermee wordt schrijf bewerkingen (PUT of PATCH) ingeschakeld. |
| `action` | Hiermee schakelt u aangepaste bewerkingen in, zoals het opnieuw opstarten van virtuele machines (POST). |
| `delete` | Hiermee worden Verwijder bewerkingen (verwijderen) ingeschakeld. |

Hier volgt de rol van [Inzender](built-in-roles.md#contributor) in JSON-indeling. De Joker bewerking (`*`) onder `Actions` geeft aan dat de principal die aan deze rol is toegewezen, alle acties kan uitvoeren, of met andere woorden, het kan alles beheren. Dit omvat acties die in de toekomst zijn gedefinieerd, aangezien Azure nieuwe resource typen toevoegt. De bewerkingen onder `NotActions` worden afgetrokken van `Actions`. In het geval van de rol [Inzender](built-in-roles.md#contributor) verwijdert `NotActions` de mogelijkheid van deze functie om de toegang tot resources te beheren en ook toegang tot resources toe te wijzen.

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

## <a name="management-and-data-operations"></a>Beheer-en gegevens bewerkingen

Toegangs beheer op basis van rollen voor beheer bewerkingen is opgegeven in de eigenschappen `Actions` en `NotActions` van een roldefinitie. Hier volgen enkele voor beelden van beheer bewerkingen in Azure:

- Toegang tot een opslag account beheren
- Een BLOB-container maken, bijwerken of verwijderen
- Een resource groep en alle bijbehorende resources verwijderen

De toegang tot het beheer wordt niet overgenomen van uw gegevens omdat de container verificatie methode is ingesteld op ' Azure AD-gebruikers account ' en niet op ' toegangs sleutel '. Deze schei ding voor komt dat rollen met Joker tekens (`*`) onbeperkte toegang tot uw gegevens hebben. Als een gebruiker bijvoorbeeld een rol van [lezer](built-in-roles.md#reader) heeft voor een abonnement, kan deze het opslag account weer geven, maar standaard kunnen ze de onderliggende gegevens niet weer geven.

Voorheen werd op rollen gebaseerd toegangs beheer niet gebruikt voor gegevens bewerkingen. Autorisatie voor gegevens bewerkingen varieerden van alle resource providers. Hetzelfde op rollen gebaseerde toegangs beheer autorisatie model dat wordt gebruikt voor beheer bewerkingen, is uitgebreid naar gegevens bewerkingen.

Voor het ondersteunen van gegevens bewerkingen zijn er nieuwe gegevens eigenschappen aan de roldefinitie structuur toegevoegd. Gegevens bewerkingen worden opgegeven in de eigenschappen `DataActions` en `NotDataActions`. Door deze gegevens eigenschappen toe te voegen, wordt de schei ding tussen beheer en gegevens gehandhaafd. Dit voor komt dat huidige roltoewijzingen met Joker tekens (`*`) plotseling toegang hebben tot gegevens. Hier volgen enkele gegevens bewerkingen die kunnen worden opgegeven in `DataActions` en `NotDataActions`:

- Een lijst met blobs in een container lezen
- Een opslag-Blob in een container schrijven
- Een bericht in een wachtrij verwijderen

Hier volgt de definitie van de rol van [BLOB-gegevens lezer voor opslag](built-in-roles.md#storage-blob-data-reader) , die bewerkingen bevat in de eigenschappen `Actions` en `DataActions`. Met deze rol kunt u de BLOB-container en ook de onderliggende BLOB-gegevens lezen.

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

Alleen gegevens bewerkingen kunnen worden toegevoegd aan de eigenschappen `DataActions` en `NotDataActions`. Resource providers bepalen welke bewerkingen gegevens bewerkingen zijn, door de eigenschap `isDataAction` in te stellen op `true`. Zie voor een overzicht van de bewerkingen waarbij `isDataAction` is `true`de bewerkingen van de [resource provider](resource-provider-operations.md). Rollen die geen gegevens bewerkingen hebben, hoeven geen `DataActions`-en `NotDataActions`-eigenschappen te hebben binnen de roldefinitie.

Autorisatie voor alle beheer bewerkingen-API-aanroepen wordt afgehandeld door Azure Resource Manager. Autorisatie voor data Operation-API-aanroepen wordt verwerkt door een resource provider of Azure Resource Manager.

### <a name="data-operations-example"></a>Voor beeld van gegevens bewerkingen

Voor een beter inzicht in hoe beheer en gegevens bewerkingen werken, kunt u een specifiek voor beeld overwegen. Lisa is toegewezen aan de rol van [eigenaar](built-in-roles.md#owner) op het abonnements bereik. Bob is toegewezen aan de rol van de [blobgegevens van de gegevens opslag](built-in-roles.md#storage-blob-data-contributor) in een bereik van een opslag account. In het volgende diagram ziet u dit voor beeld.

![Toegangs beheer op basis van rollen is uitgebreid om zowel beheer-als gegevens bewerkingen te ondersteunen](./media/role-definitions/rbac-management-data.png)

De rol van [eigenaar](built-in-roles.md#owner) voor Alice en de rol [BLOB data contributor-gegevens](built-in-roles.md#storage-blob-data-contributor) voor Bob hebben de volgende acties:

Eigenaar

&nbsp;&nbsp;&nbsp;&nbsp;acties<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Inzender voor Storage BLOB-gegevens

&nbsp;&nbsp;&nbsp;&nbsp;acties<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Aangezien Anne een actie met Joker tekens (`*`) heeft op een abonnements bereik, worden de machtigingen ervan overgenomen zodat ze alle beheer acties kunnen uitvoeren. Anja kan containers lezen, schrijven en verwijderen. Anja kan echter geen gegevens bewerkingen uitvoeren zonder extra stappen te nemen. Zo kan Anja de blobs in een container bijvoorbeeld standaard niet lezen. Om de blobs te lezen, zou Anne de toegangs sleutels voor opslag moeten ophalen en gebruiken om toegang te krijgen tot de blobs.

De machtigingen van Dirk zijn beperkt tot alleen de `Actions` en `DataActions` die zijn opgegeven in de rol [BLOB data contributor](built-in-roles.md#storage-blob-data-contributor) van de opslag. Op basis van de rol kan Bob zowel beheer-als gegevens bewerkingen uitvoeren. Bob kan bijvoorbeeld containers in het opgegeven opslag account lezen, schrijven en verwijderen en kunnen ook de blobs lezen, schrijven en verwijderen.

Zie de [Azure Storage-beveiligings handleiding](../storage/blobs/security-recommendations.md)voor meer informatie over de beveiliging van beheer en gegevenslaag voor opslag.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Welke hulpprogram ma's ondersteunen het gebruik van RBAC voor gegevens bewerkingen?

Als u gegevens bewerkingen wilt bekijken en gebruiken, moet u beschikken over de juiste versies van de hulpprogram ma's of Sdk's:

| Hulpprogramma  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 of hoger |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 of hoger |
| [Azure voor .NET](/dotnet/azure/) | 2.8.0-Preview of hoger |
| [Azure SDK voor Go](/azure/go/azure-sdk-go-install) | 15.0.0 of hoger |
| [Azure voor Java](/java/azure/) | 1.9.0 of hoger |
| [Azure voor python](/azure/python/) | 0.40.0 of hoger |
| [Azure-SDK voor Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 of hoger |

Als u de gegevens bewerkingen in het REST API wilt bekijken en gebruiken, moet u de para meter van de **API-versie** instellen op de volgende versie of hoger:

- 2018-07-01

## <a name="actions"></a>Acties

Met de machtiging `Actions` geeft u de beheer bewerkingen op die door de functie kunnen worden uitgevoerd. Het is een verzameling bewerkings reeksen waarmee Beveilig bare bewerkingen van Azure-resource providers worden geïdentificeerd. Hier volgen enkele voor beelden van beheer bewerkingen die kunnen worden gebruikt in `Actions`.

| Bewerkings reeks    | Beschrijving         |
| ------------------- | ------------------- |
| `*/read` | Verleent toegang tot Lees bewerkingen voor alle resource typen van alle Azure-resource providers.|
| `Microsoft.Compute/*` | Verleent toegang tot alle bewerkingen voor alle resource typen in de micro soft. Compute-resource provider.|
| `Microsoft.Network/*/read` | Hiermee wordt toegang verleend tot Lees bewerkingen voor alle resource typen in de resource provider micro soft. Network.|
| `Microsoft.Compute/virtualMachines/*` | Verleent toegang tot alle bewerkingen van virtuele machines en de bijbehorende onderliggende resource typen.|
| `microsoft.web/sites/restart/Action` | Hiermee wordt toegang verleend om een web-app opnieuw op te starten.|

## <a name="notactions"></a>Intact

Met de machtiging `NotActions` geeft u de beheer bewerkingen op die zijn uitgesloten van de toegestane `Actions`. Gebruik de `NotActions` machtiging als de set bewerkingen die u wilt toestaan eenvoudiger is gedefinieerd door beperkte bewerkingen uit te sluiten. De toegang die wordt verleend door een rol (efficiënte machtigingen) wordt berekend door de `NotActions` bewerkingen uit te trekken van de `Actions` bewerkingen.

> [!NOTE]
> Als aan een gebruiker een rol is toegewezen waarmee een bewerking in `NotActions`wordt uitgesloten en aan een tweede rol wordt toegewezen die toegang verleent aan dezelfde bewerking, mag de gebruiker die bewerking uitvoeren. `NotActions` is geen regel voor weigeren: het is een handige manier om een reeks toegestane bewerkingen te maken wanneer specifieke bewerkingen moeten worden uitgesloten.
>

## <a name="dataactions"></a>DataActions

Met de machtiging `DataActions` geeft u de gegevens bewerkingen op die door de functie kunnen worden uitgevoerd op uw gegevens in dat object. Als een gebruiker bijvoorbeeld BLOB-gegevens toegang tot een opslag account heeft gelezen, kunnen ze de blobs binnen dat opslag account lezen. Hier volgen enkele voor beelden van gegevens bewerkingen die kunnen worden gebruikt in `DataActions`.

| Bewerkings reeks    | Beschrijving         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Retourneert een BLOB of een lijst met blobs. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Retourneert het resultaat van het schrijven van een blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Retourneert een bericht. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Retourneert een bericht of het resultaat van het schrijven of verwijderen van een bericht. |

## <a name="notdataactions"></a>NotDataActions

Met de machtiging `NotDataActions` geeft u de gegevens bewerkingen op die zijn uitgesloten van de toegestane `DataActions`. De toegang die wordt verleend door een rol (efficiënte machtigingen) wordt berekend door de `NotDataActions` bewerkingen uit te trekken van de `DataActions` bewerkingen. Elke resource provider biedt de respectieve set Api's om te voldoen aan gegevens bewerkingen.

> [!NOTE]
> Als aan een gebruiker een rol is toegewezen waarmee een gegevens bewerking in `NotDataActions`wordt uitgesloten en aan een tweede rol wordt toegewezen die toegang verleent tot dezelfde gegevens bewerking, mag de gebruiker die gegevens bewerking uitvoeren. `NotDataActions` is geen regel voor weigeren: het is een handige manier om een set toegestane gegevens bewerkingen te maken wanneer specifieke gegevens bewerkingen moeten worden uitgesloten.
>

## <a name="assignablescopes"></a>AssignableScopes

Met de eigenschap `AssignableScopes` geeft u de bereiken (beheer groepen, abonnementen, resource groepen of resources) op waarvoor deze functie definitie beschikbaar is. U kunt de rol beschikbaar maken voor toewijzing in alleen de beheer groepen, abonnementen of resource groepen die deze nodig hebben. U moet ten minste één beheer groep, abonnement, resource groep of Resource-ID gebruiken.

Voor ingebouwde rollen is `AssignableScopes` ingesteld op het hoofd bereik (`"/"`). Het hoofd bereik geeft aan dat de rol beschikbaar is voor toewijzing in alle bereiken. Voor beelden van geldige toewijs bare bereiken zijn:

| De rol is beschikbaar voor toewijzing | Voorbeeld |
|----------|---------|
| Eén abonnement | `"/subscriptions/{subscriptionId1}"` |
| Twee abonnementen | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
| Netwerk resource groep | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
| Eén beheer groep | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
| Beheer groep en een abonnement | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
| Alle bereiken (alleen van toepassing op ingebouwde rollen) | `"/"` |

Zie [aangepaste rollen voor Azure-resources](custom-roles.md)voor meer informatie over `AssignableScopes` voor aangepaste rollen.

## <a name="next-steps"></a>Volgende stappen

* [Ingebouwde rollen voor Azure-resources](built-in-roles.md)
* [Aangepaste rollen voor Azure-resources](custom-roles.md)
* [Bewerkingen voor de resource provider Azure Resource Manager](resource-provider-operations.md)
