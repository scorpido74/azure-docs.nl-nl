---
title: Problemen met PostgreSQL grootschalige-Server groepen oplossen
description: Problemen met PostgreSQL grootschalige-Server groepen oplossen met een Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c0d3d9c74be8dabaec20ff5d4c7e7cfc74d8eef
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936050"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Problemen met PostgreSQL grootschalige-Server groepen oplossen

Notebooks kunnen procedures documenteren door de inhoud van de prijs opgave op te nemen om te beschrijven wat er moet gebeuren/hoe dit moet doen. Het kan ook uitvoer bare code bieden voor het automatiseren van een procedure.  Dit patroon is handig voor alles uit standaard procedures voor het oplossen van problemen met hand leidingen.

Laten we bijvoorbeeld een oplossing voor een PostgreSQL grootschalige-Server groep zien die mogelijk problemen ondervindt met het gebruik van Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="install-tools"></a>Hulpprogramma's installeren

Installeer Azure Data Studio `kubectl` en `azdata` op de client computer die u gebruikt om het notitie blok in azure Data Studio uit te voeren. Volg hiervoor de instructies op [client Hulpprogramma's installeren](install-client-tools.md) .

## <a name="update-the-path-environment-variable"></a>De omgevings variabele PATH bijwerken

Zorg ervoor dat deze hulpprogram ma's vanaf elke locatie op deze client computer kunnen worden aangeroepen. Werk bijvoorbeeld op een Windows-client computer de omgevings variabele PATH systeem bij en voeg de map toe waarin u kubectl hebt geïnstalleerd.

## <a name="sign-in-with-azdata"></a>Meld u aan met `azdata`

Meld u aan bij uw Arc-gegevens controller van deze client computer en voordat u Azure Data Studio start. U kunt dit doen door een opdracht als volgt uit te voeren:

```console
azdata login --endpoint https://<IP address>:<port>
```

Vervang door `<IP address>` het IP-adres van uw Kubernetes-cluster en `<port>` de poort waarop Kubernetes luistert. U wordt gevraagd om de gebruikers naam en het wacht woord. Voer de volgende handelingen uit om meer details te bekijken: _

```console
azdata login --help
```

## <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Meld u aan bij uw Kubernetes-cluster met kubectl

Als u dit wilt doen, kunt u de voorbeeld opdrachten gebruiken die in [Dit](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blog bericht worden gegeven.
U kunt bijvoorbeeld de volgende opdrachten uitvoeren:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

### <a name="the-troubleshooting-notebook"></a>Het notitie blok voor probleem oplossing

Start Azure Data Studio en open het notitie blok voor probleem oplossing. 

Implementeer de stappen die worden beschreven in  [033-Manage-postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) naar:

1. Verbinding maken met uw Arc-gegevens controller
2. Klik met de rechter muisknop op uw post gres-exemplaar en kies **[beheren]**
3. Selecteer het **dash board [problemen vaststellen en oplossen]**
4. Selecteer de **koppeling [probleem oplossen]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-open PostgreSQL-probleemoplossings notitieblok":::

De **TSG100-de Azure-postgresql grootschalige** -probleemoplossings notitie blok is geopend: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-gebruik postgresql Troubleshooting notebook":::

### <a name="run-the-scripts"></a>De scripts uitvoeren
Selecteer de knop alles uitvoeren aan de bovenkant om het notitie blok allemaal tegelijk uit te voeren, of u kunt elke code-cel één voor één stapsgewijs door lopen en uitvoeren.

Bekijk de uitvoer van de uitvoering van de code cellen voor potentiële problemen.

We voegen meer details toe aan het notitie blok over hoe u veelvoorkomende problemen herkent en hoe u deze kunt oplossen.

## <a name="next-step"></a>Volgende stap
- Meer informatie over het [ophalen van Logboeken voor Azure Arc ingeschakelde gegevens Services](troubleshooting-get-logs.md)
- Meer informatie over het [zoeken naar Logboeken met Kibana](monitor-grafana-kibana.md)
- Meer informatie over [bewaking met Grafana](monitor-grafana-kibana.md)
- Uw eigen notitie blokken maken
