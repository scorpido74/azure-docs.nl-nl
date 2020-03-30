---
title: Gezondheidssondes toevoegen aan uw AKS-pods
description: In dit artikel vindt u informatie over het toevoegen van statussondes (gereedheid en/of levendigheid) aan AKS-pods met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795599"
---
# <a name="add-health-probes-to-your-service"></a>Health Probes toevoegen aan uw service
Standaard zal de Ingress-controller een HTTP GET-sonde inrichten voor de blootgestelde pods.
De eigenschappen van de sonde kunnen worden aangepast `deployment` / `pod` door een [gereedheid of levendigheidssonde](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) toe te voegen aan uw specificaties.

## <a name="with-readinessprobe-or-livenessprobe"></a>Met `readinessProbe` of`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API-referentie:
* [Containerprobes](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet-actie](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`en `livenessProbe` worden ondersteund wanneer `httpGet`geconfigureerd met .
> * Indringende op een andere poort dan die op de pod wordt momenteel niet ondersteund.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` worden niet ondersteund.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Zonder `readinessProbe` of`livenessProbe`
Als de bovenstaande sondes niet worden geleverd, dan ingevallen `Path` Controller `backend-path-prefix` maken een veronderstelling `path` dat `ingress` de dienst bereikbaar is op opgegeven voor annotatie of de opgegeven in de definitie voor de dienst.

## <a name="default-values-for-health-probe"></a>Standaardwaarden voor statussonde
Voor elke eigenschap die niet kan worden afgeleid door de gereedheid/ levendigheidsssonde, worden standaardwaarden ingesteld.

| Application Gateway Probe , eigenschap | Standaardwaarde |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |