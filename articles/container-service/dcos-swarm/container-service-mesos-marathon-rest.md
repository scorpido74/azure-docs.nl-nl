---
title: (AFGESCHAFT) Azure DC/OS-cluster beheren met Marathon REST API
description: Containers implementeren in een Azure Container Service DC/OS-cluster met behulp van de Marathon REST API.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277787"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(AFGESCHAFT) DC/OS containerbeheer via de Marathon REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS biedt een omgeving voor het implementeren en schalen van geclusterde werkbelastingen terwijl de onderliggende hardware wordt onttrokken. Op de DC/OS ligt een framework dat de planning en uitvoering van rekenworkloads regelt. Hoewel frameworks beschikbaar zijn voor veel populaire workloads, u met dit document containerimplementaties maken en schalen met behulp van de Marathon REST API. 

## <a name="prerequisites"></a>Vereisten

Voer het uitvoeren van deze voorbeelden hebt u een DC/OS-cluster nodig dat is geconfigureerd in Azure Container Service. U hebt ook een externe verbinding met dit cluster nodig. Zie de volgende artikelen voor meer informatie over deze items:

* [Een Azure Container Service-cluster implementeren](container-service-deployment.md)
* [Verbinding maken met een Azure Container Service-cluster](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Toegang tot de DC/OS API's
Nadat u bent verbonden met het Azure Container Service-cluster, hebt u toegang\/tot de DC/OS en gerelateerde REST-API's via http: /localhost:local-port. In de voorbeelden in dit document wordt ervan uitgegaan dat u poort 80 gebruikt. De eindpunten van de marathon zijn bijvoorbeeld te\/bereiken bij URI's vanaf http: /localhost/marathon/v2/. 

Zie voor meer informatie over de verschillende API's de Mesosphere-documentatie voor de [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) en de [Chronos API](https://mesos.github.io/chronos/docs/api.html), en de Apache-documentatie voor de [Mesos Scheduler API](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Informatie verzamelen van DC/OS en Marathon
Voordat u containers implementeert in het DC/OS-cluster, verzamelt u bepaalde informatie over het DC/OS-cluster, zoals de namen en status van de DC/OS-agents. U doet dit door een query uit te voeren op het `master/slaves`-eindpunt van de DC/OS REST API. Als alles goed gaat, wordt met de query een lijst geretourneerd van DC/OS-agents en de verschillende eigenschappen voor elke agent.

```bash
curl http://localhost/mesos/master/slaves
```

Gebruik nu het Marathon `/apps`-eindpunt om te controleren op huidige implementaties naar het DC/OS-cluster. Als dit een nieuw cluster is, ziet u een lege matrix voor apps.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Een met Docker ingedeelde container implementeren
U implementeert containers met Docker-indeling via de Marathon REST API met behulp van een JSON-bestand dat de beoogde implementatie beschrijft. In het volgende voorbeeld wordt een Nginx-container geïmplementeerd in een privé-agent in het cluster. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Als u een container met Docker-indeling wilt implementeren, slaat u het JSON-bestand op een toegankelijke locatie op. Voer vervolgens de volgende opdracht uit om de container te implementeren Geef de naam van het`marathon.json` JSON-bestand op (in dit voorbeeld).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

De uitvoer lijkt op het volgende:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Als u in Marathon toepassingen opvraagt via een query, wordt deze nieuwe toepassing in de uitvoer weergegeven.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Bereik de container

U controleren of de Nginx in een container wordt uitgevoerd op een van de privéagents in het cluster. Als u de host en poort wilt vinden waar de container wordt uitgevoerd, zoekt u Marathon op voor de lopende taken: 

```bash
curl localhost/marathon/v2/tasks
```

Zoek de `host` waarde van in de uitvoer `10.32.0.x`(een IP-adres vergelijkbaar met ) en de waarde van `ports`.


Maak nu een SSH-terminalverbinding (geen getunnelde verbinding) met het beheer FQDN van het cluster. Zodra aangesloten, het volgende verzoek, ter `host` vervanging `ports`van de juiste waarden van en :

```bash
curl http://host:ports
```

De Nginx-serveruitvoer is vergelijkbaar met de volgende:

![Nginx van container](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Uw containers schalen
U de Marathon API gebruiken om toepassingsimplementaties uit te schalen of te schalen. In het vorige voorbeeld hebt u één exemplaar van een toepassing geïmplementeerd. Laten we dit uitschalen naar drie exemplaren van een toepassing. Dit doet u door een JSON-bestand te maken met behulp van de volgende JSON-tekst en dit op een toegankelijke locatie op te slaan.

```json
{ "instances": 3 }
```

Voer vanaf uw verbinding met een tunnel de volgende opdracht uit om de toepassing uit te schalen.

> [!NOTE]
> De URI is\/http: /localhost/marathon/v2/apps/ gevolgd door de ID van de applicatie om te schalen. Als u de Nginx monster gebruikt dat hier wordt\/geleverd, zou de URI http zijn: /localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Voer tenslotte een query voor toepassingen uit op het Marathon-eindpunt. U zult zien dat er nu drie Nginx-containers zijn.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Vergelijkbare PowerShell-opdrachten
U kunt dezelfde acties uitvoeren met behulp van PowerShell-opdrachten in een Windows-systeem.

Voer de volgende opdracht uit om informatie over het DC/OS-cluster te verzamelen, zoals agentnamen en agentstatus:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

U implementeert met Docker ingedeelde containers via Marathon met behulp van een JSON-bestand waarin het doel van de implementatie wordt beschreven. In het volgende voorbeeld wordt de Nginx-container geïmplementeerd, met een binding van poort 80 van de DC/OS-agent naar poort 80 van de container.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Als u een container met Docker-indeling wilt implementeren, slaat u het JSON-bestand op een toegankelijke locatie op. Voer vervolgens de volgende opdracht uit om de container te implementeren Geef het pad naar het`marathon.json` JSON-bestand op (in dit voorbeeld).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

U kunt ook de Marathon API gebruiken om in implementaties van toepassingen uit of in te schalen. In het vorige voorbeeld hebt u één exemplaar van een toepassing geïmplementeerd. Laten we dit uitschalen naar drie exemplaren van een toepassing. Dit doet u door een JSON-bestand te maken met behulp van de volgende JSON-tekst en dit op een toegankelijke locatie op te slaan.

```json
{ "instances": 3 }
```

Voer de volgende opdracht uit om de toepassing uit te schalen:

> [!NOTE]
> De URI is\/http: /localhost/marathon/v2/apps/ gevolgd door de ID van de applicatie om te schalen. Als u gebruik maakt van de Nginx monster\/hier, zou de URI zijn http: / /localhost / marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Volgende stappen
* [Lees meer over de Mesos HTTP-eindpunten](https://mesos.apache.org/documentation/latest/endpoints/)
* [Lees meer over de Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

