---
title: Overzicht van virtuele Windows-machines - Azure | Microsoft Docs
description: Meer informatie over het maken en beheren van virtuele Windows-machines in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0f2929218b3a304099b1eb009df8e93d0e43c103
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839883"
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Overzicht van virtuele Windows-machines in Azure

Azure Virtual Machines (VM) vormen een van de diverse typen [schaalbare on-demand computerresources](/azure/architecture/guide/technology-choices/compute-decision-tree) die Azure biedt. Normaal gesproken kiest u voor een VM wanneer u meer controle nodig hebt over de computeromgeving dan andere opties bieden. In dit artikel vindt u informatie over wat u moet overwegen voordat u een VM maakt, hoe u deze maakt en hoe u deze beheert.

Een VM in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de VM wordt uitgevoerd. U moet de VM echter wel onderhouden door taken uit te voeren, zoals het configureren, patchen en onderhouden van de software die erop wordt uitgevoerd.

Virtuele machines in Azure kunnen op verschillende manieren worden gebruikt. Een aantal voorbeelden:

* **Ontwikkeling en tests**: Azure-VM’s bieden een snelle en eenvoudige manier om een computer te maken met specifieke configuraties die nodig zijn voor de code van een toepassing en het testen ervan.
* **Toepassingen in de cloud**: De vraag naar uw toepassing kan variëren, dus kan het financieel verstandig zijn om deze uit te voeren op een VM in Azure. U betaalt voor extra virtuele machines wanneer u ze nodig hebt en schakelt ze uit wanneer u ze niet meer nodig hebt.
* **Uitgebreid datacenter**: Virtuele machines in een virtueel Azure-netwerk kunnen eenvoudig worden verbonden met het netwerk van uw organisatie.

Het aantal virtuele machines dat uw toepassing gebruikt, kan omhoog worden geschaald naar wat is vereist om te voldoen aan uw behoeften.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Waar moet ik over nadenken voordat ik een VM maak?
Er is altijd een groot aantal [overwegingen bij het ontwerpen](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) wanneer u de infrastructuur van een toepassing verder uitwerkt in Azure. Deze aspecten van een VM zijn belangrijk om over na te denken voordat u begint:

* De namen van uw toepassingsresources
* De locatie waar de resources worden opgeslagen
* De grootte van de VM
* Het maximumaantal VM's dat kan worden gemaakt
* Het besturingssysteem dat op de VM wordt uitgevoerd
* De configuratie van de VM nadat deze is gestart
* De gerelateerde resources die de VM nodig heeft

### <a name="naming"></a>Naamgeving
Een virtuele machine krijgt een [naam](/azure/architecture/best-practices/resource-naming) toegewezen en er wordt een computernaam geconfigureerd als onderdeel van het besturingssysteem. De naam van een VM mag uit maximaal 15 tekens bestaan.

Als u Azure gebruikt voor het maken van de schijf van het besturingssysteem, zijn de computernaam en de naam van de virtuele machine hetzelfde. Als u [uw eigen installatiekopie uploadt en gebruikt](upload-generalized-managed.md), eentje die een eerder geconfigureerd besturingssysteem bevat, en deze gebruikt om een virtuele machine maakt, kunnen de namen anders zijn. We raden u aan tijdens het uploaden van uw eigen installatiekopiebestand de computer in het besturingssysteem en de virtuele machine hetzelfde te noemen.

