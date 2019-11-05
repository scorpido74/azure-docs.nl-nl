---
title: Een zelf-hostende Azure API Management-Gateway implementeren op Kubernetes | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostende Azure API Management-gateway naar Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513833"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Een zelf-hostende Azure API Management-Gateway implementeren op Kubernetes

Dit artikel bevat de stappen voor het implementeren van een zelf-hostende Azure API Management-Gateway in een Kubernetes-cluster.

> [!NOTE]
> De zelf-hostende gateway functie is beschikbaar als preview-versie. Tijdens de preview-periode is de zelf-hostende gateway alleen beschikbaar in de lagen ontwikkelaar en Premium zonder extra kosten. De Developer-laag is beperkt tot één zelf-hostende gateway-implementatie.


## <a name="prerequisites"></a>Vereisten

- Lees de volgende snelstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Maak een Kubernetes-cluster. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) is een goede optie voor ontwikkelings-en evaluatie doeleinden. Voor werk belastingen kunt u [Azure Kubernetes service](https://azure.microsoft.com/services/kubernetes-service/) of een Kubernetes-cluster in een vreemde Cloud gebruiken.
- [Richt een gateway bron in uw API Management-exemplaar in](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>De gateway implementeren op Kubernetes

1. Selecteer **gateways** onder **instellingen**.
2. Selecteer de gateway resource die u wilt implementeren.
3. Selecteer **implementatie**.
4. Houd er rekening mee dat een nieuw token in het tekstvak **token** voor u automatisch wordt gegenereerd met de standaard waarden voor de **verlopende** en **geheime sleutel** . Pas een van beide of beide indien gewenst aan en selecteer **genereren** om een nieuw token te maken.
5. Zorg ervoor dat **Kubernetes** is geselecteerd onder **implementatie scripts**.
6. Selecteer **< gateway naam >. yml-** bestands koppeling naast **implementatie** om het bestand te downloaden.
7. Pas de poort toewijzingen en de container naam in het yml-bestand naar wens aan.
8. Selecteer het **Kopieer** pictogram dat zich aan de rechter kant van het tekstvak **implementeren** bevindt om de `kubectl` opdracht op het klem bord op te slaan. 
9. Plak de opdracht in het venster Terminal (of Command). Houd er rekening mee dat de opdracht verwacht dat het gedownloade omgevings bestand aanwezig is in de huidige map.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Voer de opdracht uit. De opdracht geeft uw Kubernetes-cluster de instructie om de container uit te voeren, waarbij de zelf-hostende gateway afbeelding wordt gedownload van de micro soft-Container Registry en om de container zo te configureren dat deze HTTP-(8080) en HTTPS-poorten (443) beschikbaar stelt.
11. Voer de onderstaande opdracht uit om te controleren of de pod van de gateway actief is. Houd er rekening mee dat de naam van uw Pod afwijkt. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Voer de onderstaande opdracht uit om te controleren of de Gateway Service wordt uitgevoerd. Houd er rekening mee dat uw service naam anders is. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Ga terug naar de Azure Portal en bevestig dat het gateway-knoop punt dat u zojuist hebt geïmplementeerd, een goede status rapporteert.

![Gateway status](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Gebruik <code>kubectl logs <gateway-pod-name></code> opdracht om een moment opname van het zelf-hostende gateway logboek weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de zelf-hostende Gateway vindt u in [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)
* Meer informatie over de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


