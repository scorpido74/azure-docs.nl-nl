---
title: Een ServiceFabric-cluster met Windows in Azure maken
description: In deze zelfstudie leert u hoe u een Windows Service Fabric-cluster implementeert in een virtuele Azure-netwerk- en netwerkbeveiligingsgroep met PowerShell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 2d170057a85a8e223fa9d1bc2bfc17e0c284afcd
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756050"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Zelfstudie: Een ServiceFabric-cluster implementeren waarop Windows wordt uitgevoerd in een virtueel Azure-netwerk

Deze zelfstudie is deel één van een serie. U leert hoe u een Azure Service Fabric-cluster met Windows implementeert in een [azure-virtuele netwerk-](../virtual-network/virtual-networks-overview.md) en [netwerkbeveiligingsgroep](../virtual-network/virtual-networks-nsg.md) met PowerShell en een sjabloon. Wanneer u klaar bent, wordt er een cluster uitgevoerd in de cloud waarop u toepassingen implementeren. Zie [Een veilig Linux-cluster maken op Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md)als u een Linux-cluster wilt maken dat gebruikmaakt van de Azure CLI.

In deze zelfstudie wordt een productiescenario beschreven. Zie [Een testcluster maken](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)als u een kleiner cluster wilt maken voor testdoeleinden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VNET maken in Azure met behulp van PowerShell
> * Een sleutelkluis maken en een certificaat uploaden
> * Azure Active Directory-verificatie instellen
> * Diagnostische verzameling configureren
> * De EventStore-service instellen
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

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.
* Installeer de [Service Fabric SDK- en PowerShell-module.](service-fabric-get-started.md)
* Azure [Powershell installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Bekijk de belangrijkste concepten van [Azure-clusters](service-fabric-azure-clusters-overview.md).
* [Plannen en voorbereiden op](service-fabric-cluster-azure-deployment-preparation.md) een implementatie van een productiecluster.

Met de volgende procedures wordt er een Service Fabric-cluster met zeven knooppunten gemaakt. Gebruik de [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te berekenen die zijn gemaakt door een Service Fabric-cluster in Azure uit te voeren.

## <a name="download-and-explore-the-template"></a>De sjabloon downloaden en verkennen

Download de volgende Azure Resource Manager-sjabloonbestanden:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Met deze sjabloon wordt een veilig cluster van zeven virtuele machines en drie knooppunttypen in een virtueel netwerk en een netwerkbeveiligingsgroep geïmplementeerd.  Andere voorbeeldsjablonen zijn te vinden op [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). De [azuredeploy.json][template] implementeert een aantal bronnen, waaronder het volgende.

### <a name="service-fabric-cluster"></a>Service Fabric-cluster

In de resource **Microsoft.ServiceFabric/clusters** wordt een Windows-cluster geconfigureerd met de volgende kenmerken:

* Drie knooppunttypes.
* Vijf knooppunten in het primaire knooppunttype (configureerbaar in de sjabloonparameters) en één knooppunt in elk van de andere twee knooppunttypen.
* BE: Windows Server 2016 Datacenter with Containers (configureerbaar in de sjabloonparameters).
* Certificaat beveiligd (configureerbaar in de sjabloonparameters).
* [Omgekeerde proxy](service-fabric-reverseproxy.md) is ingeschakeld.
* [DNS-service](service-fabric-dnsservice.md) is ingeschakeld.
* [Duurzaamheidsniveau](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) van brons (configureerbaar in de sjabloonparameters).
* [Betrouwbaarheidsniveau](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) van Zilver (configureerbaar in de sjabloonparameters).
* Eindpunt van de clientverbinding: 19000 (configureerbaar in de sjabloonparameters).
* HTTP-gatewayeindpunt: 19080 (configureerbaar in de sjabloonparameters).

### <a name="azure-load-balancer"></a>Azure Load Balancer

In de **bron Microsoft.Network/loadBalancers** is een load balancer geconfigureerd. Voor de volgende poorten zijn sondes en regels ingesteld:

* Eindpunt van clientverbinding: 19000
* het eindpunt van de HTTP-gateway: 19080
* Toepassingspoort: 80
* Toepassingspoort: 443
* omgekeerde proxy van Service Fabric: 19081

Als er andere toepassingspoorten nodig zijn, moet u de bron **Microsoft.Network/loadBalancers** en de bron **Microsoft.Network/networkSecurityGroups** aanpassen om het verkeer toe te laten.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtueel netwerk, subnet en netwerkbeveiligingsgroep

De namen van het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep zijn gedefinieerd in de sjabloonparameters. Adresruimten van het virtuele netwerk en subnet worden ook gedeclareerd in de sjabloonparameters en geconfigureerd in de resource **Microsoft.Network/virtualNetworks**:

* Virtuele netwerkadresruimte: 172.16.0.0/20
* Service Fabric-subnetadresruimte: 172.16.2.0/23

De volgende regels voor binnenkomend verkeer worden ingeschakeld in de resource **Microsoft.Network/networkSecurityGroups**. U kunt de poortwaarden wijzigen door de sjabloonvariabelen te wijzigen.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunicatie: 1025, 1026, 1027
* Kortstondige poortbereik: 49152 tot 65534 (minimaal 256 poorten nodig).
* Poorten voor toepassingsgebruik: 80 en 443
* Toepassingspoortbereik: 49152 tot 65534 (gebruikt voor service tot servicecommunicatie. Andere poorten worden niet geopend op de load balancer).
* Blokkeer alle andere poorten

Als er andere toepassingspoorten nodig zijn, moet u de bron **Microsoft.Network/loadBalancers** en de bron **Microsoft.Network/networkSecurityGroups** aanpassen om het verkeer toe te laten.

### <a name="windows-defender"></a>Windows Defender
Standaard is het [Antivirusprogramma van Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) geïnstalleerd en functioneel op Windows Server 2016. De gebruikersinterface is standaard geïnstalleerd op sommige SKU's, maar is niet vereist. Voor elk knooppunttype dat/elke VM-schaalset die is aangegeven in de sjabloon, wordt de [Antimalware voor Azure-VM-extensie](/azure/virtual-machines/extensions/iaas-antimalware-windows) gebruikt voor het uitsluiten van de Service Fabric-mappen en processen:

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

Het parameterbestand [azuredeploy.parameters.json][parameters] bepaalt veel waarden die worden gebruikt om het cluster en de bijbehorende resources te implementeren. De volgende parameters zijn die u moet wijzigen voor uw implementatie:

**Parameter** | **Voorbeeldwaarde** | **Opmerkingen** 
|---|---|---|
|adminUserName|vmadmin| De gebruikersnaam van de beheerder van de cluster-VM's. [Gebruikersnaamvereisten voor VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Het wachtwoord van de beheerder van de cluster-VM's. [Wachtwoordvereisten voor VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| De naam van het cluster. Mag alleen letters en cijfers bevatten. De lengte kan minimaal 3 en maximaal 23 tekens zijn.|
|location|southcentralus| De locatie van het cluster. |
|certificateThumbprint|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de SHA1-waarde van de certificaatvingerafdruk in. Bijvoorbeeld 6190390162C988701DB5676EB81083EA608DCCF3.</p> |
|certificateUrlValue|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft. </p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de URL van het certificaat in. Bijvoorbeeld "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de waarde van de bronkluis in. Bijvoorbeeld /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Clientverificatie via Azure Active Directory instellen
Voor Service Fabric-clusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor wederzijdse verificatie van client-naar-knooppunt:
* Gebruik Azure Active Directory voor clientidentiteit.
* Gebruik een certificaat voor serveridentiteit en TLS-versleuteling van HTTP-communicatie.

Het instellen van Azure Active Directory (Azure AD) om clients voor een Service Fabric-cluster te verifiëren, moet worden uitgevoerd voordat [het cluster wordt gemaakt.](#createvaultandcert) Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk. 

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) en [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Als gevolg hiervan, maakt u twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing.  Nadat de toepassingen zijn gemaakt, wijst u gebruikers toe aan de rollen alleen-lezen en beheerder.

> [!NOTE]
> U moet de volgende stappen uitvoeren voordat u het cluster maakt. Omdat de scripts clusternamen en eindpunten verwachten, moeten de waarden worden gepland en geen waarden zijn die u al hebt gemaakt.

In dit artikel gaan we ervan uit dat u al een tenant hebt gemaakt. Als u dit nog niet hebt gedaan, leest u nu [Hoe u een Azure Active Directory-tenant krijgen.](../active-directory/develop/quickstart-create-new-tenant.md)

Om de stappen te vereenvoudigen die betrokken zijn bij het configureren van Azure AD met een Service Fabric-cluster, hebben we een set Windows PowerShell-scripts gemaakt. [Download de scripts](https://github.com/Azure-Samples/service-fabric-aad-helpers) op uw computer.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure Active Directory-toepassingen maken en gebruikers toewijzen aan rollen
Maak twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing. Nadat u de toepassingen hebt gemaakt om uw cluster weer te geven, wijst u uw gebruikers toe aan de [rollen die worden ondersteund door Service Fabric:](service-fabric-cluster-security-roles.md)alleen-lezen en beheerder.

Voer `SetupApplications.ps1` uit, en geef de tenant-ID, de naam van het cluster en de antwoord-URL van de webtoepassing op als parameters. Geef gebruikersnamen en wachtwoorden op voor de gebruikers. Bijvoorbeeld:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Voor nationale clouds (bijvoorbeeld Azure Government, Azure China, Azure Germany) geeft u de `-Location` parameter op.

U vindt uw *TenantId* ofwel de map-id in de [Azure-portal](https://portal.azure.com). Selecteer Azure Active**Directory-eigenschappen** **Azure Active Directory** > en kopieer de **waarde van de Directory-id.**

De *Clusternaam* wordt gebruikt voor als prefix aan de Azure Active Directory-toepassingen die zijn gemaakt door het script. Het hoeft niet precies overeenkomen met de werkelijke clusternaam. Het maakt het alleen eenvoudiger om Azure AD-artefacten toe te zetten aan het in gebruik zijnde cluster Service Fabric.

*WebApplicationReplyUrl* wordt het standaardeindpunt dat Azure Active Directory retourneert naar uw gebruikers nadat ze klaar zijn aanmelden. Stel dit eindpunt in als het Service Fabric Explorer-eindpunt voor uw cluster, dit is standaard:

https://&lt;cluster_domain&gt;:19080/Explorer

U wordt gevraagd zich aan te melden bij een account met beheerdersbevoegdheden voor de Azure AD-tenant. Nadat u zich hebt aangemeld, maakt het script de web- en systeemeigen toepassingen voor uw Service Fabric-cluster. In de toepassingen van de tenant in de [Azure-portal](https://portal.azure.com)ziet u twee nieuwe vermeldingen:

   * *Clusternaamcluster*\_
   * *Clusternaamclient*\_

Het script drukt de JSON af die vereist is voor de resourcemanagersjabloon wanneer u het cluster maakt, dus het is een goed idee om het PowerShell-venster open te houden.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure Active Directory-configuratie toevoegen voor het gebruik van Azure Active Directory voor clienttoegang
Configureer in [azuredeploy.json][template] Azure Active Directory in de sectie **Microsoft.ServiceFabric/clusters**. Voeg parameters toe voor de tenant-id, de toepassings-id van het cluster en de clienttoepassings-id.  

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

Voeg de parameterwaarden toe in het parameterbestand [azuredeploy.parameters.json][parameters]. Bijvoorbeeld:

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

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Diagnostische verzameling configureren op het cluster
Wanneer u een cluster servicestructuur uitvoert, is het een goed idee om de logboeken van alle knooppunten op een centrale locatie te verzamelen. Als u de logboeken op een centrale locatie hebt, u problemen in uw cluster of problemen in de toepassingen en services die in dat cluster worden uitgevoerd, analyseren en oplossen.

Een manier om logboeken te uploaden en te verzamelen, is door de AZURE Diagnostics (WAD)-extensie te gebruiken, die logboeken uploadt naar Azure Storage, en ook de mogelijkheid heeft om logboeken naar Azure Application Insights of Event Hubs te verzenden. U ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en deze in een analyseplatformproduct te plaatsen, zoals Azure Monitor-logboeken of een andere log-parsing-oplossing.

Als u deze zelfstudie volgt, is de verzameling diagnostische gegevens al geconfigureerd in de [sjabloon.][template]

Als u een bestaand cluster hebt dat geen Diagnose heeft geïmplementeerd, u dit toevoegen of bijwerken via de clustersjabloon. Wijzig de sjabloon Resourcemanager die wordt gebruikt om het bestaande cluster te maken of de sjabloon van de portal te downloaden. Wijzig het bestand template.json door de volgende taken uit te voeren:

Voeg een nieuwe opslagbron toe aan de sectie resources in de sjabloon:
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

Voeg vervolgens parameters toe voor de naam van het opslagaccount en typ aan de parameterssectie van de sjabloon. Vervang de naam van het tijdelijke tekstopslagaccount vervangen door de naam van het opslagaccount dat u wilt.

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

Voeg vervolgens de **extensie IaaSDiagnostics** toe aan de extensiearray van de eigenschap **VirtualMachineProfile** van elke **Microsoft.Compute/virtualMachineScaleSets-bron** in het cluster.  Als u de [voorbeeldsjabloon][template]gebruikt, zijn er drie virtuele machineschaalsets (één voor elk knooppunttype in het cluster).

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

## <a name="configure-the-eventstore-service"></a>De EventStore-service configureren
De EventStore-service is een bewakingsoptie in Service Fabric. EventStore biedt een manier om de status van uw cluster of workloads op een bepaald moment te begrijpen. De EventStore is een stateful Service Fabric-service die gebeurtenissen uit het cluster bijhoudt. De gebeurtenis wordt weergegeven via de Service Fabric Explorer, REST en API's. EventStore stelt het cluster rechtstreeks op om diagnostische gegevens op een entiteit in uw cluster te verzamelen en moet worden gebruikt om te helpen:

* Problemen bij de ontwikkeling of het testen diagnosticeren of waar u mogelijk een bewakingspijplijn gebruikt
* Controleren of beheeracties die u op uw cluster onderneemt correct worden verwerkt
* Ontvang een momentopname van de manier waarop Service Fabric met een bepaalde entiteit omgaat



Als u de EventStore-service op uw cluster wilt inschakelen, voegt u het volgende toe aan de eigenschap **fabricSettings** van de bron **Microsoft.ServiceFabric/clusters:**

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

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Azure Monitor-logboeken instellen voor het cluster

Azure Monitor-logboeken is onze aanbeveling om gebeurtenissen op clusterniveau te controleren. Als u Azure Monitor-logboeken wilt instellen om uw cluster te controleren, moet u diagnostische gegevens hebben [ingeschakeld om gebeurtenissen op clusterniveau weer te geven.](#configure-diagnostics-collection-on-the-cluster)  

De werkruimte moet worden gekoppeld aan de diagnostische gegevens die afkomstig zijn van uw cluster.  Deze logboekgegevens worden opgeslagen in het opslagaccount *applicationDiagnosticsStorageAccountName,* in de tabelWADServiceFabric*EventTable, WADWindowsEventLogsTable en WADETWEventTable.

Voeg de Azure Log Analytics-werkruimte toe en voeg de oplossing toe aan de werkruimte:

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

Voeg vervolgens parameters toe
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

Voeg de extensie Log Analytics-agent toe aan elke virtuele machineschaalset in het cluster en verbind de agent met de werkruimte Log Analytics. Dit maakt het verzamelen van diagnostische gegevens over containers, toepassingen en prestatiebewaking mogelijk. Door deze toe te voegen als een uitbreiding aan de bron voor de virtuele machineschaalset, zorgt Azure Resource Manager ervoor dat het op elk knooppunt wordt geïnstalleerd, zelfs wanneer het cluster wordt geschaald.

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

Stel vervolgens de netwerktopologie in en implementeer het Service Fabric-cluster. Met de sjabloon [azuredeploy.json][template] Resource Manager wordt een virtuele netwerk-, subnet- en netwerkbeveiligingsgroep voor Service Fabric gemaakt. De sjabloon implementeert ook een cluster met certificaatbeveiliging ingeschakeld. Gebruik voor productieclusters een certificaat van een certificaatautoriteit als clustercertificaat. Een zelfondertekend certificaat kan worden gebruikt om testclusters te beveiligen.

De sjabloon in dit artikel implementeert een cluster dat gebruik maakt van de certificaatvingerafdruk voor het identificeren van het clustercertificaat. Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Het overschakelen van een geïmplementeerd cluster van certificaatduimafdrukken naar gebrese certificaatnamen vereenvoudigt het certificaatbeheer. Lees Cluster wijzigen om het algemeen naambeheer van [het certificaat te gebruiken voor](service-fabric-cluster-change-cert-thumbprint-to-cn.md)meer informatie over het bijwerken van het cluster om gemeenschappelijke certificaatnamen te gebruiken.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Een cluster maken met behulp van een bestaand certificaat

In het volgende script wordt de cmdlet [Nieuw-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) en een sjabloon gebruikt om een nieuw cluster in Azure te implementeren. De cmdlet maakt een nieuwe sleutelkluis in Azure en uploadt uw certificaat.

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

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Een cluster maken met een nieuw, zelfondertekend certificaat

In het volgende script wordt de cmdlet [Nieuw-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) en een sjabloon gebruikt om een nieuw cluster in Azure te implementeren. De cmdlet maakt een nieuwe sleutelkluis in Azure, voegt een nieuw zelfondertekend certificaat toe aan de sleutelkluis en downloadt het certificaatbestand lokaal.

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

Maak verbinding met het cluster met de Service Fabric PowerShell-module die is geïnstalleerd met de Service Fabric SDK.  Installeer eerst het certificaat in de persoonlijke opslag (Mijn opslag) van de huidige gebruiker op uw computer. Voer de volgende PowerShell-opdracht uit:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

U bent nu klaar om verbinding te maken met uw beveiligde cluster.

De **Service Fabric** PowerShell-module biedt veel cmdlets voor het beheren van Service Fabric-clusters, toepassingen en services. Gebruik de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) om verbinding te maken met het beveiligde cluster. De SHA1-vingerafdruk van het certificaat en de eindpuntdetails van de verbinding vindt u in de uitvoer van de vorige stap.

Als u eerder Azure AD-clientverificatie hebt ingesteld, voert u de volgende opdracht uit: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Als u azure AD-clientverificatie niet hebt ingesteld, voert u de volgende opdracht uit:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Controleer of u bent verbonden en of het cluster in orde is met de cmdlet [Get-ServiceFabricClusterHealth.](/powershell/module/servicefabric/get-servicefabricclusterhealth)

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Resources opschonen

De andere artikelen in deze zelfstudiereeks gebruiken het cluster dat u hebt gemaakt. Als u niet onmiddellijk naar het volgende artikel gaat, u [het cluster verwijderen](service-fabric-cluster-delete.md) om te voorkomen dat er kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie om te leren hoe u uw cluster schalen.

> [!div class="checklist"]
> * Een VNET maken in Azure met behulp van PowerShell
> * Een sleutelkluis maken en een certificaat uploaden
> * Azure Active Directory-verificatie instellen
> * Diagnostische verzameling configureren
> * De EventStore-service instellen
> * Azure Monitor-logboeken instellen
> * Een beveiligd Service Fabric-cluster maken in Azure PowerShell
> * Het cluster beveiligen met een X.509-certificaat
> * Verbinding maken met het cluster met behulp van PowerShell
> * Een cluster verwijderen

Ga vervolgens naar de volgende zelfstudie om te leren hoe u uw cluster controleren.
> [!div class="nextstepaction"]
> [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
