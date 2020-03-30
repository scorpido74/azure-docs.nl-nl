---
title: Probleemoplossing voor invallen van toepassingsgateway
description: In dit artikel vindt u documentatie over het oplossen van veelgestelde vragen en/of problemen met de Application Gateway Ingress Controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795517"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Veelvoorkomende vragen of problemen met Ingress Controller oplossen

[Azure Cloud Shell](https://shell.azure.com/) is de handigste manier om eventuele problemen met uw AKS- en AGIC-installatie op te lossen. Start je shell vanaf [shell.azure.com](https://shell.azure.com/) of door op de link te klikken:

[![Start insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Test met een eenvoudige Kubernetes-app

De onderstaande stappen gaan ervan uit:
  - U hebt een AKS-cluster, met Geavanceerde netwerken ingeschakeld
  - AGIC is geïnstalleerd op het AKS-cluster
  - U hebt al een Application Gateway op een VNET gedeeld met uw AKS-cluster

Als u wilt controleren of de installatie Application Gateway + AKS + AGIC correct is ingesteld, implementeert u de eenvoudigste app:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Kopieer en plak alle regels tegelijk van het script hierboven naar een [Azure Cloud Shell](https://shell.azure.com/). Zorg ervoor dat de volledige opdracht `cat` is gekopieerd `EOF`- te beginnen met en met inbegrip van de laatste .

![toepassen](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Na een succesvolle implementatie van de app boven uw AKS-cluster heeft een nieuwe Pod, Service en een Ingress.

Download de lijst met pods `kubectl get pods -o wide`met [Cloud Shell:](https://shell.azure.com/).
We verwachten dat er een pod met de naam 'test-agic-app-pod' is gemaakt. Het zal een IP-adres hebben. Dit adres moet zich binnen het VNET van de Application Gateway bevinden, dat wordt gebruikt met AKS.

![Peulen](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Download de lijst `kubectl get services -o wide`met services: . We verwachten een dienst genaamd 'test-agic-app-service'.

![Peulen](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Download de lijst met de `kubectl get ingress`invallen: . We verwachten dat er een Ingress-bron met de naam 'test-agic-app-ingress' is gemaakt. De resource heeft een hostnaam 'test.agic.contoso.com'.

![Peulen](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Een van de peulen zal AGIC zijn. `kubectl get pods`toont een lijst met pods, waarvan er een begint met 'ingress-azure'. Download alle logboeken `kubectl logs <name-of-ingress-controller-pod>` van die pod om te controleren of we een succesvolle implementatie hebben gehad. Een succesvolle implementatie zou de volgende regels aan het logboek hebben toegevoegd:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Als alternatief kunnen we uit [Cloud Shell](https://shell.azure.com/) alleen de regels `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`ophalen `<ingress-azure....>` die een succesvolle Application Gateway-configuratie aangeven met , waar moet de exacte naam van de AGIC-pod zijn.

Bij Application Gateway wordt de volgende configuratie toegepast:

- Listener: ![luisteraar](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Routeringsregel: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Backend Pool:
  - Er zal een IP-adres in de backend adres pool en het zal `kubectl get pods -o wide` 
 ![overeenkomen met het IP-adres van de Pod we eerder waargenomen met backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Ten slotte kunnen `cURL` we de opdracht vanuit [Cloud Shell](https://shell.azure.com/) gebruiken om een HTTP-verbinding met de nieuw geïmplementeerde app tot stand te brengen:

1. Gebruiken `kubectl get ingress` om het openbare IP-adres van Application Gateway op te halen
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` gebruiken

![Peulen](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Een resultaat `HTTP/1.1 200 OK` geeft aan dat het Application Gateway + AKS + AGIC-systeem werkt zoals verwacht.


## <a name="inspect-kubernetes-installation"></a>Kubernetes-installatie inspecteren

### <a name="pods-services-ingress"></a>Pods, Services, Ingress
Application Gateway Ingress Controller (AGIC) controleert continu de volgende Kubernetes-bronnen: [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) of [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Service](https://kubernetes.io/docs/concepts/services-networking/service/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


AGIC moet als volgt kunnen functioneren zoals verwacht:
  1. AKS moet een of meer gezonde **peulen**hebben.
     Controleer dit vanuit [Cloud Shell](https://shell.azure.com/) met `kubectl get pods -o wide --show-labels` Als `apsnetapp`u een Pod met een , uw uitvoer kan er als volgt uitzien:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Een of meer **services,** verwijzend naar `selector` de pods hierboven via bijpassende labels.
     Controleer dit vanuit [Cloud Shell](https://shell.azure.com/) met`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, geannoteerd met `kubernetes.io/ingress.class: azure/application-gateway`, verwijzend naar de service hierboven Verifieer dit van Cloud [Shell](https://shell.azure.com/) met`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Bekijk de aantekeningen van de `kubectl get ingress aspnetapp -o yaml` inbinnendringen `aspnetapp` hierboven: (substituut met de naam van uw binnendringen)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     De invallende bron moet worden `kubernetes.io/ingress.class: azure/application-gateway`geannoteerd met .
 

### <a name="verify-observed-namespace"></a>Waargenomen naamruimte verifiëren

* Download de bestaande naamruimten in het Kubernetes-cluster. In welke naamruimte wordt uw app uitgevoerd? Houdt AGIC naar die naamruimte? Raadpleeg de documentatie [Multiple Namespace Support](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) over het correct configureren van waargenomen naamruimten.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* De AGIC-pod moet `default` zich in `NAMESPACE`de naamruimte bevinden (zie kolom ). Een gezonde pod `Running` zou `STATUS` hebben in de kolom. Er moet minstens één AGIC pod zijn.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Als de AGIC-pod`STATUS` niet gezond is ( `Running`kolom van de bovenstaande opdracht is niet):
  - logboeken krijgen om te begrijpen waarom:`kubectl logs <pod-name>`
  - voor het vorige exemplaar van de pod:`kubectl logs <pod-name> --previous`
  - beschrijf de pod om meer context te krijgen:`kubectl describe pod <pod-name>`


* Heeft u een Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) en [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) bronnen?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Is uw [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) geannoteerd met: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC zal alleen kijken voor Kubernetes Ingress bronnen die deze annotatie hebben.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC zendt Kubernetes-gebeurtenissen uit voor bepaalde kritieke fouten. U deze bekijken:
  - in uw terminal via`kubectl get events --sort-by=.metadata.creationTimestamp`
  - in uw browser met behulp van de [Kubernetes Web UI (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Logboekregistratieniveaus

AGIC heeft 3 logging levels. Niveau 1 is de standaard lijn en het toont een minimaal aantal logregels.
Niveau 5, aan de andere kant, zou alle logs weer te geven, met inbegrip van ontsmetinhoud van config toegepast op ARM.

De Kubernetes-community heeft 9 niveaus van logboekregistratie vastgesteld voor de [kubectl-tool.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) In deze repository gebruiken we 3 van deze, met vergelijkbare semantiek:


| Uitgebreidheid | Beschrijving |
|-----------|-------------|
|  1        | Standaardlogboekniveau; toont opstartgegevens, waarschuwingen en fouten |
|  3        | Uitgebreide informatie over gebeurtenissen en wijzigingen; lijsten met gemaakte objecten |
|  5        | Registreert gemarshalde objecten; toont ontsmette JSON config toegepast op ARM |


De verbosity niveaus zijn `verbosityLevel` verstelbaar via de variabele in het [helm-config.yaml](#sample-helm-config-file) bestand. Verhoog het verbosity-niveau om `5` de JSON config naar [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)te sturen:
  - toe `verbosityLevel: 5` te voegen op een lijn door zelf in [helm-config.yaml](#sample-helm-config-file) en opnieuw te installeren
  - logboeken te krijgen met`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Voorbeeld helm config-bestand
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

