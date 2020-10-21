---
title: Logboeken ophalen voor het oplossen van problemen met Azure Arc enabled data controller
description: Service logboeken ophalen voor het oplossen van problemen met Azure Arc enabled data controller.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320206"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Logboeken van Azure Arc ingeschakelde data services ophalen

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, hebt u het volgende nodig:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [Installatie-instructies](./install-client-tools.md).
* Een beheerders account om u aan te melden bij de Azure Arc enabled Data Services-controller.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Logboeken van Azure Arc ingeschakelde data services ophalen

U kunt de logboeken van Azure Arc enabled Data Services voor alle doel einden of specifieken ophalen voor het oplossen van problemen. U kunt dit doen met behulp van standaard Kubernetes-hulpprogram ma's, zoals de `kubectl logs` opdracht of in dit artikel [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , waarmee u het hulp programma gebruikt, waardoor het eenvoudiger wordt om alle logboeken tegelijk op te halen.

1. Meld u aan bij de gegevens controller met een Administrator-account.

   ```console
   azdata login
   ```

2. Voer de volgende opdracht uit om de logboeken te dumpen:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Bijvoorbeeld:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

De gegevens controller maakt de logboek bestanden in de huidige werkmap in een submap met de naam `logs` . 

## <a name="options"></a>Opties

`azdata arc dc debug copy-logs` biedt de volgende opties voor het beheren van de uitvoer.

* Uitvoer de logboek bestanden naar een andere map met behulp van de `--target-folder` para meter.
* Comprimeer de bestanden door de para meter te weglaten `--skip-compress` .
* Activeer en voeg geheugen dumps toe door de te weglaten `--exclude-dumps` . Deze methode wordt niet aanbevolen, tenzij Microsoft Ondersteuning de geheugen dumps heeft aangevraagd. Voor het maken van een geheugen dump moet de gegevens controller instelling worden `allowDumps` ingesteld op `true` het moment dat de gegevens controller wordt gemaakt.
* Filter voor het verzamelen van Logboeken voor alleen een specifieke pod ( `--pod` ) of container ( `--container` ) op naam.
* Filter voor het verzamelen van Logboeken voor een specifieke aangepaste resource door de `--resource-kind` para meter en te geven `--resource-name` . De `resource-kind` parameter waarde moet een van de aangepaste resource definitie namen zijn, die kan worden opgehaald met de opdracht `kubectl get customresourcedefinition` .

Met deze para meters kunt u de `<parameters>` in het volgende voor beeld vervangen. 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Bijvoorbeeld

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Voor beeld van maphiërarchie. De maphiërarchie is ingedeeld op pod name, then container en then by Directory-hiërarchie in de container.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Volgende stappen

[azdata Arc DC-fout opsporing kopiëren-logboeken](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)