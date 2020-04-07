---
title: Gebruikersgedefinieerde routes (UDR) aanpassen in Azure Kubernetes Service (AKS)
description: Meer informatie over het definiëren van een aangepaste uitgangsroute in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 3780680c485aebf1ffc654d31c577821a9b96fff
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676507"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Clusteruitgang aanpassen met een door de gebruiker gedefinieerde route (voorbeeld)

Uitgang en een AKS-cluster kunnen worden aangepast aan specifieke scenario's. Standaard zal AKS een Standaard SKU Load Balancer inrichten die moet worden ingesteld en gebruikt voor het uittreden. De standaardinstelling voldoet echter mogelijk niet aan de vereisten van alle scenario's als openbare IP's niet zijn toegestaan of als er extra hop nodig is om uit te stappen.

In dit artikel wordt uitgelegd hoe u de uitgangsroute van een cluster aanpassen om aangepaste netwerkscenario's te ondersteunen, zoals scenario's die openbare IP's niet toestaan en waarvoor het cluster achter een netwerkvirtueel toestel (NVA) moet zitten.

> [!IMPORTANT]
> AKS preview functies zijn self-service en worden aangeboden op een opt-in basis. Previews worden verstrekt *zoals het is* en zoals *beschikbaar* en zijn uitgesloten van de service-level agreement (SLA) en beperkte garantie. AKS previews worden gedeeltelijk gedekt door customer support op een *best effort* basis. Daarom zijn de functies niet bedoeld voor productiegebruik. Zie de volgende ondersteuningsartikelen voor meer informatie:
>
> * [AKS-ondersteuningsbeleid](support-policies.md)
> * [Veelgestelde vragen over Azure Support](faq.md)

## <a name="prerequisites"></a>Vereisten
* Azure CLI-versie 2.0.81 of hoger
* Versie 0.4.28 van Azure CLI Preview-extensie of hoger
* API-versie `2020-01-01` van of groter

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>De nieuwste Azure CLI AKS Preview-extensie installeren
Als u het uitgaande type van een cluster wilt instellen, hebt u de Versie 0.4.18 van Azure CLI AKS Preview of hoger nodig. Installeer de Azure CLI AKS Preview-extensie met behulp van de opdracht Az-extensie toevoegen en controleer vervolgens op beschikbare updates met behulp van de volgende opdracht voor het bijwerken van AZ-extensie:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Beperkingen
* Tijdens de `outboundType` preview kan alleen worden gedefinieerd op de tijd van het maken van het cluster en kan het daarna niet meer worden bijgewerkt.
* Tijdens de `outboundType` preview moeten AKS-clusters Azure CNI gebruiken. Kubenet is configureerbaar, gebruik vereist handmatige associaties van de routetabel naar het AKS-subnet.
* Voor `outboundType` instelling zijn AKS-clusters `vm-set-type` nodig met een `VirtualMachineScaleSets` van en `load-balancer-sku` van `Standard`.
* Voor `outboundType` het instellen `UDR` op een waarde van vereist een door de gebruiker gedefinieerde route met geldige uitgaande connectiviteit voor het cluster.
* Als `outboundType` u een `UDR` waarde instelt van impliceert dat het IP-adres van de binnenkomende bron naar de load-balancer kan overeenkomen, komt het uitgaande bestemmingsadres van het cluster **mogelijk niet overeen** met het uitgaande bestemmingsadres van het cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Overzicht van uitgaande typen in AKS

Een AKS-cluster kan worden `outboundType` aangepast met een unieke type load balancer of door de gebruiker gedefinieerde routering.

> [!IMPORTANT]
> Uitgaand type heeft alleen invloed op het uitgaande verkeer van uw cluster. Zie [het instellen van ingress controllers](ingress-basic.md) voor meer informatie.

### <a name="outbound-type-of-loadbalancer"></a>Uitgaand type loadBalancer

Als `loadBalancer` aks is ingesteld, voltooit het automatisch de volgende instelling. De load balancer wordt gebruikt voor het uitwijken via een door AKS toegewezen openbaar IP. Een uitgaand `loadBalancer` type ondersteunt Kubernetes-services van het type `loadBalancer`, die uitgaande van de load balancer verwachten die is gemaakt door de AKS-resourceprovider.

De volgende installatie wordt uitgevoerd door AKS.
   * Er is een openbaar IP-adres ingericht voor clusteruitgangen.
   * Het openbare IP-adres wordt toegewezen aan de resource load balancer.
   * Backend-groepen voor de load balancer zijn ingesteld voor agentknooppunten in het cluster.

