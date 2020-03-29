---
title: Gereedheidsssonde instellen op containerinstantie
description: Meer informatie over het configureren van een sonde om ervoor te zorgen dat containers in Azure Container Instances alleen aanvragen ontvangen wanneer ze gereed zijn
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935685"
---
# <a name="configure-readiness-probes"></a>Gereedheidstests configureren

Voor containertoepassingen die verkeer weergeven, u controleren of uw container klaar is om binnenkomende aanvragen af te handelen. Azure Container Instances ondersteunt gereedheidssssssonderzoeken om configuraties op te nemen, zodat uw container onder bepaalde voorwaarden niet toegankelijk is. De gereedheidsssssonde gedraagt zich als een [Kubernetes gereedheidsssssonde.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) Een container-app moet bijvoorbeeld een grote gegevensset laden tijdens het opstarten en u wilt niet dat deze gedurende deze periode aanvragen ontvangt.

In dit artikel wordt uitgelegd hoe u een containergroep implementeert die een gereedheidssssonde bevat, zodat een container alleen verkeer ontvangt wanneer de sonde slaagt.

Azure Container Instances ondersteunt ook [liveness probes](container-instances-liveness-probe.md), die u configureren om ervoor te zorgen dat een ongezonde container automatisch opnieuw wordt opgestart.

> [!NOTE]
> Momenteel u geen gereedheidsssssonde gebruiken in een containergroep die is geïmplementeerd in een virtueel netwerk.

## <a name="yaml-configuration"></a>YAML-configuratie

Maak bijvoorbeeld een `readiness-probe.yaml` bestand met het volgende fragment met een gereedheidsssssonde. Dit bestand definieert een containergroep die bestaat uit een container met een kleine web-app. De app wordt geïmplementeerd `mcr.microsoft.com/azuredocs/aci-helloworld` vanuit de openbare afbeelding. Deze containerapp wordt ook gedemonstreerd in [Een containerinstantie implementeren in Azure met behulp van de Azure CLI](container-instances-quickstart.md) en andere quickstarts.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Start, opdracht

De implementatie `command` bevat een eigenschap die een startopdracht definieert die wordt uitgevoerd wanneer de container voor het eerst wordt uitgevoerd. Deze eigenschap accepteert een scala aan tekenreeksen. Met deze opdracht wordt een tijd gesimuleerd waarop de web-app wordt uitgevoerd, maar de container nog niet klaar is. 

Eerst wordt een shell-sessie `node` gestart en wordt een opdracht uitgevoerd om de web-app te starten. Het start ook een opdracht om te slapen voor 240 seconden, waarna het maakt een bestand genaamd `ready` in de `/tmp` directory:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Gereedheid, opdracht

Dit YAML-bestand `readinessProbe` definieert `exec` een bestand dat een gereedheidsopdracht ondersteunt die fungeert als de gereedheidscontrole. Met dit voorbeeld wordt de opdracht `ready` Gereedheid getest op het bestaan van het bestand in de `/tmp` map.

Wanneer `ready` het bestand niet bestaat, wordt de opdracht Gereedheid afgesloten met een waarde die niet nul is. de container blijft draaien, maar is niet toegankelijk. Wanneer de opdracht met exitcode 0 wordt afgesloten, is de container klaar om te worden geopend. 

De `periodSeconds` eigenschap wijst de gereedheidsopdracht aan die elke 5 seconden moet worden uitgevoerd. De gereedheidssssssonde loopt gedurende de levensduur van de containergroep.

## <a name="example-deployment"></a>Voorbeeldimplementatie

Voer de volgende opdracht uit om een containergroep met de vorige YAML-configuratie te implementeren:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Gereedheidscontroles weergeven

In dit voorbeeld mislukt tijdens de eerste 240 seconden de `ready` opdracht gereedheid wanneer deze controleert op het bestaan van het bestand. De statuscode geeft aan dat de container nog niet klaar is.

Deze gebeurtenissen kunnen worden bekeken vanuit de Azure-portal of Azure CLI. De portal geeft bijvoorbeeld aan `Unhealthy` dat gebeurtenissen van het type worden geactiveerd wanneer de opdracht gereedheid mislukt. 

![Portal ongezonde gebeurtenis][portal-unhealthy]

## <a name="verify-container-readiness"></a>Gereedheid voor containers controleren

Nadat u de container hebt gestart, u controleren of deze in eerste instantie niet toegankelijk is. Na inlevering krijgt u het IP-adres van de containergroep:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Probeer toegang te krijgen tot de site terwijl de gereedheidssssonde mislukt:

```bash
wget <ipAddress>
```

Uit de uitvoer wordt weergegeven dat de site in eerste instantie niet toegankelijk is:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Na 240 seconden slaagt de gereedheidsopdracht en geeft de container een seintje. Wanneer u de `wget` opdracht uitvoert, slaagt deze nu:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Wanneer de container klaar is, u ook toegang krijgen tot de web-app door te bladeren naar het IP-adres met behulp van een webbrowser.

> [!NOTE]
> De gereedheidssssssonde blijft gedurende de levensduur van de containergroep draaien. Als de opdracht gereedheid op een later tijdstip mislukt, wordt de container opnieuw ontoegankelijk. 
> 

## <a name="next-steps"></a>Volgende stappen

Een gereedheidsssssonde kan nuttig zijn in scenario's met meerdere containergroepen die bestaan uit afhankelijke containers. Zie [Containergroepen in Azure Container Instances](container-instances-container-groups.md)voor meer informatie over scenario's met meerdere containers.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
