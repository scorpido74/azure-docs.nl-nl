---
title: Een Azure Service Fabric-knooppunt type omhoog schalen
description: Meer informatie over hoe u een Service Fabric cluster kunt schalen door een Schaalset voor virtuele machines toe te voegen.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 2d700367049e0bf9bf710aad110c850a78c26220
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610690"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Een primair knooppunttype voor Service Fabric-clusters omhoog schalen
In dit artikel wordt beschreven hoe u het primaire knooppunt type van een Service Fabric cluster omhoog kunt schalen door de resources van de virtuele machine te verg Roten. Een Service Fabric cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw micro services worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Virtuele-machine schaal sets vormen een Azure Compute-resource die u gebruikt om een verzameling virtuele machines als een set te implementeren en te beheren. Elk knooppunt type dat in een Azure-cluster is gedefinieerd, wordt [ingesteld als een afzonderlijke schaalset](service-fabric-cluster-nodetypes.md). Elk knooppunt type kan vervolgens afzonderlijk worden beheerd. Nadat u een Service Fabric cluster hebt gemaakt, kunt u het type van een cluster knooppunt verticaal schalen (de resources van de knoop punten wijzigen) of het besturings systeem van het knooppunt type Vm's bijwerken.  U kunt het cluster op elk gewenst moment schalen, zelfs wanneer werk belastingen op het cluster worden uitgevoerd.  Naarmate het cluster wordt geschaald, worden uw toepassingen ook automatisch geschaald.

> [!WARNING]
> Probeer niet een procedure van een primair knooppunt type scale up uit te voeren als de cluster status niet in orde is, omdat hierdoor het cluster alleen verder wordt beschadigd.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Proces voor het bijwerken van de grootte en het besturings systeem van de virtuele machines van het primaire knooppunt type
Hier volgt het proces voor het bijwerken van de VM-grootte en het besturings systeem van de virtuele machines van het primaire knooppunt type.  Na de upgrade zijn de virtuele machines van het primaire knoop punt grootte standaard D4_V2 en worden Windows Server 2016 Data Center uitgevoerd met containers.

> [!WARNING]
> Voordat u deze procedure uitvoert op een productie cluster, raden we u aan de voorbeeld sjablonen te bestuderen en het proces te controleren op basis van een test cluster. Het cluster is ook niet beschikbaar voor een tijd. U kunt geen wijzigingen aanbrengen in meerdere VMSS die als hetzelfde NodeType worden gedeclareerd, parallel. u moet gescheiden implementatie bewerkingen uitvoeren om wijzigingen toe te passen op elke NodeType VMSS afzonderlijk.

1. Implementeer het eerste cluster met twee knooppunt typen en twee schaal sets (één schaalset per knooppunt type) met behulp van deze voorbeeld [sjabloon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) en [parameter](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) bestanden.  Beide schaal sets zijn de grootte standaard D2_V2 en Windows Server 2012 R2 Data Center uit te voeren.  Wacht totdat het cluster de basislijn upgrade heeft voltooid.   
2. Optioneel: Implementeer een stateful voor beeld naar het cluster.
3. Nadat u hebt besloten om een upgrade uit te voeren van het primaire knooppunt type Vm's, voegt u een nieuwe schaalset toe aan het primaire knooppunt type met behulp van deze voorbeeld [sjabloon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) en [parameter](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) bestanden zodat het primaire knooppunt type nu twee schaal sets heeft.  Systeem services en gebruikers toepassingen kunnen migreren tussen virtuele machines in de twee verschillende schaal sets.  De nieuwe virtuele machines van de schaalset hebben de grootte standaard D4_V2 en voeren Windows Server 2016 Data Center uit met containers.  Er wordt ook een nieuwe load balancer en een openbaar IP-adres toegevoegd met de nieuwe schaalset.  
    Als u de nieuwe schaalset in de sjabloon wilt zoeken, zoekt u naar de resource ' micro soft. Compute/virtualMachineScaleSets ' met de para meter *vmNodeType2Name* .  De nieuwe schaalset wordt toegevoegd aan het primaire knooppunt type met behulp van de eigenschappen->virtualMachineProfile->extensionProfile->Extensions->eigenschappen->instellingen->nodeTypeRef instelling.
