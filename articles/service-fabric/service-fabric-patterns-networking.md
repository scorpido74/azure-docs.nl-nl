---
title: Netwerkpatronen voor Azure Service Fabric
description: Beschrijft veelvoorkomende netwerkpatronen voor Service Fabric en hoe u een cluster maakt met Azure-netwerkfuncties.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466285"
---
# <a name="service-fabric-networking-patterns"></a>Netwerkpatronen van Service Fabric
U uw Azure Service Fabric-cluster integreren met andere Azure-netwerkfuncties. In dit artikel laten we u zien hoe u clusters maakt die de volgende functies gebruiken:

- [Bestaand virtueel netwerk of subnet](#existingvnet)
- [Statisch openbaar IP-adres](#staticpublicip)
- [Interne load balancer](#internallb)
- [Interne en externe load balancer](#internalexternallb)

Service Fabric draait in een standaard virtuele machine schaalset. Elke functionaliteit die u gebruiken in een virtuele machineschaalset, u gebruiken met een Cluster Service Fabric. De netwerksecties van de Azure Resource Manager-sjablonen voor virtuele machineschaalsets en Service Fabric zijn identiek. Nadat u bent geïmplementeerd in een bestaand virtueel netwerk, is het eenvoudig om andere netwerkfuncties op te nemen, zoals Azure ExpressRoute, Azure VPN Gateway, een netwerkbeveiligingsgroep en virtueel netwerkpeeren.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>De resourceprovider van Service Fabric toestaan uw cluster op te vragen

Service Fabric is uniek van andere netwerkfuncties in één aspect. De [Azure-portal](https://portal.azure.com) gebruikt intern de serviceprovider Service Fabric-bron om naar een cluster te bellen om informatie over knooppunten en toepassingen te krijgen. De servicefabric-resourceprovider vereist openbaar toegankelijke inkomende toegang tot de HTTP-gatewaypoort (poort 19080, standaard) op het beheereindpunt. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) gebruikt het beheereindpunt om uw cluster te beheren. De resourceprovider servicefabric gebruikt deze poort ook om informatie over uw cluster op te vragen om weer te geven in de Azure-portal. 

Als poort 19080 niet toegankelijk is vanuit de resourceprovider Service Fabric, wordt een bericht als *Knooppunten niet gevonden* in de portal weergegeven en wordt uw knooppunt- en toepassingslijst leeg weergegeven. Als u uw cluster in de Azure-portal wilt zien, moet uw load balancer een openbaar IP-adres weergeven en moet uw netwerkbeveiligingsgroep inkomende poort 19080-verkeer toestaan. Als uw installatie niet aan deze vereisten voldoet, wordt in de Azure-portal de status van uw cluster niet weergegeven.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Sjablonen

Alle Service Fabric-sjablonen bevinden zich in [GitHub.](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking) U moet de sjablonen kunnen implementeren met behulp van de volgende PowerShell-opdrachten. Als u de bestaande Azure Virtual Network-sjabloon of de statische openbare IP-sjabloon implementeert, leest u eerst het gedeelte [Initiële installatie](#initialsetup) van dit artikel.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Initiële installatie

### <a name="existing-virtual-network"></a>Bestaand virtueel netwerk

In het volgende voorbeeld beginnen we met een bestaand virtueel netwerk **ExistingRG** genaamd ExistingRG-vnet, in de ExistingRG-resourcegroep. Het subnet heeft de naam standaard. Deze standaardresources worden gemaakt wanneer u de Azure-portal gebruikt om een virtuele virtuele machine (standaardmachine) te maken. U het virtuele netwerk en subnet maken zonder de VM te maken, maar het belangrijkste doel van het toevoegen van een cluster aan een bestaand virtueel netwerk is het bieden van netwerkconnectiviteit met andere VM's. Het maken van de VM geeft een goed voorbeeld van hoe een bestaand virtueel netwerk doorgaans wordt gebruikt. Als uw Cluster Servicefabric alleen een interne load balancer gebruikt, zonder een openbaar IP-adres, u de VM en het openbare IP-adres gebruiken als een veilige *springbox.*

### <a name="static-public-ip-address"></a>Statisch openbaar IP-adres

Een statisch openbaar IP-adres is over het algemeen een speciale bron die afzonderlijk wordt beheerd van de VM of VM's waaraan het is toegewezen. Het is ingericht in een speciale netwerkbrongroep (in tegenstelling tot in de clusterbrongroep ServiceFabric zelf). Maak een statisch openbaar IP-adres met de naam staticIP1 in dezelfde ExistingRG-brongroep, in de Azure-portal of met PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Sjabloon Servicefabric

In de voorbeelden in dit artikel gebruiken we de sjabloon Service Fabric.json. U de standaardportalwizard gebruiken om de sjabloon van de portal te downloaden voordat u een cluster maakt. U ook een van de [voorbeeldsjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates)gebruiken, zoals het [cluster Beveiligde servicestructuur met vijf node.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Bestaand virtueel netwerk of subnet

1. Wijzig de subnetparameter in de naam van het bestaande subnet en voeg vervolgens twee nieuwe parameters toe om naar het bestaande virtuele netwerk te verwijzen:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Opmerking `nicPrefixOverride` uit `Microsoft.Compute/virtualMachineScaleSets`attribuut van , omdat u bestaande subnet gebruikt en u deze variabele hebt uitgeschakeld in stap 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Wijzig `vnetID` de variabele om het bestaande virtuele netwerk aan te wijzen:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Verwijder `Microsoft.Network/virtualNetworks` uit uw resources, zodat Azure geen nieuw virtueel netwerk maakt:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Reageer op het virtuele `dependsOn` netwerk `Microsoft.Compute/virtualMachineScaleSets`vanuit het kenmerk van , zodat u niet afhankelijk bent van het maken van een nieuw virtueel netwerk:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Na implementatie moet uw virtuele netwerk de nieuwe vm's voor de schaalset bevatten. Het type knooppunt van de virtuele machineschaal moet het bestaande virtuele netwerk en subnet weergeven. U rdp (Remote Desktop Protocol) ook gebruiken om toegang te krijgen tot de vm die zich al in het virtuele netwerk bevond en om de nieuwe vm's van de schaalset te pingen:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Zie in een ander voorbeeld [een voorbeeld dat niet specifiek is voor Service Fabric.](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statisch openbaar IP-adres

1. Parameters toevoegen voor de naam van de bestaande statische IP-brongroep, naam en volledig gekwalificeerde domeinnaam (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Verwijder `dnsName` de parameter. (Het statische IP-adres heeft er al een.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Voeg een variabele toe om naar het bestaande statische IP-adres te verwijzen:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Verwijder `Microsoft.Network/publicIPAddresses` uit uw resources, zodat Azure geen nieuw IP-adres maakt:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Reageer op het IP-adres van het `dependsOn` kenmerk van `Microsoft.Network/loadBalancers`, zodat u niet afhankelijk bent van het maken van een nieuw IP-adres:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Wijzig `Microsoft.Network/loadBalancers` in de `publicIPAddress` resource `frontendIPConfigurations` het element van het verwijzen naar het bestaande statische IP-adres in plaats van een nieuw gemaakt ip-adres:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Wijzig `Microsoft.ServiceFabric/clusters` in `managementEndpoint` de bron de DNS FQDN van het statische IP-adres. Als u een beveiligd cluster gebruikt, moet u ervoor zorgen dat u *http://* wijzigt in *https://.* (Houd er rekening mee dat deze stap alleen van toepassing is op clusters van ServiceFabric. Als u een virtuele machineschaalset gebruikt, slaat u deze stap over.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Na implementatie u zien dat uw load balancer is gekoppeld aan het openbare statische IP-adres van de andere resourcegroep. Het eindpunt van de servicefabric-clientverbinding en het eindpunt [van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) wijzen op de DNS FQDN van het statische IP-adres.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Interne load balancer

Dit scenario vervangt de externe load balancer in de standaard sjabloon Servicefabric door een interne load balancer. Zie [eerder in het artikel](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) voor implicaties voor de Azure-portal en voor de provider van servicefabric-bronnen.

1. Verwijder `dnsName` de parameter. (Het is niet nodig.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Als u een statische toewijzingsmethode gebruikt, u optioneel een statische IP-adresparameter toevoegen. Als u een dynamische toewijzingsmethode gebruikt, hoeft u deze stap niet te doen.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Verwijder `Microsoft.Network/publicIPAddresses` uit uw resources, zodat Azure geen nieuw IP-adres maakt:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Verwijder het `dependsOn` IP-adreskenmerk van `Microsoft.Network/loadBalancers`, zodat u niet afhankelijk bent van het maken van een nieuw IP-adres. Voeg het `dependsOn` kenmerk van het virtuele netwerk toe omdat de load balancer nu afhankelijk is van het subnet van het virtuele netwerk:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Wijzig de instelling van `frontendIPConfigurations` de load `publicIPAddress`balancer van het `privateIPAddress`gebruik van een subnet naar het gebruik van een subnet en . `privateIPAddress`gebruikt een vooraf gedefinieerd statisch ip-adres. Als u een dynamisch IP-adres wilt gebruiken, verwijdert u het `privateIPAddress` element en wijzigt u vervolgens `privateIPAllocationMethod` in **Dynamisch**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Wijzig `Microsoft.ServiceFabric/clusters` in de `managementEndpoint` resource de aanwijs naar het adres interne load balancer. Als u een beveiligd cluster gebruikt, moet u *http://* wijzigen in *https://.* (Houd er rekening mee dat deze stap alleen van toepassing is op clusters van ServiceFabric. Als u een virtuele machineschaalset gebruikt, slaat u deze stap over.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Na de implementatie gebruikt uw load balancer het privéstatische 10.0.0.250 IP-adres. Als u een andere machine in datzelfde virtuele netwerk hebt, u naar het interne [eindpunt van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) gaan. Houd er rekening mee dat het verbinding maakt met een van de knooppunten achter de load balancer.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interne en externe load balancer

In dit scenario begint u met de bestaande externe lastenbalancer met één knooppunt en voegt u een interne load balancer toe voor hetzelfde knooppunttype. Een back-endpoort die is gekoppeld aan een back-end-adresgroep, kan slechts aan één load balancer worden toegewezen. Kies welke load balancer uw applicatiepoorten heeft en welke load balancer uw beheereindpunten heeft (poorten 19000 en 19080). Als u de beheereindpunten op de interne load balancer plaatst, moet u rekening houden met de beperkingen van de serviceprovider servicefabric die [eerder in het artikel](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)zijn besproken. In het voorbeeld dat we gebruiken, blijven de beheereindpunten op de externe load balancer staan. U voegt ook een poort 80-toepassingspoort toe en plaatst deze op de interne load balancer.

In een cluster met twee node-type is één knooppunttype op de externe load balancer. Het andere knooppunttype bevindt zich op de interne load balancer. Als u een cluster met twee node-type wilt gebruiken, schakelt u in de door portal gemaakte tweenode-type (die wordt geleverd met twee load balancers), de tweede load balancer in een interne load balancer. Zie de sectie [Intern-only load balancer](#internallb) voor meer informatie.

1. Voeg de parameter ip-adres van de statische interne load balancer toe. (Zie eerdere delen van dit artikel voor notities met betrekking tot het gebruik van een dynamisch IP-adres.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Voeg een parameter toepassingspoort 80 toe.

3. Als u interne versies van de bestaande netwerkvariabelen wilt toevoegen, kopieert en plakt u deze en voegt u "-Int" toe aan de naam:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Als u begint met de door portal gegenereerde sjabloon die toepassingspoort 80 gebruikt, voegt de standaardportalsjabloon AppPort1 (poort 80) toe aan de externe load balancer. Verwijder in dit geval AppPort1 uit `loadBalancingRules` de externe load balancer en sondes, zodat u deze toevoegen aan de interne load balancer:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Voeg een `Microsoft.Network/loadBalancers` tweede resource toe. Het lijkt op de interne load balancer gemaakt in de [interne-only load balancer](#internallb) sectie, maar het maakt gebruik van de "-Int" load balancer variabelen, en implementeert alleen de toepassing poort 80. Dit verwijdert `inboundNatPools`ook , om RDP-eindpunten op de openbare load balancer te houden. Als u RDP op de interne `inboundNatPools` load balancer wilt, gaat u van de externe load balancer naar deze interne load balancer:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. Voeg `networkProfile` de `Microsoft.Compute/virtualMachineScaleSets` interne back-endadresgroep toe voor de resource:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Na implementatie ziet u twee load balancers in de resourcegroep. Als u door de load balancers bladert, ziet u de openbare IP-adres- en beheereindpunten (poorten 19000 en 19080) die zijn toegewezen aan het openbare IP-adres. U ook het statische interne IP-adres en het eindpunt van de toepassing (poort 80) zien dat is toegewezen aan de interne load balancer. Beide load balancers maken gebruik van dezelfde virtuele machine schaal set back-end pool.

## <a name="notes-for-production-workloads"></a>Opmerkingen voor productieworkloads

De bovenstaande GitHub-sjablonen zijn ontworpen om te werken met de standaard SKU voor Azure Standard Load Balancer (SLB), de Basic SKU. Deze SLB heeft geen SLA, dus voor productieworkloads moet de standaard SKU worden gebruikt. Zie het [overzicht Azure Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview)voor meer informatie hierover. Elk servicefabriccluster dat de Standaard SKU voor SLB gebruikt, moet ervoor zorgen dat elk knooppunttype een regel heeft die uitgaand verkeer op poort 443 mogelijk maakt. Dit is nodig om de clusterinstallatie te voltooien en elke implementatie zonder een dergelijke regel mislukt. In het bovenstaande voorbeeld van een "interne alleen"-loadbalancer moet een extra externe load balancer aan de sjabloon worden toegevoegd met een regel die uitgaand verkeer voor poort 443 mogelijk maakt.

## <a name="next-steps"></a>Volgende stappen
[Een cluster maken](service-fabric-cluster-creation-via-arm.md)

Na implementatie ziet u twee load balancers in de resourcegroep. Als u door de load balancers bladert, ziet u de openbare IP-adres- en beheereindpunten (poorten 19000 en 19080) die zijn toegewezen aan het openbare IP-adres. U ook het statische interne IP-adres en het eindpunt van de toepassing (poort 80) zien dat is toegewezen aan de interne load balancer. Beide load balancers maken gebruik van dezelfde virtuele machine schaal set back-end pool.

