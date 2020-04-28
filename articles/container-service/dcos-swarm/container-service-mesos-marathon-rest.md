---
title: KEUR Een Azure DC/OS-cluster beheren met marathon REST API
description: Implementeer containers in een Azure Container Service DC/OS-cluster met behulp van de marathon REST API.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277787"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>KEUR DC/OS-container beheer via de marathon-REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS biedt een omgeving voor het implementeren en schalen van geclusterde werkbelastingen terwijl de onderliggende hardware wordt onttrokken. Op de DC/OS ligt een framework dat de planning en uitvoering van rekenworkloads regelt. Er zijn frameworks beschikbaar voor veel populaire werk belastingen, maar met dit document kunt u beginnen met het maken en schalen van container implementaties met behulp van de marathon-REST API. 

## <a name="prerequisites"></a>Vereisten

Voer het uitvoeren van deze voorbeelden hebt u een DC/OS-cluster nodig dat is geconfigureerd in Azure Container Service. U hebt ook een externe verbinding met dit cluster nodig. Zie de volgende artikelen voor meer informatie over deze items:

* [Een Azure Container Service-cluster implementeren](container-service-deployment.md)
* [Verbinding maken met een Azure Container Service-cluster](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Toegang tot de DC/OS-Api's
Nadat u verbinding hebt gemaakt met het Azure Container Service-cluster, kunt u toegang krijgen tot de DC/OS en gerelateerde REST\/api's via http:/localhost: Local-Port. In de voorbeelden in dit document wordt ervan uitgegaan dat u poort 80 gebruikt. De marathon-eind punten kunnen bijvoorbeeld worden bereikt op Uri's die beginnen met http:\//localhost/Marathon/v2/. 

Zie voor meer informatie over de verschillende API's de Mesosphere-documentatie voor de [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) en de [Chronos API](https://mesos.github.io/chronos/docs/api.html), en de Apache-documentatie voor de [Mesos Scheduler API](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Informatie verzamelen van DC/OS en Marathon
Voordat u containers implementeert in het DC/OS-cluster, moet u bepaalde informatie over het DC/OS-cluster verzamelen, zoals de namen en de status van de DC/OS-agents. U doet dit door een query uit te voeren op het `master/slaves`-eindpunt van de DC/OS REST API. Als alles goed gaat, wordt met de query een lijst geretourneerd van DC/OS-agents en de verschillende eigenschappen voor elke agent.

```bash
curl http://localhost/mesos/master/slaves
```

Gebruik nu het Marathon `/apps`-eindpunt om te controleren op huidige implementaties naar het DC/OS-cluster. Als dit een nieuw cluster is, ziet u een lege matrix voor apps.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Een met Docker ingedeelde container implementeren
U implementeert docker-geformatteerde containers via de marathon-REST API met behulp van een JSON-bestand dat de beoogde implementatie beschrijft. In het volgende voor beeld wordt een nginx-container geïmplementeerd naar een privé-agent in het cluster. 

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

Als u een container met docker-indeling wilt implementeren, moet u het JSON-bestand opslaan op een toegankelijke locatie. Voer vervolgens de volgende opdracht uit om de container te implementeren Geef de naam van het JSON-bestand`marathon.json` op (in dit voor beeld).

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

## <a name="reach-the-container"></a>De container bereiken

U kunt controleren of de nginx wordt uitgevoerd in een container op een van de particuliere agents in het cluster. Als u wilt zoeken naar de host en poort waarop de container wordt uitgevoerd, voert u een query uit op marathon voor de actieve taken: 

```bash
curl localhost/marathon/v2/tasks
```

Zoek de waarde van `host` in de uitvoer (een IP-adres dat `10.32.0.x`vergelijkbaar is met) en de `ports`waarde van.


Maak nu een SSH-terminal verbinding (geen tunnel verbinding) met de FQDN van het beheer van het cluster. Nadat de verbinding tot stand is gebracht, maakt u de volgende aanvraag, `host` waarbij `ports`u de juiste waarden vervangt door en:

```bash
curl http://host:ports
```

De nginx-server uitvoer is vergelijkbaar met het volgende:

![Nginx uit container](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Uw containers schalen
U kunt de marathon-API gebruiken om uit te schalen of te schalen in toepassings implementaties. In het vorige voorbeeld hebt u één exemplaar van een toepassing geïmplementeerd. Laten we dit uitschalen naar drie exemplaren van een toepassing. Dit doet u door een JSON-bestand te maken met behulp van de volgende JSON-tekst en dit op een toegankelijke locatie op te slaan.

```json
{ "instances": 3 }
```

Voer de volgende opdracht uit via een tunnel verbinding om de toepassing uit te breiden.

> [!NOTE]
> De URI is http:\//localhost/Marathon/v2/apps/gevolgd door de id van de toepassing die moet worden geschaald. Als u gebruikmaakt van het nginx-voor beeld dat hier wordt vermeld, is de URI http\/:/localhost/Marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Voer tenslotte een query voor toepassingen uit op het Marathon-eindpunt. U zult zien dat er nu drie Nginx-containers zijn.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Vergelijkbare PowerShell-opdrachten
U kunt dezelfde acties uitvoeren met behulp van PowerShell-opdrachten in een Windows-systeem.

Als u informatie wilt verzamelen over het DC/OS-cluster, zoals agent namen en agent status, voert u de volgende opdracht uit:

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

Als u een container met docker-indeling wilt implementeren, moet u het JSON-bestand opslaan op een toegankelijke locatie. Voer vervolgens de volgende opdracht uit om de container te implementeren Geef het pad op naar het JSON-`marathon.json` bestand (in dit voor beeld).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

U kunt ook de Marathon API gebruiken om in implementaties van toepassingen uit of in te schalen. In het vorige voorbeeld hebt u één exemplaar van een toepassing geïmplementeerd. Laten we dit uitschalen naar drie exemplaren van een toepassing. Dit doet u door een JSON-bestand te maken met behulp van de volgende JSON-tekst en dit op een toegankelijke locatie op te slaan.

```json
{ "instances": 3 }
```

Voer de volgende opdracht uit om de toepassing uit te breiden:

> [!NOTE]
> De URI is http:\//localhost/Marathon/v2/apps/gevolgd door de id van de toepassing die moet worden geschaald. Als u gebruikmaakt van het nginx-voor beeld dat hier wordt vermeld, is de\/URI http:/localhost/Marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over de Mesos HTTP-eind punten](https://mesos.apache.org/documentation/latest/endpoints/)
* [Meer informatie over de marathon-REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

