---
title: Liveness-sonde instellen op containerinstantie
description: Meer informatie over het configureren van liveness probes om ongezonde containers opnieuw op te starten in Azure Container Instances
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934166"
---
# <a name="configure-liveness-probes"></a>Activiteitstests configureren

Gecontaineriseerde toepassingen kunnen gedurende langere tijd worden uitgevoerd, wat resulteert in verbroken toestanden die mogelijk moeten worden gerepareerd door de container opnieuw op te starten. Azure Container Instances ondersteunt liveness probes, zodat u uw containers binnen uw containergroep configureren om opnieuw te starten als kritieke functionaliteit niet werkt. De levendigheidssonde gedraagt zich als een [Kubernetes levendigheidsssonde.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

In dit artikel wordt uitgelegd hoe u een containergroep implementeert die een levendigheidsssdetectie bevat, waarbij de automatische herstart van een gesimuleerde ongezonde container wordt aangetoond.

Azure Container Instances ondersteunt ook [gereedheidssssssondes,](container-instances-readiness-probe.md)die u configureren om ervoor te zorgen dat het verkeer een container alleen bereikt wanneer deze er klaar voor is.

> [!NOTE]
> Momenteel u geen liveness-sonde gebruiken in een containergroep die is geïmplementeerd in een virtueel netwerk.

## <a name="yaml-deployment"></a>YAML-implementatie

Maak `liveness-probe.yaml` een bestand met het volgende fragment. Dit bestand definieert een containergroep die bestaat uit een NGNIX-container die uiteindelijk ongezond wordt.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Voer de volgende opdracht uit om deze containergroep te implementeren met de bovenstaande YAML-configuratie:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Start, opdracht

De implementatie `command` bevat een eigenschap die een startopdracht definieert die wordt uitgevoerd wanneer de container voor het eerst wordt uitgevoerd. Deze eigenschap accepteert een scala aan tekenreeksen. Met deze opdracht wordt gesimuleerd dat de container een ongezonde status invoert.

Eerst begint het een bashsessie en `healthy` maakt `/tmp` een bestand dat in de map wordt aangeroepen. Het slaapt dan gedurende 30 seconden voor het verwijderen van het bestand, dan voert een 10-minuten slaap:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Levendigheid, opdracht

Deze implementatie `livenessProbe` definieert `exec` een opdracht die een levendigheidsopdracht ondersteunt die fungeert als de liveness-controle. Als deze opdracht wordt afgesloten met een niet-nulwaarde, wordt `healthy` de container gedood en opnieuw gestart, waardoor het bestand niet kan worden gevonden. Als deze opdracht met succes wordt afgesloten met exitcode 0, wordt er geen actie ondernomen.

De `periodSeconds` eigenschap wijst de liveness opdracht moet elke 5 seconden uit te voeren.

## <a name="verify-liveness-output"></a>Liveness-uitvoer controleren

Binnen de eerste 30 `healthy` seconden bestaat het bestand dat is gemaakt door de startopdracht. Wanneer de opdracht levendigheid `healthy` controleert op het bestaan van het bestand, retourneert de statuscode 0, waardoor succes wordt gesignaleerd, zodat er geen opnieuw wordt gestart.

Na 30 seconden `cat /tmp/healthy` begint het commando te mislukken, waardoor ongezonde en dodende gebeurtenissen optreden.

Deze gebeurtenissen kunnen worden bekeken vanuit de Azure-portal of Azure CLI.

![Portal ongezonde gebeurtenis][portal-unhealthy]

Door de gebeurtenissen in de Azure-portal te bekijken, worden gebeurtenissen van het type `Unhealthy` geactiveerd wanneer de opdracht levendigheid mislukt. De volgende gebeurtenis `Killing`is van type, wat betekent dat een container verwijderen, zodat een herstart kan beginnen. Het aantal opnieuw startende stappen voor de containerstappen telkens wanneer deze gebeurtenis optreedt.

Opnieuw opstarten wordt voltooid, zodat bronnen zoals openbare IP-adressen en knooppuntspecifieke inhoud worden bewaard.

![Teller voor het opnieuw opstarten van portalen][portal-restart]

Als de levendigheidssonde continu uitvalt en te veel herstarten activeert, treedt uw container een exponentiële vertraging in.

## <a name="liveness-probes-and-restart-policies"></a>Levendigheidssprobes en beleid opnieuw opstarten

Het beleid voor opnieuw starten vervangt het herstartgedrag dat wordt geactiveerd door liveness-sondes. Als u bijvoorbeeld een `restartPolicy = Never` liveness-sonde instelt, wordt de containergroep niet opnieuw opgestart vanwege een mislukte liveness-controle. *and* De containergroep houdt zich in plaats daarvan `Never`aan het herstartbeleid van de containergroep van .

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's vereisen mogelijk een levendigheidssonde om automatische herstart mogelijk te maken als een vereiste functie niet goed werkt. Zie [Containertaken uitvoeren in Azure Container Instances](container-instances-restart-policy.md)voor meer informatie over het uitvoeren van op taken gebaseerde containers.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
