---
title: Een Service Fabric cluster met Windows maken in azure | Microsoft Docs
description: In deze zelf studie leert u hoe u een Windows Service Fabric-cluster kunt implementeren in een virtueel Azure-netwerk en een netwerk beveiligings groep met behulp van Power shell.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 28571584fbd82b245e85e2ebe5b1d282ab5ae979
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177983"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Zelf studie: een Service Fabric cluster met Windows implementeren in een virtueel Azure-netwerk

Deze zelfstudie is deel één van een serie. U leert hoe u een Azure Service Fabric cluster met Windows implementeert in een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md) en een [netwerk beveiligings groep](../virtual-network/virtual-networks-nsg.md) met behulp van Power shell en een sjabloon. Wanneer u klaar bent, hebt u een cluster dat wordt uitgevoerd in de Cloud waarop u toepassingen kunt implementeren. Zie [een beveiligd Linux-cluster maken in azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md)voor informatie over het maken van een Linux-cluster dat gebruikmaakt van de Azure cli.

In deze zelfstudie wordt een productiescenario beschreven. Zie [een test cluster maken](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)als u een kleiner cluster wilt maken voor test doeleinden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VNET maken in Azure met behulp van PowerShell
> * Een sleutelkluis maken en een certificaat uploaden
> * Azure Active Directory-verificatie instellen
> * Diagnostische verzameling configureren
> * De Event Store-service instellen
> * Azure Monitor-logboeken instellen
> * Een beveiligd Service Fabric-cluster maken in Azure PowerShell
> * Het cluster beveiligen met een X.509-certificaat
> * Verbinding maken met het cluster met behulp van PowerShell
> * Een cluster verwijderen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een beveiligd cluster maken in Azure
> * [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)
> * [Een cluster in- of uitschalen](service-fabric-tutorial-scale-cluster.md)
> * [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)
> * [Een cluster verwijderen](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer de [service Fabric SDK en Power shell-module](service-fabric-get-started.md).
* Installeer [Azure Power shell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Bekijk de belangrijkste concepten van [Azure-clusters](service-fabric-azure-clusters-overview.md).
* [Plan en bereid](service-fabric-cluster-azure-deployment-preparation.md) u voor op een productie cluster implementatie.

Met de volgende procedures wordt er een Service Fabric-cluster met zeven knooppunten gemaakt. Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te berekenen die worden gemaakt door een service Fabric cluster uit te voeren in Azure.

## <a name="download-and-explore-the-template"></a>De sjabloon downloaden en verkennen

Down load de volgende Azure Resource Manager sjabloon bestanden:

* [azuredeploy. json][template]
* [azuredeploy. para meters. json][parameters]

Met deze sjabloon wordt een veilig cluster van zeven virtuele machines en drie knooppunttypen in een virtueel netwerk en een netwerkbeveiligingsgroep geïmplementeerd.  Andere voorbeeldsjablonen zijn te vinden op [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [Azuredeploy. json][template] implementeert een aantal resources, waaronder de volgende.

### <a name="service-fabric-cluster"></a>Service Fabric-cluster

In de resource **Microsoft.ServiceFabric/clusters** wordt een Windows-cluster geconfigureerd met de volgende kenmerken:

* Drie knooppunt typen.
* Vijf knoop punten in het primaire knooppunt type (configureerbaar in de sjabloon parameters) en één knoop punt in elk van de andere twee typen knoop punten.
* Besturings systeem: Windows Server 2016 Data Center met containers (configureerbaar in de sjabloon parameters).
* Certificaat beveiligd (configureerbaar in de sjabloon parameters).
* [Omgekeerde proxy](service-fabric-reverseproxy.md) is ingeschakeld.
* De [DNS-service](service-fabric-dnsservice.md) is ingeschakeld.
* [Duurzaamheids niveau](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) van Bronze (configureerbaar in de sjabloon parameters).
* [Betrouwbaarheids niveau](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) van zilver (configureerbaar in de sjabloon parameters).
* Eind punt voor client verbinding: 19000 (configureerbaar in de sjabloon parameters).
* HTTP-gateway-eind punt: 19080 (configureerbaar in de sjabloon parameters).

### <a name="azure-load-balancer"></a>Azure Load Balancer

In de resource **micro soft. Network/loadBalancers** is een Load Balancer geconfigureerd. Er worden tests en regels ingesteld voor de volgende poorten:

* Eind punt client verbinding: 19000
* het eindpunt van de HTTP-gateway: 19080
* Toepassings poort: 80
* Toepassings poort: 443
* omgekeerde proxy van Service Fabric: 19081

Als er andere toepassings poorten nodig zijn, moet u de resource **micro soft. Network/loadBalancers** en de resource **micro soft. Network/networkSecurityGroups** aanpassen om het verkeer in toe te staan.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtueel netwerk, subnet en netwerkbeveiligingsgroep

De namen van het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep zijn gedefinieerd in de sjabloonparameters. Adresruimten van het virtuele netwerk en subnet worden ook gedeclareerd in de sjabloonparameters en geconfigureerd in de resource **Microsoft.Network/virtualNetworks**:

* Adres ruimte van virtueel netwerk: 172.16.0.0/20
* Service Fabric-subnetadresruimte: 172.16.2.0/23

De volgende regels voor binnenkomend verkeer worden ingeschakeld in de resource **Microsoft.Network/networkSecurityGroups**. U kunt de poortwaarden wijzigen door de sjabloonvariabelen te wijzigen.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* Internodecommunication: 1025, 1026, 1027
* Tijdelijk poort bereik: 49152 tot 65534 (mini maal 256 poorten nodig).
* Poorten voor toepassingsgebruik: 80 en 443
* Poort bereik van de toepassing: 49152 tot 65534 (wordt gebruikt voor service to service-communicatie. Er zijn geen andere poorten geopend op de Load Balancer.
* Blokkeer alle andere poorten

Als er andere toepassings poorten nodig zijn, moet u de resource **micro soft. Network/loadBalancers** en de resource **micro soft. Network/networkSecurityGroups** aanpassen om het verkeer in toe te staan.

### <a name="windows-defender"></a>Windows Defender
Het [Windows Defender anti virus-programma](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) is standaard geïnstalleerd en werkt op Windows Server 2016. De gebruikers interface wordt standaard geïnstalleerd op sommige Sku's, maar is niet vereist. Voor elk knooppunttype dat/elke VM-schaalset die is aangegeven in de sjabloon, wordt de [Antimalware voor Azure-VM-extensie](/azure/virtual-machines/extensions/iaas-antimalware-windows) gebruikt voor het uitsluiten van de Service Fabric-mappen en processen:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>De sjabloonparameters instellen

Het bestand [azuredeploy. para meters. json][parameters] -para meters declareert veel waarden die worden gebruikt voor het implementeren van het cluster en de bijbehorende resources. Hier volgen de para meters die u kunt wijzigen voor uw implementatie:

**Bepaalde** | **Voorbeeld waarde** | **Opmerkingen** 
|---|---|---|
|adminUserName|vmadmin| De gebruikersnaam van de beheerder van de cluster-VM's. De [gebruikers naam is vereist voor de VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Het wachtwoord van de beheerder van de cluster-VM's. [Wachtwoord vereisten voor de virtuele machine](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| De naam van het cluster. Mag alleen letters en cijfers bevatten. De lengte kan minimaal 3 en maximaal 23 tekens zijn.|
|location|southcentralus| De locatie van het cluster. |
|certificateThumbprint|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de SHA1-waarde van de certificaatvingerafdruk in. Bijvoorbeeld 6190390162C988701DB5676EB81083EA608DCCF3.</p> |
|certificateUrlValue|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft. </p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de URL van het certificaat in. Bijvoorbeeld ' https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346 '.</p>|
|sourceVaultValue||<p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de waarde van de bronkluis in. Bijvoorbeeld /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Clientverificatie via Azure Active Directory instellen
Voor Service Fabric-clusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor wederzijdse verificatie van client-naar-knooppunt:
* Gebruik Azure Active Directory voor client identiteit.
* Gebruik een certificaat voor server identiteit en SSL-versleuteling van HTTP-communicatie.

Het instellen van Azure Active Directory (Azure AD) voor het verifiëren van clients voor een Service Fabric cluster moet worden uitgevoerd voordat [u het cluster maakt](#createvaultandcert). Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk. 

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) en [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Als gevolg hiervan, maakt u twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing.  Nadat de toepassingen zijn gemaakt, wijst u gebruikers toe aan de rollen alleen-lezen en beheerder.

> [!NOTE]
> U moet de volgende stappen uitvoeren voordat u het cluster maakt. Omdat de scripts clusternamen en eindpunten verwachten, moeten de waarden worden gepland en geen waarden zijn die u al hebt gemaakt.

In dit artikel wordt ervan uitgegaan dat u al een Tenant hebt gemaakt. Als dat niet het geval is, lees dan eerst [hoe u een Azure Active Directory Tenant kunt ophalen](../active-directory/develop/quickstart-create-new-tenant.md).

We hebben een set Windows Power shell-scripts gemaakt om de stappen voor het configureren van Azure AD met een Service Fabric-cluster te vereenvoudigen. [Download de scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) op uw computer.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure Active Directory-toepassingen maken en gebruikers toewijzen aan rollen
Maak twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing. Nadat u de toepassingen hebt gemaakt om uw cluster te vertegenwoordigen, wijst u uw gebruikers toe aan de rollen die worden [ondersteund door service Fabric](service-fabric-cluster-security-roles.md): alleen-lezen en beheerder.

Voer `SetupApplications.ps1` uit, en geef de tenant-ID, de naam van het cluster en de antwoord-URL van de webtoepassing op als parameters. Geef gebruikers namen en wacht woorden voor de gebruikers op. Bijvoorbeeld:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Voor nationale Clouds (bijvoorbeeld Azure Government, Azure China, Azure Duitsland), geeft u de para meter `-Location` op.

U vindt uw *TenantId* ofwel de map-id in de [Azure-portal](https://portal.azure.com). Selecteer **Azure Active Directory** > **Eigenschappen** en kopieer de waarde van de **map-id** .

De *Clusternaam* wordt gebruikt voor als prefix aan de Azure Active Directory-toepassingen die zijn gemaakt door het script. Het hoeft niet exact overeen te komen met de daad werkelijke cluster naam. Het is alleen gemakkelijker om Azure AD-artefacten toe te wijzen aan de Service Fabric cluster in gebruik.

*WebApplicationReplyUrl* wordt het standaardeindpunt dat Azure Active Directory retourneert naar uw gebruikers nadat ze klaar zijn aanmelden. Stel dit eindpunt in als het Service Fabric Explorer-eindpunt voor uw cluster, dit is standaard:

https://&lt;cluster_domain&gt;:19080/Explorer

U wordt gevraagd om u aan te melden bij een account met beheerders bevoegdheden voor de Azure AD-Tenant. Nadat u zich hebt aangemeld, maakt het script de web- en systeemeigen toepassingen voor uw Service Fabric-cluster. In de toepassingen van de Tenant in de [Azure Portal](https://portal.azure.com)ziet u twee nieuwe vermeldingen:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Het script drukt de JSON af die vereist is voor de Resource Manager-sjabloon bij het maken van het cluster. Daarom is het een goed idee om het Power shell-venster geopend te laten.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure Active Directory-configuratie toevoegen voor het gebruik van Azure Active Directory voor clienttoegang
In [azuredeploy. json][template]configureert u Azure AD in de sectie **micro soft. ServiceFabric/clusters** . Voeg parameters toe voor de tenant-id, de toepassings-id van het cluster en de clienttoepassings-id.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Voeg de parameter waarden toe aan het bestand [azuredeploy. para meters. json][parameters] -para meters. Bijvoorbeeld:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>De verzameling diagnostische gegevens configureren op het cluster
Wanneer u een Service Fabric cluster uitvoert, is het een goed idee om de logboeken te verzamelen van alle knoop punten op een centrale locatie. Met de logboeken op een centrale locatie kunt u problemen in uw cluster analyseren en oplossen, of problemen in de toepassingen en services die in dat cluster worden uitgevoerd.

Een manier om logboeken te uploaden en te verzamelen, is door de uitbrei ding Azure Diagnostics (WAD) te gebruiken, waarmee logboeken naar Azure Storage worden geüpload en ook de mogelijkheid is om logboeken te verzenden naar Azure-toepassing inzichten of Event Hubs. U kunt ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en deze te plaatsen in een analyse platform product, zoals Azure Monitor Logboeken of een andere oplossing voor het parseren van Logboeken.

Als u deze zelf studie volgt, is de diagnostische verzameling al geconfigureerd in de [sjabloon][template].

Als u een bestaand cluster hebt waarvoor geen diagnostische gegevens zijn geïmplementeerd, kunt u het toevoegen of bijwerken via de cluster sjabloon. Wijzig de Resource Manager-sjabloon die wordt gebruikt voor het maken van het bestaande cluster of down load de sjabloon vanuit de portal. Wijzig het bestand template. json door de volgende taken uit te voeren:

Voeg een nieuwe opslag resource toe aan de sectie resources in de sjabloon:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Voeg vervolgens de para meters voor de naam van het opslag account en het type toe aan de sectie para meters van de sjabloon. Vervang de naam van het opslag account voor tijdelijke aanduidingen hier de naam van het opslag account dat u wilt.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Voeg vervolgens de uitbrei ding **IaaSDiagnostics** toe aan de uitbrei dingen-matrix van de eigenschap **VirtualMachineProfile** van elke **micro soft. Compute/virtualMachineScaleSets-** resource in het cluster.  Als u de [voorbeeld sjabloon][template]gebruikt, zijn er drie virtuele-machine schaal sets (één voor elk knooppunt type in het cluster).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>De Event Store-service configureren
De Event Store-service is een bewakings optie in Service Fabric. Event Store biedt een manier om inzicht te krijgen in de status van uw cluster of workloads op een bepaald moment. De Event Store is een stateful Service Fabric service die gebeurtenissen van het cluster onderhoudt. De gebeurtenis wordt weer gegeven via de Service Fabric Explorer, REST en Api's. Event Store vraagt het cluster rechtstreeks om diagnostische gegevens op te halen uit een wille keurige entiteit in uw cluster en moet worden gebruikt om het volgende te helpen:

* Problemen met het ontwikkelen of testen vaststellen of een bewakings pijplijn gebruiken
* Controleer of beheer acties die u uitvoert op het cluster correct worden verwerkt
* Een ' moment opname ' van de interactie van Service Fabric met een bepaalde entiteit ophalen



Als u de Event Store-service in uw cluster wilt inschakelen, voegt u het volgende toe aan de eigenschap **fabricSettings** van de resource **micro soft. ServiceFabric/clusters** :

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Azure Monitor logboeken instellen voor het cluster

Azure Monitor-Logboeken is onze aanbeveling om gebeurtenissen op cluster niveau te bewaken. Als u Azure Monitor logboeken wilt instellen om uw cluster te bewaken, moet u [Diagnostische gegevens inschakelen om gebeurtenissen op cluster niveau weer te geven](#configure-diagnostics-collection-on-the-cluster).  

De werk ruimte moet zijn verbonden met de diagnostische gegevens die afkomstig zijn uit uw cluster.  Deze logboek gegevens worden opgeslagen in het *applicationDiagnosticsStorageAccountName* -opslag account in de tabellen WADServiceFabric * EventTable, WADWindowsEventLogsTable en WADETWEventTable.

Voeg de Azure Log Analytics-werk ruimte toe en voeg de oplossing toe aan de werk ruimte:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Voeg vervolgens para meters toe
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Voeg vervolgens variabelen toe:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Voeg de Log Analytics agent-extensie toe aan elke schaalset voor virtuele machines in het cluster en verbind de agent met de werk ruimte Log Analytics. Hierdoor kunt u Diagnostische gegevens over containers, toepassingen en prestatie bewaking verzamelen. Door het toe te voegen als een uitbrei ding van de bron van de schaalset voor virtuele machines, zorgt Azure Resource Manager ervoor dat deze wordt geïnstalleerd op elk knoop punt, zelfs wanneer het cluster wordt geschaald.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Het virtuele netwerk en het cluster implementeren

Stel vervolgens de netwerktopologie in en implementeer het Service Fabric-cluster. De Resource Manager-sjabloon [azuredeploy. json][template] maakt een virtueel netwerk, subnet en netwerk beveiligings groep voor service Fabric. De sjabloon implementeert ook een cluster met certificaatbeveiliging ingeschakeld. Gebruik voor productie clusters een certificaat van een certificerings instantie als het cluster certificaat. Een zelfondertekend certificaat kan worden gebruikt om testclusters te beveiligen.

De sjabloon in dit artikel implementeert een cluster dat gebruik maakt van de certificaatvingerafdruk voor het identificeren van het clustercertificaat. Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Het overschakelen van een geïmplementeerd cluster van certificaat vingerafdrukken naar algemene certificaat namen vereenvoudigt het beheer van certificaten. Als u wilt weten hoe u het cluster bijwerkt voor het gebruik van algemene namen voor certificaat beheer, lees dan [cluster wijzigen in common name Management voor certificaten](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Een cluster maken met behulp van een bestaand certificaat

Het volgende script maakt gebruik van de cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) en een sjabloon voor het implementeren van een nieuw cluster in Azure. De cmdlet maakt een nieuwe sleutel kluis in Azure en uploadt uw certificaat.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Een cluster maken met een nieuw zelfondertekend certificaat

Het volgende script maakt gebruik van de cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) en een sjabloon voor het implementeren van een nieuw cluster in Azure. De cmdlet maakt een nieuwe sleutel kluis in azure, voegt een nieuw zelfondertekend certificaat toe aan de sleutel kluis en downloadt het certificaat bestand lokaal.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Verbinding maken met het beveiligde cluster

Maak verbinding met het cluster met behulp van de Power shell-module Service Fabric die is geïnstalleerd met de Service Fabric SDK.  Installeer eerst het certificaat in de persoonlijke opslag (Mijn opslag) van de huidige gebruiker op uw computer. Voer de volgende PowerShell-opdracht uit:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

U bent nu klaar om verbinding te maken met uw beveiligde cluster.

De **Service Fabric** PowerShell-module biedt veel cmdlets voor het beheren van Service Fabric-clusters, toepassingen en services. Gebruik de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) om verbinding te maken met het beveiligde cluster. De SHA1-vingerafdruk van het certificaat en de eindpuntdetails van de verbinding vindt u in de uitvoer van de vorige stap.

Als u eerder Azure AD-client verificatie hebt ingesteld, voert u de volgende opdracht uit: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Als u geen Azure AD-client verificatie hebt ingesteld, voert u de volgende opdracht uit:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Controleer of u bent verbonden en of het cluster in orde is met behulp van de cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) .

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Resources opschonen

De andere artikelen in deze zelfstudie reeks gebruiken het cluster dat u hebt gemaakt. Als u niet meteen verdergaat met het volgende artikel, is het wellicht een goed idee om [het cluster te verwijderen](service-fabric-cluster-delete.md). U bespaart dan kosten.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelf studie voor meer informatie over het schalen van uw cluster.

> [!div class="checklist"]
> * Een VNET maken in Azure met behulp van PowerShell
> * Een sleutelkluis maken en een certificaat uploaden
> * Azure Active Directory-verificatie instellen
> * Diagnostische verzameling configureren
> * De Event Store-service instellen
> * Azure Monitor-logboeken instellen
> * Een beveiligd Service Fabric-cluster maken in Azure PowerShell
> * Het cluster beveiligen met een X.509-certificaat
> * Verbinding maken met het cluster met behulp van PowerShell
> * Een cluster verwijderen

Ga vervolgens verder met de volgende zelf studie voor meer informatie over het controleren van uw cluster.
> [!div class="nextstepaction"]
> [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
