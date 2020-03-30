---
title: Beveiligde pods met netwerkbeleid in Azure Kubernetes Service (AKS)
description: Meer informatie over het beveiligen van verkeer dat in en uit pods stroomt met kubernetes-netwerkbeleid in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 37b6ebd1c8b147db0a9cead4678a0b2bb4ed234d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473605"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Verkeer tussen pods beveiligen met netwerkbeleid in Azure Kubernetes Service (AKS)

Wanneer u moderne, op microservices gebaseerde toepassingen in Kubernetes uitvoert, wilt u vaak bepalen welke componenten met elkaar kunnen communiceren. Het principe van de minste bevoegdheden moet worden toegepast op hoe verkeer kan stromen tussen pods in een AKS-cluster (Azure Kubernetes Service). Stel dat u verkeer rechtstreeks naar back-endtoepassingen wilt blokkeren. Met de functie *Netwerkbeleid* in Kubernetes u regels definiëren voor binnendringend en uitgangsverkeer tussen pods in een cluster.

In dit artikel ziet u hoe u de netwerkbeleidsengine installeert en kubernetes-netwerkbeleid maakt om de verkeersstroom tussen pods in AKS te beheren. Netwerkbeleid mag alleen worden gebruikt voor op Linux gebaseerde knooppunten en pods in AKS.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.61 of hoger installeren en configureren. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

