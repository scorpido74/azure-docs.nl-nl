---
title: Veelgestelde vragen over schaalsets voor virtuele Azure-machine
description: Krijg antwoorden op veelgestelde vragen over schaal sets voor virtuele machines in Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: manayar
ms.openlocfilehash: 222f26febb7b14c627307295a8cdd68a17694d03
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275903"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Veelgestelde vragen over schaalsets voor virtuele Azure-machine

Krijg antwoorden op veelgestelde vragen over virtuele-machineschaalsets in Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Boven de veelgestelde vragen over schaalsets

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hoeveel virtuele machines kan een schaalset bevatten?

Een schaalset kan 0 tot 1000 virtuele machines op basis van platforminstallatiekopieën, of 0-600 virtuele machines op basis van aangepaste installatiekopieën hebben.

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

Maken en vastleggen van een VM-installatiekopie en vervolgens gebruiken die als bron voor uw schaalset. Voor een zelfstudie over het maken en gebruiken van een aangepaste VM-installatiekopie, kunt u de [Azure CLI](tutorial-use-custom-image-cli.md) of [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Als ik de capaciteit van mijn schaalset verlaag van 20 naar 15, welke virtuele machines worden er dan verwijderd?

Virtuele machines worden gelijkmatig verwijderd uit updatedomeinen en foutdomeinen van de schaalset om de beschikbaarheid te maximaliseren. VM's met de hoogste id's worden het eerst verwijderd.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Wat gebeurt er als ik de capaciteit verhoog van 15 naar 18?

Als u de capaciteit verhoogt naar 18, worden er 3 nieuwe virtuele machines gemaakt. De id van elk VM-exemplaar wordt oplopend gegenereerd vanaf de vorige hoogste waarde (bijvoorbeeld 20, 21, 22). VM's worden verdeeld over foutdomeinen en updatedomeinen.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kan ik een uitvoeringsvolgorde toepassen wanneer ik meerdere extensies in een schaalset gebruik?

Ja, u kunt de extensie voor [uitbrei dingen](virtual-machine-scale-sets-extension-sequencing.md)van de schaalset gebruiken.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Maken schaalsets gebruik van beschikbaarheidssets van Azure?

Regionale (niet-zonegebonden) van de schaalset maakt gebruik van *plaatsingsgroepen*, die dienen als een impliciete met vijf foutdomeinen beschikbaarheidsset en vijf updatedomeinen. Schaalsets met meer dan 100 virtuele machines omvatten meerdere plaatsingsgroepen. Zie voor meer informatie over de plaatsing van groepen [Werken met grote schaalsets voor virtuele machines](virtual-machine-scale-sets-placement-groups.md). Een beschikbaarheidsset met virtuele machines kan bestaan in hetzelfde virtuele netwerk als een schaalset met virtuele machines. Een veelvoorkomende configuratie is om beheerknooppunt-VM's (waarvoor vaak een unieke configuratie is vereist) in een beschikbaarheidsset te zetten en gegevensknooppunten in de schaalset te zetten.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Schalen sets in combinatie met Azure-beschikbaarheidszones?

Ja. Zie voor meer informatie de [Virtual Machine scale sets zone doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatisch schalen

### <a name="what-are-best-practices-for-azure-autoscale"></a>Wat zijn de aanbevolen procedures voor automatisch schalen van Azure?

Zie voor aanbevolen procedures voor automatisch schalen [aanbevolen procedures voor automatisch schalen virtuele machines](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Waar vind ik metrische namen voor automatisch schalen die gebruikmaakt van hostgebaseerde metrische gegevens

Zie voor namen van de metrische gegevens voor automatisch schalen die gebruikmaakt van hostgebaseerde metrische gegevens, [ondersteunde metrische gegevens met Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Zijn er voorbeelden van automatisch schalen op basis van de lengte van een Azure Service Bus-onderwerp en wachtrij?

Ja. Zie voor meer voorbeelden van automatisch schalen op basis van een Azure Service Bus-onderwerp en wachtrij lengte [Azure Monitor autoscaling common metrics](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Voor een Service Bus-wachtrij, gebruikt u de volgende JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Voor een storage-wachtrij, gebruikt u de volgende JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Voorbeelden van waarden vervangen door uw resource Uniform Resource-id's (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Moet ik me voor automatisch schalen met behulp van hostgebaseerde metrische gegevens of een extensie voor diagnostische gegevens?

U kunt een instelling voor automatisch schalen maken op een virtuele machine hostniveau metrische gegevens of metrische gegevens voor gasten op basis van een besturingssysteem te gebruiken.

Zie voor een lijst van ondersteunde metrische gegevens, [Azure Monitor autoscaling common metrics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Zie voor een volledig voorbeeld voor virtuele-machineschaalsets [Geavanceerd automatisch schalen configureren met behulp van Resource Manager-sjablonen voor virtuele-machineschaalsets](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

Het voorbeeld wordt het hostniveau CPU metrische gegevens en een bericht aantal metrische gegevens.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hoe kan ik regels voor waarschuwingen instellen op een virtuele-machineschaalset?

U kunt waarschuwingen maken op de metrische gegevens voor virtuele-machineschaalsets via PowerShell of Azure CLI. Zie voor meer informatie, [voorbeelden van Azure Monitor PowerShell-snelstartgids](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) en [Quick Start-voorbeelden van Azure Monitor platformoverschrijdende CLI](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

De TargetResourceId van de virtuele-machineschaalset ziet er als volgt:

/Subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.COMPUTE/virtualMachineScaleSets/yourvmssname

U kunt een prestatiemeteritem VM als de metrische gegevens om in te stellen van een waarschuwing voor. Zie voor meer informatie, [Guest OS metrische gegevens voor Windows-VM's op basis van Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) en [Guest OS metrische gegevens voor virtuele Linux-machines](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) in de [Azure Monitor autoscaling common metrics](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)artikel.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hoe stel ik automatisch schalen in een virtuele-machineschaalset met behulp van PowerShell

Als u wilt instellen voor automatisch schalen op een virtuele-machineschaalset met behulp van PowerShell, Zie [automatisch schalen van een virtuele-machineschaalset](tutorial-autoscale-powershell.md). U kunt ook configureren voor automatisch schalen met de [Azure CLI](tutorial-autoscale-cli.md) en [Azure-sjablonen](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Als ik gestopt heb (toewijzing opgeheven) een virtuele machine is die virtuele machine als onderdeel van een bewerking voor automatisch schalen is gestart?

Nee. Als regels voor automatisch schalen extra exemplaren van de virtuele machine als onderdeel van een schaalset vereist, wordt een nieuwe VM-exemplaar wordt gemaakt. VM-exemplaren die zijn gestopt (toewijzing opgeheven) worden niet gestart als onderdeel van een gebeurtenis voor automatisch schalen. Maar kunnen deze gestopt (toewijzing opgeheven) virtuele machines worden verwijderd als onderdeel van een gebeurtenis voor automatisch schalen die worden geschaald in het aantal exemplaren, dezelfde manier dat een VM-exemplaar kan worden verwijderd op basis van orde van grootte VM-exemplaar-id.



## <a name="certificates"></a>Certificaten

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hoe ik een certificaat met de virtuele machine veilig verzendt?

Als u wilt een certificaat met de virtuele machine veilig te verzenden, kunt u een certificaat van de klant rechtstreeks in een Windows-certificaatarchief van de sleutelkluis van de klant te installeren.

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

De code biedt ondersteuning voor Windows en Linux.

Zie voor meer informatie, [maken of bijwerken een virtuele-machineschaalset](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hoe kan ik zelfondertekende certificaten gebruiken die zijn ingericht voor Azure Service Fabric-clusters?
Voor het meest recente voor beeld gebruikt u de volgende Azure CLI-instructie in azure shell, lees de voor beeld van een CLI-module voor service fabrics, die wordt afgedrukt op stdout:

```bash
az sf cluster create -h
```

Zelfondertekende certificaten kunnen niet worden gebruikt voor gedistribueerde vertrouwens relaties die worden verstrekt door een certificerings instantie en mogen niet worden gebruikt voor een Service Fabric cluster dat is bedoeld voor het hosten van bedrijfs productie oplossingen. Raadpleeg de [Aanbevolen procedures voor Azure service Fabric Security](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) en [service Fabric scenario's voor cluster beveiliging](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)voor aanvullende service Fabric beveiligings richtlijnen.

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan ik een SSH-sleutelpaar gebruiken voor SSH-verificatie met een Linux VM-schaalset van Resource Manager-sjabloon opgeven?

Ja. De REST-API voor **osProfile** is vergelijkbaar met de standard VM REST-API.

Opnemen **osProfile** in uw sjabloon:

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

Dit JSON-blok wordt gebruikt in [deze Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)start-sjabloon.

Zie voor meer informatie, [maken of bijwerken een virtuele-machineschaalset](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Hoe verwijder ik afgeschaft certificaten?

Als u wilt verwijderen afgeschaft certificaten, het oude certificaat uit de lijst van de certificaten kluis te verwijderen. Laat u alle certificaten die u wilt blijven op uw computer in de lijst. Het certificaat wordt niet verwijderd van uw virtuele machines. Deze ook heeft niet het certificaat toevoegen aan nieuwe virtuele machines die zijn gemaakt in de virtuele-machineschaalset.

Als u het certificaat van bestaande Vm's wilt verwijderen, gebruikt u een aangepaste script extensie om de certificaten hand matig uit het certificaat archief te verwijderen.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hoe ik een bestaande openbare SSH-sleutel invoeren in de virtual machine scale set SSH laag tijdens het inrichten?

Als u de virtuele machines alleen met een openbare SSH-sleutel opgeeft, moet u niet om de openbare sleutels in Key Vault. Openbare sleutels zijn niet geheim.

Wanneer u een Linux-VM maakt, kunt u openbare SSH-sleutels als tekst zonder opmaak opgeven:

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

de naam van de linuxConfiguration-element | Verplicht | Type | Beschrijving
--- | --- | --- | ---
SSH | Nee | Verzameling | Hiermee geeft u de SSH-sleutel-configuratie voor een Linux-besturingssysteem
Pad | Ja | Tekenreeks | Hiermee geeft u de Linux-bestandspad waarin de SSH-sleutels of het certificaat moet zich bevinden
keyData | Ja | Tekenreeks | Hiermee geeft u een met base64 gecodeerde openbare SSH-sleutel

Zie voor een voorbeeld [101-vm-SSH-sleutelbestand GitHub quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Wanneer ik uitvoeren `Update-AzVmss` na het toevoegen van meer dan één certificaat vanuit de dezelfde key vault, zie ik het volgende bericht:

>Update-AzVmss: List Secret bevat herhaalde exemplaren van/Subscriptions/\<mijn abonnement-id >/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, die niet is toegestaan.

Dit kan gebeuren als u probeert toe te voegen opnieuw de dezelfde kluis in plaats van een nieuwe kluis-certificaat voor de bestaande bronkluis. De `Add-AzVmssSecret` opdracht werkt niet goed als u extra geheimen toevoegt.

Als u wilt meer geheimen toevoegen vanuit de dezelfde key vault, moet u de $vmss.properties.osProfile.secrets[0].vaultCertificates-lijst bijwerken.

Zie voor de verwachte invoerstructuur [maken of bijwerken een virtuele machine instellen](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Het geheim niet vinden in de virtuele machine schaalsetobject die zich in de key vault. Vervolgens voegt u uw certificaatverwijzing (de URL en de naam van het geheim) aan de lijst die is gekoppeld aan de kluis.

> [!NOTE]
> U verwijderen niet op dit moment certificaten van virtuele machines met behulp van de virtuele machine scale set API.
>

Nieuwe VM's geen het oude certificaat. Virtuele machines waarvoor het certificaat en die al zijn geïmplementeerd, hebben echter wordt het oude certificaat.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan ik certificaten push naar de virtuele-machineschaalset zonder op te geven van het wachtwoord, wanneer het certificaat in het archief met geheime is?

U hoeft niet te programmeren wachtwoorden in scripts. U kunt dynamisch ophalen van wachtwoorden met de machtigingen die u gebruikt om uit te voeren van het script voor implementatie. Als u een script dat wordt verplaatst van een certificaat uit de store geheime sleutel hebt kluis, de geheime store `get certificate` opdracht levert ook het wachtwoord van het pfx-bestand.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hoe de eigenschap geheimen van virtualMachineProfile.osProfile voor een virtuele-machineschaalset werk instellen? Waarom moet ik de waarde sourceVault wanneer ik de absolute URI zijn die voor een certificaat opgeven via de eigenschap certificateUrl heb?

Een verwijzing van Windows Remote Management (WinRM)-certificaat moet aanwezig zijn in de eigenschap geheimen van de OS-profiel.

Het doel van die wijzen op de bronkluis is om af te dwingen toegangsbeheerlijst (ACL) toegangscontrolebeleid die aanwezig zijn in Azure Cloud Service-model van een gebruiker. Als de bronkluis is niet opgegeven, zou gebruikers die geen machtigingen om te implementeren of toegang tot geheimen tot een key vault kunnen via een Compute Resource Provider (CRP). ACL's bestaan, zelfs voor resources die niet bestaan.

Als u een onjuiste bron kluis-ID, maar een geldige sleutelkluis-URL opgeeft, wordt er een fout gerapporteerd bij het controleren van de bewerking.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Als ik geheimen toevoegen aan een bestaande worden de virtuele-machineschaalset ingesteld, zijn de geheimen die zijn toegevoegd aan bestaande virtuele machines, of alleen nieuwe?

Certificaten worden toegevoegd aan alle virtuele machines, zelfs vooraf bestaande toepassingsgroepen. Als uw virtuele-upgradePolicy-eigenschap machineschaalset is ingesteld op **handmatige**, het certificaat wordt toegevoegd aan de virtuele machine wanneer u een handmatige update op de virtuele machine uitvoeren.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Waar ik certificaten moet plaatsen voor Linux-VM's?

Zie voor meer informatie over het implementeren van certificaten voor Linux-VM's, [certificaten aan virtuele machines implementeren vanaf een door de klant beheerde key vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hoe voeg ik een nieuw certificaat voor de kluis naar een nieuw certificaatobject?

Als u wilt een kluis-certificaat toevoegen aan een bestaand geheim, Zie de volgende PowerShell-voorbeeld. Gebruik slechts één geheime-object.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Wat gebeurt er met certificaten als u een installatiekopie van een virtuele machine terugzetten?

Als u de installatiekopie van een virtuele machine terugzetten, zijn certificaten verwijderd. Installatiekopie terugzetten schijf verwijdert het volledige besturingssysteem.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Wat gebeurt er als u een certificaat uit de key vault verwijderen?

Als het geheim uit key vault wordt verwijderd en u voert `stop deallocate` voor uw VM's en start deze opnieuw, wordt er een fout optreedt. De fout treedt op omdat de CRP hoeft op te halen van de geheimen van de key vault, maar deze niet. In dit scenario kunt u de certificaten verwijderen uit het model van virtual machine scale set.

De CRP-component is niet persistent geheimen van de klant. Als u `stop deallocate` voor alle virtuele machines in de virtuele-machineschaalset, de cache wordt verwijderd. In dit scenario worden geheimen opgehaald uit de key vault.

U kunt dit probleem niet tegenkomen bij het uitschalen omdat er een kopie van het geheim in Azure Service Fabric (in het model met één fabric tenant).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Waarom heb ik de certificaatversie opgeven wanneer ik Key Vault gebruiken?

Het doel van de vereiste voor Key Vault om op te geven van de certificaatversie moet maken voor de gebruiker duidelijk welk certificaat wordt geïmplementeerd op hun VM's.

Als u een virtuele machine maken en werk vervolgens uw geheim in de key vault, wordt het nieuwe certificaat wordt niet gedownload naar uw virtuele machines. Maar uw virtuele machines worden weergegeven om te verwijzen naar deze en nieuwe VM's het nieuwe geheim ophalen. Om dit te voorkomen, moet u verwijzen naar een geheime versie.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mijn team werkt met verschillende certificaten die aan ons worden gedistribueerd als cer openbare sleutels. Wat is de aanbevolen aanpak voor het implementeren van deze certificaten op een virtuele-machineschaalset ingesteld?

CER implementeren openbare sleutels op een virtuele-machineschaalset is ingesteld, kunt u een .pfx-bestand met alleen cer-bestanden genereren. U doet dit door gebruik `X509ContentType = Pfx`. Bijvoorbeeld: het cer-bestand laden als een object x509Certificate2 in C# of PowerShell en vervolgens de methode aanroepen.

Zie voor meer informatie, [X509Certificate.Export methode (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Certificaten Hoe kan ik door geven als base64-teken reeksen?

Om te emuleren doorgeven in een certificaat als een met base64-tekenreeks, kunt u de meest recente versies URL in Resource Manager-sjabloon extraheren. De volgende JSON-eigenschap in het Resource Manager-sjabloon opnemen:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Heb ik het inpakken van certificaten in JSON-objecten in sleutelkluizen?

In schaalsets voor virtuele machines en virtuele machines, moeten de certificaten worden verpakt in JSON-objecten.

We ondersteunen ook het inhoudstype application/x-pkcs12.

We ondersteunen momenteel geen cer-bestanden. Cer-bestanden wilt gebruiken, door ze te exporteren naar pfx-containers.



## <a name="compliance-and-security"></a>Naleving en beveiliging

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Zijn de virtuele-machineschaalsets PCI-compatibel?

Schaalsets van virtuele machines zijn een thin-API-laag bovenop de CRP. Beide componenten maken deel uit van het computerplatform in de boomstructuur van de Azure-service.

Vanuit het perspectief van naleving zijn schaalsets van virtuele machines een fundamenteel onderdeel van het Azure-computerplatform. Ze delen een team, hulpprogramma's, processen, implementatiemethode, beveiligingsmechanismen, JIT-compilatie (just-in-time), bewaking, waarschuwingen enz. met de CRP zelf. Schaalsets van virtuele machines zijn compatibel met PCI (Payment Card Industry) omdat de CRP deel uitmaakt van het huidige PCI DSS-attestation (Data Security Standard).

Zie [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI) voor meer informatie.

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Biedt [beheerde identiteiten voor een Azure-resources](https://docs.microsoft.com/azure/active-directory/msi-overview) werken met virtuele-machineschaalsets?

Ja. U ziet enkele voor beelden van MSI-sjablonen in azure Quick Start-sjablonen voor [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) en [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Deleting 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Worden de vergren delingen die ik heb ingesteld voor instanties van virtuele-machine schaal sets gerespecteerd bij het verwijderen van instanties?

In azure Portal kunt u een afzonderlijk exemplaar of bulksgewijs verwijderen verwijderen door meerdere exemplaren te selecteren. Als u probeert een enkele instantie te verwijderen die een vergren deling heeft, wordt de vergren deling gerespecteerd en kunt u het exemplaar niet verwijderen. Als u echter meerdere instanties tegelijk selecteert en een van deze instanties een vergren deling heeft, worden de vergren delingen niet geëerbiedigd en worden alle geselecteerde exemplaren verwijderd. 
 
In azure CLI hebt u alleen de mogelijkheid om een afzonderlijk exemplaar te verwijderen. Als u probeert om één exemplaar te verwijderen dat een vergren deling heeft, wordt de vergren deling gerespecteerd en kunt u dat exemplaar niet verwijderen. 

## <a name="extensions"></a>Extensies

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hoe kan ik een extensie van virtuele-machineschaalset verwijderen?

Als u wilt een extensie van virtuele-machineschaalset verwijderen, gebruik de volgende PowerShell-voorbeeld:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

U vindt de waarde extensienaam in `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Is er een voor beeld van een sjabloon voor virtuele-machine schaal sets die kan worden geïntegreerd met Azure Monitor-logboeken?

Voor een voor beeld van een sjabloon voor virtuele-machine schaal sets die kan worden geïntegreerd met Azure Monitor-logboeken, raadpleegt u het tweede voor beeld in [een Azure service Fabric-cluster implementeren en bewaking inschakelen met behulp van Azure monitor logboeken](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hoe voeg ik een uitbreiding op alle VM's in mijn schaalset voor virtuele machine?

Als de updatebeleid is ingesteld op **automatische**, alle virtuele machines opnieuw implementeren van de sjabloon met de nieuwe extensie-eigenschappen bijgewerkt.

Als de updatebeleid is ingesteld op **handmatige**, eerst de extensie bijwerken en alle exemplaren in uw VM's vervolgens handmatig bijwerken.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Als de uitbreidingen die zijn gekoppeld aan een bestaande virtuele-machineschaalset zijn bijgewerkt, zijn de bestaande virtuele machines die worden beïnvloed?

Als de definitie van de extensie in de virtuele-machineschaalset ingesteld model wordt bijgewerkt en de eigenschap upgradePolicy is ingesteld op **automatische**, het bijwerken van de virtuele machines. Als de eigenschap upgradePolicy is ingesteld op **handmatige**, extensies zijn gemarkeerd als niet overeenkomt met het model.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Worden uitbrei dingen opnieuw uitgevoerd wanneer een bestaande machine een service opnieuw heeft hersteld of een installatie kopie heeft hersteld?

Als een bestaande virtuele machine wordt hersteld, wordt deze weer gegeven als opnieuw opstarten en de uitbrei dingen worden niet opnieuw uitgevoerd. Als er een installatie kopie van een virtuele machine wordt uitgevoerd, is het proces vergelijkbaar met het vervangen van het station van het besturings systeem met de bron installatie kopie. Elke specialisatie van het meest recente model, zoals uitbrei dingen, wordt opnieuw uitgevoerd.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hoe ik deelnemen aan een virtuele-machineschaalset aan een Active Directory-domein?

Als u wilt deelnemen aan een virtuele-machineschaalset aan een domein met Active Directory (AD), kunt u een extensie definiëren.

Voor het definiëren van een uitbreiding, gebruikt u de eigenschap JsonADDomainExtension:

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Mijn extensie van virtuele-machineschaalset probeert te installeren die worden opgestart moet.

Als de extensie van virtuele-machineschaalset is probeert te installeren die worden opgestart moet, kunt u de extensie Azure Automation Desired State Configuration (DSC Automation). Als het besturingssysteem Windows Server 2012 R2 is, wordt Azure wordt tijdens de installatie van Windows Management Framework (WMF) 5.0, opnieuw wordt opgestart, en vervolgens verder met de configuratie.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hoe kan ik anti-malware inschakelen in mijn schaalset voor virtuele machine?

Als u wilt inschakelen anti-malware op uw virtuele-machineschaalset, gebruik de volgende PowerShell-voorbeeld:

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

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hoe kan ik een aangepast script uitvoeren dat wordt gehost in een privé opslag account?

Voor het uitvoeren van een aangepast script dat wordt gehost in een persoonlijke storage-account, beveiligde instellingen instellen met de sleutel van het opslagaccount en de naam. Zie [Custom Script extension](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings)(Engelstalig) voor meer informatie.

## <a name="passwords"></a>Wachtwoorden

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hoe ik het wachtwoord opnieuw instellen voor VM's in mijn schaalset voor virtuele machine?

Er zijn twee manieren voor het wijzigen van het wachtwoord voor virtuele machines in schaalsets.

- Model met de virtuele machine een schaalset rechtstreeks wijzigen. Beschikbaar met API 2017-12-01 en hoger.

    De beheerdersreferenties rechtstreeks in het model met een schaalset (bijvoorbeeld met de Azure Resource Explorer, PowerShell of CLI) bijwerken. Zodra de schaalset bijgewerkt, worden alle nieuwe is hebben virtuele machines de nieuwe referenties. Bestaande virtuele machines hebben alleen de nieuwe referenties als ze zijn teruggezet.

- Het wachtwoord met behulp van de VM-extensies voor toegang opnieuw instellen.

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

## <a name="networking"></a>Networking

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Is het mogelijk een Netwerkbeveiligingsgroep (NSG) toewijzen aan een schaalset, zodat deze wordt toegepast op alle VM NIC's in de set?

Ja. Een Netwerkbeveiligingsgroep kan rechtstreeks aan een schaalset door te verwijzen naar deze in het gedeelte networkInterfaceConfigurations van het netwerkprofiel worden toegepast. Voorbeeld:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hoe doe ik geen VIP's wisselen voor schaalsets voor virtuele machines in hetzelfde abonnement en dezelfde regio?

Als u twee virtuele-machineschaalsets met de Azure Load Balancer van front-ends, en ze zich in hetzelfde abonnement en regio, kan u het openbare IP-adressen van elkaar toewijzing ongedaan maken en toewijzen aan de andere. Zie [wisselen van VIP: Blue-green implementatie in Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) bijvoorbeeld. Dit betekent dat er een vertraging hoofdgeheugen niveau echter de resources zijn de toewijzing ongedaan gemaakt/toegewezen op het netwerk. Een snellere optie is het gebruik van Azure Application Gateway met twee back-endpools en een regel voor doorsturen. U kunt ook u uw toepassing met kan hosten [Azure App service](https://azure.microsoft.com/services/app-service/) die ondersteuning biedt voor het snel schakelen tussen fasering en productie-sleuven.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hoe geef ik een bereik van particuliere IP-adressen te gebruiken voor statische privé IP-adrestoewijzing?

IP-adressen zijn geselecteerd in een subnet dat u opgeeft.

De toewijzingsmethode van virtual machine scale set IP-adressen is altijd 'dynamische', maar dat betekent niet dat deze IP-adressen kunnen wijzigen. In dit geval betekent 'dynamische' alleen dat u het IP-adres niet in een PUT-aanvraag opgeeft. Geef de statische instellen met behulp van het subnet.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hoe implementeer ik een virtuele-machineschaalset met een bestaande Azure-netwerk?

Zie voor het implementeren van een virtuele-machineschaalset met een bestaande Azure-netwerk, [implementeren een virtuele-machineschaalset is ingesteld op een bestaand virtueel netwerk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan ik scale sets met versnelde netwerken gebruiken?

Ja. Voor het gebruik van versneld netwerken ingesteld enableAcceleratedNetworking op waar in uw schaalset networkInterfaceConfigurations van instellingen. Bijvoorbeeld
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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hoe kan ik de DNS-servers die worden gebruikt door een schaalset configureren?

Voor het maken van een virtuele-machineschaalset met een aangepaste DNS-configuratie toevoegen een dnsSettings JSON-pakket naar het gedeelte networkInterfaceConfigurations van de schaal. Voorbeeld:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hoe kan ik een schaalset voor een openbaar IP-adres toewijzen aan elke virtuele machine configureren?

Voor het maken van een virtuele-machineschaalset waarmee een openbaar IP-adres worden toegewezen aan elke virtuele machine, zorg ervoor dat de API-versie van de resource Microsoft.Compute/virtualMachineScaleSets 2017-03-30 is en voeg een _publicipaddressconfiguration_ JSON pakket met de schaal ingesteld gedeelte ipConfigurations. Voorbeeld:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kan ik een schaalset om te werken met meerdere Application Gateways configureren?

Ja. U kunt de resource-Id's voor meerdere Application Gateway back-end-adres groepen toevoegen aan de lijst _applicationGatewayBackendAddressPools_ in het gedeelte _ipConfigurations_ van het netwerk profiel van de schaalset.

## <a name="scale"></a>Schaal

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>In welk geval zou ik een virtuele-machineschaalset met minder dan twee VM's maken?

Reden voor het maken van een virtuele-machineschaalset met minder dan twee VM's worden gebruikt de elastische eigenschappen van een virtuele-machineschaalset. U kunt bijvoorbeeld een virtuele-machineschaalset met nul VM's voor het definiëren van uw infrastructuur zonder kosten voor de uitvoering van VM's implementeren. Vervolgens, wanneer u klaar bent voor virtuele machines implementeren, vergroten de 'capaciteit' van de virtuele-machineschaalset ingesteld op het aantal productie-exemplaren.

Een andere reden, die kunt u een virtuele-machineschaalset met minder dan twee VM's maken, is als u zich zorgen maakt minder met beschikbaarheid dan in met behulp van een beschikbaarheidsset met discrete VM's. Virtuele-machineschaalsets bieden u een manier om te werken met niet-gedifferentieerde rekeneenheden die onderling vervangbaar zijn. Deze uniformiteit is een belangrijk verschil tussen virtuele-machineschaalsets ten opzichte van beschikbaarheidssets. Veel stateless workloads volgen afzonderlijke eenheden niet. Als de werkbelasting komt, kunt u omlaag schalen naar een compute-eenheid, en vervolgens kan worden uitgebreid naar veel wanneer de werkbelasting toeneemt.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hoe kan ik het aantal virtuele machines in een schaalset voor virtuele machine wijzigen

Als u wilt wijzigen van het aantal virtuele machines in een virtuele-machineschaalset in Azure portal, van de virtuele-machineschaalset sectie met eigenschappen instellen, klikt u op de blade 'Schaal' en gebruik de schuifregelaar.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hoe definieer ik aangepaste waarschuwingen voor als bepaalde drempelwaarden zijn bereikt?

Hebt u enige flexibiliteit in hoe u waarschuwingen voor de opgegeven drempelwaarden verwerken. U kunt bijvoorbeeld aangepaste webhooks definiëren. Het volgende voorbeeld van de webhook is van een Resource Manager-sjabloon:

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


## <a name="patching-and-operations"></a>Patching en bewerkingen

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kan ik een schaalset maken in een bestaande resource groep?

Ja, u kunt een schaalset maken in een bestaande resource groep.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kan ik een schaalset verplaatsen naar een andere resource groep?

Ja, u kunt resources van de schaalset naar een nieuw abonnement of resourcegroep verplaatsen.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hoe bijwerken ik aan mijn virtuele-machineschaalset met een nieuwe installatiekopie? Hoe kan ik het toepassen van patches beheren?

Bijwerken van uw virtuele-machineschaalset met een nieuwe installatiekopie en voor het beheren van patches, Zie [upgraden van een virtuele-machineschaalset](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan ik de bewerking installatiekopie terugzetten uitvoeren om een virtuele machine zonder te hoeven wijzigen van de installatiekopie opnieuw in te gebruiken? (Dat wil zeggen, ik wil een virtuele machine opnieuw instellen naar de fabrieksinstellingen, in plaats van naar een nieuwe installatiekopie.)

Ja, kunt u de bewerking installatiekopie terugzetten uitvoeren in te stellen van een virtuele machine zonder de afbeelding te wijzigen. Echter, als uw virtuele-machineschaalset verwijst naar een platforminstallatiekopie met `version = latest`, uw virtuele machine kunt bijwerken naar een hoger installatiekopie van het besturingssysteem bij het aanroepen van `reimage`.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Is het mogelijk om schaal sets te integreren met Azure Monitor-logboeken?

Ja, u kunt de Azure Monitor-extensie installeren op de schaal sets Vm's. Hier volgt een voorbeeld van Azure CLI:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
U kunt de vereiste workspaceId en workspaceKey vinden in de Log Analytics-werkruimte van Azure-portal. Klik op de tegel van de instellingen op de pagina overzicht. Klik op het tabblad verbonden bronnen aan de bovenkant.

> [!NOTE]
> Als uw schaalset _upgrade Policy_ is ingesteld op hand matig, moet u de uitbrei ding Toep assen op alle virtuele machines in de set door de upgrade hiervoor aan te roepen. In de CLI zou dit _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hoe kan ik diagnostische gegevens over opstarten inschakelen?

Voor diagnostische gegevens over opstarten inschakelt, moet u eerst een opslagaccount maken. Zet dit blok JSON in uw virtuele-machineschaalset **virtualMachineProfile**, en de virtuele-machineschaalset bijwerken:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Wanneer een nieuwe virtuele machine is gemaakt, toont de InstanceView-eigenschap van de virtuele machine de details voor de schermafbeelding, enzovoort. Hier volgt een voorbeeld:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Eigenschappen van de virtuele machine

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hoe krijg ik informatie over de eigenschappen voor elke virtuele machine zonder meerdere aanroepen? Bijvoorbeeld, hoe zou ik krijgen het foutdomein voor elk van de 100 VM's in mijn schaalset voor virtuele machine?

Als u informatie over de eigenschappen voor elke virtuele machine zonder meerdere aanroepen, u kunt aanroepen `ListVMInstanceViews` aan de hand van een REST-API `GET` op de volgende resource-URI:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan ik andere extensie argumenten doorgeven aan verschillende virtuele machines in een virtuele-machineschaalset?

U kunt andere extensie argumenten Nee, niet kan doorgeven aan verschillende virtuele machines in een virtuele-machineschaalset. Extensies fungeren, op basis van de unieke eigenschappen van de virtuele machine waarop ze worden uitgevoerd, bijvoorbeeld als u op de naam van de machine. Extensies ook kunnen een query uitvoeren metagegevens van het exemplaar op http://169.254.169.254 voor meer informatie over de virtuele machine.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Waarom zijn er onderbrekingen tussen mijn virtuele machine scale set VM machine namen en VM-id's? Bijvoorbeeld: 0, 1, 3...

Er onderbrekingen tussen uw virtuele machine scale set VM machine namen en VM-id's zijn omdat de virtuele-machineschaalset **overprovision** eigenschap is ingesteld op de standaardwaarde van **waar**. Als overmatige inrichting is ingesteld op **waar**, meer virtuele machines bevat dan het aangevraagde worden gemaakt. Extra virtuele machines vervolgens verwijderd. In dit geval u verbeterde implementatie betrouwbaarheid krijgen, maar regels ten koste van de aaneengesloten naamgeving en aaneengesloten Network Address Translation (NAT).

U kunt deze eigenschap instellen op **false**. Dit heeft geen aanzienlijk voor kleine virtuele-machineschaalsets, invloed op de betrouwbaarheid van de implementatie.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Wat is het verschil tussen het verwijderen van een virtuele machine in een virtuele-machineschaalset en de toewijzing van de virtuele machine ongedaan maken? Wanneer moet ik kiezen ene of het andere?

Het belangrijkste verschil tussen een virtuele machine in een virtuele-machineschaalset verwijderen en toewijzing ongedaan maken van de virtuele machine is die `deallocate` de virtuele harde schijven (VHD's) worden niet verwijderd. Er zijn kosten voor opslag die is gekoppeld aan die wordt uitgevoerd `stop deallocate`. U kunt een of andere gebruiken om een van de volgende redenen:

- U wilt stoppen met het betalen van de kosten voor rekenuren, maar u wilt behouden van de status van de schijf van de virtuele machines.
- Wilt u sneller dan de schaal van een virtuele-machineschaalset starten van een set van virtuele machines.
  - Met betrekking tot dit scenario, u mogelijk hebt gemaakt een eigen engine voor automatisch schalen en wilt een snellere end-to-end-schaal.
- U hebt een virtuele-machineschaalset die ongelijkmatig verdeeld is over domeinen met fouten of update-domeinen. Dit kan zijn omdat u selectief virtuele machines verwijderd, of omdat virtuele machines zijn verwijderd na het piekmomenten. Met `stop deallocate` gevolgd door `start` op de virtuele machine schaalset gelijkmatig verdeeld over de virtuele machines domeinen met fouten of update-domeinen.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hoe kan ik een moment opname van een virtuele-machine Scale set-exemplaar maken?
Maak een moment opname van een exemplaar van een virtuele-machine schaalset.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Een beheerde schijf maken op basis van de moment opname.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
