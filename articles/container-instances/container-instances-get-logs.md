---
title: Logboeken van containerinstantie & gebeurtenissen ophalen
description: Meer informatie over het ophalen van containerlogboeken en -gebeurtenissen in Azure Container Instances om containerproblemen op te lossen
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: 0991b9cb1f99606910dbdf2c87b111f67da6da7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250001"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Containerlogboeken en gebeurtenissen ophalen in Azure Container Instances

Wanneer u een zich misdragende container in Azure Container Instances hebt, moet u de logboeken met [az-containerlogboeken][az-container-logs]bekijken en de standaard-out- en standaardfout met [de AZ-containerkoppelen][az-container-attach]streamen. U logboeken en gebeurtenissen voor containerexemplaren ook weergeven in de Azure-portal of logboek- en gebeurtenisgegevens voor containergroepen naar [Azure Monitor-logboeken](container-instances-log-analytics.md)verzenden.

## <a name="view-logs"></a>Logboeken weergeven

Als u logboeken wilt weergeven vanuit uw toepassingscode in een container, u de opdracht [AZ-containerlogboeken][az-container-logs] gebruiken.

Het volgende is logboekuitvoer van de op een voorbeeldtaak gebaseerde container in [De opdrachtregel instellen in een containerinstantie,](container-instances-start-command.md#azure-cli-example)nadat u een ongeldige URL hebt opgegeven met behulp van een opdrachtregeloverschrijven:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
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

De opdracht [az-containerattache][az-container-attach] biedt diagnostische informatie tijdens het opstarten van containers. Zodra de container is gestart, streamt deze STDOUT en STDERR naar uw lokale console.

Hier vindt u bijvoorbeeld uitvoer uit de op taak gebaseerde container in [De opdrachtregel instellen in een containerinstantie,](container-instances-start-command.md#azure-cli-example)nadat u een geldige URL van een groot tekstbestand hebt opgegeven om te verwerken:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
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

Als uw container niet succesvol is geïmplementeerd, controleert u de diagnostische gegevens die worden verstrekt door de azure containerinstances-bronprovider. Voer de opdracht AZ Container [Show uit][az-container-show] om de gebeurtenissen voor uw container weer te geven:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

De uitvoer omvat de kerneigenschappen van uw container, samen met implementatiegebeurtenissen (hier afgekapt):

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
Meer informatie over het [oplossen van veelvoorkomende container- en implementatieproblemen](container-instances-troubleshooting.md) voor Azure Container Instances.

Meer informatie over het verzenden van logboek- en gebeurtenisgegevens voor containergroepen naar [Azure Monitor-logboeken](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show