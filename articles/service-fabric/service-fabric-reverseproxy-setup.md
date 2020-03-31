---
title: Azure Service Fabric stelt omgekeerde proxy in
description: Meer informatie over het instellen en configureren van de reverse proxy-service voor een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 131440036896d323cbf821d7a220328456e1db36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645443"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Omgekeerde proxy instellen en configureren in Azure Service Fabric
Reverse proxy is een optionele Azure Service Fabric-service waarmee microservices die worden uitgevoerd in een Service Fabric-cluster kunnen worden ontdekt en communiceren met andere services met http-eindpunten. Zie [Proxy omkeren in Azure Service Fabric](service-fabric-reverseproxy.md)voor meer informatie. In dit artikel ziet u hoe u omgekeerde proxy in uw cluster instelt en configureert. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Omgekeerde proxy inschakelen met Azure-portal

Azure-portal biedt een optie om omgekeerde proxy in te schakelen wanneer u een nieuw Cluster Van ServiceFabric maakt. U een bestaand cluster niet upgraden om reverse proxy via de portal te gebruiken. 

Als u de omgekeerde proxy wilt configureren wanneer u [een cluster maakt met Azure-portal,](./service-fabric-cluster-creation-via-portal.md)controleert u het volgende:

1. Selecteer in **stap 2: clusterconfiguratie**onder **knooppunttypeconfiguratie**de optie **Omgekeerde proxy inschakelen**.

   ![Omgekeerde proxy inschakelen op portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Optioneel) Als u een veilige omgekeerde proxy wilt configureren, moet u een SSL-certificaat configureren. Selecteer in **stap 3: Beveiliging**, op **Clusterbeveiligingsinstellingen configureren**onder **Configuratietype**, selecteer **Aangepast**. Selecteer vervolgens onder **Reverse Proxy SSL-certificaat**de optie **Een SSL-certificaat opnemen voor omgekeerde proxy** en voer uw certificaatgegevens in.

   ![Beveiligde omgekeerde proxy configureren op portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Als u ervoor kiest de omgekeerde proxy niet te configureren met een certificaat wanneer u het cluster maakt, u dit later doen via de resourcemanagersjabloon voor de brongroep van het cluster. Zie [Omgekeerde proxy inschakelen via Azure Resource Manager-sjablonen](#enable-reverse-proxy-via-azure-resource-manager-templates)voor meer informatie.

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Omgekeerde proxy inschakelen via Azure Resource Manager-sjablonen

Voor clusters op Azure u de sjabloon Azure Resource Manager gebruiken om de omgekeerde proxy in Service Fabric in te schakelen. U omgekeerde proxy inschakelen wanneer u het cluster maakt of inschakelen door het cluster op een later tijdstip bij te werken. 

Voor een nieuw cluster u [een aangepaste resourcemanagersjabloon maken](service-fabric-cluster-creation-via-arm.md) of een voorbeeldsjabloon gebruiken. 

U voorbeeldsjablonen voor resourcebeheer vinden waarmee u de veilige omgekeerde proxy voor een Azure-cluster configureren in de [secure reverse proxy sample templates](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) op GitHub. Raadpleeg [HTTPS Reverse Proxy configureren in een beveiligd cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) in het README-bestand voor instructies en de sjablonen die moeten worden gebruikt om veilige omgekeerde proxy met een certificaat te configureren en om certificaatrollover te verwerken.

Voor een bestaand cluster u de resourcebeheersjabloon voor de brongroep van het cluster exporteren met behulp van de [Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)of azure [CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Nadat u een resourcemanagersjabloon hebt, u de omgekeerde proxy inschakelen met de volgende stappen:

1. Definieer een poort voor de omgekeerde proxy in de [sectie Parameters](../azure-resource-manager/templates/template-syntax.md) van de sjabloon.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Geef de poort op voor elk van de nodetype-objecten in de sectie [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resourcetype](../azure-resource-manager/templates/template-syntax.md).

    De poort wordt geïdentificeerd door de parameternaam, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Als u SSL-certificaten op de poort wilt configureren voor de omgekeerde proxy, voegt u het certificaat toe aan de eigenschap ***reverseProxyCertificate*** in de sectie **Microsoft.ServiceFabric/clusters** [Resourcetype](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Ondersteuning voor een reverse proxy-certificaat dat verschilt van het clustercertificaat
 Als het reverse proxy-certificaat verschilt van het certificaat dat het cluster beveiligt, moet het eerder gespecificeerde certificaat op de virtuele machine worden geïnstalleerd en worden toegevoegd aan de lijst met toegangscontrole (ACL), zodat Service Fabric er toegang toe heeft. Dit kan in de sectie [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [Resource type](../resource-group-authoring-templates.md). Voeg voor de installatie het certificaat toe aan het osProfiel. De uitbreidingssectie van de sjabloon kan het certificaat in de ACL bijwerken.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Wanneer u certificaten gebruikt die afwijken van het clustercertificaat om de omgekeerde proxy op een bestaand cluster in te schakelen, installeert u het reverse proxy-certificaat en werkt u de ACL op het cluster bij voordat u de omgekeerde proxy inschakelt. Voltooi de implementatie van azure [resource beheer-sjablonen](service-fabric-cluster-creation-via-arm.md) met behulp van de eerder genoemde instellingen voordat u een implementatie start om de omgekeerde proxy in stap 1-3 in te schakelen.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Omgekeerde proxy inschakelen op zelfstandige clusters

Voor zelfstandige clusters schakelt u omgekeerde proxy in het clusterconfig.json-bestand in. U omgekeerde proxy inschakelen bij het maken van een cluster of door de configuratie voor een bestaand cluster te upgraden. Zie [Zelfstandige clusterinstellingen](./service-fabric-cluster-manifest.md)voor meer informatie over de instellingen die beschikbaar zijn in ClusterConfig.json-bestanden.

In de volgende stappen ziet u de instellingen die u moet gebruiken om omgekeerde proxy in te schakelen en, optioneel, om de omgekeerde proxy te beveiligen met een X.509-certificaat. 

1. Als u omgekeerde proxy wilt inschakelen, stelt u de **omgekeerde ProxyEndpointPort-waarde** in voor het knooppunttype onder **eigenschappen** in de clusterconfig. In de volgende JSON wordt de omgekeerde proxy-eindpuntpoort ingesteld op 19081 voor knooppunten met een type 'NodeType0':

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Optioneel) Configureer voor een veilige omgekeerde proxy een certificaat in de **beveiligingssectie** onder **eigenschappen**. 
   - Voor een ontwikkel- of testomgeving u de instelling **ReverseProxyCertificate** gebruiken:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Voor een productieomgeving wordt de instelling **ReverseProxyCertificateCommonNames** aanbevolen:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Zie [X509-beveiliging](./service-fabric-windows-cluster-x509-security.md)op basis van certificaten voor het configureren en beheren van certificaten voor een zelfstandig cluster en meer informatie over het configureren van certificaten die worden gebruikt om omgekeerde proxy te beveiligen.

Nadat u het clusterconfig.json-bestand hebt gewijzigd om omgekeerde proxy in te schakelen, volgt u de instructies in [De clusterconfiguratie bijwerken](service-fabric-cluster-config-upgrade-windows-server.md) om de wijzigingen in uw cluster te pushen.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Omgekeerde proxy op een openbare poort blootleggen via Azure Load Balancer

Als u de omgekeerde proxy van buiten een Azure-cluster wilt aanpakken, stelt u Azure Load Balancer-regels en een Azure Health Probe in voor de omgekeerde proxypoort. Deze stappen kunnen op elk gewenst moment nadat u het cluster hebt gemaakt, worden uitgevoerd met de Azure-portal of de resourcemanagersjabloon. 

> [!WARNING]
> Wanneer u de poort van de omgekeerde proxy configureert in Load Balancer, zijn alle microservices in het cluster die een HTTP-eindpunt blootleggen, adresseerbaar van buiten het cluster. Dit betekent dat microservices bedoeld om intern te zijn kan worden vindbaar door een bepaalde kwaadwillende gebruiker. Dit brengt mogelijk ernstige kwetsbaarheden met zich mee die kunnen worden misbruikt; bijvoorbeeld:
>
> * Een kwaadwillende gebruiker kan een denial of service-aanval starten door herhaaldelijk een interne service te bellen die geen voldoende gehard aanvalsoppervlak heeft.
> * Een kwaadwillende gebruiker kan misvormde pakketten leveren aan een interne service wat resulteert in onbedoeld gedrag.
> * Een dienst die bedoeld is als intern, kan privé- of gevoelige informatie retourneren die niet bedoeld is om te worden blootgesteld aan services buiten het cluster, waardoor deze gevoelige informatie wordt blootgesteld aan een kwaadwillende gebruiker. 
>
> Zorg ervoor dat u de mogelijke beveiligingsgevolgen voor uw cluster en de apps die erop worden uitgevoerd volledig begrijpt en vermindert, voordat u de omgekeerde proxypoort openbaar maakt. 
>

Als u reverse proxy openbaar wilt maken voor een standalone cluster, is de manier waarop u dit doet afhankelijk van het systeem dat het cluster host en valt buiten het bereik van dit artikel. De voorafgaande waarschuwing over het openbaar blootstellen van omgekeerde proxy is echter nog steeds van toepassing.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>De omgekeerde proxy blootleggen met Azure-portal 

1. Klik op de Azure-portal op de brongroep voor uw cluster en klik vervolgens op de load balancer voor uw cluster.
2. Als u een statussonde wilt toevoegen voor de omgekeerde proxypoort, klikt u in het linkerdeelvenster van het venster load balancer onder **INSTELLINGEN**op **Statussondes**. Klik vervolgens boven aan het venster Statussondes **toevoegen** en voer details in voor de omgekeerde proxypoort en klik vervolgens op **OK**. Standaard is de omgekeerde proxypoort 19081, tenzij u deze hebt gewijzigd toen u het cluster maakte.

   ![Reverse proxy health probe configureren](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Als u een regel Load Balancer wilt toevoegen om de omgekeerde proxypoort bloot te leggen, klikt u in het linkerdeelvenster van het venster load balancer onder **INSTELLINGEN**op **Taakbalanceringsregels**. Klik vervolgens boven aan het venster Taakverdelingsregels **toevoegen** en voer details in voor de omgekeerde proxypoort. Zorg ervoor dat u de **waarde van** de poort instelt op de poort waarop de omgekeerde proxy moet worden weergegeven, de waarde van de **backendpoort** op de poort die u hebt ingesteld toen u de omgekeerde proxy hebt ingeschakeld en de waarde van de **statussonde** op de statussonde die u in de vorige stap hebt geconfigureerd. Stel de juiste velden in en klik op **OK**.

   ![Regel load balancer configureren voor omgekeerde proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>De omgekeerde proxy blootleggen via Resource Manager-sjablonen

De volgende JSON verwijst naar dezelfde sjabloon die wordt gebruikt in [Reverse proxy inschakelen via Azure Resource Manager-sjablonen.](#enable-reverse-proxy-via-azure-resource-manager-templates) Raadpleeg dat gedeelte van het document voor informatie over het maken van een resourcemanagersjabloon of het exporteren van een sjabloon voor een bestaand cluster.  De wijzigingen worden aangebracht in de sectie [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [Resource type](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Omgekeerd proxygedrag aanpassen met fabric-instellingen

U het gedrag van omgekeerde proxy aanpassen via fabric-instellingen in de resourcemanagersjabloon voor clusters die worden gehost in Azure of in het clusterconfig.json-bestand voor zelfstandige clusters. Instellingen die het omgekeerde proxygedrag beheren, bevinden zich in de sectie [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) in de sectie **fabricSettings** onder de sectie **clustereigenschappen.** 

U bijvoorbeeld instellen dat de waarde van **StandaardHttpRequestTimeout** de time-out voor aanvragen instelt op de omgekeerde proxy op 180 seconden, zoals in de volgende JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Zie [Clusterinstellingen aanpassen met ResourceBeheer-sjablonen voor](service-fabric-cluster-config-upgrade-azure.md)meer informatie over het bijwerken van fabric-instellingen voor Azure-clusters. Zie [Clusterinstellingen voor zelfstandige clusters aanpassen voor](service-fabric-cluster-config-upgrade-windows-server.md)zelfstandige clusters voor zelfstandige clusters. 

Verschillende fabric-instellingen worden gebruikt om veilige communicatie tussen reverse proxy en services tot stand te brengen. Zie Verbinding maken met [een beveiligde service met de omgekeerde proxy](service-fabric-reverseproxy-configure-secure-communication.md)voor gedetailleerde informatie over deze instellingen.

## <a name="next-steps"></a>Volgende stappen
* [Doorsturen instellen om http-service te beveiligen met de omgekeerde proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Zie sectie ApplicationGateway/Http voor reverse proxy-configuratieopties [in clusterinstellingen servicestructuur aanpassen.](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)
