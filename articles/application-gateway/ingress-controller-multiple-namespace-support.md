---
title: Meerdere naamruimteondersteuningen inschakelen voor Application Gateway Ingress Controller
description: In dit artikel vindt u informatie over het inschakelen van meerdere naamruimteondersteuning in een Kubernetes-cluster met een Application Gateway Ingress Controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279921"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Ondersteuning voor meerdere naamruimte inschakelen in een AKS-cluster met Application Gateway Ingress Controller

## <a name="motivation"></a>Motivatie
Kubernetes [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) maken het mogelijk om een Kubernetes-cluster te verdelen en toe te geven aan subgroepen van een groter team. Deze subteams kunnen vervolgens infrastructuur implementeren en beheren met fijnere besturingselementen van resources, beveiliging, configuratie enz. Kubernetes maakt het mogelijk om een of meer binnendringende resources onafhankelijk te definiëren binnen elke naamruimte.

Vanaf versie 0.7 Azure Application Gateway kan [Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) gebeurtenissen van meerdere naamruimten innemen en observeren. Als de AKS-beheerder besluit [app-gateway](https://azure.microsoft.com/services/application-gateway/) als een binnendringende toepassing te gebruiken, gebruiken alle naamruimten dezelfde instantie van Application Gateway. Een enkele installatie van Ingress Controller controleert toegankelijke naamruimten en configureert de Application Gateway waar deze aan is gekoppeld.

Versie 0.7 van AGIC blijft uitsluitend `default` de naamruimte observeren, tenzij deze expliciet wordt gewijzigd in een of meer verschillende naamruimten in de Helm-configuratie (zie sectie hieronder).

## <a name="enable-multiple-namespace-support"></a>Ondersteuning voor meerdere naamruimten inschakelen
Ga als u meerdere naamruimteondersteuning inschakelen:
1. het [helm-config.yaml-bestand](#sample-helm-config-file) op een van de volgende manieren wijzigen:
   - verwijder `watchNamespace` de sleutel volledig van [helm-config.yaml](#sample-helm-config-file) - AGIC zal alle naamruimten observeren
   - ingesteld `watchNamespace` op een lege tekenreeks - AGIC observeert alle naamruimten
   - meerdere naamruimten toevoegen die gescheiden`watchNamespace: default,secondNamespace`zijn door een komma ( ) - AGIC zal deze naamruimten uitsluitend observeren
2. Wijzigingen in helmsjabloon toepassen met:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Zodra agic is geïmplementeerd met de mogelijkheid om meerdere naamruimten te observeren, zal:
  - binnenkomende bronnen uit alle toegankelijke naamruimten weergeven
  - filter om resources binnen te laten die zijn geannoteerd met`kubernetes.io/ingress.class: azure/application-gateway`
  - gecombineerde [Application Gateway config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) samenstellen
  - de config toepassen op de bijbehorende Application Gateway via [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Conflicterende configuraties
Meerdere [ingevallen insletiëten kunnen](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) AGIC instrueren om conflicterende configuraties te maken voor één toepassingsgateway. (Twee ingresses die hetzelfde domein claimen bijvoorbeeld.)

Boven aan de hiërarchie kunnen **luisteraars** (IP-adres, poort en host) en **routeringsregels** (bindinglistener, backendpool en HTTP-instellingen) worden gemaakt en gedeeld door meerdere naamruimten/insers.

Aan de andere kant - paden, backendpools, HTTP-instellingen en TLS-certificaten kunnen alleen met één naamruimte worden gemaakt en duplicaten worden verwijderd.

Denk bijvoorbeeld aan de volgende dubbele ingress `staging` `production` resources `www.contoso.com`gedefinieerde naamruimten en voor:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Ondanks de twee invallende `www.contoso.com` resources die verkeer eisen om naar de respectievelijke Kubernetes-naamruimten te worden gerouteerd, kan slechts één backend het verkeer bedienen. AGIC zou een configuratie maken op basis van "wie het eerst komt, het eerst maalt" voor een van de resources. Als er twee insuasmiddelen tegelijkertijd worden gemaakt, heeft de eerder in het alfabet voorrang. Vanuit het bovenstaande voorbeeld kunnen we alleen `production` instellingen maken voor de intrede. Application Gateway wordt geconfigureerd met de volgende bronnen:

  - Luisteraar:`fl-www.contoso.com-80`
  - Routeringsregel:`rr-www.contoso.com-80`
  - Backend Pool:`pool-production-contoso-web-service-80-bp-80`
  - HTTP-instellingen:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Gezondheid Sonde:`pb-production-contoso-web-service-80-websocket-ingress`

Houd er rekening mee dat, behalve voor *listener-* en *routeringsregel,* de resources van de toepassingsgateway die zijn gemaakt, de naam bevatten van de naamruimte (`production`) waarvoor ze zijn gemaakt.

Als de twee invallende resources op verschillende tijdspunten in het AKS-cluster worden geïntroduceerd, is het waarschijnlijk dat AGIC `namespace-B` `namespace-A`in een scenario terechtkomt waarin het application gateway opnieuw wordt geconfigureerd en het verkeer opnieuw wordt omgeleid van naar .

Als u bijvoorbeeld `staging` als eerste hebt toegevoegd, configureert AGIC Application Gateway om verkeer naar de backendpool met fasering te leiden. In een later stadium, de invoering van `production` binnendringen, zal leiden tot AGIC `production` te herprogrammeren Application Gateway, die zal beginnen met het routeren van verkeer naar de backend pool.

## <a name="restrict-access-to-namespaces"></a>Toegang tot naamruimten beperken
Standaard configureert AGIC Application Gateway op basis van geannoteerde Ingress binnen een naamruimte. Als u dit gedrag wilt beperken, hebt u de volgende opties:
  - de naamruimten te beperken door naamruimten die AGIC `watchNamespace` expliciet moet definiëren, moet worden waargenomen via de YAML-toets in [helm-config.yaml](#sample-helm-config-file)
  - [Rol/Rolbinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) gebruiken om AGIC te beperken tot specifieke naamruimten

## <a name="sample-helm-config-file"></a>Voorbeeld helm config-bestand
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

