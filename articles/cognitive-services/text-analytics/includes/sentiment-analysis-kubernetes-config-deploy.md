---
title: Sentiment Analyse Kubernetes config en implementeren stappen
titleSuffix: Azure Cognitive Services
description: Sentiment Analyse Kubernetes config en implementeren stappen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877812"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>De container Sentimentanalyse implementeren in een AKS-cluster

1. Open de Azure CLI en meld u aan bij Azure.

    ```azurecli
    az login
    ```

1. Meld u aan bij het AKS-cluster. Vervang `your-cluster-name` `your-resource-group` en met de juiste waarden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Nadat deze opdracht is uitgevoerd, wordt een bericht gerapporteerd dat vergelijkbaar is met het volgende:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Als u meerdere abonnementen beschikbaar hebt op `az aks get-credentials` uw Azure-account en de opdracht met een fout wordt geretourneerd, is een veelvoorkomend probleem dat u het verkeerde abonnement gebruikt. Stel de context van uw Azure CLI-sessie in om hetzelfde abonnement te gebruiken waarmee u de resources hebt gemaakt en probeer het opnieuw.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Open de teksteditor naar keuze. In dit voorbeeld wordt gebruik gebruikt voor Visual Studio Code.

    ```console
    code .
    ```

1. Maak in de teksteditor een nieuw bestand met de naam *sentiment.yaml*en plak de volgende YAML erin. Zorg ervoor `billing/value` dat `apikey/value` u en met uw eigen informatie te vervangen.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Sla het bestand op en sluit de teksteditor.
1. Voer de `apply` opdracht Kubernetes uit met het *sentiment.yaml-bestand* als doel:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Nadat de opdracht de implementatieconfiguratie heeft toegepast, wordt een bericht weergegeven dat lijkt op de volgende uitvoer:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Controleer of de pod is geïmplementeerd:

    ```console
    kubectl get pods
    ```

    De uitvoer voor de lopende status van de pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Controleer of de service beschikbaar is en haal het IP-adres op.

    ```console
    kubectl get services
    ```

    De uitvoer voor de uitvoerstatus van de *gevoelsservice* in de pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