4. Controleer de cluster status en controleer of alle knoop punten in orde zijn.
5. Schakel de knoop punten in de oude schaalset van het primaire knooppunt type uit met het doel om het knoop punt te verwijderen. U kunt alle tegelijk uitschakelen en de bewerkingen worden in de wachtrij geplaatst. Wacht totdat alle knoop punten zijn uitgeschakeld. Dit kan enige tijd duren.  Als de oudere knoop punten in het knooppunt type zijn uitgeschakeld, worden de systeem services en Seed-knoop punten gemigreerd naar de virtuele machines van de nieuwe schaalset in het primaire knooppunt type.
6. Verwijder de oudere schaalset van het primaire knooppunt type. (Nadat de knoop punten zijn uitgeschakeld zoals in stap 5, wijst u in de Blade virtuele-machine schaalset in de Azure Portal de toewijzing van de knoop punten van het oude knooppunt type één voor één toe.)
7. Verwijder het load balancer dat is gekoppeld aan de oude schaalset. Het cluster is niet beschikbaar terwijl het nieuwe open bare IP-adres en load balancer zijn geconfigureerd voor de nieuwe schaalset.  
8. Sla de DNS-instellingen op van het open bare IP-adres dat is gekoppeld aan de oude schaalset van het primaire knoop punt in een variabele en verwijder het open bare IP-adres.
9. Vervang de DNS-instellingen van het open bare IP-adres dat is gekoppeld aan de nieuwe schaalset van het primaire knoop punt door de DNS-instellingen van het verwijderde open bare IP-adres.  Het cluster is nu weer bereikbaar.
10. Verwijder de knooppunt status van de knoop punten uit het cluster.  Als het duurzaamheids niveau van de oude schaalset zilver of goud is, wordt deze stap automatisch door het systeem uitgevoerd.
11. Als u de stateful toepassing in een vorige stap hebt geïmplementeerd, controleert u of de toepassing functioneel is.

## <a name="set-up-the-test-cluster"></a>Het test cluster instellen

Begin met het downloaden van de twee sets met bestanden die u nodig hebt voor deze zelf studie, de [sjabloon]() vóór en de [para meters]() en de na- [sjabloon]() en- [para meters]().

Meld u vervolgens aan bij uw Azure-account.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

