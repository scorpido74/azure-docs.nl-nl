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
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936044"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Logboeken van Azure Arc ingeschakelde data services ophalen

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

U hebt het Azure data CLI-hulp programma nodig om de logboeken van Azure Arc ingeschakelde Data Services op te halen. [Installatie-instructies](./install-client-tools.md)

U moet zich kunnen aanmelden bij de service Azure Arc enabled Data Services controller als beheerder.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Logboeken van Azure Arc ingeschakelde data services ophalen

U kunt de logboeken van Azure Arc enabled Data Services voor alle doel einden of specifieken ophalen voor het oplossen van problemen.  U kunt dit doen met behulp van de standaard Kubernetes-hulpprogram ma's, zoals de `kubectl logs` opdracht of in dit artikel, met behulp van het Azure data cli-hulp programma, waarmee u eenvoudig alle logboeken tegelijk kunt ophalen.

Zorg er eerst voor dat u bent aangemeld bij de gegevens controller.

```console
azdata login
```

Voer vervolgens de volgende opdracht uit om de logboeken te dumpen:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

De logboek bestanden worden standaard in de huidige werkmap gemaakt in een submap met de naam logs.  U kunt de logboek bestanden met behulp van de para meter naar een andere map uitvoeren `--target-folder` .

U kunt ervoor kiezen om de bestanden te comprimeren door de `--skip-compress` para meter te weglaten.

U kunt geheugen dumps activeren en toevoegen door de te weglaten `--exclude-dumps` , maar dit wordt niet aanbevolen, tenzij Microsoft ondersteuning de geheugen dumps heeft aangevraagd.  Voor het maken van een geheugen dump moet de gegevens controller instelling worden `allowDumps` ingesteld op `true` het moment dat de gegevens controller wordt gemaakt.

U kunt er ook voor kiezen om te filteren op het verzamelen van Logboeken voor een specifieke pod ( `--pod` ) of container ( `--container` ) op naam.

U kunt er ook voor kiezen om te filteren op Logboeken voor een specifieke aangepaste resource door de `--resource-kind` en para meter door te geven `--resource-name` .  De `resource-kind` parameter waarde moet een van de aangepaste resource definitie namen zijn die kunnen worden opgehaald met de opdracht `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Voor beeld van maphiërarchie.  Houd er rekening mee dat mappen hiërarchie is ingedeeld op pod naam naam en vervolgens op container en vervolgens op Directory hiërarchie in de container.

```console
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