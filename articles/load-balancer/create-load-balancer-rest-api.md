---
title: Een load balancer maken met behulp van REST API
titleSuffix: Azure Load Balancer
description: In dit artikel leert u hoe u een Azure Load Balancer maakt met behulp van REST API.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: b52c554617bdcbe88b65639473044eb9c5eb7fa8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045427"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Een Azure Basic Load Balancer maken met REST-API

Een Azure Load Balancer verdeelt nieuwe binnenkomende stromen die op de load balancer-frontend naar de exemplaren van de back-end-groep op basis van regels en tests binnenkomen. De Load Balancer is beschikbaar in twee SKU's: Basic en Standard. Aan het verschil tussen de twee versies van de SKU [Load Balancer-SKU vergelijkingen](concepts-limitations.md#skus).
 
Deze instructies wordt beschreven hoe het maken van een Azure Basic Load Balancer met [REST API van Azure](/rest/api/azure/) om te helpen bij de inkomende saldoaanvraag op meerdere virtuele machines binnen een virtueel Azure-netwerk. Volledige naslaginformatie en extra voorbeelden zijn beschikbaar in de [naslaginformatie over de Azure Load Balancer REST](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>De aanvraag maken
Gebruik de volgende HTTP PUT-aanvraag te maken van een nieuwe Azure Basic Load Balancer.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI-parameters

|Name  |In  |Verplicht |Type |Beschrijving |
|---------|---------|---------|---------|--------|
|subscriptionId   |  Pad       |  Waar       |   string      |  De referenties van het abonnement die unieke identificatie van de Microsoft Azure-abonnement. De abonnements-ID maakt deel uit van de URI voor elke Serviceaanroep.      |
|resourceGroupName     |     Pad    | Waar        |  string       |   De naam van de resourcegroep.     |
|loadBalancerName     |  Pad       |      Waar   |    string     |    De naam van de load balancer.    |
|api-version    |   query     |  Waar       |     string    |  Client-API-versie.      |



### <a name="request-body"></a>Aanvraagbody

Is de enige vereiste parameter `location`. Als u geen definieert de *SKU* versie, een Basic Load Balancer wordt standaard gemaakt.  Gebruik [optionele parameters](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) om aan te passen van de load balancer.

| Name | Type | Beschrijving |
| :--- | :--- | :---------- |
| location | string | Resourcelocatie. Een huidige lijst met locaties met behulp van de [lijst met locaties](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) bewerking. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Voorbeeld: Maken en bijwerken van een Basic Load Balancer

In dit voorbeeld moet u eerst een Basic Load Balancer, samen met de daarbij behorende bronnen maken. Vervolgens configureert u de load balancer-resources die zijn een front-end-IP-configuratie, een back-endadresgroep, een load balancing-regel, een statustest en een binnenkomende NAT-regel.

Voordat u een load balancer met behulp van het volgende voorbeeld maakt, maakt u een virtueel netwerk met de naam *vnetlb* met een subnet met de naam *subnetlb* in een resourcegroep met de naam *rg1* in de **East US** locatie.

### <a name="step-1-create-a-basic-load-balancer"></a>STAP 1. Een Basic load balancer maken
In deze stap maakt u een Basic Load Balancer met de naam *lb* op de **EAST US** locatie binnen de *rg1* resourcegroep.
#### <a name="sample-request"></a>Voorbeeld van een aanvraag

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Aanvraagbody

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>STAP 2. Resources voor load balancer configureren
In deze stap configureert u de load balancer *lb* resources toe met een front-end-IP-configuratie (*fe-lb*), een back-endadresgroep (*worden lb*), een regel (voor taakverdeling *rulelb*), een statustest (*test-lb*), en een binnenkomende NAT-regel (*in-nat-rule*).
#### <a name="sample-request"></a>Voorbeeld van een aanvraag

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Aanvraagbody

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