> [!TIP]
> Als u de functie netwerkbeleid tijdens de preview hebt gebruikt, raden we u aan [een nieuw cluster te maken.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Als u bestaande testclusters wilt blijven gebruiken die het netwerkbeleid tijdens de preview hebben gebruikt, u uw cluster upgraden naar een nieuwe Kubernetes-versie voor de nieuwste GA-release en vervolgens het volgende YAML-manifest implementeren om de crashende metricsserver en Kubernetes op te lossen Dashboard. Deze oplossing is alleen vereist voor clusters die de Calico-netwerkbeleidsengine hebben gebruikt.
>
> Als best practice voor beveiliging [bekijkt u de inhoud van dit YAML-manifest][calico-aks-cleanup] om te begrijpen wat er in het AKS-cluster wordt geïmplementeerd.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Overzicht van het netwerkbeleid

Alle pods in een AKS-cluster kunnen standaard verkeer verzenden en ontvangen. Om de beveiliging te verbeteren, u regels definiëren die de verkeersstroom regelen. Back-end toepassingen worden vaak alleen blootgesteld aan de vereiste front-end diensten, bijvoorbeeld. Of databasecomponenten zijn alleen toegankelijk voor de toepassingslagen die ermee verbinding maken.

Netwerkbeleid is een Kubernetes-specificatie die toegangsbeleid voor communicatie tussen Pods definieert. Met netwerkbeleid definieert u een geordende set regels voor het verzenden en ontvangen van verkeer en past u deze toe op een verzameling pods die overeenkomen met een of meer labelselectors.

Deze netwerkbeleidsregels worden gedefinieerd als YAML-manifesten. Netwerkbeleid kan worden opgenomen als onderdeel van een breder manifest dat ook een implementatie of service maakt.

### <a name="network-policy-options-in-aks"></a>Netwerkbeleidsopties in AKS

Azure biedt twee manieren om het netwerkbeleid te implementeren. U kiest een optie voor netwerkbeleid wanneer u een AKS-cluster maakt. De beleidsoptie kan niet worden gewijzigd nadat het cluster is gemaakt:

* Azure's eigen implementatie, *azure-netwerkbeleid*genaamd.
* *Calico Network Policies*, een open-source netwerk- en netwerkbeveiligingsoplossing opgericht door [Tigera][tigera].

Beide implementaties gebruiken Linux *IPTables* om het opgegeven beleid af te dwingen. Beleidsregels worden vertaald in sets toegestane en niet-toegestane IP-paren. Deze paren worden vervolgens geprogrammeerd als IPTable-filterregels.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Verschillen tussen Azure- en Calico-beleid en hun mogelijkheden

| Mogelijkheid                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Ondersteunde platforms                      | Linux                      | Linux                       |
| Ondersteunde netwerkopties             | Azure CNI                  | Azure CNI en kubenet       |
| Naleving van kubernetes-specificatie | Alle ondersteunde beleidstypen |  Alle ondersteunde beleidstypen |
| Extra functies                      | Geen                       | Uitgebreid beleidsmodel bestaande uit Global Network Policy, Global Network Set en Host Endpoint. Zie `calicoctl` [calicoctl-gebruikersreferentie][calicoctl]voor meer informatie over het gebruik van de CLI voor het beheren van deze uitgebreide functies. |
| Ondersteuning                                  | Ondersteund door Azure-ondersteunings- en engineeringteam | Calico community support. Zie Ondersteuningsopties voor Project [Calico][calico-support]voor meer informatie over extra betaalde ondersteuning. |
| Logboekregistratie                                  | Regels die in IPTables zijn toegevoegd / verwijderd, worden op elke host geregistreerd onder */var/log/azure-npm.log* | Zie [Calico-componentlogboeken voor][calico-logs] meer informatie |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Een AKS-cluster maken en netwerkbeleid inschakelen

Als u het netwerkbeleid in actie wilt weergeven, maken en vervolgens uitbreiden we een beleid dat de verkeersstroom definieert:

* Weiger al het verkeer naar de pod.
* Verkeer toestaan op basis van podlabels.
* Verkeer toestaan op basis van naamruimte.

Laten we eerst een AKS-cluster maken dat netwerkbeleid ondersteunt. 

> [!IMPORTANT]
>
> De functie netwerkbeleid kan alleen worden ingeschakeld wanneer het cluster wordt gemaakt. U het netwerkbeleid voor een bestaand AKS-cluster niet inschakelen.

Als u Azure Network Policy wilt gebruiken, moet u de [Azure CNI-plug-in][azure-cni] gebruiken en uw eigen virtuele netwerk en subnetten definiëren. Zie [geavanceerde netwerken configureren][use-advanced-networking]voor meer gedetailleerde informatie over het plannen van de vereiste subnetbereiken. Calico Network Policy kan worden gebruikt met dezelfde Azure CNI-plug-in of met de Kubenet CNI-plug-in.

Het volgende voorbeeldscript:

* Hiermee maakt u een virtueel netwerk en subnet.
* Hiermee maakt u een Azure Active Directory -serviceprincipal (Azure AD) voor gebruik met het AKS-cluster.
* Wijst *inzendermachtigingen* toe voor de AKS-clusterserviceprincipal op het virtuele netwerk.
* Hiermee maakt u een AKS-cluster in het gedefinieerde virtuele netwerk en wordt netwerkbeleid mogelijk.
    * De *azure* optie azure-netwerkbeleid wordt gebruikt. Als u Calico als optie voor `--network-policy calico` netwerkbeleid wilt gebruiken, gebruikt u de parameter. Opmerking: Calico kan worden `--network-plugin azure` `--network-plugin kubenet`gebruikt met een van beide of .

Zorg voor uw eigen veilige *SP_PASSWORD.* U de *RESOURCE_GROUP_NAME-* en *CLUSTER_NAME* variabelen vervangen:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Het duurt een paar minuten om het cluster te maken. Wanneer het cluster klaar `kubectl` is, configureert u verbinding met uw Kubernetes-cluster met behulp van de opdracht [az aks get-credentials.][az-aks-get-credentials] Met deze opdracht worden referenties gedownload en configureert u de Kubernetes CLI om deze te gebruiken:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Al het binnenkomende verkeer naar een pod weigeren

Voordat u regels definieert om specifiek netwerkverkeer toe te staan, maakt u eerst een netwerkbeleid om al het verkeer te weigeren. Dit beleid geeft u een startpunt om te beginnen met alleen het gewenste verkeer op de witte lijst te krijgen. U ook duidelijk zien dat het verkeer wordt verwijderd wanneer het netwerkbeleid wordt toegepast.

Laten we voor de voorbeeldtoepassingsomgeving en -verkeersregels eerst een naamruimte maken die *de naamsruimte* wordt genoemd om de voorbeeldpods uit te voeren:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Maak een voorbeeld back-end pod die NGINX draait. Deze back-end pod kan worden gebruikt om een voorbeeld back-end web-based applicatie te simuleren. Maak deze pod in de *naamruimte voor ontwikkeling* en open poort *80* om webverkeer weer te geven. Label de pod met *app=webapp,role=backend,* zodat we deze kunnen targeten met een netwerkbeleid in de volgende sectie:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Maak een andere pod en voeg een terminalsessie toe om te testen of u de standaard NGINX-webpagina bereiken:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Gebruik bij de `wget` shell prompt om te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

De volgende voorbeelduitvoer geeft aan dat de standaard NGINX-webpagina is geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de bijgevoegde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Een netwerkbeleid maken en toepassen

Nu u hebt bevestigd dat u de basiswebpagina NGINX op de voorbeeldback-endpod gebruiken, maakt u een netwerkbeleid om al het verkeer te weigeren. Maak een `backend-policy.yaml` bestand met de naam en plak het volgende YAML-manifest. Dit manifest gebruikt een *podSelector* om het beleid toe te voegen aan pods met de *app:webapp,rol:backendlabel,* zoals uw voorbeeld NGINX-pod. Er worden geen regels gedefinieerd *onder binnendringen,* dus al het inkomende verkeer naar de pod wordt geweigerd:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Ga [https://shell.azure.com](https://shell.azure.com) naar Azure Cloud Shell openen in uw browser.

Pas het netwerkbeleid toe met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Het netwerkbeleid testen

Laten we eens kijken of u de NGINX-webpagina op de back-endpod opnieuw gebruiken. Maak een andere testpod en voeg een terminalsessie toe:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Gebruik bij de `wget` shell prompt om te zien of u toegang hebt tot de standaard NGINX-webpagina. Stel deze keer een time-outwaarde in op *2* seconden. Het netwerkbeleid blokkeert nu al het binnenkomende verkeer, zodat de pagina niet kan worden geladen, zoals in het volgende voorbeeld wordt weergegeven:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Sluit de bijgevoegde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Binnenkomend verkeer toestaan op basis van een podlabel

In het vorige gedeelte werd een back-end NGINX-pod gepland en is een netwerkbeleid gemaakt om al het verkeer te weigeren. Laten we een front-end pod maken en het netwerkbeleid bijwerken om verkeer van front-end pods toe te staan.

Werk het netwerkbeleid bij om verkeer van pods toe te staan met de *labels-app:webapp,rol:frontend* en in elke naamruimte. Bewerk het vorige *backend-policy.yaml-bestand* en voeg regels voor *invallen* van matchLabels toe, zodat uw manifest er als volgt uitziet:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> In dit netwerkbeleid wordt een *naamruimtekiezer en* een *podSelector-element* gebruikt voor de invallenregel. De YAML-syntaxis is belangrijk dat de invallende regels additief zijn. In dit voorbeeld moeten beide elementen overeenkomen met de inkomende regel die moet worden toegepast. Kubernetes-versies vóór *1.12* interpreteren deze elementen mogelijk niet correct en beperken het netwerkverkeer zoals u verwacht. Zie [Gedrag van en naar selectors voor][policy-rules]meer informatie over dit gedrag.

Pas het bijgewerkte netwerkbeleid toe met de opdracht [kubectl apply en][kubectl-apply] geef de naam van uw YAML-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

Plan een pod die is gelabeld als *app=webapp,role=frontend* en voeg een terminalsessie toe:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Gebruik bij de `wget` shell prompt om te zien of u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

Omdat de ingress regel verkeer met pods met de labels *app: webapp, rol: frontend*, het verkeer van de front-end pod is toegestaan. In de volgende voorbeelduitvoer wordt de standaard NGINX-webpagina weergegeven:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de bijgevoegde terminalsessie af. De pod wordt automatisch verwijderd.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Een pod testen zonder bijpassend label

Het netwerkbeleid maakt verkeer van pods gelabeld *app: webapp,rol: frontend*, maar moet alle andere verkeer te weigeren. Laten we testen om te zien of een andere pod zonder deze labels toegang heeft tot de back-end NGINX-pod. Maak een andere testpod en voeg een terminalsessie toe:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Gebruik bij de `wget` shell prompt om te zien of u toegang hebt tot de standaard NGINX-webpagina. Het netwerkbeleid blokkeert het binnenkomende verkeer, zodat de pagina niet kan worden geladen, zoals in het volgende voorbeeld wordt weergegeven:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Sluit de bijgevoegde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Alleen verkeer toestaan vanuit een gedefinieerde naamruimte

In de vorige voorbeelden hebt u een netwerkbeleid gemaakt dat al het verkeer heeft geweigerd en vervolgens het beleid bijgewerkt om verkeer van pods met een specifiek label toe te staan. Een andere veel voorkomende noodzaak is om het verkeer te beperken tot alleen binnen een bepaalde naamruimte. Als de vorige voorbeelden voor verkeer in een *ontwikkelingsnaamruimte* waren, maakt u een netwerkbeleid dat voorkomt dat verkeer uit een andere naamruimte, zoals *productie,* de pods bereikt.

Maak eerst een nieuwe naamruimte om een productienaamruimte te simuleren:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Plan een testpod in de *productienaamruimte* die is gelabeld als *app=webapp,role=frontend*. Een terminalsessie toevoegen:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Gebruik bij de `wget` shell prompt om te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend.development
```

Omdat de labels voor de pod overeenkomen met wat momenteel is toegestaan in het netwerkbeleid, is het verkeer toegestaan. Het netwerkbeleid kijkt niet naar de naamruimten, alleen naar de podlabels. In de volgende voorbeelduitvoer wordt de standaard NGINX-webpagina weergegeven:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de bijgevoegde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

### <a name="update-the-network-policy"></a>Het netwerkbeleid bijwerken

Laten we de sectie inkomende *regelnaamruimte bijwerkenOm* alleen verkeer vanuit de *ontwikkelingsnaamruimte* toe te staan. Bewerk het *manifestbestand backend-policy.yaml* zoals in het volgende voorbeeld:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

In complexere voorbeelden u meerdere invallenregels definiëren, zoals een *naamruimtekiezer* en vervolgens een *podSelector*.

Pas het bijgewerkte netwerkbeleid toe met de opdracht [kubectl apply en][kubectl-apply] geef de naam van uw YAML-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Het bijgewerkte netwerkbeleid testen

Plan een andere pod in de *productienaamruimte* en voeg een terminalsessie toe:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Gebruik bij de `wget` shell prompt om te zien dat het netwerkbeleid nu verkeer weigert:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Exit uit de testpod:

```console
exit
```

Als verkeer uit de *productienaamruimte* wordt geweigerd, plant u een testpod terug in de *naamruimte voor ontwikkeling* en koppelt u een terminalsessie:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Gebruik bij de `wget` shellprompt om te zien dat het netwerkbeleid het verkeer toestaat:

```console
wget -qO- http://backend
```

Verkeer is toegestaan omdat de pod is gepland in de naamruimte die overeenkomt met wat is toegestaan in het netwerkbeleid. In de volgende voorbeelduitvoer wordt de standaard NGINX-webpagina weergegeven:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de bijgevoegde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebben we twee naamruimten gemaakt en een netwerkbeleid toegepast. Als u deze bronnen wilt opschonen, gebruikt u de opdracht [kubectl verwijderen][kubectl-delete] en geeft u de bronnamen op:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Volgende stappen

Zie [Netwerkconcepten voor toepassingen in Azure Kubernetes Service (AKS) voor][concepts-network]meer informatie over netwerkbronnen.

Zie [Netwerkbeleid][kubernetes-network-policies]voor Kubernetes voor meer informatie over beleid.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
