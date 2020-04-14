---
title: Veelgestelde vragen over schaalsets voor virtuele Azure-machines
description: Krijg antwoorden op de meest gestelde vragen over virtuele machineschaalsets in Azure.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mimckitt
ms.openlocfilehash: 1dbc08e01b9a36b1bc80ee6b81ceb2d92ff831cc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273712"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Veelgestelde vragen over schaalsets voor virtuele Azure-machines

Antwoorden op veelgestelde vragen over virtuele machineschaalsets in Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Top veelgestelde vragen voor schaalsets

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hoeveel virtuele machines kan een schaalset bevatten?

Een schaalset kan 0 tot 1000 VM's hebben op basis van platformafbeeldingen of 0 tot 600 VM's op basis van aangepaste afbeeldingen.

### <a name="are-data-disks-supported-within-scale-sets"></a>Worden gegevensschijven binnen schaalsets ondersteund?

Ja. Een schaalset kan een configuratie voor gekoppelde gegevensschijven definiëren die op alle VM's in de set wordt toegepast. Zie [Schaalsets en gekoppelde gegevensschijven in Azure](virtual-machine-scale-sets-attached-disks.md) voor meer informatie. Andere opties voor het opslaan van gegevens zijn:

* Azure-bestanden (gedeelde SMB-stations)
* Station van het besturingssysteem
* Tijdelijk station (lokaal, niet ondersteund met Azure Storage)
* Azure-gegevensservice (bijvoorbeeld Azure-tabellen, Azure-blobs)
* Externe gegevensservice (bijvoorbeeld externe database)

### <a name="which-azure-regions-support-scale-sets"></a>Welke Azure-regio's ondersteunen schaalsets?

Alle regio's ondersteunen schaalsets.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Hoe maak ik een schaalset met behulp van een aangepaste installatiekopie?

Maak en leg een VM-afbeelding vast en gebruik die vervolgens als bron voor uw schaalset. Voor een zelfstudie over het maken en gebruiken van een aangepaste VM-afbeelding, u de [Azure CLI](tutorial-use-custom-image-cli.md) of [Azure PowerShell](tutorial-use-custom-image-powershell.md) gebruiken

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Als ik de capaciteit van mijn schaalset verlaag van 20 naar 15, welke virtuele machines worden er dan verwijderd?

Virtuele machines worden gelijkmatig verwijderd uit updatedomeinen en foutdomeinen van de schaalset om de beschikbaarheid te maximaliseren. VM's met de hoogste id's worden het eerst verwijderd.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Wat gebeurt er als ik de capaciteit verhoog van 15 naar 18?

Als u de capaciteit verhoogt naar 18, worden er 3 nieuwe virtuele machines gemaakt. De id van elk VM-exemplaar wordt oplopend gegenereerd vanaf de vorige hoogste waarde (bijvoorbeeld 20, 21, 22). VM's worden verdeeld over foutdomeinen en updatedomeinen.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kan ik een uitvoeringsvolgorde toepassen wanneer ik meerdere extensies in een schaalset gebruik?

Ja, u de volgorde van de [schaalsetextensie](virtual-machine-scale-sets-extension-sequencing.md)gebruiken.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Maken schaalsets gebruik van beschikbaarheidssets van Azure?

Een regionale (niet-zonale) schaalset maakt gebruik van *plaatsingsgroepen,* die fungeren als een impliciete beschikbaarheidsset met vijf foutdomeinen en vijf updatedomeinen. Schaalsets van meer dan 100 VM's bestrijken meerdere plaatsingsgroepen. Zie voor meer informatie over de plaatsing van groepen [Werken met grote schaalsets voor virtuele machines](virtual-machine-scale-sets-placement-groups.md). Een beschikbaarheidsset met virtuele machines kan bestaan in hetzelfde virtuele netwerk als een schaalset met virtuele machines. Een veelvoorkomende configuratie is om beheerknooppunt-VM's (waarvoor vaak een unieke configuratie is vereist) in een beschikbaarheidsset te zetten en gegevensknooppunten in de schaalset te zetten.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Werken schaalsets met Azure-beschikbaarheidszones?

Ja. Zie het document [schaalsetzone](./virtual-machine-scale-sets-use-availability-zones.md)voor meer informatie .


## <a name="autoscale"></a>Automatisch schalen

### <a name="what-are-best-practices-for-azure-autoscale"></a>Wat zijn aanbevolen procedures voor Azure Autoscale?

Zie [Aanbevolen procedures voor het automatisch schalen van virtuele machines](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices)voor aanbevolen procedures voor automatische procedures.

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Waar vind ik metrische namen voor automatisch schalen die hostgebaseerde statistieken gebruiken?

