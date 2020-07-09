---
title: Azure Container Registry als Event Grid bron
description: Hierin worden de eigenschappen beschreven die worden gegeven voor Container Registry gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d216fe88ee6aaad33fbbe3b93b8c4f8a6e952a71
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113714"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Azure Container Registry als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor Container Registry gebeurtenissen.Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's.

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Azure Container Registry worden de volgende gebeurtenis typen meeverzonden:

| Gebeurtenistype | Description |
| ---------- | ----------- |
| Micro soft. ContainerRegistry. ImagePushed | Deze gebeurtenis treedt op wanneer een afbeelding wordt gepusht. |
| Micro soft. ContainerRegistry. ImageDeleted | Deze gebeurtenis treedt op wanneer een afbeelding wordt verwijderd. |
| Micro soft. ContainerRegistry. ChartPushed | Deze gebeurtenis treedt op wanneer een helm-grafiek wordt gepusht. |
| Micro soft. ContainerRegistry. ChartDeleted | Deze gebeurtenis treedt op wanneer een helm-grafiek wordt verwijderd. |

### <a name="example-event"></a>Voorbeeld gebeurtenis

In het volgende voor beeld ziet u het schema van een gepushte gebeurtenis van een installatie kopie: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Het schema voor een verwijderde afbeeldings gebeurtenis is vergelijkbaar:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Het schema voor een gepushte gebeurtenis van een grafiek is vergelijkbaar met het schema voor een pushed gebeurtenis van een installatie kopie, maar bevat geen aanvraag object:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Het schema voor een verwijderde grafiek gebeurtenis is vergelijkbaar met het schema voor een verwijderde afbeeldings gebeurtenis, maar bevat geen aanvraag object:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| Type | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| gegevens | object | Gebeurtenis gegevens van Blob-opslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| id | tekenreeks | De gebeurtenis-ID. |
| tijdstempel | tekenreeks | Het tijdstip waarop de gebeurtenis heeft plaatsgevonden. |
| action | tekenreeks | De actie die de gegeven gebeurtenis omvat. |
| stemming | object | Het doel van de gebeurtenis. |
| schot | object | De aanvraag die de gebeurtenis heeft gegenereerd. |

Het doel object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| Type | tekenreeks | Het MIME-type van het object waarnaar wordt verwezen. |
| grootte | geheel getal | Het aantal bytes van de inhoud. Gelijk aan het veld lengte. |
| digest | tekenreeks | De samen vatting van de inhoud, zoals gedefinieerd in de HTTP API-specificatie van Regi ster v2. |
| lengte | geheel getal | Het aantal bytes van de inhoud. Gelijk aan het veld grootte. |
| opslag plaats | tekenreeks | De naam van de opslag plaats. |
| tag | tekenreeks | De naam van de tag. |
| naam | tekenreeks | De naam van de grafiek. |
| versie | tekenreeks | De grafiek versie. |

Het object Request heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| id | tekenreeks | De ID van de aanvraag die de gebeurtenis heeft gestart. |
| addr | tekenreeks | Het IP-adres of de hostnaam en mogelijk poort van de client verbinding die de gebeurtenis heeft gestart. Deze waarde is de RemoteAddr van de standaard-HTTP-aanvraag. |
| host | tekenreeks | De extern toegankelijke hostnaam van het register exemplaar, zoals opgegeven door de http-Host-header op binnenkomende aanvragen. |
| method | tekenreeks | De aanvraag methode waarmee de gebeurtenis is gegenereerd. |
| User agent | tekenreeks | De header van de gebruikers agent van de aanvraag. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel |Beschrijving  |
|---------|---------|
| [Snelstartgids: container register gebeurtenissen verzenden](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Container Registry-gebeurtenissen kunt verzenden. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Azure Event grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
