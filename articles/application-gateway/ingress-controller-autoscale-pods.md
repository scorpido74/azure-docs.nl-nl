---
title: Aks-pods automatisch schalen met Azure Application Gateway-statistieken
description: In dit artikel vindt u instructies over het schalen van uw AKS-backendpods met behulp van Application Gateway-statistieken en Azure Kubernetes Metric Adapter
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239455"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Uw AKS-pods automatisch schalen met application gateway metrics (bèta)

Naarmate het inkomende verkeer toeneemt, wordt het cruciaal om uw applicaties op te schalen op basis van de vraag.

In de volgende zelfstudie leggen we uit `AvgRequestCountPerHealthyHost` hoe u de statistiek van Application Gateway gebruiken om uw toepassing op te schalen. `AvgRequestCountPerHealthyHost`meet gemiddelde aanvragen die naar een specifieke backendpool en backend HTTP-instellingscombinatie worden verzonden.

We gaan volgende twee componenten gebruiken:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- We gebruiken de metrische adapter om Application Gateway-statistieken via de metrische server bloot te leggen. De Azure Kubernetes Metric Adapter is een open source-project onder Azure, vergelijkbaar met de Application Gateway Ingress Controller. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- We gebruiken HPA om Application Gateway-statistieken te gebruiken en een implementatie te targeten voor schalen.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Azure Kubernetes-metrische adapter instellen

1. We maken eerst een Azure AAD-serviceprincipal en wijzen deze toe via de `Monitoring Reader` brongroep van Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Nu, We zullen [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) het gebruik van de AAD service principal hierboven gemaakt implementeren.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. We maken `ExternalMetric` een resource `appgw-request-count-metric`met naam. Deze resource geeft de metrische `AvgRequestCountPerHealthyHost` adapter `myApplicationGateway` opdracht `myResourceGroup` om metrische gegevens bloot te leggen voor resource in resourcegroep. U `filter` het veld gebruiken om een specifieke backendpool en backend HTTP-instelling te targeten in de Toepassingsgateway.

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

U nu een verzoek indienen bij de metrische server om te zien of onze nieuwe statistiek wordt blootgesteld:
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>De nieuwe statistiek gebruiken om de implementatie op te schalen

Zodra we in `appgw-request-count-metric` staat zijn om bloot te [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) leggen via de metrische server, zijn we klaar om te gebruiken om onze doelimplementatie op te schalen.

In het volgende voorbeeld richten `aspnet`we ons op een voorbeeldimplementatie. We zullen Pods `appgw-request-count-metric` opschalen als > 200 `10` per Pod tot een maximum aan Pods.

Vervang de naam van uw doelimplementatie en pas de volgende automatische schaalconfiguratie toe:
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

Test je setup met behulp van een load test tool zoals apache bench:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Volgende stappen
- [**Problemen met de Ingress-controller oplossen:**](ingress-controller-troubleshoot.md)problemen met de Ingress-controller oplossen.