---
title: Problemen met de Application Gateway-controller oplossen
description: Dit artikel bevat informatie over het oplossen van veelvoorkomende vragen en/of problemen met de Application Gateway ingangs controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2019
ms.author: caya
ms.openlocfilehash: 6ee6239f9b1fbb66bac5d3920a888fb5a288a300
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513326"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Veelvoorkomende vragen of problemen met de ingangs controller oplossen

[Azure Cloud shell](https://shell.azure.com/) is de handigste manier om problemen met uw AKS-en AGIC-installatie op te lossen. Start uw shell vanuit [shell.Azure.com](https://shell.azure.com/) of door op de koppeling te klikken:

[![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testen met een eenvoudige Kubernetes-app

In de onderstaande stappen wordt ervan uitgegaan:
  - U hebt een AKS-cluster met geavanceerde netwerken ingeschakeld
  - AGIC is geïnstalleerd op het AKS-cluster
  - U hebt al een Application Gateway hebt in een VNET dat is gedeeld met uw AKS-cluster

Als u wilt controleren of de installatie van Application Gateway + AKS + AGIC correct is ingesteld, implementeert u de eenvoudigste mogelijke app:

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

Kopieer en plak alle regels vanuit het bovenstaande script in een [Azure Cloud shell](https://shell.azure.com/). Zorg ervoor dat de volledige opdracht is gekopieerd, te beginnen met `cat` en de laatste `EOF`op te nemen.

![Toep assen](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Nadat een geslaagde implementatie van de app boven uw AKS-cluster een nieuwe Pod, dienst en ingang heeft.

Haal de lijst met voor het gehele [Cloud shell](https://shell.azure.com/): `kubectl get pods -o wide`.
Er wordt verwacht dat er een pod met de naam ' test-agic-app-pod ' is gemaakt. Het heeft een IP-adres. Dit adres moet zich binnen het VNET van de Application Gateway bevindt, dat wordt gebruikt met AKS.

![gehele](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

De lijst met services ophalen: `kubectl get services -o wide`. Verwacht wordt dat er een service met de naam ' test-agic-app-service ' wordt weer geven.

![gehele](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

De lijst met ingresses: `kubectl get ingress`ophalen. Er wordt verwacht dat een ingangs bron met de naam ' test-agic-app-ingang ' is gemaakt. De resource heeft de hostnaam ' test.agic.contoso.com '.

![gehele](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Een van de peulen is AGIC. in `kubectl get pods` wordt een lijst weer gegeven met een van de peulen waarvan een van deze begint met ' Inkomend-Azure '. Haal alle logboeken van die pod met `kubectl logs <name-of-ingress-controller-pod>` om te controleren of de implementatie is geslaagd. Bij een geslaagde implementatie zijn de volgende regels aan het logboek toegevoegd:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

U kunt ook van [Cloud shell](https://shell.azure.com/) alleen de regels ophalen met een geslaagde Application Gateway configuratie met `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`, waarbij `<ingress-azure....>` de exacte naam moet zijn van de AGIC pod.

Application Gateway wordt de volgende configuratie toegepast:

- Listener: ![listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Routerings regel: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Back-end-groep:
  - Er is één IP-adres in de back-endadresgroep en dit komt overeen met het IP-adres van de pod die eerder zijn waargenomen met `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Ten slotte kunnen we de `cURL` opdracht in [Cloud shell](https://shell.azure.com/) gebruiken om een http-verbinding met de zojuist geïmplementeerde app tot stand te brengen:

1. `kubectl get ingress` gebruiken om het open bare IP-adres van Application Gateway op te halen
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` gebruiken

![gehele](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Een resultaat van `HTTP/1.1 200 OK` geeft aan dat het Application Gateway + AKS + AGIC-systeem werkt zoals verwacht.


## <a name="inspect-kubernetes-installation"></a>Kubernetes-installatie controleren

### <a name="pods-services-ingress"></a>Peul, services, inkomend
Application Gateway ingangs controller (AGIC) bewaakt voortdurend de volgende Kubernetes bronnen: [implementatie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) of [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [service](https://kubernetes.io/docs/concepts/services-networking/service/), [](https://kubernetes.io/docs/concepts/services-networking/ingress/) inkomend verkeer


Het volgende moet worden uitgevoerd om AGIC te laten werken zoals verwacht:
  1. AKS moet een of **meer goeden**hebben.
     Controleer dit op [Cloud shell](https://shell.azure.com/) met `kubectl get pods -o wide --show-labels` als u een pod hebt met een `apsnetapp`, kan uw uitvoer er als volgt uitzien:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Een of meer **Services**, die verwijzen naar de bovenstaande treffers via overeenkomende `selector` labels.
     Controleer dit op [Cloud shell](https://shell.azure.com/) met `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. Inkomend, aantekeningen maken met **`kubernetes.io/ingress.class: azure/application-gateway`en verwijzen**naar de bovenstaande service, controleer dit vanuit [Cloud shell](https://shell.azure.com/) met `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Bekijk de aantekeningen van de bovenstaande inkomende leden: `kubectl get ingress aspnetapp -o yaml` (Vervang `aspnetapp` door de naam van uw ingang)
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

     De ingangs resource moet aantekeningen hebben met `kubernetes.io/ingress.class: azure/application-gateway`.
 

### <a name="verify-observed-namespace"></a>Waargenomen naam ruimte controleren

* Haal de bestaande naam ruimten op in het Kubernetes-cluster. In welke naam ruimte wordt de app uitgevoerd? Is AGIC de naam ruimte aan het volgen? Raadpleeg de documentatie over [meerdere naam ruimte ondersteuning](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) voor het correct configureren van geobserveerde naam ruimten.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* De AGIC-pod moet zich bevinden in de naam ruimte `default` (zie kolom `NAMESPACE`). Een gezonde pod zou `Running` hebben in de kolom `STATUS`. Er moet ten minste één AGIC-pod zijn.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Als de AGIC pod niet in orde is (`STATUS` kolom van de bovenstaande opdracht niet `Running`):
  - Logboeken ophalen om te begrijpen waarom: `kubectl logs <pod-name>`
  - voor het vorige exemplaar van de Pod: `kubectl logs <pod-name> --previous`
  - Beschrijf de pod om meer context te krijgen: `kubectl describe pod <pod-name>`


* Hebt u een Kubernetes- [service](https://kubernetes.io/docs/concepts/services-networking/service/) en [zijn](https://kubernetes.io/docs/concepts/services-networking/ingress/) er bronnen voor binnenkomend verkeer?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Maakt uw [ingangen](https://kubernetes.io/docs/concepts/services-networking/ingress/) aantekeningen met: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC controleert alleen op Kubernetes ingangs resources die deze aantekening hebben.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC verzendt Kubernetes-gebeurtenissen voor bepaalde kritieke fouten. U kunt deze bekijken:
  - in uw terminal via `kubectl get events --sort-by=.metadata.creationTimestamp`
  - in uw browser met behulp van de [Kubernetes-webgebruikersinterface (dash board)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Registratie niveaus

AGIC heeft drie registratie niveaus. Niveau 1 is de standaard instelling en geeft het minimale aantal logboek regels weer.
Op niveau 5 worden daarentegen alle logboeken weer gegeven, inclusief de opgeschoonde inhoud van de configuratie die wordt toegepast op ARM.

De Kubernetes-Community heeft negen niveaus voor logboek registratie vastgesteld voor het [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) -hulp programma. In deze opslag plaats gebruiken we drie van deze, met vergelijk bare semantiek:


| Uitbreidings | Beschrijving |
|-----------|-------------|
|  1        | Standaard logboek niveau; opstart Details, waarschuwingen en fouten weer geven |
|  3        | Uitgebreide informatie over gebeurtenissen en wijzigingen; lijsten met gemaakte objecten |
|  5        | Gemarshalde objecten vastleggen; gezuiverde JSON-configuratie weer geven die wordt toegepast op ARM |


De uitbreidings niveaus zijn aanpasbaar via de `verbosityLevel` variabele in het bestand [helm-config. yaml](#sample-helm-config-file) . Verhoog het niveau uitgebreid tot `5` om de JSON-configuratie die wordt verzonden naar [arm](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)te verkrijgen:
  - `verbosityLevel: 5` op een regel zelfstandig toevoegen in [helm-config. yaml](#sample-helm-config-file) en opnieuw installeren
  - Logboeken met `kubectl logs <pod-name>` ophalen

### <a name="sample-helm-config-file"></a>Voor beeld van helm-configuratie bestand
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

