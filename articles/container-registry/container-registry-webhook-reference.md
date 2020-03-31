---
title: Register webhook schema referentie
description: Referentie voor JSON-payload voor webhook-aanvragen in een Azure-containerregister, die worden gegenereerd wanneer webhooks zijn ingeschakeld voor artefactpush- of verwijdergebeurtenissen
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455976"
---
# <a name="azure-container-registry-webhook-reference"></a>Webhook-verwijzing naar Azure Container Registry

U [webhooks configureren](container-registry-webhook.md) voor uw containerregister die gebeurtenissen genereren wanneer bepaalde acties ertegen worden uitgevoerd. Schakel bijvoorbeeld webhooks in die worden geactiveerd wanneer een containerafbeelding of Helm-diagram naar een register wordt gepusht of wordt verwijderd. Wanneer een webhook wordt geactiveerd, geeft Azure Container Registry een HTTP- of HTTPS-verzoek met informatie over de gebeurtenis uit aan een eindpunt dat u opgeeft. Uw eindpunt kan dan de webhook verwerken en dienovereenkomstig handelen.

In de volgende secties wordt het schema beschreven van webhook-aanvragen die worden gegenereerd door ondersteunde gebeurtenissen. De gebeurtenissecties bevatten het payload-schema voor het gebeurtenistype, een voorbeeldaanvraagpayload en een of meer voorbeeldopdrachten die de webhook zouden activeren.

Zie [Webhooks](container-registry-webhook.md)gebruiken voor informatie over het configureren van webhooks voor uw Azure-containerregister.

## <a name="webhook-requests"></a>Webhook-verzoeken

### <a name="http-request"></a>HTTP-aanvraag

Een geactiveerde webhook `POST` maakt een HTTP-aanvraag naar het URL-eindpunt dat u hebt opgegeven toen u de webhook configureerde.

### <a name="http-headers"></a>HTTP-kopteksten

Webhook-verzoeken `Content-Type` bevatten `application/json` een van als `Content-Type` u geen aangepaste header voor uw webhook hebt opgegeven.

Er worden geen andere kopteksten aan de aanvraag toegevoegd die verder gaan dan de aangepaste kopteksten die u mogelijk voor de webhook hebt opgegeven.

## <a name="push-event"></a>Pushgebeurtenis

Webhook geactiveerd wanneer een containerafbeelding naar een opslagplaats wordt gepusht.

### <a name="push-event-payload"></a>Laadgebeurtenispayload pushen

