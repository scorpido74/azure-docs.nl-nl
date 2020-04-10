---
title: Clusterknooppunten upgraden om azure-beheerde schijven te gebruiken
description: U als u een bestaand cluster van ServiceFabric upgraden om door Azure beheerde schijven te gebruiken met weinig of geen downtime van uw cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991208"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Clusterknooppunten upgraden om azure-beheerde schijven te gebruiken

[Azure managed disks](../virtual-machines/windows/managed-disks-overview.md) zijn de aanbevolen schijfopslagaanbieding voor gebruik met virtuele Azure-machines voor permanente opslag van gegevens. U de tolerantie van uw Service Fabric-workloads verbeteren door de virtuele machineschaalsets te upgraden die ten grondslag liggen aan uw knooppunttypen om beheerde schijven te gebruiken. U als u een bestaand cluster van ServiceFabric upgraden om door Azure beheerde schijven te gebruiken met weinig of geen downtime van uw cluster.

De algemene strategie voor het upgraden van een clusterknooppunt servicestructuur voor het gebruik van beheerde schijven is:

1. Implementeer een anders dubbele virtuele machineschaalset van dat knooppunttype, maar `osDisk` met het [beheerde schijfobject](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) dat is toegevoegd aan het gedeelte van de implementatiesjabloon voor de virtuele machineschaalset. De nieuwe schaalset moet worden gekoppeld aan dezelfde load balancer / IP als het origineel, zodat uw klanten geen servicestoring ervaren tijdens de migratie.

