---
title: Sleuteltermextractie Kubernetes-configuratie en implementatie stappen
titleSuffix: Azure Cognitive Services
description: Sleuteltermextractie Kubernetes-configuratie en implementatie stappen
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: e29aec66d9986b509a5133f5ebe8a99a00f7b9cf
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051863"
---
## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>De Sleuteltermextractie-container implementeren in een AKS-cluster

1. Open de Azure CLI en meld u aan bij Azure.

    ```azurecli
    az login
    ```

1. Meld u aan bij het AKS-cluster. Vervang `your-cluster-name` en`your-resource-group` door de juiste waarden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Wanneer deze opdracht wordt uitgevoerd, wordt een bericht met de volgende strekking gerapporteerd:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Als er meerdere abonnementen voor u beschikbaar zijn in uw Azure-account en `az aks get-credentials` de opdracht wordt geretourneerd met een fout, is een veelvoorkomend probleem dat u het verkeerde abonnement gebruikt. Stel de context van uw Azure CLI-sessie in op het gebruik van hetzelfde abonnement dat u hebt gemaakt voor de resources en probeer het opnieuw.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Open de gewenste tekst editor. In dit voor beeld wordt Visual Studio code gebruikt.

    ```azurecli
    code .
    ```

1. Maak in de tekst editor een nieuw bestand met de naam *yaml*en plak de volgende YAML hierin. Vervang `billing/value` en`apikey/value` door uw eigen gegevens.

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

1. Sla het bestand op en sluit de tekst editor.
1. Voer de opdracht `apply` Kubernetes uit met het bestand *woordgroep. yaml* als doel:

    ```console
    kuberctl apply -f keyphrase.yaml
    ```

    Nadat de opdracht de implementatie configuratie heeft toegepast, wordt een bericht weer gegeven zoals in de volgende uitvoer:

    ```console
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Controleer of de Pod is geïmplementeerd:

    ```console
    kubectl get pods
    ```

    De uitvoer voor de uitvoerings status van de Pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Controleer of de service beschikbaar is en haal het IP-adres op.

    ```console
    kubectl get services
    ```

    De uitvoer voor de uitvoerings status van de *sentiment* -service in de Pod:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
