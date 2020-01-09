---
title: Overzicht van virtuele Linux-machines in azure
description: Overzicht van virtuele Linux-machines in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 46a1198b4052cb8663c60e53e8c2b965f78af948
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644287"
---
# <a name="linux-virtual-machines-in-azure"></a>Virtuele Linux-machines in Azure

Azure Virtual Machines (VM) vormen een van de diverse typen [schaalbare on-demand computerresources](/azure/architecture/guide/technology-choices/compute-decision-tree) die Azure biedt. Normaal gesproken kiest u voor een VM wanneer u meer controle nodig hebt over de computeromgeving dan andere opties bieden. In dit artikel vindt u informatie over wat u moet overwegen voordat u een VM maakt, hoe u deze maakt en hoe u deze beheert.

Een VM in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de VM wordt uitgevoerd. U moet de VM echter wel onderhouden door taken uit te voeren, zoals het configureren, patchen en onderhouden van de software die erop wordt uitgevoerd.

Virtuele machines in Azure kunnen op verschillende manieren worden gebruikt. Een aantal voorbeelden:

* **Ontwikkeling en tests**: Azure-VM’s bieden een snelle en eenvoudige manier om een computer te maken met specifieke configuraties die nodig zijn voor de code van een toepassing en het testen ervan.
* **Toepassingen in de cloud**: De vraag naar uw toepassing kan variëren, dus kan het financieel verstandig zijn om deze uit te voeren op een VM in Azure. U betaalt voor extra virtuele machines wanneer u ze nodig hebt en schakelt ze uit wanneer u ze niet meer nodig hebt.
* **Uitgebreid datacenter**: Virtuele machines in een virtueel Azure-netwerk kunnen eenvoudig worden verbonden met het netwerk van uw organisatie.

Het aantal virtuele machines dat uw toepassing gebruikt, kan omhoog worden geschaald naar wat is vereist om te voldoen aan uw behoeften.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Waar moet ik over nadenken voordat ik een VM maak?
Er is altijd een groot aantal [overwegingen bij het ontwerpen](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) wanneer u de infrastructuur van een toepassing verder uitwerkt in Azure. Deze aspecten van een VM zijn belangrijk om over na te denken voordat u begint:

* De namen van uw toepassingsresources
* De locatie waar de resources worden opgeslagen
* De grootte van de VM
* Het maximumaantal VM's dat kan worden gemaakt
* Het besturingssysteem dat op de VM wordt uitgevoerd
* De configuratie van de VM nadat deze is gestart
* De gerelateerde resources die de VM nodig heeft