2. Zodra zowel de oorspronkelijke als de bijgewerkte schaalsets naast elkaar worden uitgevoerd, schakelt u de oorspronkelijke knooppuntinstanties één voor één uit, zodat de systeemservices (of replica's van stateful services) migreren naar de nieuwe schaalset.

3. Controleer of het cluster en de nieuwe knooppunten in orde zijn en verwijder vervolgens de oorspronkelijke schaalset en knooppuntstatus voor de verwijderde knooppunten.

In dit artikel vindt u de stappen doorlopen om het primaire knooppunttype van een voorbeeldcluster te upgraden om beheerde schijven te gebruiken, terwijl u uitvaltijd van het cluster vermijdt (zie opmerking hieronder). De initiële status van het voorbeeldtestcluster bestaat uit één knooppunttype [Silver-duurzaamheid,](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)ondersteund door een enkele schaalset met vijf knooppunten.

> [!CAUTION]
> U krijgt alleen last van een storing in deze procedure als u afhankelijk bent van de cluster-DNS (bijvoorbeeld bij het openen van [Service Fabric Explorer).](service-fabric-visualizing-your-cluster.md) Architecturale [best practice voor front-end services](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) is om een soort van load [balancer](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) voor uw knooppunt types om knooppunt swapping mogelijk te maken zonder een storing.

Hier volgen de [sjablonen en cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) voor Azure Resource Manager die we gebruiken om het upgradescenario te voltooien. De sjabloonwijzigingen worden uitgelegd in [Een aangepaste schaalset implementeren voor het primaire knooppunttype](#deploy-an-upgraded-scale-set-for-the-primary-node-type) hieronder.

## <a name="set-up-the-test-cluster"></a>Het testcluster instellen

Laten we het eerste testcluster van Service Fabric instellen. [Download](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) eerst de voorbeeldsjablonen voor Azure-bronbeheer die we gebruiken om dit scenario te voltooien.

Meld u vervolgens aan bij uw Azure-account.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Met de volgende opdrachten u een nieuw zelfondertekend certificaat genereren en het testcluster implementeren. Als u al een certificaat hebt dat u wilt gebruiken, gaat u naar [Een bestaand certificaat gebruiken om het cluster te implementeren.](#use-an-existing-certificate-to-deploy-the-cluster)

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Een zelfondertekend certificaat genereren en het cluster implementeren

Wijs eerst de variabelen toe die u nodig hebt voor de implementatie van het Cluster Servicefabric. Pas de `resourceGroupName`waarden `certSubjectName` `parameterFilePath`aan `templateFilePath` voor , , en voor uw specifieke account en omgeving:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Controleer of `certOutputFolder` de locatie op uw lokale machine aanwezig is voordat u de opdracht uitvoert om een nieuw cluster van ServiceFabric te implementeren.

Open vervolgens het bestand [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) `clusterName` en `dnsName` pas de waarden aan voor en om overeen te komen met de dynamische waarden die u in PowerShell instelt en sla uw wijzigingen op.

Implementeer vervolgens het testcluster Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Zodra de implementatie is voltooid, zoekt`$certPfx`u het *.pfx-bestand* ( ) op uw lokale machine en importeert u het in uw certificaatarchief:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

De bewerking retourneert de duimafdruk van het certificaat, waarmee u [verbinding maakt met het nieuwe cluster](#connect-to-the-new-cluster-and-check-health-status) en de status van de status controleert. (Sla de volgende sectie over, een alternatieve benadering van clusterimplementatie.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Een bestaand certificaat gebruiken om het cluster te implementeren

U ook een bestaand Azure Key Vault-certificaat gebruiken om het testcluster te implementeren. Om dit te doen, moet u verwijzingen naar uw Key Vault en certificaat duimafdruk [verkrijgen.](#obtain-your-key-vault-references)

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Open het bestand [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) en `clusterName` `dnsName` wijzig de waarden voor en in iets unieks.

Geef ten slotte een naam van de `templateFilePath` `parameterFilePath` brongroep voor het cluster aan en stel de en de locaties in van uw *Initial-1NodeType-UnmanagedDisks-bestanden:*

> [!NOTE]
> De aangewezen resourcegroep moet al bestaan en zich in dezelfde regio bevinden als uw sleutelkluis.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Voer ten slotte de volgende opdracht uit om het eerste testcluster te implementeren:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Verbinding maken met het nieuwe cluster en de status controleren

Maak verbinding met het cluster en zorg ervoor dat `clusterName` alle `thumb` vijf knooppunten in orde zijn (ter vervanging van de en variabelen voor uw cluster):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Daarmee zijn we klaar om de upgradeprocedure te starten.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Een bijgewerkte schaalset implementeren voor het primaire knooppunttype

Om een knooppunttype te upgraden of verticaal te *schalen,* moeten we een kopie van de virtuele machineschaalset van dat knooppunttype implementeren, die `nodeTypeRef`anders `subnet`identiek `loadBalancerBackendAddressPools`is aan de oorspronkelijke schaalset (inclusief verwijzing naar dezelfde , en ) behalve dat deze de gewenste upgrade/wijzigingen en een eigen afzonderlijke subnet- en inbound NAT-adresgroep bevat. Omdat we een primair knooppunttype upgraden, wordt de nieuwe`isPrimary: true`schaalset gemarkeerd als primaire ( ), net als de oorspronkelijke schaalset. (Voor upgrades van niet-primaire knooppunttypen laat u dit gewoon weg.)

Voor het gemak zijn de vereiste wijzigingen al voor u aangebracht in de [sjabloon-](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) en [parametersbestanden](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) *voor upgrade-1NodeType-2ScaleSets manageddisks.*

In de volgende secties worden de sjabloonwijzigingen in detail uitgelegd. Als u dat liever hebt, u de uitleg overslaan en doorgaan naar [de volgende stap van de upgradeprocedure.](#obtain-your-key-vault-references)

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>De clustersjabloon bijwerken met de bijgewerkte schaalset

Hier volgen de sectie-voor-sectie wijzigingen van de oorspronkelijke clusterimplementatiesjabloon voor het toevoegen van een bijgewerkte schaalset voor het primaire knooppunttype.

#### <a name="parameters"></a>Parameters

Parameters toevoegen voor de instantienaam, het aantal en de grootte van de nieuwe schaalset. Houd `vmNodeType1Name` er rekening mee dat dit uniek is voor de nieuwe schaalset, terwijl de tel- en groottewaarden identiek zijn aan de oorspronkelijke schaalset.

**Sjabloonbestand**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Bestand Parameters**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Variabelen

Voeg in `variables` de sectie implementatiesjabloon een vermelding toe voor de binnenkomende NAT-adresgroep van de nieuwe schaalset.

**Sjabloonbestand**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Resources

Voeg in de sectie resources voor *implementatiesjablonen* de nieuwe virtuele machineschaalset toe, rekening houdend met deze dingen:

* De nieuwe schaalset verwijst naar hetzelfde knooppunttype als het origineel:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* De nieuwe schaalset verwijst naar hetzelfde backendadres en subnet van de load balancer (maar gebruikt een andere inkomende NAT-groep van load balancer):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Net als de oorspronkelijke schaalset wordt de nieuwe schaalset gemarkeerd als het primaire knooppunttype. (Wanneer u niet-primaire knooppunttypen upgradet, laat u deze wijziging weg.)

    ```json
    "isPrimary": true,
    ```

* In tegenstelling tot de oorspronkelijke schaalset wordt de nieuwe schaalset geüpgraded om beheerde schijven te gebruiken.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Zodra u alle wijzigingen in uw sjabloon- en parametersbestanden hebt geïmplementeerd, gaat u naar de volgende sectie om uw Key Vault-referenties te verkrijgen en de updates naar uw cluster te implementeren.

### <a name="obtain-your-key-vault-references"></a>Uw Key Vault-referenties verkrijgen

Als u de bijgewerkte configuratie wilt implementeren, ontvangt u eerst verschillende verwijzingen naar uw clustercertificaat dat is opgeslagen in uw Key Vault. De eenvoudigste manier om deze waarden te vinden is via Azure portal. U hebt het volgende nodig:

* **De KEY Vault-URL van uw clustercertificaat.** Selecteer **certificaten** > *uw gewenste certificaatgeheime* > **id**in uw Sleutelkluis in Azure-portal:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **De duimafdruk van uw clustercertificaat.** (U hebt dit waarschijnlijk al als u [verbinding hebt gemaakt met het oorspronkelijke cluster](#connect-to-the-new-cluster-and-check-health-status) om de status ervan te controleren.) Kopieer **X.509 SHA-1 Thumbprint (in hex)** vanaf hetzelfde certificaatblad **(Certificaten** > *uw gewenste certificaat)* in Azure-portal:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **De Resource ID van uw Key Vault.** Selecteer **Eigenschappen** > **resource-id**in uw Sleutelkluis in Azure-portal:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren

Pas `parameterFilePath` de `templateFilePath` en e-as nodig aan en voer vervolgens de volgende opdracht uit:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Wanneer de implementatie is voltooid, controleert u de clusterstatus opnieuw en zorgt u ervoor dat alle tien knooppunten (vijf op de oorspronkelijke en vijf op de nieuwe schaalset) in orde zijn.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Seed-knooppunten migreren naar de nieuwe schaalset

We zijn nu klaar om te beginnen met het uitschakelen van de knooppunten van de oorspronkelijke schaalset. Als deze knooppunten worden uitgeschakeld, migreren de systeemservices en seednodes naar de VM's van de nieuwe schaalset omdat deze ook is gemarkeerd als het primaire knooppunttype.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Gebruik Service Fabric Explorer om de migratie van seednodes naar de nieuwe schaalset en de voortgang van knooppunten in de oorspronkelijke schaalset te controleren *van Uitschakelen* naar *Uitgeschakelde* status.

![Service Fabric Explorer met status van uitgeschakelde knooppunten](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Het kan enige tijd duren voordat de uitschakelende bewerking is voltooid op alle knooppunten van de oorspronkelijke schaalset. Om de consistentie van de gegevens te garanderen, kan slechts één seed node tegelijk veranderen. Elke wijziging van het seedknooppunt vereist een clusterupdate; dus het vervangen van een zaadknooppunt vereist twee cluster upgrades (een elk voor knooppunt toevoeging en verwijdering). Het upgraden van de vijf seed nodes in dit voorbeeldscenario resulteert in tien clusterupgrades.

## <a name="remove-the-original-scale-set"></a>De oorspronkelijke schaalset verwijderen

Zodra de uitschakelbewerking is voltooid, verwijdert u de schaalset.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

In Service Fabric Explorer worden de verwijderde knooppunten (en dus de *status clusterstatus)* nu weergegeven in *de status Fout.*

![ServiceFabric Explorer met uitgeschakelde knooppunten in foutstatus](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Verwijder de verouderde knooppunten uit het cluster Servicefabric om de clusterstatus te herstellen naar *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer met downknooppunten in foutstatus verwijderd](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Volgende stappen

In deze walkthrough hebt u geleerd hoe u de virtuele machineschaalsets van een Service Fabric-cluster upgraden om beheerde schijven te gebruiken terwijl u serviceonderbrekingen tijdens het proces voorkomt. Voor meer informatie over gerelateerde onderwerpen bekijk de volgende bronnen.

Leer hoe u het volgende doet:

* [Een primair knooppunttype voor Service Fabric-clusters omhoog schalen](service-fabric-scale-up-node-type.md)

* [Een sjabloon voor een schaalset converteren naar beheerde schijven](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Een knooppunttype servicestof verwijderen](service-fabric-how-to-remove-node-type.md)

Zie ook:

* [Voorbeeld: Clusterknooppunten upgraden om azure-beheerde schijven te gebruiken](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Overwegingen voor verticale schaling](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)