Hieronder vindt u een netwerktopologie die standaard is `outboundType` geïmplementeerd `loadBalancer`in AKS-clusters, die een van .

![uitgaand type-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Uitgaand type userDefinedRouting

> [!NOTE]
> Het gebruik van uitgaand type is een geavanceerd netwerkscenario en vereist een goede netwerkconfiguratie.

Als `userDefinedRouting` deze is ingesteld, configureert AKS niet automatisch uitgangspaden. Het volgende zal naar verwachting worden gedaan door **de gebruiker**.

Cluster moet worden geïmplementeerd in een bestaand virtueel netwerk met een subnet dat is geconfigureerd. Er moet een geldige door de gebruiker gedefinieerde route (UDR) bestaan op het subnet met uitgaande connectiviteit.

De AKS-resourceprovider implementeert een standaard load balancer (SLB). De load balancer is niet geconfigureerd met regels en [brengt geen kosten in rekening totdat een regel is geplaatst.](https://azure.microsoft.com/pricing/details/load-balancer/) AKS zal **niet** automatisch een openbaar IP-adres voor de SLB frontend inrichten. AKS configureert de backendpool load balancer **niet** automatisch.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Een cluster implementeren met een uitgaand type UDR en Azure Firewall

Als u de toepassing van een cluster met uitgaand type wilt illustreren met behulp van een door de gebruiker gedefinieerde route, kan een cluster worden geconfigureerd op een virtueel netwerk dat is gekeken met een Azure Firewall.

![Vergrendelde topologie](media/egress-outboundtype/outboundtype-udr.png)

* Ingress wordt gedwongen om door firewallfilters te stromen
   * Een geïsoleerd subnet bevat een interne load balancer voor routering naar agentknooppunten
   * Agentknooppunten worden geïsoleerd in een speciaal subnet
* Uitgaande aanvragen starten van agentknooppunten naar het interne IP-adres van Azure Firewall met behulp van een door de gebruiker gedefinieerde route
   * Aanvragen van AKS-agentknooppunten volgen een UDR die is geplaatst op het subnet waar het AKS-cluster in is geïmplementeerd.
   * Azure Firewall valt uit het virtuele netwerk van een openbare IP-frontend
   * Toegang tot het AKS-besturingsvlak wordt beschermd door een NSG, waardoor het IP-adres van de firewall frontend is ingeschakeld
   * Toegang tot het openbare internet of andere Azure-services stromen van en naar het IP-adres van de firewall frontend

### <a name="set-configuration-via-environment-variables"></a>Configuratie instellen via omgevingsvariabelen

Definieer een set omgevingsvariabelen die moeten worden gebruikt in resourcecreaties.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Stel vervolgens abonnements-id's in.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Een virtueel netwerk met meerdere subnetten maken

Inrichten van een virtueel netwerk met drie afzonderlijke subnetten, een voor het cluster, een voor de firewall, en een voor service-ingress.

![Lege netwerktopologie](media/egress-outboundtype/empty-network.png)

Maak een resourcegroep om alle resources vast te houden.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Maak een twee virtuele netwerken om het AKS-cluster en de Azure Firewall te hosten. Elk heeft zijn eigen subnet. Laten we beginnen met het AKS-netwerk.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Een Azure Firewall maken en instellen met een UDR

Inkomende en uitgaande azure firewall-regels moeten zijn geconfigureerd. Het belangrijkste doel van de firewall is om organisaties in staat te stellen gedetailleerde binnenvallen en uitgang verkeersregels in en uit het AKS-cluster in te stellen.

![Firewall en UDR](media/egress-outboundtype/firewall-udr.png)

Maak een standaard SKU-openbare IP-bron die wordt gebruikt als frontend-adres van Azure Firewall.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registreer de preview-cli-extensie om een Azure Firewall te maken.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Het IP-adres dat eerder is gemaakt, kan nu worden toegewezen aan de frontend van de firewall.
> [!NOTE]
> Het instellen van het openbare IP-adres op de Azure Firewall kan enkele minuten duren.
> 
> Als er herhaaldelijk fouten worden ontvangen op de onderstaande opdracht, verwijdert u de bestaande firewall en het openbare IP-adres en bedien t u tegelijkertijd het openbare IP-adres en de Azure Firewall via de portal.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Wanneer de vorige opdracht is geslaagd, slaat u het IP-adres van de firewall-frontend op voor de configuratie later.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Een UDR maken met een hop naar Azure Firewall

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u een van de standaardroutering van Azure wilt wijzigen, u dit doen door een routetabel te maken.

Maak een lege routetabel die aan een bepaald subnet moet worden gekoppeld. In de routetabel wordt de volgende hop gedefinieerd als de Azure Firewall die hierboven is gemaakt. Elk subnet kan zijn gekoppeld aan geen enkele of één routetabel.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Zie documentatie over [de tabel voor virtuele netwerkroute's](../virtual-network/virtual-networks-udr-overview.md#user-defined) over hoe u de standaardsysteemroutes van Azure overschrijven of extra routes toevoegen aan de routetabel van een subnet.

## <a name="adding-network-firewall-rules"></a>Netwerkfirewallregels toevoegen

> [!WARNING]
> Hieronder ziet u een voorbeeld van het toevoegen van een firewallregel. Alle uitgangseindpunten die zijn gedefinieerd in de [vereiste uitgangseindpunten,](egress.md) moeten worden ingeschakeld door toepassingsfirewallregels voor AKS-clusters om te functioneren. Zonder dat deze eindpunten zijn ingeschakeld, kan uw cluster niet werken.

Hieronder vindt u een voorbeeld van een netwerk- en toepassingsregel. We voegen een netwerkregel toe waarmee elk protocol, bronadres, bestemmingsadres en bestemmingspoorten mogelijk is. We voegen ook een toepassingsregel toe voor **een aantal** eindpunten die door AKS worden vereist.

In een productiescenario moet u alleen toegang tot vereiste eindpunten voor uw toepassing inschakelen en de eindpunten die zijn gedefinieerd in [AKS-vereiste uitgang.](egress.md)

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Zie [Azure Firewall-documentatie](https://docs.microsoft.com/azure/firewall/overview) voor meer informatie over de Azure Firewall-service.

## <a name="associate-the-route-table-to-aks"></a>De routetabel koppelen aan AKS

Als u het cluster wilt koppelen aan de firewall, moet het speciale subnet voor het subnet van het cluster verwijzen naar de bovenstaande routetabel. Koppeling kan worden gedaan door een opdracht uit te geven aan het virtuele netwerk dat zowel het cluster als de firewall vasthoudt om de routetabel van het subnet van het cluster bij te werken.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>AKS implementeren met uitgaand type UDR naar het bestaande netwerk

Nu kan een AKS-cluster worden geïmplementeerd in de bestaande virtuele netwerkinstelling. Om een cluster-uitgaande tekst in te stellen op door de gebruiker gedefinieerde routering, moet een bestaand subnet aan AKS worden verstrekt.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Een serviceprincipal maken met toegang tot voorzieningen binnen het bestaande virtuele netwerk

Een serviceprincipal wordt door AKS gebruikt om clusterbronnen te maken. De serviceprincipal die wordt doorgegeven tijdens het maken van de tijd, wordt gebruikt om onderliggende AKS-resources te maken, zoals VM's, Opslag en Load Balancers die door AKS worden gebruikt. Als er te weinig machtigingen worden verleend, kan het geen AKS-cluster inrichten.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Vervang nu `APPID` `PASSWORD` de en hieronder door de service principal appid en service principal password automatisch gegenereerd door de vorige opdracht uitvoer. We verwijzen naar de VNET-bron-ID om de machtigingen aan de serviceprincipal te verlenen, zodat AKS er resources in kan implementeren.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>AKS implementeren

Ten slotte kan het AKS-cluster worden geïmplementeerd in het bestaande subnet dat we voor het cluster hebben toegewezen. Het doelsubnet waarin moet worden ingezet, wordt `$SUBNETID`gedefinieerd met de omgevingsvariabele. We hebben de `$SUBNETID` variabele in de vorige stappen niet gedefinieerd. Als u de waarde voor de subnet-id wilt instellen, u de volgende opdracht gebruiken:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

We definiëren het uitgaande type dat de UDR volgt die op het subnet bestaat, waardoor AKS setup en IP-provisioning kan overslaan voor de load balancer die nu strikt intern kan zijn.

De AKS-functie voor [api-servergeautoriseerde IP-bereiken](api-server-authorized-ip-ranges.md) kan worden toegevoegd om de toegang tot API-servers te beperken tot alleen het openbare eindpunt van de firewall. De geautoriseerde IP-bereiken functie wordt aangeduid in het diagram als de NSG die moet worden doorgegeven om toegang te krijgen tot het controlevlak. Wanneer u de geautoriseerde IP-bereikfunctie inschakelt om de toegang tot API-servers te beperken, moeten uw hulpprogramma's voor ontwikkelaars een jumpbox gebruiken uit het virtuele netwerk van de firewall of moet u alle eindpunten van ontwikkelaars toevoegen aan het geautoriseerde IP-bereik.

> [!TIP]
> Extra functies kunnen worden toegevoegd aan de clusterimplementatie, zoals (Privécluster)[]. Bij het gebruik van geautoriseerde IP-bereiken is een jumpbox vereist binnen het clusternetwerk om toegang te krijgen tot de API-server.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Ontwikkelaarstoegang tot de API-server inschakelen

Vanwege de geautoriseerde IP-bereiken die voor het cluster zijn ingesteld, moet u uw IP-adressen voor ontwikkelaarstooling toevoegen aan de AKS-clusterlijst met goedgekeurde IP-bereiken om toegang te krijgen tot de API-server. Een andere optie is het configureren van een jumpbox met de benodigde tooling in een apart subnet in het virtuele netwerk van de Firewall.

Een ander IP-adres toevoegen aan de goedgekeurde bereiken met de volgende opdracht

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Gebruik de opdracht [az aks-referenties][az-aks-get-credentials] om verbinding te maken `kubectl` met uw nieuw gemaakte Kubernetes-cluster. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>De interne load balancer instellen

AKS heeft een load balancer met het cluster geïmplementeerd die kan worden ingesteld als interne [load balancer.](internal-lb.md)

Als u een interne load balancer wilt maken, maakt u een servicemanifest met de naam internal-lb.yaml met het servicetype LoadBalancer en de interne annotatie azure-load-balancer-balancer zoals in het volgende voorbeeld wordt weergegeven:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Implementeer de interne load balancer met behulp van de kubectl toe te passen en geef de naam van uw YAML manifest:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Een Kubernetes-service implementeren

Aangezien het uitgaande clustertype is ingesteld als UDR, wordt het koppelen van de agentknooppunten als de backendpool voor de load balancer niet automatisch voltooid door AKS op de tijd van het maken van het cluster. Backend pool-koppeling wordt echter afgehandeld door de Kubernetes Azure-cloudprovider wanneer de Kubernetes-service wordt geïmplementeerd.

Implementeer de toepassing van de Azure-stem-app door `example.yaml`de yaml hieronder te kopiëren naar een bestand met de naam .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Implementeer de service door het uitvoeren van:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Een DNAT-regel toevoegen aan Azure Firewall

Als u inkomende connectiviteit wilt configureren, moet een DNAT-regel naar de Azure Firewall worden geschreven. Om de connectiviteit met ons cluster te testen, wordt een regel gedefinieerd voor het openbare IP-adres van de firewall frontend om naar het interne IP-adres te leiden dat door de interne service wordt blootgesteld.

Het doeladres kan worden aangepast omdat het de poort op de firewall is die moet worden geopend. Het vertaalde adres moet het IP-adres van de interne load balancer zijn. De vertaalde poort moet de blootgestelde poort voor uw Kubernetes-service zijn.

U moet het interne IP-adres opgeven dat is toegewezen aan de load balancer die is gemaakt door de Kubernetes-service. Haal het adres op door het uitvoeren van:

```bash
kubectl get services
```

Het benodigde IP-adres wordt vermeld in de kolom EXTERN-IP, vergelijkbaar met het volgende.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Resources opschonen

> [!NOTE]
> Wanneer u de interne service van Kubernetes verwijdert, verwijdert de Azure-cloudprovider de interne load balancer als deze niet meer door een service wordt gebruikt. Bij de volgende service-implementatie wordt een load balancer geïmplementeerd als deze niet kan worden gevonden met de gevraagde configuratie.

Als u Azure-bronnen wilt opschonen, verwijdert u de AKS-brongroep.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Connectiviteit valideren

Navigeer naar het IP-adres azure firewall frontend in een browser om de connectiviteit te valideren.

U ziet een afbeelding van de Azure-stem-app.

## <a name="next-steps"></a>Volgende stappen

Zie [UDR-overzicht azure networking](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Bekijk [hoe u een routetabel maakt, wijzigt of verwijdert.](https://docs.microsoft.com/azure/virtual-network/manage-route-table)

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