In deze zelf studie wordt het scenario voor het maken van een zelfondertekend certificaat door lopen. Als u een bestaand certificaat van Azure Key Vault wilt gebruiken, slaat u de onderstaande stap over en spiegelt u in plaats daarvan de stappen in [een bestaand certificaat om het cluster te implementeren](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Een zelfondertekend certificaat genereren en het cluster implementeren

Wijs eerst de variabelen toe die u nodig hebt voor Service Fabric cluster implementatie. Pas de waarden voor `resourceGroupName` , `certSubjectName` ,, `parameterFilePath` en `templateFilePath` voor uw specifieke account en omgeving aan:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Zorg ervoor dat de `certOutputFolder` locatie aanwezig is op de lokale computer voordat u de opdracht uitvoert om een nieuw service Fabric-cluster te implementeren.

Open vervolgens de *Deploy-2NodeTypes-2ScaleSets.parameters.jsin* het bestand en wijzig de waarden voor `clusterName` en, `dnsName` zodat deze overeenkomen met de dynamische waarden die u hebt ingesteld in Power shell en sla uw wijzigingen op.

Implementeer vervolgens het Service Fabric test cluster:

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

Zodra de implementatie is voltooid, gaat u naar het *PFX* -bestand ( `$certPfx` ) op de lokale computer en importeert u het in uw certificaat archief:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

De bewerking retourneert de vinger afdruk van het certificaat, die u gebruikt om verbinding te maken met het nieuwe cluster en de integriteits status te controleren.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Verbinding maken met het nieuwe cluster en de status controleren

Maak verbinding met het cluster en controleer of alle knoop punten in orde zijn (Vervang `clusterName` de `thumb` variabelen en voor uw cluster):

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

We zijn klaar om de upgrade procedure te starten.

## <a name="upgrade-the-primary-node-type-vms"></a>De virtuele machines van het primaire knooppunt type upgraden

Nadat u hebt besloten om een upgrade uit te voeren van het primaire knooppunt type Vm's, voegt u een nieuwe schaalset toe aan het primaire knooppunt type, zodat het primaire knooppunt type nu twee schaal sets heeft. Er zijn een voorbeeld [sjabloon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) en [parameter](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) bestanden opgegeven om de benodigde wijzigingen weer te geven. De nieuwe virtuele machines van de schaalset hebben de grootte standaard D4_V2 en voeren Windows Server 2016 Data Center uit met containers. Er wordt ook een nieuwe load balancer en een openbaar IP-adres toegevoegd met de nieuwe schaalset. 

Als u de nieuwe schaalset in de sjabloon wilt zoeken, zoekt u naar de resource ' micro soft. Compute/virtualMachineScaleSets ' met de para meter vmNodeType2Name. De nieuwe schaalset wordt toegevoegd aan het primaire knooppunt type met behulp van de eigenschappen->virtualMachineProfile->extensionProfile->Extensions->eigenschappen->instellingen->nodeTypeRef instelling.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren

Pas de `parameterFilePath` en `templateFilePath` zo nodig aan en voer de volgende opdracht uit:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Wanneer de implementatie is voltooid, controleert u de cluster status opnieuw en zorgt u ervoor dat alle knoop punten (op het origineel en op de nieuwe schaalset) in orde zijn.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Knoop punten migreren naar de nieuwe schaalset

U kunt nu beginnen met het uitschakelen van de knoop punten van de oorspronkelijke schaalset. Wanneer deze knoop punten worden uitgeschakeld, worden de systeem services en Seed-knoop punten gemigreerd naar de virtuele machines van de nieuwe schaalset, omdat deze ook als primair knooppunt type is gemarkeerd.

Voor het omhoog schalen van niet-primaire knooppunt typen, in deze stap wijzigt u de beperking voor service plaatsing in de nieuwe virtuele-machine schaalset/knooppunt type en vermindert u vervolgens het aantal exemplaren van de oude virtuele-machine schaalset naar nul, één knoop punt tegelijk (om ervoor te zorgen dat het verwijderen van knoop punten niet van invloed is op de betrouw baarheid van het cluster).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Gebruik Service Fabric Explorer om de migratie van Seed-knoop punten te bewaken naar de nieuwe schaalset en de voortgang van knoop punten in de oorspronkelijke schaalset uit te *scha kelen* naar *Uitgeschakelde* status.

> [!NOTE]
> Het kan enige tijd duren voordat de bewerking is uitgeschakeld op alle knoop punten van de oorspronkelijke schaalset. Om consistentie van gegevens te garanderen, kan slechts één Seed-knoop punt tegelijk worden gewijzigd. Voor elke wijziging van het Seed-knoop punt is een cluster update vereist; voor het vervangen van een Seed-knoop punt zijn twee cluster upgrades vereist (één voor het toevoegen en verwijderen van knoop punten). Het bijwerken van de vijf Seed-knoop punten in dit voorbeeld scenario resulteert in tien cluster upgrades.

## <a name="remove-the-original-scale-set"></a>De oorspronkelijke schaalset verwijderen

Wanneer het uitschakelen is voltooid, verwijdert u de schaalset.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

In Service Fabric Explorer wordt de verwijderde knoop punten (en dus de status van het *cluster*) nu weer gegeven met de status *fout* .

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>De oude load balancer verwijderen en DNS-instellingen bijwerken

Nu kunnen we de resources die zijn gerelateerd aan het oude primaire knooppunt type verwijderen, beginnend met de load balancer en het oude open bare IP-adres. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Vervolgens werkt u de DNS-instellingen van het nieuwe open bare IP-adres bij om de instellingen van het oude open bare IP-knooppunt type te spie gelen.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Controleer de cluster status en probeer het opnieuw

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Ten slotte verwijdert u de status van het knoop punt voor elk van de gerelateerde knoop punten. Als duurzaamheids niveau van de oude schaalset zilver of goud is, wordt dit automatisch uitgevoerd.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Het primaire knooppunt type van het cluster is nu bijgewerkt. Controleer of de geïmplementeerde toepassingen goed functioneren en of de cluster status OK is.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een knooppunt type aan een cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in-of uitschalen](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met behulp van de Fluent Azure Compute SDK.
* [Een zelfstandige cluster in-of uitschalen](service-fabric-cluster-windows-server-add-remove-nodes.md).

