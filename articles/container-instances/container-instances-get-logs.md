---
title: Logboeken van container instanties & gebeurtenissen ophalen
description: Meer informatie over het opsporen van fouten met container logboeken en gebeurtenissen met Azure Container Instances
ms.topic: article
ms.date: 03/21/2019
ms.custom: mvc
ms.openlocfilehash: 57d35b9423fd8c64e5a58ee4d8055aa3b238ba8c
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481749"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Container logboeken en gebeurtenissen ophalen in Azure Container Instances

Wanneer u een niet-gestarte container hebt, moet u eerst de logboeken weer geven met [AZ container logs][az-container-logs]en de standaard-en standaard fout streamen met [AZ container attach][az-container-attach].

## <a name="view-logs"></a>Logboeken weergeven

Als u logboeken wilt weer geven vanuit de code van uw toepassing in een container, kunt u de opdracht [AZ container logs][az-container-logs] gebruiken.

Het volgende is de logboek uitvoer van de voorbeeld taak op basis van taken in [een container taak uitvoeren in ACI](container-instances-restart-policy.md), nadat het een ongeldige URL heeft ingevoerd voor verwerking:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Uitvoerstromen koppelen

De opdracht [AZ container attach][az-container-attach] bevat diagnostische gegevens tijdens het opstarten van de container. Zodra de container is gestart, worden STDOUT en STDERR naar uw lokale console gestreamd.

Hier ziet u bijvoorbeeld de uitvoer van de container op basis van taken in [een container taak uitvoeren in ACI](container-instances-restart-policy.md), nadat u een geldige URL hebt opgegeven van een groot tekst bestand dat moet worden verwerkt:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Diagnostische gebeurtenissen ophalen

Als uw container niet kan worden geïmplementeerd, moet u de diagnostische gegevens controleren die door de Azure Container Instances resource provider zijn verstrekt. Als u de gebeurtenissen voor uw container wilt weer geven, voert u de opdracht [AZ container Show] [AZ-container-Show] uit:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

De uitvoer bevat de kern eigenschappen van uw container, samen met de implementatie gebeurtenissen (die hier zijn afgekapt):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [oplossen van veelvoorkomende problemen met betrekking tot containers en implementaties](container-instances-troubleshooting.md) voor Azure container instances.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
