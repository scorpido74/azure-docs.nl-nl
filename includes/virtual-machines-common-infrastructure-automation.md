---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 1a18812c6447266d34f2c8aa1ccff8984f68570a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008830"
---
Als u wilt maken en beheren van virtuele Azure-machines (VM's) op een consistente manier op schaal, een vorm van automatisering doorgaans gewenst. Er zijn veel hulpprogramma's en oplossingen waarmee u kunt de volledige Azure-infrastructuur-implementatie en beheer van levenscyclus automatiseren. In dit artikel worden enkele van de infrastructuur voor automation-hulpprogramma's die u in Azure gebruiken kunt. Deze hulpprogramma's wordt vaak aanpassen aan een van de volgende methoden:

- De configuratie van VM's automatiseren
    - Hulpprogramma's omvatten [Ansible](#ansible), [Chef](#chef), en [Puppet](#puppet).
    - Hulpprogramma's die specifiek zijn voor VM-aanpassingen omvatten [cloud-init](#cloud-init) voor virtuele Linux-machines [PowerShell Desired State Configuration (DSC)](#powershell-dsc), en de [Azure Custom Script Extension](#azure-custom-script-extension) voor alle Azure VM's.
 
- Geautomatiseerd beheer van infrastructuur
    - Hulpprogramma's omvatten [Packer](#packer) voor het automatiseren van aangepaste VM image te maken, en [Terraform](#terraform) voor het automatiseren van de procedure voor het maken van de infrastructuur.
    - [Azure Automation](#azure-automation) acties kunnen uitvoeren in uw Azure en on-premises infrastructuur.

- Implementatie van toepassingen en -levering automatiseren
    - Voorbeelden zijn onder meer [Azure DevOps Services](#azure-devops-services) en [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) is een automatiseringsengine voor voor Configuratiebeheer, het maken van VM of implementatie van de toepassing. Ansible maakt gebruik van een model zonder agent, meestal met SSH-sleutels, om te verifiëren en beheren van doelmachines. Configuratietaken zijn gedefinieerd in playbooks, met een aantal Ansible-modules die beschikbaar zijn voor het uitvoeren van specifieke taken. Zie voor meer informatie, [hoe Ansible werkt](https://www.ansible.com/how-ansible-works).

Leer hoe u het volgende doet:

- [Ansible installeren en configureren op Linux voor gebruik met Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Maken van een virtuele Linux-machine](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Een virtuele Linux-machine beheren](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) is een platform voor toepassingsautomatisering waarmee u kunt bepalen hoe uw infrastructuur is geconfigureerd, geïmplementeerd en beheerd. Extra onderdelen opgenomen Chef, Habitat voor automatisering van de levenscyclus van toepassingen in plaats van de infrastructuur en Chef inspecties waarmee het automatiseren van naleving van beveiliging en beleid te voldoen. Chef-Clients worden geïnstalleerd op de doelmachines, met een of meer centrale Chef-Servers die opslaan en beheren van de configuraties. Zie voor meer informatie, [een overzicht van Chef](https://docs.chef.io/chef_overview.html).

Leer hoe u het volgende doet:

- [Chef implementeren in Azure Marketplace automatiseren](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Chef in Windows installeren en maken van virtuele Azure-machines](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) is een automatiseringsplatform van enterprise-ready die verantwoordelijk is voor het toepassingsproces levering en implementatie. Agents zijn geïnstalleerd op de doelmachines om toe te staan Puppet Master om uit te voeren van de manifesten die de gewenste configuratie van de Azure-infrastructuur definieert en virtuele machines. Puppet kunt integreren met andere oplossingen, zoals Jenkins en GitHub voor een verbeterde devops-werkstroom. Zie voor meer informatie, [hoe Puppet werkt](https://puppet.com/products/how-puppet-works).

Leer hoe u het volgende doet:

- [Puppet in Azure Marketplace implementeren](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat cloud-init wordt aangeroepen tijdens het opstartproces, zijn er geen extra stappen of agents vereist om toe te passen van uw configuratie.  Voor meer informatie over het juiste indeling uw `#cloud-config` bestanden, Zie de [cloud-init-documentatiesite](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` bestanden zijn tekstbestanden die zijn gecodeerd in base64.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

We zijn actief werkt met onze onderschreven Linux-distributie partners om cloud-init ingeschakeld installatiekopieën die beschikbaar zijn in de Azure marketplace. Deze installatiekopieën maken uw cloud-init-implementaties en configuraties naadloos werken met virtuele machines en virtuele-machineschaalsets. Meer informatie over cloud-init op Azure:

- [Cloud-init-ondersteuning voor virtuele Linux-machines in Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Probeer een zelfstudie over automatische VM-configuratie met behulp van cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheerplatform voor het definiëren van de configuratie van doelmachines. DSC kan ook worden gebruikt op Linux via de [Open Management Infrastructure (OMI) server](https://collaboration.opengroup.org/omi/).

DSC-configuraties definiëren wat u wilt installeren op een computer en het configureren van de host. Een engine voor de lokale Configuration Manager (LCM) wordt uitgevoerd op elk doelknooppunt waarmee aangevraagde acties op basis van gepushte configuraties worden verwerkt. Een pull-server is een webservice die wordt uitgevoerd op een centrale host voor het opslaan van de DSC-configuraties en de bijbehorende resources. De pull-server communiceert met de LCM-engine op de doelhost voor opgeven van de vereiste configuraties en rapporteren van naleving.

Leer hoe u het volgende doet:

- [Maak een eenvoudige DSC-configuratie](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart).
- [Een DSC-pull-server configureren voor](/powershell/scripting/dsc/pull-server/pullserver).
- [Gebruik van DSC voor Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Aangepaste Scriptextensie voor Azure
De Azure Custom Script Extension voor [Linux](../articles/virtual-machines/linux/extensions-customscript.md) of [Windows](../articles/virtual-machines/windows/extensions-customscript.md) worden gedownload en scripts uitgevoerd op Azure Virtual machines. U kunt de extensie kunt gebruiken wanneer u een virtuele machine maakt, of elk gewenst moment na de virtuele machine gebruikt wordt. 

Scripts kunnen worden gedownload vanuit Azure storage of op een openbare locatie, zoals een GitHub-opslagplaats. Met de extensie voor aangepaste scripts, kunt u scripts schrijven in elke taal die wordt uitgevoerd op de bron-VM. Deze scripts kunnen worden gebruikt om toepassingen installeren of de virtuele machine naar wens configureren. Als u wilt beveiligen referenties, kan gevoelige gegevens zoals wachtwoorden worden opgeslagen in een beveiligde configuratie. Deze referenties worden alleen ontsleuteld binnen de virtuele machine.

Leer hoe u het volgende doet:

- [Een Linux-VM maken met de Azure CLI en gebruiken van de aangepaste Scriptextensie](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Een virtuele Windows-machine maken met Azure PowerShell en gebruik de aangepaste Scriptextensie](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiseert het bouwproces wanneer u een aangepaste VM-installatiekopie in Azure maakt. U Packer gebruiken voor het definiëren van het besturingssysteem en na configuratie scripts uitvoeren die de virtuele machine voor uw specifieke behoeften aanpassen. Wanneer geconfigureerd, wordt de virtuele machine vervolgens vastgelegd als een installatiekopie van een beheerde schijf. Packer automatiseert het proces voor het maken van de bron VM-, netwerk-en opslagbronnen, configuratiescripts uitvoeren en vervolgens de VM-installatiekopie te maken.

Leer hoe u het volgende doet:

- [Packer gebruiken voor het maken van een Linux VM-installatiekopie in Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Packer gebruiken voor het maken van een Windows VM-installatiekopie in Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) is een automation-hulpprogramma dat u kunt definiëren en een volledige Azure-infrastructuur maken met een enkele sjabloon indelingstaal - HashiCorp configuratie taal (HCL). Met Terraform definieert u de sjablonen die het proces voor het maken van netwerk-, opslag- en VM-resources voor een bepaalde toepassingsoplossing automatiseren. U kunt uw bestaande Terraform-sjablonen voor andere platforms met Azure gebruiken om te zorgen voor consistentie en vereenvoudigen van de Infrastructuurimplementatie hoeft te converteren naar een Azure Resource Manager-sjabloon.

Leer hoe u het volgende doet:

- [Terraform installeren en configureren met Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Maken van een Azure-infrastructuur met Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) worden runbooks gebruikt voor het verwerken van een set taken op de virtuele machines is gericht. Azure Automation wordt gebruikt voor het beheren van bestaande VM's in plaats van te maken van een infrastructuur. Azure Automation kunt uitvoeren voor zowel Linux en Windows-VM's, evenals on-premises virtuele of fysieke computers met een hybrid runbook worker. Runbooks kunnen worden opgeslagen in een opslagplaats voor bronbeheer, zoals GitHub. Deze runbooks kunnen vervolgens handmatig uitvoeren of op een ingesteld schema.

Azure Automation biedt ook een Desired State Configuration (DSC)-service waarmee u kunt het maken van definities voor de wijze waarop een bepaalde set van virtuele machines moet zijn geconfigureerd. Vervolgens zorgt de DSC dat de vereiste configuratie is toegepast en de virtuele machine consistent blijft. Azure Automation DSC wordt uitgevoerd op Windows en Linux-computers.

Leer hoe u het volgende doet:

- [Een PowerShell-runbook maken](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Hybrid Runbook Worker gebruiken voor het beheren van on-premises bronnen](../articles/automation/automation-hybrid-runbook-worker.md).
- [Gebruik van Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps-Services](https://www.visualstudio.com/team-services/) is een suite met hulpprogramma's waarmee u delen en bijhouden code, gebruikt u geautomatiseerde builds en een volledige continue integratie en (CI/CD)-ontwikkelingspijplijn te maken. Azure DevOps-Services kan worden geïntegreerd met Visual Studio en andere editors voor het vereenvoudigen van gebruik. Azure DevOps-Services kan ook maken en configureren van Azure-VM's en code te implementeren.

Meer informatie over:

- [Azure DevOps-Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) is een doorlopende integratie die helpt bij het implementeren en testen van toepassingen en geautomatiseerde pijplijnen voor de levering van code maken. Er zijn honderden van invoegtoepassingen uit te breiden het Jenkins-kernplatform en u kunt ook worden geïntegreerd met veel andere producten en oplossingen op basis van webhooks. U kunt handmatig Jenkins installeren op een Azure-VM, Jenkins uit binnen een Docker-container uitvoeren of een vooraf gemaakte Azure Marketplace-installatiekopie gebruiken.

Leer hoe u het volgende doet:

- [Een infrastructuur voor ontwikkeling maken op een Linux-VM in Azure met Jenkins, GitHub en Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Volgende stappen
Er zijn veel verschillende opties voor het gebruik van hulpprogramma's voor werkstroomautomatisering infrastructuur in Azure. Hebt u de vrijheid om te gebruiken van de oplossing die het beste past bij uw behoeften en de omgeving. Aan de slag en probeer het aantal van de hulpprogramma's ingebouwd in Azure, informatie over het automatiseren van het aanpassen van een [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) of [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
