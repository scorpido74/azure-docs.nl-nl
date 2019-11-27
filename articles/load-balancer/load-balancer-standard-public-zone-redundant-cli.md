---
title: Taak verdeling van zone-redundante Vm's met behulp van Azure CLI
titleSuffix: Azure Load Balancer
description: Meer informatie over het maken van een openbaar Standard Load Balancer met een zone-redundante frontend met Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: af327f751a0af67b6d17330dbaeb717df8660bfd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225272"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Taak verdeling van virtuele machines in alle beschikbaarheids zones met behulp van Azure CLI

In dit artikel wordt beschreven hoe u een open bare [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) maakt met een zone-redundante front-end om zone-redundantie te bieden zonder afhankelijk te zijn van meerdere DNS-records. Eén front-end-IP-adres is automatisch zone-redundant.  Het gebruik van een zone-redundante front-end voor uw load balancer, met één IP-adres, kunt u nu elke VM in een virtueel netwerk bereiken binnen een regio die voor alle Beschikbaarheidszones. Gebruik beschikbaarheidszones om uw apps en gegevens te beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter.

Zie [Standard Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md) voor meer informatie over het gebruik van beschikbaarheidszones met Standard Load Balancer.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van Azure CLI versie 2.0.17 of hoger.  Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

> [!NOTE]
> Ondersteuning voor Beschikbaarheidszones is beschikbaar voor geselecteerde Azure-resources en regio's en groottefamilies van de virtuele machine. Zie [overzicht van Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over hoe u aan de slag kunt gaan en welke Azure-resources,-regio's en-Series met VM-grootten u de beschikbaarheids zones wilt proberen. Voor ondersteuning kunt u vragen stellen op [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) of [een Azure-ondersteuningsticket openen](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroupSLB* gemaakt op de locatie *Europa West* :

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Een zone maken met redundante open bare IP-standaard
Om toegang te krijgen tot uw app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Een zone-redundante front-end wordt door alle beschikbaarheids zones in een regio tegelijk bediend. Maak een zone-redundant openbaar IP-adres met [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Wanneer u een standaard openbaar IP-adres maakt, is het standaard zone redundant.

In het volgende voor beeld wordt een zone onnodig openbaar IP-adres gemaakt met de naam *myPublicIP* in de resource groep *myresourcegrouploadbalancer gemaakt* .

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Azure-Standard Load Balancer maken
In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:
- een front-end IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
- een back-end-IP-pool waar de front-end-pool het netwerkverkeer op de load balancer naartoe stuurt.
- een statustest die de status van de back-end-VM-exemplaren vaststelt.
- een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer"></a>Load balancer maken
Maak een standaard load balancer met [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). In het volgende voor beeld wordt een load balancer gemaakt met de naam *myLoadBalancer* en wordt het *myPublicIP* -adres toegewezen aan de front-end-IP-configuratie.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Status test maken op poort 80

Een statuscontrole controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen verzenden. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak een status test met AZ Network lb probe Create om de status van de virtuele machines te bewaken. U gebruikt [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) om een TCP-statustest te maken. In het volgende voorbeeld wordt een statustest gemaakt met de naam *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>load balancer regel maken voor poort 80
Een load balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-IP-pool om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak met *az network lb rule create* de regel [myLoadBalancerRuleWeb](/cli/azure/network/lb/rule#az-network-lb-rule-create) voor het luisteren naar poort 80 in de front-endpool *myFrontEnd* en het verzenden van netwerkverkeer met evenredige taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren
Voordat u enkele VM's implementeert om uw load balancer te testen, maakt u de ondersteunende virtuele-netwerkbronnen.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met de naam *myVnet* met een subnet met de naam *MySubnet* in de myResourceGroup met [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerk beveiligings groep met de naam *myNetworkSecurityGroup* om binnenkomende verbindingen met uw virtuele netwerk te definiëren met [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Maak een regel voor de netwerk beveiligings groep met de naam *myNetworkSecurityGroupRule* voor poort 80 met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>NIC's maken
Maak drie virtuele Nic's met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) en koppel deze aan het open bare IP-adres en de netwerk beveiligings groep. In het volgende voor beeld worden zes virtuele Nic's gemaakt. (Eén virtuele NIC voor elke VM die u in de volgende stappen voor uw app maakt). U kunt op elk gewenst moment extra virtuele NIC's en VM's maken en toevoegen aan de load balancer:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Back-endservers maken
In dit voor beeld maakt u drie virtuele machines in zone 1, zone 2 en zone 3 die moeten worden gebruikt als back-endservers voor de load balancer. U installeert ook NGINX op de virtuele machines om te controleren of de load balancer is gemaakt.

### <a name="create-cloud-init-config"></a>Een cloud-init-configuratiebestand maken

U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. Maak in uw huidige shell een bestand met de naam cloud-init.txt en plak de volgende configuratie in de shell. Zorg ervoor dat u het hele cloud-init-bestand correct kopieert, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>De virtuele zonegebonden-machines maken
Maak de virtuele machines met [AZ VM Create](/cli/azure/vm#az-vm-create) in zone 1, zone 2 en zone 3. In het volgende voor beeld wordt een virtuele machine gemaakt in elke zone en worden SSH-sleutels gegenereerd als deze nog niet bestaan:

Maak een virtuele machine in elke zone (zone 1, zone2 en zone 3) van de locatie *Europa West* .

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Load balancer testen

Haal het open bare IP-adres van de load balancer met [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Vervolgens kunt u het openbare IP-adres invoeren in een webbrowser. Het duurt enkele minuten voordat de virtuele machines gereed zijn en de load balancer begint met het distribueren van verkeer naar de VM's. De app wordt weergegeven met de hostnaam van de VM waarnaar de load balancer verkeer heeft gedistribueerd, zoals in het volgende voorbeeld:

![Node.js-app uitvoeren](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Als u de load balancer het distribueren van verkeer tussen virtuele machines in alle drie de beschikbaarheids zones met uw app wilt zien, kunt u een virtuele machine in een bepaalde zone stoppen en uw browser vernieuwen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Standard Load Balancer](./load-balancer-standard-overview.md)



