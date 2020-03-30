---
title: Netwerkmodi configureren voor containerservices
description: Meer informatie over het instellen van de verschillende netwerkmodi die worden ondersteund door Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: ba1fa92559d39a481008d1dd18036e4232be1bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639799"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric-containernetwerkmodi

Een Azure Service Fabric-cluster voor containerservices maakt standaard gebruik van de nat-netwerkmodus. **nat** Wanneer meer dan één containerservice luistert op dezelfde poort en de nat-modus wordt gebruikt, kunnen implementatiefouten optreden. Om meerdere containerservices te ondersteunen die op dezelfde poort luisteren, biedt Service Fabric **de open** netwerkmodus (versies 5.7 en hoger). In de open modus heeft elke containerservice een intern, dynamisch toegewezen IP-adres dat meerdere services ondersteunt die op dezelfde poort luisteren.  

Als u één containerservice hebt met een statisch eindpunt in uw servicemanifest, u nieuwe services maken en verwijderen met behulp van de modus Openen zonder implementatiefouten. Hetzelfde docker-compose.yml-bestand kan ook worden gebruikt met statische poorttoewijzingen om meerdere services te maken.

Wanneer een containerservice opnieuw wordt opgestart of naar een ander knooppunt in het cluster wordt verplaatst, wordt het IP-adres gewijzigd. Daarom raden we niet aan om het dynamisch toegewezen IP-adres te gebruiken om containerservices te ontdekken. Alleen de Service Fabric Naming Service of de DNS-service mogen worden gebruikt voor het uitvoeren van de service. 

>[!WARNING]
>Azure maakt in totaal 65.356 IP's per virtueel netwerk mogelijk. De som van het aantal knooppunten en het aantal containerservice-exemplaren (die de open modus gebruiken) mag niet hoger zijn dan 65.356 IP's binnen een virtueel netwerk. Voor scenario's met hoge dichtheid raden we de nat-netwerkmodus aan. Bovendien zullen andere afhankelijkheden, zoals de load balancer, rekening houden met andere [beperkingen.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Momenteel zijn tot 50 IP's per knooppunt getest en stabiel gebleken. 
>

## <a name="set-up-open-networking-mode"></a>Netwerkmodus openen instellen

1. Stel de sjabloon Azure Resource Manager in. Schakel in de sectie **fabricSettings** van de clusterbron de DNS-service en de IP-provider in: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Stel de sectie netwerkprofiel van de resource Virtuele machineschaalset in. Hierdoor kunnen meerdere IP-adressen worden geconfigureerd op elk knooppunt van het cluster. In het volgende voorbeeld worden vijf IP-adressen per knooppunt ingesteld voor een Cluster van Windows/Linux Service Fabric. U vijf service-exemplaren op de poort op elk knooppunt laten luisteren. Als u wilt dat de vijf IP's toegankelijk zijn vanuit de Azure Load Balancer, schrijft u de vijf IP's in de Azure Load Balancer Backend Address Pool in, zoals hieronder wordt weergegeven.  U moet ook de variabelen aan de bovenkant van uw sjabloon toevoegen in de sectie variabelen.

    Voeg deze sectie toe aan variabelen:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Voeg deze sectie toe aan de bron Virtuele machineschaalset:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Alleen voor Windows-clusters stelt u een NSG-regel (Azure Network Security Group) in die poort UDP/53 opent voor het virtuele netwerk met de volgende waarden:

   |Instelling |Waarde | |
   | --- | --- | --- |
   |Prioriteit |2000 | |
   |Name |Custom_Dns  | |
   |Bron |VirtualNetwork | |
   |Doel | VirtualNetwork | |
   |Service | DNS (UDP/53) | |
   |Actie | Toestaan  | |
   | | |

4. Geef de netwerkmodus op in het `<NetworkConfig NetworkType="Open">`toepassingsmanifest voor elke service: . **De open** netwerkmodus resulteert in het verkrijgen van een specifiek IP-adres door de service. Als een modus niet is opgegeven, wordt de **nat-modus** standaard weergegeven. In het volgende duidelijke `NodeContainerServicePackage1` `NodeContainerServicePackage2` voorbeeld kunnen de en diensten elk op `Endpoint1`dezelfde poort luisteren (beide services luisteren mee). Wanneer de netwerkmodus `PortBinding` openen is opgegeven, kunnen configuraties niet worden opgegeven.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    U verschillende netwerkmodi mixen en matchen tussen services binnen een toepassing voor een Windows-cluster. Sommige services kunnen de open modus gebruiken, terwijl andere de nat-modus gebruiken. Wanneer een service is geconfigureerd om de nat-modus te gebruiken, moet de poort waarop de service luistert, uniek zijn.

    >[!NOTE]
    >Op Linux-clusters wordt het mengen van netwerkmodi voor verschillende services niet ondersteund. 
    >

5. Wanneer de **modus Openen** is geselecteerd, moet de definitie **van eindpunt** in het servicemanifest expliciet wijzen op het codepakket dat overeenkomt met het eindpunt, zelfs als het servicepakket slechts één codepakket heeft. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Voor Windows zorgt een vm-reboot ervoor dat het open netwerk opnieuw wordt gemaakt. Dit is om een onderliggend probleem in de netwerkstack te beperken. Het standaardgedrag is het opnieuw maken van het netwerk. Als dit gedrag moet worden uitgeschakeld, kan de volgende configuratie worden gebruikt, gevolgd door een config-upgrade.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Volgende stappen
* [Inzicht krijgen in het Service Fabric-toepassingsmodel](service-fabric-application-model.md)
* [Meer informatie over de manifestbronnen van de Service Fabric-service](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Een Windows-container implementeren voor servicefabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een Docker-container implementeren voor servicefabric op Linux](service-fabric-get-started-containers-linux.md)