### <a name="locations"></a>Locaties
Alle resources die in Azure zijn gemaakt, worden verdeeld over meerdere [geografische regio's](https://azure.microsoft.com/regions/) over de hele wereld. Normaal gesproken heet de regio **locatie** wanneer u een VM maakt. Voor een VM geeft de locatie aan waar de virtuele harde schijven zijn opgeslagen.

In deze tabel staan enkele manieren om een lijst met beschikbare locaties te verkrijgen.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |Selecteer een locatie in de lijst bij het maken van een VM. |
| Azure PowerShell |Gebruik de opdracht [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| REST API |Gebruik de bewerking [Locaties vermelden](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| Azure CLI |Gebruik de bewerking [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

### <a name="vm-size"></a>VM-grootte
De [grootte](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) van de VM die u gebruikt, wordt bepaald door de workload die u wilt uitvoeren. De grootte die u vervolgens kiest, bepaalt factoren als processorsnelheid, geheugen en opslagcapaciteit. Azure biedt een groot aantal verschillende grootten voor verschillende manieren van gebruik.

Azure rekent een [uurprijs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) op basis van de grootte en het besturingssysteem van de VM. Voor niet-hele uren worden alleen de minuten van gebruik in rekening gebracht. De opslag wordt afzonderlijk berekend en in rekening gebracht.

### <a name="vm-limits"></a>VM-limieten
Uw abonnement heeft een standaard [quotumlimiet](../../azure-subscription-service-limits.md) ingebouwd die de implementatie van veel VM’s voor uw project kan beïnvloeden. De huidige limiet per abonnement is 20 VM's per regio. Limieten kunnen worden verhoogd door [een ondersteuningsticket in te dienen met een aanvraag voor een verhoging](../../azure-supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Schijven en installatiekopieën voor een besturingssysteem
Virtuele machines maken gebruik van [virtuele harde schijven (VHD's)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de opslag van het besturingssysteem (OS) en de gegevens. VHD's worden ook gebruikt voor de installatiekopieën waarmee u een besturingssysteem kunt installeren. 

Azure biedt veel [marketplace-installatiekopieën](https://azure.microsoft.com/marketplace/virtual-machines/) voor gebruik met verschillende versies en typen van de Windows Server-besturingssystemen. Marketplace-installatiekopieën worden aangeduid met uitgever, aanbieding, SKU en versie van de installatiekopie (de versie wordt meestal gespecificeerd als meest recente). Alleen 64-bits besturingssystemen worden ondersteund. Zie [Microsoft-serversoftwareondersteuning voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) voor meer informatie over de ondersteunde gastbesturingssystemen, rollen en onderdelen.

In deze tabel ziet u een aantal manieren waarop u de gegevens voor een installatiekopie kunt vinden.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |De waarden worden automatisch opgegeven wanneer u een installatiekopie selecteert om te gebruiken. |
| Azure PowerShell |[Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API's |[Uitgevers van installatiekopieën weergeven](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Aanbiedingen van installatiekopieën weergeven](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Installatiekopie-SKU's weergeven](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --locatie *location*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *locatie* --publisher *publisherName*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --location *locatie* --publisher *publisherName* --offer *offerName*|

U kunt ervoor kiezen om [uw eigen installatiekopie te uploaden en te gebruiken](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account). Wanneer u dit doet, worden de uitgeversnaam, aanbieding en SKU niet gebruikt.

### <a name="extensions"></a>Extensies
VM-[extensies](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) geven uw VM meer mogelijkheden via de post-implementatieconfiguratie en geautomatiseerde taken.

Deze algemene taken kunnen worden uitgevoerd met extensies:

* **Aangepaste scripts uitvoeren**: de [aangepaste scriptextensie](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) helpt u workloads op de VM te configureren door uw script uit te voeren wanneer de VM is ingericht.
* **Configuraties implementeren en beheren**: de [PowerShell Desired State Configuration (DSC)-extensie](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) helpt bij het instellen van DSC op een VM voor het beheren van configuraties en omgevingen.
* **Diagnostische gegevens verzamelen**: de [Azure Diagnostics-extensie](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) helpt u bij het configureren van de VM voor het verzamelen van diagnostische gegevens die kunnen worden gebruikt voor het bewaken van de status van uw toepassing.

### <a name="related-resources"></a>Gerelateerde resources
De resources in deze tabel worden gebruikt door de VM en moeten bestaan of worden gemaakt wanneer de VM wordt gemaakt.

| Bron | Vereist | Beschrijving |
| --- | --- | --- |
| [Resourcegroep](../../azure-resource-manager/resource-group-overview.md) |Ja |De VM moet zijn opgenomen in een resourcegroep. |
| [Opslagaccount](../../storage/common/storage-create-storage-account.md) |Ja |De VM heeft het opslagaccount nodig voor het opslaan van de virtuele harde schijven. |
| [Virtueel netwerk](../../virtual-network/virtual-networks-overview.md) |Ja |De VM moet lid zijn van een virtueel netwerk. |
| [Openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nee |Aan de VM kan een openbaar IP-adres worden toegewezen voor externe toegang. |
| [Netwerkinterface](../../virtual-network/virtual-network-network-interface.md) |Ja |De netwerkinterface van de VM moet in het netwerk communiceren. |
| [Gegevensschijven](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nee |De VM kan gegevensschijven bevatten om opslagmogelijkheden uit te breiden. |

## <a name="how-do-i-create-my-first-vm"></a>Hoe kan ik mijn eerste VM maken?
U hebt verschillende mogelijkheden voor het maken van uw VM. De keuze die u maakt, is afhankelijk van de omgeving waarin u zich bevindt. 

Deze tabel bevat informatie om u te helpen uw VM te maken.

| Methode | Artikel |
| --- | --- |
| Azure Portal |[Een virtuele machine maken waarop Windows wordt uitgevoerd, met behulp van de portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Sjablonen |[Een virtuele Windows-machine maken met een Resource Manager-sjabloon](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Een Windows-VM maken met behulp van PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Client-SDK 's |[Azure-bronnen implementeren met C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API's |[Een VM maken of bijwerken](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure CLI |[Een virtuele machine maken met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

U hoopt natuurlijk dat alles goed gaat, maar soms gaat er iets fout. Als deze situatie u ooit overkomt, bekijkt u de informatie in [Problemen met Resource Manager-implementatie bij het maken van een virtuele Windows-machine in Azure oplossen](../troubleshooting/troubleshoot-deployment-new-vm-windows.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Hoe beheer ik de VM die ik heb gemaakt?
VM's kunnen worden beheerd via een op een browser gebaseerde portal, opdrachtregelprogramma's met ondersteuning voor het uitvoeren van scripts of rechtstreeks via API's. Typische beheertaken die u uitvoert, zijn bijvoorbeeld: informatie over een VM ophalen, u aanmelden op een VM, de beschikbaarheid beheren en back-ups maken.

### <a name="get-information-about-a-vm"></a>Informatie over een VM ophalen
In deze tabel ziet u enkele van de manieren waarop u informatie over een VM kunt ophalen.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |Klik in het hub-menu op **Virtuele Machines** en selecteer vervolgens de VM uit de lijst. Op de blade voor de VM kunt u informatie inzien, waarden instellen en metrische gegevens controleren. |
| Azure PowerShell |Zie [Create and Manage Windows VMs with the Azure PowerShell module](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuele Windows-machines maken en beheren met de Azure PowerShell-module) voor informatie over het gebruik van PowerShell voor het beheren van virtuele machines. |
| REST API |Gebruik de bewerking [VM-informatie ophalen](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) voor het verkrijgen van informatie over een VM. |
| Client-SDK 's |Zie voor meer informatie over het gebruik van C# voor het beheren van VM's [Virtuele machines van Azure beheren met behulp van Azure Resource Manager en C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Azure CLI |Zie [Referentie Azure CLI](https://docs.microsoft.com/cli/azure/vm) voor informatie over het gebruik van Azure CLI voor het beheren van virtuele machines. |

### <a name="log-on-to-the-vm"></a>Aanmelden bij de VM
U gebruikt de knop [Verbinden](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) in de Azure Portal om een Extern bureaublad-sessie te starten. Soms kan er iets fout gaan wanneer u probeert om een externe verbinding te gebruiken. Als deze situatie u overkomt, raadpleegt u de help-informatie in [Problemen oplossen met Extern bureaublad-verbindingen met een virtuele Windows-machine in Azure](../troubleshooting/troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Beschikbaarheid beheren
Het is belangrijk dat u begrijpt hoe u [hoge beschikbaarheid garandeert](manage-availability.md) voor uw toepassing. Deze configuratie bestaat onder meer uit het maken van meerdere VM’s om ervoor te zorgen dat er ten minste één wordt uitgevoerd.

Om uw implementatie te laten voldoen aan onze 99.95 VM Service Level Agreement, moet u twee of meer VM's waarop uw workload wordt uitgevoerd, implementeren binnen een [beschikbaarheidsset](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Deze configuratie zorgt ervoor dat uw virtuele machines worden verdeeld over meerdere foutdomeinen en worden geïmplementeerd op hosts met verschillende onderhoudsvensters. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

### <a name="back-up-the-vm"></a>Een back-up maken van de VM
In zowel Azure Backup als Azure Site Recovery-services wordt een [Recovery Services-kluis](../../backup/backup-introduction-to-azure-backup.md) gebruikt voor het beveiligen van gegevens en assets. U kunt een Recovery Services-kluis gebruiken om [back-ups voor door Resource Manager geïmplementeerde virtuele machines te implementeren en te beheren met behulp van PowerShell](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Volgende stappen
* Als u met Linux-VM's wilt werken, bekijkt u [Azure en Linux](../linux/overview.md).
* Meer informatie over de richtlijnen voor het instellen van uw infrastructuur vindt u in het [Voorbeeldoverzicht van Azure-infrastructuur](infrastructure-example.md).
