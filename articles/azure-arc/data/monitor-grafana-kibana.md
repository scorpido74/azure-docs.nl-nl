---
title: Logboeken en metrische gegevens weer geven met Kibana en Grafana
description: Logboeken en metrische gegevens weer geven met Kibana en Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d876862d8f41ab8df646bef051629fd45c4d4601
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936328"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Logboeken en metrische gegevens weer geven met Kibana en Grafana

Kibana-en Grafana-Web-Dash boards zijn beschikbaar om inzicht en duidelijkheid te bieden in de Kubernetes-naam ruimten die worden gebruikt door Azure Arc ingeschakelde Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Het IP-adres van uw cluster ophalen

Als u toegang wilt krijgen tot de Dash boards, moet u het IP-adres van uw cluster ophalen. De methode voor het ophalen van het juiste IP-adres is afhankelijk van hoe u ervoor hebt gekozen om Kubernetes te implementeren. Door loop de opties hieronder om de juiste voor u te vinden.

### <a name="azure-virtual-machine"></a>Azure virtuele machine

Gebruik de volgende opdracht om het open bare IP-adres op te halen:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm-cluster

Gebruik de volgende opdracht om het IP-adres van het cluster op te halen:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS of een ander cluster met gelijke taak verdeling

Als u uw omgeving in AKS of een ander cluster met gelijke taak verdeling wilt bewaken, moet u het IP-adres van de beheer proxy service ophalen. Gebruik deze opdracht om het **externe IP-** adres op te halen:

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Aanvullende firewall configuratie

Mogelijk moet u de poorten in de firewall openen om toegang te krijgen tot de Kibana-en Grafana-eind punten.

Hieronder ziet u een voor beeld van hoe u dit kunt doen voor een Azure-VM. U moet dit doen als u Kubernetes hebt geïmplementeerd met behulp van het script.

In de onderstaande stappen wordt uitgelegd hoe u een NSG-regel maakt voor de eind punten Kibana en Grafana:

### <a name="find-the-name-of-the-nsg"></a>De naam van de NSG zoeken

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>De regel NSG toevoegen

Zodra u de naam van de NSG hebt, kunt u een regel toevoegen met behulp van de volgende opdracht:

```console
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Beheerde Azure SQL-instanties in azure Arc bewaken

Nu u het IP-adres hebt en de poorten beschikbaar hebt, kunt u toegang krijgen tot Grafana en Kibana.

> [!NOTE]
>  Wanneer u wordt gevraagd om een gebruikers naam en wacht woord in te voeren, voert u de gebruikers naam en het wacht woord in die u hebt opgegeven op het moment dat u de Azure arctangens-gegevens controller hebt gemaakt.

> [!NOTE]
>  U wordt gevraagd om een certificaat waarschuwing omdat de certificaten die worden gebruikt in de preview zelfondertekende certificaten zijn.

Gebruik het volgende URL-patroon om toegang te krijgen tot de Dash boards voor logboek registratie en controle voor Azure SQL Managed instance:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

De relevante Dash boards zijn:

* "Metrische gegevens van Azure SQL Managed instance"
* "Metrische gegevens van het host-knoop punt"
* "Peul metrische gegevens van de host"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Azure Database for PostgreSQL grootschalige-Azure-boog bewaken

Gebruik het volgende URL-patroon om toegang te krijgen tot de Dash boards voor logboek registratie en controle voor PostgreSQL grootschalige:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

De relevante Dash boards zijn:

* "Metrische gegevens over post gres"
* "Metrische tabel gegevens post gres"
* "Metrische gegevens van het host-knoop punt"
* "Peul metrische gegevens van de host"

## <a name="next-steps"></a>Volgende stappen
- Probeer [metrische gegevens en logboeken te uploaden naar Azure monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Meer informatie over Grafana:
   - [Aan de slag](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grond beginselen van Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Zelf studies voor Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Meer informatie over Kibana
   - [Inleiding](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana-hand leiding](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Inleiding tot dash board DrillDowns met gegevens visualisaties in Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana-Dash boards maken](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

