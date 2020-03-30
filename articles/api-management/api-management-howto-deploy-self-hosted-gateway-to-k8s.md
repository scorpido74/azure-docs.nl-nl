---
title: Een zelf gehoste Azure API Management-gateway implementeren voor Kubernetes | Microsoft Documenten
description: Meer informatie over het implementeren van een zelfgehoste Azure API Management-gateway naar Kubernetes
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513833"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Een zelf gehoste Azure API Management-gateway implementeren voor Kubernetes

In dit artikel vindt u de stappen voor het implementeren van een zelf gehoste Azure API Management-gateway in een Kubernetes-cluster.

> [!NOTE]
> Zelf gehoste gateway-functie is in preview. Tijdens de preview is de zelf gehoste gateway alleen beschikbaar in de lagen Developer en Premium zonder extra kosten. De ontwikkelaarslaag is beperkt tot één zelf gehoste gateway-implementatie.


## <a name="prerequisites"></a>Vereisten

- De volgende quickstart voltooien: [een azure API-beheerexemplaar maken](get-started-create-service-instance.md)
- Maak een Kubernetes-cluster. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) is een goede optie voor ontwikkelings- en evaluatiedoeleinden. Voor productieworkloads u [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) of een Kubernetes-cluster gebruiken in een buitenlandse cloud.
- [Een gatewaybron inrichten in uw API-beheerexemplaar](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>De gateway naar Kubernetes implementeren

1. Selecteer **Gateways** onder **Instellingen**.
2. Selecteer de gatewaybron die u wilt implementeren.
3. Selecteer **Implementatie**.
4. Houd er rekening mee dat een nieuw token in het tekstvak **Token** automatisch is gegenereerd met de waarden **standaard verloop** en **geheime sleutel.** Pas een van beide of beide aan indien gewenst en selecteer **Genereren** om een nieuw token te maken.
5. Controleer of **Kubernetes** is geselecteerd onder **Implementatiescripts**.
6. Selecteer **<>.yml-bestandskoppeling** naast **Implementatie** om het bestand te downloaden.
7. Pas de poorttoewijzingen en containernaam in het yml-bestand zo nodig aan.
8. Selecteer het **kopieerpictogram** aan de **Deploy** rechterkant van het `kubectl` tekstvak Implementeren om de opdracht op het klembord op te slaan. 
9. Plak de opdracht op het terminalvenster (of opdracht). Houd er rekening mee dat de opdracht verwacht dat het gedownloade omgevingsbestand aanwezig is in de huidige map.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Voer de opdracht uit. De opdracht instrueert uw Kubernetes-cluster om de container uit te voeren, met behulp van zelf gehoste gatewayafbeelding die is gedownload uit het Microsoft Container Registry, en om de container te configureren om HTTP -poorten (8080) en HTTPS (443) bloot te leggen.
11. Voer de onderstaande opdracht uit om te controleren of de gatewaypod wordt uitgevoerd. Houd er rekening mee dat de naam van uw pod anders zal zijn. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Voer de onderstaande opdracht uit om te controleren of de gatewayservice wordt uitgevoerd. Houd er rekening mee dat uw servicenaam anders zal zijn. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Ga terug naar de Azure-portal en bevestig dat het gatewayknooppunt dat u zojuist hebt geïmplementeerd, de status van een gezonde status rapporteert.

![gatewaystatus](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Gebruik <code>kubectl logs <gateway-pod-name></code> de opdracht om een momentopname van zelf gehost gatewaylogboek weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Zie Azure [API Management zelf gehostgatewayoverzicht](self-hosted-gateway-overview.md) voor meer informatie over de zelfgehoste gateway
* Meer informatie over [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


