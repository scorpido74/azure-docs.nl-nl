---
title: AKS automatisch schalen met metrische gegevens over Azure-toepassing gateway
description: In dit artikel vindt u instructies voor het schalen van uw AKS-back-end met behulp van Application Gateway metrische gegevens en de Azure Kubernetes metric-adapter
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b98ab8d3c4d03115ea689b4dfd3d8dee753f019d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715075"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Uw AKS-peul automatisch schalen met behulp van Application Gateway metrische gegevens (bèta)

Als binnenkomend verkeer toeneemt, wordt het cruciaal om uw toepassingen op basis van de vraag te schalen.

In de volgende zelf studie wordt uitgelegd hoe u de `AvgRequestCountPerHealthyHost`waarde van Application Gateway kunt gebruiken om uw toepassing omhoog te schalen. `AvgRequestCountPerHealthyHost` gemiddeld aantal aanvragen dat is verzonden naar een specifieke back-end van een backend-groep en een back-end-HTTP-instellingen

We gaan de volgende twee onderdelen gebruiken:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) -we gebruiken de metrische adapter om Application Gateway metrische gegevens beschikbaar te stellen via de metrische server. De Azure Kubernetes metric-adapter is een open-source project onder Azure, vergelijkbaar met de Application Gateway ingangs controller. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) -we gebruiken HPA om Application Gateway metrische gegevens te gebruiken en een implementatie te richten op schalen.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>De metrische adapter voor Azure Kubernetes instellen

1. Eerst maakt u een Azure AAD-Service-Principal en wijst u deze toe `Monitoring Reader` toegang tot de resource groep van Application Gateway. 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Nu gaan we de [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) implementeren met behulp van de hierboven gemaakte Aad-Service-Principal.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Er wordt een `ExternalMetric` resource met de naam `appgw-request-count-metric`gemaakt. Met deze resource wordt de metrische adapter geïnstrueerd om `AvgRequestCountPerHealthyHost` metriek voor `myApplicationGateway` resource in `myResourceGroup` resource groep beschikbaar te maken. U kunt het veld `filter` gebruiken om een specifieke back-end-en back-end-HTTP-instelling in de Application Gateway te richten.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

U kunt nu een aanvraag indienen bij de metrische server om te zien of onze nieuwe metrische gegevens beschikbaar worden gesteld:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>De nieuwe maat eenheid gebruiken om de implementatie omhoog te schalen

Zodra we `appgw-request-count-metric` kunnen weer geven via de metrische server, zijn we klaar om [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) te gebruiken om onze doel implementatie te verg Roten.

In het volgende voor beeld wordt een voor beeld van een implementatie `aspnet`. We zullen het Peul omhoog schalen wanneer `appgw-request-count-metric` > 200 per pod tot Maxi maal `10` peul.

Vervang de naam van de doel implementatie en pas de volgende configuratie voor automatisch schalen toe:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Test uw installatie met behulp van een load test tool zoals Apache Bank:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Volgende stappen
- Problemen met [**ingangs controller**](ingress-controller-troubleshoot.md)oplossen: problemen met de ingangs controller oplossen.