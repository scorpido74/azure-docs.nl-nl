---
title: Key Phrase Extraction Kubernetes config en implementeren stappen
titleSuffix: Azure Cognitive Services
description: Key Phrase Extraction Kubernetes config en implementeren stappen
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1a96b5e4d03ce72bac29126028ca61e11e8c7324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262237"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>De container Sleutelzinextractie implementeren in een AKS-cluster

1. Open de Azure CLI en meld u aan bij Azure.

    ```azurecli
    az login
    ```

1. Meld u aan bij het AKS-cluster. Vervang `your-cluster-name` `your-resource-group` en met de juiste waarden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Nadat deze opdracht is uitgevoerd, wordt een bericht gerapporteerd dat vergelijkbaar is met het volgende:

    ```output
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

1. Maak in de teksteditor een nieuw bestand met de naam *keyphrase.yaml*en plak de volgende YAML erin. Zorg ervoor `billing/value` dat `apikey/value` u en met uw eigen informatie te vervangen.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Sla het bestand op en sluit de teksteditor.
1. Voer de `apply` opdracht Kubernetes uit met het *bestand keyphrase.yaml* als doel:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Nadat de opdracht de implementatieconfiguratie heeft toegepast, wordt een bericht weergegeven dat lijkt op de volgende uitvoer:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Controleer of de pod is geïmplementeerd:

    ```console
    kubectl get pods
    ```

    De uitvoer voor de lopende status van de pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Controleer of de service beschikbaar is en haal het IP-adres op.

    ```console
    kubectl get services
    ```

    De uitvoer voor de uitvoerstatus van de *sleutelzinsservice* in de pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