### <a name="locations"></a>Locaties
Alle resources die in Azure zijn gemaakt, worden verdeeld over meerdere [geografische regio's](https://azure.microsoft.com/regions/) over de hele wereld. Normaal gesproken heet de regio **locatie** wanneer u een VM maakt. Voor een VM geeft de locatie aan waar de virtuele harde schijven zijn opgeslagen.

In deze tabel staan enkele manieren om een lijst met beschikbare locaties te verkrijgen.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |Selecteer een locatie in de lijst bij het maken van een VM. |
| Azure PowerShell |Gebruik de opdracht [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| REST API |Gebruik de bewerking [Locaties vermelden](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| Azure-CLI |Gebruik de bewerking [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

## <a name="availability"></a>Beschikbaarheid
Voor Azure is een toonaangevende serviceovereenkomst (SLA) van 99,9% aangekondigd voor één VM-instantie. Hiervoor geldt wel als voorwaarde dat de virtuele machine wordt geïmplementeerd met Premium-opslag voor alle schijven.  Als u wilt dat uw VM-implementatie in aanmerking komt voor de SLA van 99,95%, moet u bovendien een beschikbaarheidsset maken met ten minste twee VM's waarop uw workload wordt uitgevoerd. Dit zorgt ervoor dat uw VM's worden verdeeld over meerdere foutdomeinen in de Azure-datacenters en worden geïmplementeerd op hosts met verschillende onderhoudsvensters. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

## <a name="vm-size"></a>VM-grootte
De [grootte](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) van de VM die u gebruikt, wordt bepaald door de workload die u wilt uitvoeren. De grootte die u vervolgens kiest, bepaalt factoren als processorsnelheid, geheugen en opslagcapaciteit. Azure biedt een groot aantal verschillende grootten voor verschillende manieren van gebruik.

Azure rekent een [uurprijs](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) op basis van de grootte en het besturingssysteem van de VM. Voor niet-hele uren worden alleen de minuten van gebruik in rekening gebracht. De opslag wordt afzonderlijk berekend en in rekening gebracht.

## <a name="vm-limits"></a>VM-limieten
Uw abonnement heeft een standaard [quotumlimiet](../../azure-resource-manager/management/azure-subscription-service-limits.md) ingebouwd die de implementatie van veel VM’s voor uw project kan beïnvloeden. De huidige limiet per abonnement is 20 VM's per regio. Limieten kunnen worden verhoogd door [een ondersteuningsticket in te dienen met een aanvraag voor een verhoging](../../azure-supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

Managed Disks beheert het maken/beheren van Azure Storage-accounts op de achtergrond en zorgt ervoor dat u zich geen zorgen hoeft te maken over de schaalbaarheidslimieten van het opslagaccount. U hoeft alleen de schijfgrootte en prestatielaag (Standard of Premium) op te geven en Azure maakt en beheert de schijf voor u. Ook als u schijven toevoegt of de virtuele machine omhoog of omlaag schaalt, hoeft u zich geen zorgen te maken over de gebruikte opslag. Als u nieuwe VM's gaat maken, [gebruik dan de Azure CLI 2.0](quick-create-cli.md) of Azure Portal om VM's te maken met een beheerd besturingssysteem en beheerde gegevensschijven. Als u VM's hebt met niet-beheerde schijven, kunt u [deze converteren om ze te ondersteunen met Managed Disks](convert-unmanaged-to-managed-disks.md).

U kunt ook uw aangepaste installatiekopieën in één opslagaccount per Azure-regio beheren en deze gebruiken om honderden virtuele machines onder hetzelfde abonnement te maken. Voor meer informatie over Managed Disks raadpleegt u [Managed Disks Overview](../linux/managed-disks-overview.md) (Overzicht van Managed Disks).

## <a name="distributions"></a>Distributies 
Microsoft Azure ondersteunt de uitvoering van een aantal populaire Linux-distributies die worden geleverd en onderhouden door een aantal partners.  In de Microsoft Azure Marketplace vindt u onder meer distributies als Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS en FreeBSD. Microsoft werkt actief samen met diverse Linux-community's om nog meer varianten toe te voegen aan [de lijst met Linux-distributies die zijn goedgekeurd voor Azure](endorsed-distros.md).

Als de Linux-distributie van uw keuze niet op de lijst staat, kunt u zelf een Linux-VM maken ('Bring Your Own Linux') door [een Linux-VHD te maken en uploaden in Azure](create-upload-generic.md).

Microsoft werkt nauw samen met partners om ervoor te zorgen dat de beschikbare installatiekopieën worden bijgewerkt en geoptimaliseerd voor een Azure-runtime.  Voor meer informatie over Azure-partners klikt u op de volgende koppelingen:

* [Linux op door Azure goedgekeurde distributies](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library voor Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS voor Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - Azure Container Service met Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="vm-sizes"></a>VM-grootten
De [grootte](sizes.md) van de VM die u gebruikt, wordt bepaald door de workload die u wilt uitvoeren. De grootte die u vervolgens kiest, bepaalt factoren als processorsnelheid, geheugen en opslagcapaciteit. Azure biedt een groot aantal verschillende grootten voor verschillende manieren van gebruik.

Azure rekent een [uurprijs](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) op basis van de grootte en het besturingssysteem van de VM. Voor niet-hele uren worden alleen de minuten van gebruik in rekening gebracht. De opslag wordt afzonderlijk berekend en in rekening gebracht.

## <a name="cloud-init"></a>Cloud-init 

Voor een juiste DevOps-cultuur moet alle infrastructuur uit code bestaan.  Wanneer alle infra structuur in code wordt geduurd, kan deze eenvoudig opnieuw worden gemaakt.  Azure werkt met de belangrijkste automatiseringstools zoals Ansible, Chef, SaltStack en Puppet.  Azure heeft ook eigen tools voor automatisering:

* [Azure-sjablonen](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure biedt ondersteuning voor [Cloud-init](https://cloud-init.io/) voor de meeste Linux-distributies die dit ondersteunen.  We zijn actief werkt met onze onderschreven Linux-distributie partners om cloud-init ingeschakeld installatiekopieën die beschikbaar zijn in de Azure marketplace. Met deze installatie kopieën kunnen uw Cloud-init-implementaties en-configuraties naadloos werken met Vm's en virtuele-machine schaal sets.

* [Cloud-init gebruiken op virtuele Linux-machines in Azure](using-cloud-init.md)

## <a name="quotas"></a>Quota
Elk Azure-abonnement heeft een standaard-quotumlimiet die van invloed kan zijn op de implementatie van een groot aantal VM’s voor uw project. De huidige limiet per abonnement is 20 VM's per regio.  De quotalimiet kan snel en gemakkelijk worden verhoogd door het indienen van een ondersteuningsticket met het verzoek om de limiet te verhogen.  Meer informatie over quotalimieten vindt u hier:

* [Azure-abonnement en servicelimieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)


## <a name="storage"></a>Storage
* [Inleiding tot Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Een schijf toevoegen aan een virtuele Linux-machine](add-disk.md)
* [Een gegevensschijf koppelen aan een Linux-VM in Azure Portal](attach-disk-portal.md)

## <a name="networking"></a>Networking
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Poorten openen voor een Linux-VM in Azure](nsg-quickstart.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](portal-create-fqdn.md)


## <a name="next-steps"></a>Volgende stappen

Maak uw eerste VM.

- [Portal](quick-create-portal.md)
- [Azure-CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)

