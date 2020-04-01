---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: f2eb503b58f1679d138b6a1dd9304896be098ad6
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419197"
---
Als u virtuele Azure-machines (VM's) op een consistente manier op schaal wilt maken en beheren, wordt een of andere vorm van automatisering meestal gewenst. Er zijn veel tools en oplossingen waarmee u de volledige implementatie- en beheerlevenscyclus van azure-infrastructuur automatiseren. In dit artikel worden enkele hulpprogramma's voor infrastructuurautomatisering geïntroduceerd die u in Azure gebruiken. Deze tools passen vaak in een van de volgende benaderingen:

- De configuratie van VM's automatiseren
    - De hulpprogramma's omvatten [ansible](#ansible), [chef,](#chef) [puppet](#puppet)en [Azure Resource Manager-sjabloon](#azure-resource-manager-template).
    - Tools die specifiek zijn voor [VM-aanpassing omvatten cloud-init](#cloud-init) voor Linux VM's, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)en de [Azure Custom Script-extensie](#azure-custom-script-extension) voor alle Azure VM's.

- Infrastructuurbeheer automatiseren
    - Tools omvatten [Packer](#packer) om aangepaste VM-imagebuilds te automatiseren en [Terraform](#terraform) om het infrastructuurbouwproces te automatiseren.
    - [Azure Automation](#azure-automation) kan acties uitvoeren in uw Azure- en on-premises infrastructuur.

- Implementatie en levering van toepassingen automatiseren
    - Voorbeelden hiervan zijn [Azure DevOps Services](#azure-devops-services) en [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) is een automatiseringsengine voor configuratiebeheer, het maken van vm's of implementatie van toepassingen. Ansible gebruikt een agentloos model, meestal met SSH-sleutels, om doelmachines te verifiëren en te beheren. Configuratietaken worden gedefinieerd in playbooks, met een aantal Ansible-modules beschikbaar voor het uitvoeren van specifieke taken. Zie [Hoe Ansible werkt](https://www.ansible.com/how-ansible-works)voor meer informatie.

Leer hoe u het volgende doet:

- [Ansible op Linux installeren en configureren voor gebruik met Azure.](../articles/ansible/ansible-install-configure.md)
- [Maak een Linux virtuele machine.](../articles/ansible/ansible-create-vm.md)
- [Een Virtuele Linux-machine beheren.](../articles/ansible/ansible-manage-linux-vm.md)


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) is een automatiseringsplatform dat helpt bij het definiëren van de manier waarop uw infrastructuur wordt geconfigureerd, geïmplementeerd en beheerd. Aanvullende componenten waren Chef Habitat voor automatisering van de levenscyclus van toepassingen in plaats van de infrastructuur, en Chef InSpec die helpt bij het automatiseren van de naleving van beveiligings- en beleidsvereisten. Chef Clients worden geïnstalleerd op doelmachines, met een of meer centrale Chef Servers die de configuraties opslaan en beheren. Zie [Een overzicht van chef-kok](https://docs.chef.io/chef_overview.html)voor meer informatie.

Leer hoe u het volgende doet:

- [Implementeer Chef Automatiseer vanuit de Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Installeer Chef op Windows en maak Azure VM's.](../articles/chef/chef-automation.md)


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) is een bedrijfsklaar automatiseringsplatform dat het leverings- en implementatieproces van toepassingen afhandelt. Agents zijn geïnstalleerd op doelmachines, zodat Puppet Master manifesten kan uitvoeren die de gewenste configuratie van de Azure-infrastructuur en VM's definiëren. Puppet kan integreren met andere oplossingen zoals Jenkins en GitHub voor een verbeterde devops workflow. Zie [Hoe Puppet werkt](https://puppet.com/products/how-puppet-works)voor meer informatie.

Leer hoe u het volgende doet:

- [Implementeer Puppet vanuit de Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview)


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat cloud-init wordt aangeroepen tijdens het eerste opstartproces, zijn er geen extra stappen of vereiste agents om uw configuratie toe te passen.  Zie de `#cloud-config` [documentatiesite van cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)voor meer informatie over het correct opmaken van uw bestanden.  `#cloud-config`bestanden zijn tekstbestanden gecodeerd in base64.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

We werken actief samen met onze goedgekeurde Linux distro-partners om cloud-init-afbeeldingen beschikbaar te hebben in de Azure-marktplaats. Deze afbeeldingen zorgen ervoor dat uw cloud-init-implementaties en -configuraties naadloos werken met VM's en virtuele machineschaalsets.
Meer informatie over cloud-init op Azure:

- [Cloud-init-ondersteuning voor Virtuele Linux-machines in Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Probeer een zelfstudie over geautomatiseerde VM-configuratie met behulp van cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheerplatform om de configuratie van doelmachines te definiëren. DSC kan ook op Linux worden gebruikt via de [Open Management Infrastructure (OMI) server.](https://collaboration.opengroup.org/omi/)

DSC-configuraties definiëren wat u op een machine moet installeren en hoe u de host configureert. Een LCM-engine (Local Configuration Manager) draait op elk doelknooppunt dat gevraagde acties verwerkt op basis van gepushte configuraties. Een pull-server is een webservice die wordt uitgevoerd op een centrale host om de DSC-configuraties en bijbehorende resources op te slaan. De pull-server communiceert met de LCM-engine op elke doelhost om de vereiste configuraties te bieden en verslag uit te brengen over de naleving.

Leer hoe u het volgende doet:

- [Maak een basisDSC-configuratie](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Een DSC pull-server configureren](/powershell/scripting/dsc/pull-server/pullserver).
- [Gebruik DSC voor Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Aangepaste scriptextensie voor Azure
De Azure Custom Script-extensie voor [Linux](../articles/virtual-machines/linux/extensions-customscript.md) of [Windows](../articles/virtual-machines/windows/extensions-customscript.md) downloadt en voert scripts uit op Azure VM's. U de extensie gebruiken wanneer u een vm maakt of wanneer de vm in gebruik is.

Scripts kunnen worden gedownload van Azure-opslag of een openbare locatie, zoals een GitHub-opslagplaats. Met de aangepaste scriptextensie u scripts schrijven in elke taal die op de bron-VM wordt uitgevoerd. Deze scripts kunnen worden gebruikt om toepassingen te installeren of de VM naar wens te configureren. Om referenties te beveiligen, kunnen gevoelige informatie zoals wachtwoorden worden opgeslagen in een beveiligde configuratie. Deze referenties worden alleen gedecodeerd in de VM.

Leer hoe u het volgende doet:

- [Maak een Linux-VM met de Azure CLI en gebruik de Custom Script-extensie](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Maak een Windows VM met Azure PowerShell en gebruik de Aangepaste Script-extensie](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiseert het buildproces wanneer u een aangepaste VM-afbeelding in Azure maakt. U gebruikt Packer om het besturingssysteem te definiëren en scripts na de configuratie uit te voeren die de VM aanpassen aan uw specifieke behoeften. Eenmaal geconfigureerd wordt de VM vervolgens vastgelegd als een beheerde schijfafbeelding. Packer automatiseert het proces om de bron-VM-, netwerk- en opslagbronnen te maken, configuratiescripts uit te voeren en vervolgens de VM-afbeelding te maken.

Leer hoe u het volgende doet:

- [Gebruik Packer om een Linux VM-afbeelding in Azure te maken.](../articles/virtual-machines/linux/build-image-with-packer.md)
- [Gebruik Packer om een Windows VM-afbeelding te maken in Azure.](../articles/virtual-machines/windows/build-image-with-packer.md)


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) is een automatiseringstool waarmee u een volledige Azure-infrastructuur definiëren en maken met één sjabloonindelingstaal - de HashiCorp-configuratietaal (HCL). Met Terraform definieert u sjablonen die het proces automatiseren om netwerk-, opslag- en VM-bronnen voor een bepaalde toepassingsoplossing te maken. U uw bestaande Terraform-sjablonen voor andere platforms met Azure gebruiken om consistentie te garanderen en de implementatie van de infrastructuur te vereenvoudigen zonder dat u hoeft te converteren naar een Azure Resource Manager-sjabloon.

Leer hoe u het volgende doet:

- [Terraform installeren en configureren met Azure](../articles/terraform/terraform-install-configure.md).
- [Maak een Azure-infrastructuur met Terraform](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) gebruikt runbooks om een reeks taken te verwerken op de VM's die u target. Azure Automation wordt gebruikt om bestaande VM's te beheren in plaats van een infrastructuur te maken. Azure Automation kan worden uitgevoerd in zowel Linux- als Windows-VM's, evenals on-premises virtuele of fysieke machines met een hybride runbook-medewerker. Runbooks kunnen worden opgeslagen in een bronbeheeropslagplaats, zoals GitHub. Deze runbooks kunnen vervolgens handmatig of volgens een gedefinieerd schema worden uitgevoerd.

Azure Automation biedt ook een DSC-service (Desired State Configuration) waarmee u definities maken voor hoe een bepaalde set VM's moet worden geconfigureerd. DSC zorgt er vervolgens voor dat de vereiste configuratie wordt toegepast en dat de VM consistent blijft. Azure Automation DSC draait op zowel Windows- als Linux-machines.

Leer hoe u het volgende doet:

- [Maak een PowerShell-runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Gebruik Hybride runbookworker om on-premises resources te beheren.](../articles/automation/automation-hybrid-runbook-worker.md)
- [Azure Automation DSC gebruiken](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) is een reeks hulpprogramma's waarmee u code delen en bijhouden, geautomatiseerde builds gebruiken en een volledige CI/CD-pijplijn (continuous integration and development) maken. Azure DevOps Services integreert met Visual Studio en andere editors om het gebruik te vereenvoudigen. Azure DevOps Services kan ook Azure VM's maken en configureren en er vervolgens code naar implementeren.

Meer informatie over:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) is een continue integratieserver die helpt bij het implementeren en testen van applicaties en het maken van geautomatiseerde pijplijnen voor het leveren van code. Er zijn honderden plug-ins om het kernJenkins-platform uit te breiden, en u ook integreren met vele andere producten en oplossingen via webhooks. U Jenkins handmatig installeren op een Azure VM, Jenkins uitvoeren vanuit een Docker-container of een vooraf gebouwde Azure Marketplace-afbeelding gebruiken.

Leer hoe u het volgende doet:

- [Maak een ontwikkelinfrastructuur op een Linux-VM in Azure met Jenkins, GitHub en Docker.](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md)


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
[Azure Resource Manager](../articles/azure-resource-manager/templates/overview.md) is de implementatie- en beheerservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-abonnement maken, bijwerken en verwijderen. U gebruikt beheerfuncties, zoals toegangscontrole, vergrendelingen en tags, om uw resources na implementatie te beveiligen en te ordenen.

Leer hoe u het volgende doet:

- [Implementeer spotVM's met behulp van een resourcemanagersjabloon](../articles/virtual-machines/linux/spot-template.md).
- [Een Azure Virtual Machine implementeren met C# en een resourcemanagersjabloon](../articles/virtual-machines/windows/csharp-template.md).
- [Maak een virtuele Windows-machine op basis van een resourcemanagersjabloon.](../articles/virtual-machines/windows/ps-template.md)
- [Download de sjabloon voor een vm](../articles/virtual-machines/windows/download-template.md).
- [Maak een azure image builder-sjabloon](../articles/virtual-machines/linux/image-builder-json.md).

## <a name="next-steps"></a>Volgende stappen
Er zijn veel verschillende opties om infrastructuurautomatiseringstools in Azure te gebruiken. U heeft de vrijheid om de oplossing te gebruiken die het beste past bij uw behoeften en omgeving. Als u aan de slag wilt gaan en een aantal van de tools wilt uitproberen die zijn ingebouwd in Azure, ziet u hoe u de aanpassing van een [Linux-](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) of [Windows-vm](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) automatiseren.