Zie Ondersteunde statistieken met Azure Monitor voor metrische namen voor automatisch schalen die hostgebaseerde statistieken [gebruiken.](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/)

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Zijn er voorbeelden van automatisch schalen op basis van een Azure Service Bus-onderwerp en wachtrijlengte?

Ja. Zie [Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)automatisch schalen voor voorbeelden van automatisch schalen op basis van een Azure Service Bus-onderwerp en wachtrijlengte.

Gebruik voor een wachtrij voor servicebus de volgende JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Gebruik voor een opslagwachtrij de volgende JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Vervang voorbeeldwaarden door uw resource Uniform Resource Identifiers (URI's).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Moet ik automatisch schalen met behulp van host-gebaseerde statistieken of een diagnostische extensie?

U een instelling voor automatische schaal maken op een VM om statistieken op hostniveau of statistieken op basis van gastbesturingssysteemen te gebruiken.

Zie [Azure Monitor automatisch schalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics)van algemene statistieken voor een lijst met ondersteunde statistieken.

Zie [Geavanceerde automatische schaalconfiguratie met Resource Manager-sjablonen voor virtuele machineschaalsets voor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)een volledig voorbeeld voor virtuele machineschaalsets.

Het voorbeeld maakt gebruik van de CPU-statistiek op hostniveau en een statistiek voor het aantal berichten.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hoe stel ik waarschuwingsregels in op een virtuele machineschaalset?

U waarschuwingen maken over metrische gegevens voor virtuele machineschaalsets via PowerShell of Azure CLI. Zie [Voorbeelden van Azure Monitor PowerShell quickstart-voorbeelden](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) en Azure [Monitor-snelstartvoorbeelden voor cross-platform.For](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)more information, see Azure Monitor PowerShell quickstart samples and Azure Monitor cross-platform CLI quickstart samples .

De TargetResourceId van de virtuele machineschaalset ziet er als volgt uit:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

U een VM-prestatiemeter kiezen als statistiek om een waarschuwing voor in te stellen. Zie [Statistieken van Gastbesturingssysteem voor Windows VM's](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) en [gastbesturingssysteemvoor Linux VM's voor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) meer informatie in het algemene statistiekenartikel azure monitor automatisch [schalen.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hoe stel ik automatisch schalen in op een virtuele machineschaal die is ingesteld met PowerShell?

Als u automatisch schalen wilt instellen op een virtuele machineschaal die is ingesteld met PowerShell, raadpleegt u [automatisch een grote schaalset voor virtuele machines.](tutorial-autoscale-powershell.md) U ook automatisch schalen configureren met de Azure CLI- en [Azure-sjablonen](tutorial-autoscale-cli.md) [Azure templates](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Als ik een VM heb gestopt (deallocated), is die VM dan gestart als onderdeel van een autoscale-bewerking?

Nee. Als regels voor automatische waarden extra VM-exemplaren vereisen als onderdeel van een schaalset, wordt een nieuwe VM-instantie gemaakt. VM-exemplaren die zijn gestopt (deallocated) worden niet gestart als onderdeel van een automatisch schaalgebeurtenis. Deze gestopte (deallocated) VM's kunnen echter worden verwijderd als onderdeel van een gebeurtenis op automatische schaal die wordt geschaald in het aantal exemplaren, op dezelfde manier dat een VM-instantie kan worden verwijderd op basis van de volgorde van vm-instantie-id.



## <a name="certificates"></a>Certificaten

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hoe kan ik een certificaat veilig verzenden naar de VM?

Als u een certificaat veilig naar de VM wilt verzenden, u een klantencertificaat rechtstreeks in een Windows-certificaatarchief installeren vanuit de sleutelkluis van de klant.

Gebruik de volgende JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

De code ondersteunt Windows en Linux.

Zie [Een virtuele machineschaalset maken of bijwerken](https://msdn.microsoft.com/library/mt589035.aspx)voor meer informatie.


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hoe gebruik ik zelfondertekende certificaten die zijn ingericht voor Azure Service Fabric-clusters?
Lees voor het laatste voorbeeld de volgende azure CLI-instructie in azure shell, lees De voorbeelddocumentatie van de SERVICE Fabrics CLI-module, die wordt afgedrukt op stdout:

```azurecli
az sf cluster create -h
```

Zelfondertekende certificaten kunnen niet worden gebruikt voor gedistribueerde vertrouwensrelaties die worden verstrekt door een certificeringsinstantie en mogen niet worden gebruikt voor een Service Fabric-cluster dat bedoeld is om bedrijfsproductieoplossingen te hosten; Voor aanvullende richtlijnen voor servicefabricbeveiliging, bekijk [de best practices voor Azure Service Fabric Fabric Security](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) en [beveiligingsscenario's voor servicefabric.for](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)additional Service Fabric Security guidance, review Azure Service Fabric Security Best Practices and Service Fabric cluster security scenarios .

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan ik een SSH-sleutelpaar opgeven dat moet worden gebruikt voor SSH-verificatie met een Linux-sjabloon voor virtuele machineschaal vanuit een Resource Manager-sjabloon?

Ja. De REST API voor **osProfile** is vergelijkbaar met de standaard VM REST API.

**OsProfile** opnemen in uw sjabloon:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Dit JSON-blok wordt gebruikt in [deze Azure quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Zie [Een virtuele machineschaalset maken of bijwerken](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration)voor meer informatie.

### <a name="how-do-i-remove-deprecated-certificates"></a>Hoe verwijder ik afgeschafte certificaten?

Als u afgeschafte certificaten wilt verwijderen, verwijdert u het oude certificaat uit de lijst met kluiscertificaten. Laat alle certificaten die u op uw computer wilt blijven in de lijst achter. Hierdoor wordt het certificaat niet uit al uw VM's verwijderd. Het voegt het certificaat ook niet toe aan nieuwe VM's die zijn gemaakt in de virtuele machineschaalset.

Als u het certificaat uit bestaande VM's wilt verwijderen, gebruikt u een aangepaste scriptextensie om de certificaten handmatig uit uw certificaatarchief te verwijderen.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hoe injecteer ik een bestaande SSH public key in de virtuele machine schaal set SSH laag tijdens de inrichting?

Als u de VM's alleen een openbare SSH-sleutel verstrekt, hoeft u de openbare sleutels niet in Key Vault te plaatsen. Openbare sleutels zijn niet geheim.

U SSH-openbare sleutels in platte tekst verstrekken wanneer u een Linux-vm maakt:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguratie-elementnaam | Vereist | Type | Beschrijving
--- | --- | --- | ---
Ssh | Nee | Verzameling | Hiermee geeft u de SSH-sleutelconfiguratie voor een Linux-besturingssysteem op
path | Ja | Tekenreeks | Hiermee geeft u het Linux-bestandspad op waar de SSH-sleutels of -certificaten zich moeten bevinden
keyData | Ja | Tekenreeks | Hiermee geeft u een door basis64-gecodeerde SSH-openbare sleutel op

Zie bijvoorbeeld [de 101-vm-sshkey GitHub quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Wanneer ik `Update-AzVmss` na het toevoegen van meer dan één certificaat uit dezelfde sleutelkluis werk, zie ik het volgende bericht:

>Update-AzVmss: Lijstgeheim bevat herhaalde exemplaren\<van /subscriptions/ my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, die niet is toegestaan.

Dit kan gebeuren als u dezelfde kluis opnieuw probeert toe te voegen in plaats van een nieuw kluiscertificaat te gebruiken voor de bestaande bronkluis. De `Add-AzVmssSecret` opdracht werkt niet goed als u extra geheimen toevoegt.

Als u meer geheimen uit dezelfde sleutelkluis wilt toevoegen, werkt u de lijst $vmss.properties.osProfile.secrets[0].vaultCertificates bij.

Zie [Een virtuele machineset maken of bijwerken voor](https://msdn.microsoft.com/library/azure/mt589035.aspx)de verwachte invoerstructuur .

Zoek het geheim in het object met de virtuele machineschaalset dat zich in de sleutelkluis bevindt. Voeg vervolgens uw certificaatverwijzing (de URL en de geheime winkelnaam) toe aan de lijst die aan de kluis is gekoppeld.

> [!NOTE]
> Momenteel u certificaten uit VM's niet verwijderen met behulp van de API voor de virtuele machineschaalset.
>

Nieuwe VM's hebben het oude certificaat niet. Vm's die het certificaat hebben en die al zijn geïmplementeerd, hebben echter het oude certificaat.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan ik certificaten naar de virtuele machineschaal instellen zonder het wachtwoord op te geven, wanneer het certificaat zich in de geheime winkel bevindt?

U hoeft geen harde code wachtwoorden in scripts. U wachtwoorden dynamisch ophalen met de machtigingen die u gebruikt om het implementatiescript uit te voeren. Als u een script hebt dat een certificaat uit de `get certificate` geheime winkelsleutelkluis verplaatst, wordt in de opdracht geheime winkel ook het wachtwoord van het PFX-bestand uitgevoerd.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hoe werkt de eigenschap Secrets van virtualMachineProfile.osProfile voor een virtuele machineschaalset? Waarom heb ik de sourceVault-waarde nodig wanneer ik de absolute URI voor een certificaat moet opgeven met behulp van de eigenschap certificateUrl?

Een Certificaatreferentie voor Windows Remote Management (Windows Remote Management) moet aanwezig zijn in de eigenschap Secrets van het OS-profiel.

Het doel van het aangeven van de bronkluis is het afdwingen van ACL-beleidsregels (Access Control List) die in het Azure Cloud Service-model van een gebruiker bestaan. Als de bronkluis niet is opgegeven, kunnen gebruikers die geen machtigingen hebben om geheimen te implementeren of toegang te krijgen tot een sleutelkluis via een CRP (Compute Resource Provider). ACL's bestaan zelfs voor resources die niet bestaan.

Als u een onjuiste bronkluis-id opgeeft, maar een geldige URL van de sleutelkluis, wordt een fout gemeld wanneer u de bewerking peilt.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Als ik geheimen toe te voegen aan een bestaande virtuele machine schaal set, zijn de geheimen geïnjecteerd in bestaande VM's, of alleen in nieuwe?

Certificaten worden toegevoegd aan al uw VM's, zelfs reeds bestaande. Als de eigenschap upgrade van uw virtuele machineschaalset is ingesteld op **handmatig,** wordt het certificaat aan de VM toegevoegd wanneer u een handmatige update uitvoert op de vm.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Waar kan ik certificaten voor Linux VM's?

Zie Certificaten implementeren voor [VM's implementeren vanuit een door de klant beheerde sleutelkluis](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/)voor meer informatie over het implementeren van certificaten voor Linux-VM's.

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hoe voeg ik een nieuw kluiscertificaat toe aan een nieuw certificaatobject?

Zie het volgende PowerShell-voorbeeld als u een kluiscertificaat wilt toevoegen aan een bestaand geheim. Gebruik slechts één geheim object.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Wat gebeurt er met certificaten als u een virtuele machine opnieuw afbeelding?

Als u een virtuele machine opnieuw afbeelding geeft, worden certificaten verwijderd. Reimaging verwijdert de hele osschijf.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Wat gebeurt er als u een certificaat uit de sleutelkluis verwijdert?

Als het geheim uit de sleutelkluis wordt `stop deallocate` verwijderd en u voor al uw VM's loopt en deze vervolgens opnieuw start, komt u een fout tegen. De fout treedt op omdat het CRP de geheimen uit de sleutelkluis moet halen, maar dat kan niet. In dit scenario u de certificaten verwijderen uit het model van de virtuele machineschaalset.

De CRP-component blijft niet bestaan klantgeheimen. Als u `stop deallocate` voor alle VM's in de virtuele machineschaalset uitvoert, wordt de cache verwijderd. In dit scenario worden geheimen opgehaald uit de sleutelkluis.

U ondervindt dit probleem niet bij het uitschalen omdat er een in de cache opgeslagen kopie van het geheim in Azure Service Fabric is (in het tenantmodel met één stof).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Waarom moet ik de certificaatversie opgeven wanneer ik Key Vault gebruik?

Het doel van de Key Vault-vereiste om de certificaatversie op te geven, is om de gebruiker duidelijk te maken welk certificaat op hun VM's wordt geïmplementeerd.

Als u een vm maakt en vervolgens uw geheim in de sleutelkluis bijwerkt, wordt het nieuwe certificaat niet gedownload naar uw VM's. Maar uw VM's lijken te verwijzen, en nieuwe VM's krijgen het nieuwe geheim. Om dit te voorkomen, moet u verwijzen naar een geheime versie.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mijn team werkt met verschillende certificaten die aan ons worden uitgedeeld als .cer public keys. Wat is de aanbevolen aanpak voor het implementeren van deze certificaten op een virtuele machineschaalset?

Als u .cer-openbare sleutels wilt implementeren voor een virtuele machineschaalset, u een .pfx-bestand genereren dat alleen .cer-bestanden bevat. Gebruik hiervoor `X509ContentType = Pfx`. Laad het .cer-bestand bijvoorbeeld als een x509Certificate2-object in C# of PowerShell en roep de methode aan.

Zie [X509Certificate.Export Method (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx))voor meer informatie.

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Hoe geef ik certificaten door als base64-tekenreeksen?

Als u het doorgeven van een certificaat wilt emuleren als een base64-tekenreeks, u de nieuwste versie-URL in een Resource Manager-sjabloon extraheren. Neem de volgende JSON-eigenschap op in de sjabloon ResourceManager:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Moet ik certificaten in JSON-objecten in sleutelkluizen verpakken?

In virtuele machineschaalsets en VM's moeten certificaten worden verpakt in JSON-objecten.

We ondersteunen ook de inhoudstype applicatie/x-pkcs12.

We ondersteunen momenteel geen .cer-bestanden. Als u .cer-bestanden wilt gebruiken, exporteert u ze naar .pfx-containers.



## <a name="compliance-and-security"></a>Compliance en beveiliging

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Zijn virtuele machineschaalsets PCI-compatibel?

Schaalsets van virtuele machines zijn een thin-API-laag bovenop de CRP. Beide componenten maken deel uit van het computerplatform in de boomstructuur van de Azure-service.

Vanuit het perspectief van naleving zijn schaalsets van virtuele machines een fundamenteel onderdeel van het Azure-computerplatform. Ze delen een team, hulpprogramma's, processen, implementatiemethode, beveiligingsmechanismen, JIT-compilatie (just-in-time), bewaking, waarschuwingen enz. met de CRP zelf. Schaalsets van virtuele machines zijn compatibel met PCI (Payment Card Industry) omdat de CRP deel uitmaakt van het huidige PCI DSS-attestation (Data Security Standard).

Zie [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI) voor meer informatie.

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Werken [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/msi-overview) met virtuele machineschaalsets?

Ja. U enkele voorbeeld-MSI-sjablonen zien in Azure Quickstart-sjablonen voor [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) en [Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)

## <a name="deleting"></a>Verwijderen 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Worden de vergrendelingen die ik op zijn plaats heb ingesteld op exemplaren met virtuele machineschaal ingesteld, gerespecteerd bij het verwijderen van instanties?

In de Azure Portal u een afzonderlijke instantie of bulkverwijderen verwijderen door meerdere exemplaren te selecteren. Als u probeert een enkele instantie met een vergrendeling te verwijderen, wordt het slot gerespecteerd en u de instantie niet verwijderen. Als u echter meerdere exemplaren selecteert en een van deze exemplaren een vergrendeling heeft, wordt de vergrendeling(en) niet gerespecteerd en worden alle geselecteerde exemplaren verwijderd. 
 
In Azure CLI hebt u alleen de mogelijkheid om een afzonderlijke instantie te verwijderen. Als u probeert een enkele instantie met een vergrendeling te verwijderen, wordt het slot gerespecteerd en u die instantie niet verwijderen. 

## <a name="extensions"></a>Extensies

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hoe verwijder ik een extensie voor virtuele machineschaalsets?

Als u een extensie voor een virtuele machineschaalset wilt verwijderen, gebruikt u het volgende PowerShell-voorbeeld:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

U de waarde `$vmss`extensieNaam vinden in.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Is er een voorbeeld van een sjabloon voor virtuele machineschalen die integreert met Azure Monitor-logboeken?

Zie het tweede voorbeeld in [Een Azure Service Fabric-cluster implementeren en bewaking inschakelen met Azure Monitor-logboeken](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)voor een sjabloon voor virtuele machineschalen die zijn geïntegreerd met Azure Monitor-logboeken.

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hoe voeg ik een extensie toe aan alle VM's in mijn virtuele machineschaalset?

Als het updatebeleid is ingesteld op **automatisch,** wordt de sjabloon opnieuw geïmplementeerd met de nieuwe extensie-eigenschappen en worden alle VM's bijgewerkt.

Als het updatebeleid is ingesteld op **handmatig,** werkt u eerst de extensie bij en werkt u vervolgens alle exemplaren in uw VM's handmatig bij.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Als de extensies die zijn gekoppeld aan een bestaande virtuele machineschaalset worden bijgewerkt, worden bestaande VM's dan beïnvloed?

Als de extensiedefinitie in het model van de virtuele machineschaalset wordt bijgewerkt en de eigenschap upgradeBeleid is ingesteld op **automatisch,** worden de VM's bijgewerkt. Als de eigenschap upgradeBeleid is ingesteld op **handmatig,** worden extensies gemarkeerd als niet overeenkomend met het model.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Worden extensies opnieuw uitgevoerd wanneer een bestaande machine is hersteld of opnieuw wordt weergegeven?

Als een bestaande VM is hersteld, wordt deze weergegeven als een reboot en worden de extensies niet opnieuw uitgevoerd. Als een virtuele machine opnieuw wordt weergegeven, wordt het besturingssysteem vergelijkbaar met de bronafbeelding. Elke specialisatie van het nieuwste model, zoals extensies, wordt opnieuw uitgevoerd.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hoe kan ik lid worden van een virtuele machineschaal die is ingesteld op een Active Directory-domein?

Als u wilt deelnemen aan een virtuele machineschaal die is ingesteld op een AD-domein (Active Directory), u een extensie definiëren.

Als u een extensie wilt definiëren, gebruikt u de eigenschap JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Mijn virtuele machine schaal set extensie probeert om iets dat een reboot vereist te installeren.

Als uw extensie voor de grootte van uw virtuele machine probeert iets te installeren waarvoor een herstart vereist is, u de azure automation desired state configuration (Automation DSC)-extensie gebruiken. Als het besturingssysteem Windows Server 2012 R2 is, trekt Azure de Installatie van Windows Management Framework (WMF) 5.0 in, wordt opnieuw opgestart en gaat vervolgens verder met de configuratie.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hoe schakel ik antimalware in in mijn virtuele machineschaalset?

Als u antimalware wilt inschakelen op uw virtuele machineschaalset, gebruikt u het volgende PowerShell-voorbeeld:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hoe voer ik een aangepast script uit dat wordt gehost in een privéopslagaccount?

Als u een aangepast script wilt uitvoeren dat wordt gehost in een privéopslagaccount, stelt u beveiligde instellingen in met de opslagaccountsleutel en -naam. Zie [Aangepaste scriptextensie](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings)voor meer informatie .

## <a name="passwords"></a>Wachtwoorden

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hoe kan ik het wachtwoord voor VM's opnieuw instellen in mijn virtuele machineschaalset?

Er zijn twee belangrijke manieren om het wachtwoord voor VM's in schaalsets te wijzigen.

- Wijzig het model voor de virtuele machineschaalset rechtstreeks. Beschikbaar met API 2017-12-01 en hoger.

    Werk de beheerdersreferenties rechtstreeks in het model van de schaalset bij (bijvoorbeeld met de Azure Resource Explorer, PowerShell of CLI). Zodra de schaalset is bijgewerkt, hebben alle nieuwe VM's de nieuwe referenties. Bestaande VM's hebben alleen de nieuwe referenties als ze opnieuw worden weergegeven.

- Stel het wachtwoord opnieuw in met de VM-toegangsextensies.

    Gebruik het volgende PowerShell-voorbeeld:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Netwerken

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Is het mogelijk om een Network Security Group (NSG) toe te wijzen aan een schaalset, zodat deze van toepassing is op alle VM NIC's in de set?

Ja. Een netwerkbeveiligingsgroep kan rechtstreeks worden toegepast op een schaal die is ingesteld door ernaar te verwijzen in het gedeelte networkInterfaceConfigurations van het netwerkprofiel. Voorbeeld:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hoe doe ik een VIP-swap voor virtuele machineschaalsets in hetzelfde abonnement en dezelfde regio?

Als u twee virtuele machineschaalsets hebt met azure load balancer front-ends en deze zich in hetzelfde abonnement en dezelfde regio bevinden, u de openbare IP-adressen van elke set detoewijzing en aan de andere toewijzen. Zie [bijvoorbeeld VIP Swap: Blauwgroene implementatie in Azure Resource Manager.](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) Dit impliceert echter een vertraging als de middelen worden deallocated / toegewezen op het netwerk niveau. Een snellere optie is het gebruik van Azure Application Gateway met twee backendpools en een routeringsregel. U uw toepassing ook hosten met [azure-app-service,](https://azure.microsoft.com/services/app-service/) die ondersteuning biedt voor snel schakelen tussen faserings- en productiesleuven.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hoe geef ik een reeks privé-IP-adressen op die moeten worden gebruikt voor statische toewijzing van privé-IP-adressen?

IP-adressen worden geselecteerd uit een subnet dat u opgeeft.

De toewijzingsmethode van ip-adressen met virtuele machineschaal is altijd 'dynamisch', maar dat betekent niet dat deze IP-adressen kunnen veranderen. In dit geval betekent 'dynamisch' alleen dat u het IP-adres niet opgeeft in een PUT-aanvraag. Geef de statische set op met behulp van het subnet.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hoe implementeer ik een virtuele machineschaal die is ingesteld op een bestaand virtueel Azure-netwerk?

Zie Een virtuele machineschaal implementeren voor een bestaand virtueel Azure-netwerk als u een virtuele machineschaal wilt implementeren [in een bestaand virtueel netwerk.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan ik schaalsets gebruiken met Accelerated Networking?

Ja. Als u versnelde netwerken wilt gebruiken, stelt u AcceleratedNetworking in om true te gebruiken in de netwerkinterfaceconfiguratiesinstellingen van uw schaalset. Bijvoorbeeld

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hoe kan ik de DNS-servers configureren die worden gebruikt door een schaalset?

Als u een virtuele machineschaalset wilt maken met een aangepaste DNS-configuratie, voegt u een JSON-pakket dnsSettings toe aan de sectie netwerkinterfaceconfiguraties van de schaalset. Voorbeeld:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hoe kan ik een schaalset configureren om een openbaar IP-adres toe te wijzen aan elke virtuele machine?

Als u een virtuele machineschaalset wilt maken die een openbaar IP-adres aan elke VM toewijst, controleert u of de API-versie van de Microsoft.Compute/virtualMachineScaleSets-bron 2017-03-30 is en voegt u een JSON-pakket voor _openbare ip-adresconfiguratie_ toe aan de sectie schaalsetipConfigurations. Voorbeeld:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kan ik een schaalset configureren die met meerdere toepassingsgateways werkt?

Ja. U de bron-id's voor meerdere backend-adresgroepen van Application Gateway toevoegen aan de lijst _met toepassingsgatewaybackendadrespools_ in het gedeelte _ipConfigurations_ van uw schaalsetnetwerkprofiel.

## <a name="scale"></a>Schalen

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>In welk geval zou ik een virtuele machine schaal set met minder dan twee VM's?

Een reden om een virtuele machine schaal set met minder dan twee VM's te maken zou zijn om de elastische eigenschappen van een virtuele machine schaal set te gebruiken. U bijvoorbeeld een virtuele machineschaalset met nul VM's implementeren om uw infrastructuur te definiëren zonder dat u de gebruikskosten van vm's hoeft te betalen. Wanneer u vervolgens klaar bent om VM's te implementeren, verhoogt u de 'capaciteit' van de virtuele machineschaal die is ingesteld op het aantal productie-instantie.

Een andere reden waarom u een virtuele machineschaalset met minder dan twee VM's maken, is als u minder betrokken bent bij beschikbaarheid dan met het gebruik van een beschikbaarheidsset met afzonderlijke VM's. Virtuele machineschaalsets bieden u een manier om te werken met ongedifferentieerde compute units die fungibel zijn. Deze uniformiteit is een belangrijke differentiator voor virtuele machineschaalsets versus beschikbaarheidssets. Veel stateloze workloads houden geen afzonderlijke eenheden bij. Als de werkbelasting daalt, u omlaag schalen naar één rekeneenheid en vervolgens opschalen naar veel wanneer de werkbelasting toeneemt.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hoe wijzig ik het aantal VM's in een virtuele machineschaalset?

Als u het aantal VM's in een virtuele machineschaal in de Azure-portal wilt wijzigen, klikt u in de sectie Eigenschappen van de virtuele machineschaalset op het blad 'Schalen' en gebruikt u de schuifregelaarbalk.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hoe definieer ik aangepaste waarschuwingen voor wanneer bepaalde drempelwaarden worden bereikt?

U hebt enige flexibiliteit in de manier waarop u waarschuwingen voor bepaalde drempelwaarden aanpakt. U bijvoorbeeld aangepaste webhooks definiëren. Het volgende voorbeeld van webhook is afkomstig van een resourcemanagersjabloon:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Patchen en bewerkingen

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kan ik een schaalset maken in een bestaande resourcegroep?

Ja, u een schaalset maken in een bestaande resourcegroep.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kan ik een schaalset verplaatsen naar een andere resourcegroep?

Ja, u schaalsetresources verplaatsen naar een nieuw abonnement of resourcegroep.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hoe werk ik mijn virtuele machineschaal bij die is ingesteld op een nieuwe afbeelding? Hoe beheer ik patchen?

Zie [Een virtuele machineschaalset](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)upgraden als u uw virtuele machineschaal wilt bijwerken die is ingesteld op een nieuwe afbeelding en de patching wilt beheren.

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan ik de bewerking voor reimage gebruiken om een vm opnieuw in te stellen zonder de afbeelding te wijzigen? (Dat wil zeggen, ik wil een VM resetten naar fabrieksinstellingen in plaats van naar een nieuwe afbeelding.)

Ja, u de bewerking voor reimage gebruiken om een vm opnieuw in te stellen zonder de afbeelding te wijzigen. Als uw virtuele machineschaalset echter verwijst `version = latest`naar een platformafbeelding met , kan `reimage`uw VM bijwerken naar een latere afbeelding van het besturingssysteem wanneer u aanroept .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Is het mogelijk om schaalsets te integreren met Azure Monitor-logboeken?

Ja, u de Azure Monitor-extensie installeren op de vm's van de schaalset. Hier is een Azure CLI-voorbeeld:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

U de vereiste workspaceId en workspaceKey vinden in de Log Analytics-werkruimte van Azure-portal. Klik op de pagina Overzicht op de tegel Instellingen. Klik bovenaan op het tabblad Verbonden bronnen.

> [!NOTE]
> Als uw _upgradebeleid_ voor schaalset is ingesteld op Handmatig, moet u de extensie toepassen op alle VM's in de set door er een upgrade op aan te roepen. In CLI zou dit _az vmss update-instances_zijn.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hoe schakel ik opstartdiagnoses in?

Als u opstartdiagnosewilt inschakelen, maakt u eerst een opslagaccount aan. Plaats vervolgens dit JSON-blok in uw virtuele machineschaalset **virtualMachineProfile**en werk de virtuele machineschaalset bij:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Wanneer een nieuwe virtuele machine wordt gemaakt, worden in de eigenschap InstanceView van de VM de details voor de schermafbeelding weergegeven, enzovoort. Hier volgt een voorbeeld:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Eigenschappen van virtuele machines

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hoe krijg ik eigenschapsinformatie voor elke virtuele machine zonder meerdere gesprekken te voeren? Bijvoorbeeld, hoe zou ik het foutdomein voor elk van de 100 VM's in mijn virtuele machine schaalset krijgen?

Als u eigenschapsinformatie voor elke virtuele machine `ListVMInstanceViews` wilt opvragen `GET` zonder meerdere aanroepen, u bellen door een REST-API uit te voeren op de volgende resource URI:

/abonnementen/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceBekijk&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan ik verschillende extensieargumenten doorgeven aan verschillende VM's in een virtuele machineschaalset?

Nee, u geen verschillende extensieargumenten doorgeven aan verschillende VM's in een virtuele machineschaalset. Extensies kunnen echter werken op basis van de unieke eigenschappen van de VM waarop ze worden uitgevoerd, zoals op de naam van de machine. Extensies kunnen ook metagegevens van instantie opvragen http://169.254.169.254 om meer informatie over de VM te krijgen.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Waarom zijn er hiaten tussen mijn virtuele machine schaal set VM machine namen en VM-iD's? Bijvoorbeeld: 0, 1, 3...

Er zijn hiaten tussen de namen vm-machinenamen van uw virtuele machine en VM-id's omdat de eigenschap **overprovision** van uw virtuele machine is ingesteld op de standaardwaarde van **true.** Als overprovisioning is ingesteld op **true,** worden er meer VM's gemaakt dan gevraagd. Extra VM's worden vervolgens verwijderd. In dit geval krijgt u meer betrouwbaarheid van de implementatie, maar ten koste van aaneengesloten naamgeving en aaneengesloten REGELS voor netwerkadresvertaling (NAT).

U deze eigenschap op **vals instellen.** Voor kleine virtuele machineschaalsets heeft dit geen aanzienlijke invloed op de betrouwbaarheid van de implementatie.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Wat is het verschil tussen het verwijderen van een VM in een virtuele machineschaalset en het deallocatie van de VM? Wanneer moet ik kiezen voor een boven de andere?

Het belangrijkste verschil tussen het verwijderen van een VM in een virtuele `deallocate` machine schaal set en detoewijzing van de VM is dat niet de virtuele harde schijven (VHD's) te verwijderen. Er zijn opslagkosten `stop deallocate`verbonden aan het uitvoeren van . U het ene of het andere gebruiken om een van de volgende redenen:

- U wilt stoppen met het betalen van rekenkosten, maar u wilt de schijfstatus van de VM's behouden.
- U wilt een set VM's sneller starten dan u een virtuele machineschaalset schalen.
  - In verband met dit scenario hebt u mogelijk uw eigen autoscale-engine gemaakt en wilt u een snellere end-to-end-schaal.
- U hebt een virtuele machineschaalset die ongelijk verdeeld is over foutdomeinen of updatedomeinen. Dit kan zijn omdat u VM's selectief hebt verwijderd of omdat VM's zijn verwijderd na overprovisioning. Als `stop deallocate` u `start` wordt uitgevoerd op de virtuele machineschaalset, worden de VM's gelijkmatig verdeeld over foutdomeinen of updatedomeinen.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hoe maak ik een momentopname van een voorbeeld van een virtuele machineschaalset?
Maak een momentopname op basis van een instantie van een virtuele machineschaalset.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Maak een beheerde schijf op basis van de momentopname.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