|Element|Type|Beschrijving|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het webhook evenement.|
|`timestamp`|DateTime|Het moment waarop de webhook-gebeurtenis werd geactiveerd.|
|`action`|Tekenreeks|De actie die de webhook-gebeurtenis heeft geactiveerd.|
|[Doel](#target)|Complex Type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|
|[Verzoek](#request)|Complex Type|Het verzoek dat de webhook-gebeurtenis heeft gegenereerd.|

### <a name="target"></a><a name="target"></a>Doel

|Element|Type|Beschrijving|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`size`|Int32|Het aantal bytes van de inhoud. Hetzelfde als het veld Lengte.|
|`digest`|Tekenreeks|De samenvatting van de inhoud, zoals gedefinieerd in de Registry V2 HTTP API Specification.|
|`length`|Int32|Het aantal bytes van de inhoud. Hetzelfde als het veld Grootte.|
|`repository`|Tekenreeks|De naam van de opslagplaats.|
|`tag`|Tekenreeks|De naam van de afbeeldingstag.|

### <a name="request"></a><a name="request"></a>Verzoek

|Element|Type|Beschrijving|
|------------------|----------|-----------|
|`id`|Tekenreeks|De id van het verzoek dat de gebeurtenis heeft geïnitieerd.|
|`host`|Tekenreeks|De extern toegankelijke hostnaam van de registerinstantie, zoals opgegeven door de HTTP-hostheader bij binnenkomende aanvragen.|
|`method`|Tekenreeks|De aanvraagmethode die de gebeurtenis heeft gegenereerd.|
|`useragent`|Tekenreeks|De header van de gebruikersagent van de aanvraag.|

### <a name="payload-example-image-push-event"></a>Payload voorbeeld: image push gebeurtenis

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Voorbeeld Van de opdracht [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) die de webhook van de **afbeeldingspushgebeurtenis** activeert:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Grafiekpushgebeurtenis

Webhook geactiveerd wanneer een Helm grafiek wordt geduwd naar een repository.

### <a name="chart-push-event-payload"></a>Payload van grafiekpushgebeurtenis

|Element|Type|Beschrijving|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het webhook evenement.|
|`timestamp`|DateTime|Het moment waarop de webhook-gebeurtenis werd geactiveerd.|
|`action`|Tekenreeks|De actie die de webhook-gebeurtenis heeft geactiveerd.|
|[Doel](#helm_target)|Complex Type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|

### <a name="target"></a><a name="helm_target"></a>Doel

|Element|Type|Beschrijving|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`size`|Int32|Het aantal bytes van de inhoud.|
|`digest`|Tekenreeks|De samenvatting van de inhoud, zoals gedefinieerd in de Registry V2 HTTP API Specification.|
|`repository`|Tekenreeks|De naam van de opslagplaats.|
|`tag`|Tekenreeks|De naam van de grafiektag.|
|`name`|Tekenreeks|De naam van de grafiek.|
|`version`|Tekenreeks|De grafiekversie.|

### <a name="payload-example-chart-push-event"></a>Payload voorbeeld: grafiek push gebeurtenis

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Voorbeeld [van de](/cli/azure/acr) opdracht Azure CLI waarmee de **webhook van** de chart_push gebeurtenis wordt geactiveerd:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Gebeurtenis verwijderen

Webhook geactiveerd wanneer een afbeeldingsopslagplaats of -manifest wordt verwijderd. Niet geactiveerd wanneer een tag wordt verwijderd.

### <a name="delete-event-payload"></a>Gebeurtenispayload verwijderen

|Element|Type|Beschrijving|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het webhook evenement.|
|`timestamp`|DateTime|Het moment waarop de webhook-gebeurtenis werd geactiveerd.|
|`action`|Tekenreeks|De actie die de webhook-gebeurtenis heeft geactiveerd.|
|[Doel](#delete_target)|Complex Type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|
|[Verzoek](#delete_request)|Complex Type|Het verzoek dat de webhook-gebeurtenis heeft gegenereerd.|

### <a name="target"></a><a name="delete_target"></a>Doel

|Element|Type|Beschrijving|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`digest`|Tekenreeks|De samenvatting van de inhoud, zoals gedefinieerd in de Registry V2 HTTP API Specification.|
|`repository`|Tekenreeks|De naam van de opslagplaats.|

### <a name="request"></a><a name="delete_request"></a>Verzoek

|Element|Type|Beschrijving|
|------------------|----------|-----------|
|`id`|Tekenreeks|De id van het verzoek dat de gebeurtenis heeft geïnitieerd.|
|`host`|Tekenreeks|De extern toegankelijke hostnaam van de registerinstantie, zoals opgegeven door de HTTP-hostheader bij binnenkomende aanvragen.|
|`method`|Tekenreeks|De aanvraagmethode die de gebeurtenis heeft gegenereerd.|
|`useragent`|Tekenreeks|De header van de gebruikersagent van de aanvraag.|

### <a name="payload-example-image-delete-event"></a>Payload-voorbeeld: gebeurtenis voor het verwijderen van afbeeldingen

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Voorbeeld [van Azure CLI-opdrachten](/cli/azure/acr) die een webhook **voor verwijderen** activeren:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Gebeurtenis voor het verwijderen van grafieken

Webhook geactiveerd wanneer een Helm-grafiek of -opslagplaats wordt verwijderd. 

### <a name="chart-delete-event-payload"></a>Gebeurtenispayload voor het verwijderen van grafieken

|Element|Type|Beschrijving|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het webhook evenement.|
|`timestamp`|DateTime|Het moment waarop de webhook-gebeurtenis werd geactiveerd.|
|`action`|Tekenreeks|De actie die de webhook-gebeurtenis heeft geactiveerd.|
|[Doel](#chart_delete_target)|Complex Type|Het doel van de gebeurtenis die de gebeurtenis webhook heeft geactiveerd.|

### <a name="target"></a><a name="chart_delete_target"></a>Doel

|Element|Type|Beschrijving|
|------------------|----------|-----------|
|`mediaType`|Tekenreeks|Het MIME-type van het object waarnaar wordt verwezen.|
|`size`|Int32|Het aantal bytes van de inhoud.|
|`digest`|Tekenreeks|De samenvatting van de inhoud, zoals gedefinieerd in de Registry V2 HTTP API Specification.|
|`repository`|Tekenreeks|De naam van de opslagplaats.|
|`tag`|Tekenreeks|De naam van de grafiektag.|
|`name`|Tekenreeks|De naam van de grafiek.|
|`version`|Tekenreeks|De grafiekversie.|

### <a name="payload-example-chart-delete-event"></a>Payload-voorbeeld: gebeurtenis voor het verwijderen van grafieken

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Voorbeeld [van de](/cli/azure/acr) opdracht Azure CLI waarmee de **webhook van** de chart_delete gebeurtenis wordt geactiveerd:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Volgende stappen

[Webhooks van Azure Container Registry gebruiken](container-registry-webhook.md)