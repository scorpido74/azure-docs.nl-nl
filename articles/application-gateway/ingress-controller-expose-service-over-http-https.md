---
title: Een AKS-service blootstellen via HTTP of HTTPS met behulp van Application Gateway
description: In dit artikel vindt u informatie over het blootstellen van een AKS-service via HTTP of HTTPS met behulp van Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795576"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Een AKS-service blootstellen via HTTP of HTTPS met behulp van Application Gateway 

Deze zelfstudies illustreren het gebruik van [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) om een voorbeeld kubernetes-service via de [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) via HTTP of HTTPS bloot te leggen.

## <a name="prerequisites"></a>Vereisten

- Geïnstalleerde `ingress-azure` helmdiagram.
  - [**Greenfield Deployment:**](ingress-controller-install-new.md)Als u helemaal opnieuw begint, raadpleegt u deze installatie-instructies, waarin stappen worden beschreven om een AKS-cluster met Application Gateway te implementeren en de ingress-controller voor toepassingsgateway op het AKS-cluster te installeren.
  - [**Brownfield Deployment:**](ingress-controller-install-existing.md)Als u een bestaand AKS-cluster en Application Gateway hebt, raadpleegt u deze instructies om de toepassinggateway-ingress-controller op het AKS-cluster te installeren.
- Als u HTTPS wilt gebruiken op deze toepassing, hebt u een x509-certificaat en de privésleutel nodig.

## <a name="deploy-guestbook-application"></a>Toepassing `guestbook` implementeren

De guestbook-toepassing is een canonieke Kubernetes-toepassing die een Web UI frontend, een backend en een Redis-database samenstelt. Standaard `guestbook` wordt de toepassing ervan weergegeven `frontend` via `80`een service met naam op poort. Zonder een Kubernetes Ingress Resource is de service niet toegankelijk van buiten het AKS-cluster. We gebruiken de toepassing en installatie Ingress Resources om toegang te krijgen tot de toepassing via HTTP en HTTPS.

Volg de onderstaande instructies om de gastenboektoepassing te implementeren.

1. Download `guestbook-all-in-one.yaml` [hier](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Implementeren `guestbook-all-in-one.yaml` in uw AKS-cluster door

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Nu is `guestbook` de toepassing geïmplementeerd.

## <a name="expose-services-over-http"></a>Services blootstellen via HTTP

Om de toepassing van het gastenboek bloot te leggen, gebruiken we de volgende ingress-bron:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Deze ingress zal `frontend` de `guestbook-all-in-one` service van de implementatie blootstellen als een standaard backend van de Application Gateway.

Sla de bovenstaande ingress resource op als `ing-guestbook.yaml`.

1. Implementeren `ing-guestbook.yaml` door uit te voeren:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Controleer het logboek van de invallende controller op de implementatiestatus.

Nu `guestbook` moet de applicatie beschikbaar zijn. U dit controleren door het openbare adres van de Application Gateway te bezoeken.

## <a name="expose-services-over-https"></a>Services blootstellen via HTTPS

### <a name="without-specified-hostname"></a>Zonder opgegeven hostnaam

Zonder hostnaam op te geven, is de gastenboekservice beschikbaar op alle hostnamen die naar de toepassingsgateway verwijzen.

1. Voordat u invallen implementeert, moet u een kubernetes-geheim maken om het certificaat en de privésleutel te hosten. U een kubernetes-geheim maken door

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definieer de volgende intis. Geef in de instotu de `secretName` naam van het geheim op in de sectie.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Vervang `<guestbook-secret-name>` in de bovenstaande Ingress Resource door de naam van je geheim. Sla de bovenstaande Ingress-bron `ing-guestbook-tls.yaml`op in een bestandsnaam .

1. Implementeren-gastenboek-tls.yaml door

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Controleer het logboek van de invallende controller op de implementatiestatus.

Nu `guestbook` zal de applicatie beschikbaar zijn op zowel HTTP als HTTPS.

### <a name="with-specified-hostname"></a>Met opgegeven hostnaam

U ook de hostnaam op de ingress opgeven om TLS-configuraties en -services te multiplexen.
Door hostnaam op te geven, is de gastenboekservice alleen beschikbaar op de opgegeven host.

1. Definieer de volgende intis.
    Geef in de instotu de `secretName` naam van het geheim `hosts` in de sectie op en vervang de hostnaam in de sectie dienovereenkomstig.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Implementeren `ing-guestbook-tls-sni.yaml` door uit te voeren

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Controleer het logboek van de invallende controller op de implementatiestatus.

Nu `guestbook` is de toepassing alleen beschikbaar op zowel HTTP`<guestbook.contoso.com>` als HTTPS op de opgegeven host (in dit voorbeeld).

## <a name="integrate-with-other-services"></a>Integreren met andere services

Met de volgende ingress u extra paden toevoegen aan deze binnendringen en deze paden omleiden naar andere services:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
