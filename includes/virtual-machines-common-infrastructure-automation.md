---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 525eba8a5a4e891526eb32a24287ea3887ee6743
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474193"
---
Als u wilt maken en beheren van virtuele Azure-machines (VM's) op een consistente manier op schaal, een vorm van automatisering doorgaans gewenst. Er zijn veel hulpprogramma's en oplossingen waarmee u kunt de volledige Azure-infrastructuur-implementatie en beheer van levenscyclus automatiseren. In dit artikel worden enkele van de infrastructuur voor automation-hulpprogramma's die u in Azure gebruiken kunt. Deze hulpprogramma's wordt vaak aanpassen aan een van de volgende methoden:

- De configuratie van VM's automatiseren
    - Hulpprogram ma's zijn [Ansible](#ansible), [chef](#chef)en [Puppet](#puppet).
    - Hulpprogram ma's die specifiek zijn voor VM-aanpassing zijn onder andere [Cloud-init](#cloud-init) voor Linux Vm's, [Power shell desired state Configuration (DSC)](#powershell-dsc)en de [aangepaste script extensie voor Azure](#azure-custom-script-extension) voor alle Azure-vm's.
 
- Geautomatiseerd beheer van infrastructuur
    - Hulpprogram ma's [voor het](#packer) automatiseren van aangepaste VM-installatie kopieën en [terraform](#terraform) voor het automatiseren van het proces voor het maken van de infra structuur.
    - [Azure Automation](#azure-automation) kunt acties uitvoeren in uw Azure-en on-premises infra structuur.

- Implementatie van toepassingen en -levering automatiseren
    - Voor beelden hiervan zijn [Azure DevOps Services](#azure-devops-services) en [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) is een automatiserings engine voor configuratie beheer, het maken van vm's of het implementeren van toepassingen. Ansible maakt gebruik van een model zonder agent, meestal met SSH-sleutels, om te verifiëren en beheren van doelmachines. Configuratietaken zijn gedefinieerd in playbooks, met een aantal Ansible-modules die beschikbaar zijn voor het uitvoeren van specifieke taken. Zie [How Ansible Works](https://www.ansible.com/how-ansible-works)(Engelstalig) voor meer informatie.

Leer hoe u het volgende doet:

- [Ansible op Linux installeren en configureren voor gebruik met Azure](../articles/ansible/ansible-install-configure.md).
- [Maak een virtuele Linux-machine](../articles/ansible/ansible-create-vm.md).
- [Een virtuele Linux-machine beheren](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) is een automatiserings platform waarmee u kunt bepalen hoe uw infra structuur is geconfigureerd, geïmplementeerd en beheerd. Extra onderdelen opgenomen Chef, Habitat voor automatisering van de levenscyclus van toepassingen in plaats van de infrastructuur en Chef inspecties waarmee het automatiseren van naleving van beveiliging en beleid te voldoen. Chef-Clients worden geïnstalleerd op de doelmachines, met een of meer centrale Chef-Servers die opslaan en beheren van de configuraties. Zie [een overzicht van chef](https://docs.chef.io/chef_overview.html)voor meer informatie.

Leer hoe u het volgende doet:

- [Implementeer chef automatiseren vanuit Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installeer chef in Windows en maak Azure-vm's](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) is een platform voor automatisering op bedrijfs niveau waarmee het leverings-en implementatie proces van de toepassing wordt afgehandeld. Agents zijn geïnstalleerd op de doelmachines om toe te staan Puppet Master om uit te voeren van de manifesten die de gewenste configuratie van de Azure-infrastructuur definieert en virtuele machines. Puppet kunt integreren met andere oplossingen, zoals Jenkins en GitHub voor een verbeterde devops-werkstroom. Zie [How Puppet Works](https://puppet.com/products/how-puppet-works)(Engelstalig) voor meer informatie.

Leer hoe u het volgende doet:

- [Implementeer Puppet vanuit Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat cloud-init wordt aangeroepen tijdens het opstartproces, zijn er geen extra stappen of agents vereist om toe te passen van uw configuratie.  Zie de [Cloud-init-documentatie site](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)voor meer informatie over het correct format teren van uw `#cloud-config`-bestanden.  `#cloud-config`-bestanden zijn tekst bestanden die worden gecodeerd in base64.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

We zijn actief werkt met onze onderschreven Linux-distributie partners om cloud-init ingeschakeld installatiekopieën die beschikbaar zijn in de Azure marketplace. Deze installatiekopieën maken uw cloud-init-implementaties en configuraties naadloos werken met virtuele machines en virtuele-machineschaalsets. Meer informatie over cloud-init op Azure:

- [Cloud-init-ondersteuning voor virtuele Linux-machines in azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Probeer een zelf studie over automatische VM-configuratie met behulp van Cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Power shell desired state Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheer platform voor het definiëren van de configuratie van doel computers. DSC kan ook worden gebruikt op Linux via de [Omi-server (open Management Infrastructure)](https://collaboration.opengroup.org/omi/).

DSC-configuraties definiëren wat u wilt installeren op een computer en het configureren van de host. Een engine voor de lokale Configuration Manager (LCM) wordt uitgevoerd op elk doelknooppunt waarmee aangevraagde acties op basis van gepushte configuraties worden verwerkt. Een pull-server is een webservice die wordt uitgevoerd op een centrale host voor het opslaan van de DSC-configuraties en de bijbehorende resources. De pull-server communiceert met de LCM-engine op de doelhost voor opgeven van de vereiste configuraties en rapporteren van naleving.

Leer hoe u het volgende doet:

- [Maak een basis DSC-configuratie](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Een DSC-pull-server configureren](/powershell/scripting/dsc/pull-server/pullserver).
- [Gebruik DSC voor Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Aangepaste Scriptextensie voor Azure
De aangepaste script extensie van Azure voor [Linux](../articles/virtual-machines/linux/extensions-customscript.md) of [Windows](../articles/virtual-machines/windows/extensions-customscript.md) downloadt en voert scripts uit op Azure vm's. U kunt de extensie kunt gebruiken wanneer u een virtuele machine maakt, of elk gewenst moment na de virtuele machine gebruikt wordt. 

Scripts kunnen worden gedownload vanuit Azure storage of op een openbare locatie, zoals een GitHub-opslagplaats. Met de extensie voor aangepaste scripts, kunt u scripts schrijven in elke taal die wordt uitgevoerd op de bron-VM. Deze scripts kunnen worden gebruikt om toepassingen installeren of de virtuele machine naar wens configureren. Als u wilt beveiligen referenties, kan gevoelige gegevens zoals wachtwoorden worden opgeslagen in een beveiligde configuratie. Deze referenties worden alleen ontsleuteld binnen de virtuele machine.

Leer hoe u het volgende doet:

- [Maak een virtuele Linux-machine met de Azure CLI en gebruik de aangepaste script extensie](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Maak een Windows-VM met Azure PowerShell en gebruik de aangepaste script extensie](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
De [verpakker](https://www.packer.io) automatiseert het bouw proces wanneer u een aangepaste VM-installatie kopie maakt in Azure. U Packer gebruiken voor het definiëren van het besturingssysteem en na configuratie scripts uitvoeren die de virtuele machine voor uw specifieke behoeften aanpassen. Wanneer geconfigureerd, wordt de virtuele machine vervolgens vastgelegd als een installatiekopie van een beheerde schijf. Packer automatiseert het proces voor het maken van de bron VM-, netwerk-en opslagbronnen, configuratiescripts uitvoeren en vervolgens de VM-installatiekopie te maken.

Leer hoe u het volgende doet:

- [Gebruik Packer voor het maken van een installatie kopie van een virtuele Linux-machine in azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Gebruik Packer om een Windows VM-installatie kopie te maken in azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) is een Automation-hulp programma waarmee u een volledige Azure-infra structuur kunt definiëren en maken met een taal voor de indeling van één sjabloon-de HashiCorp-configuratie taal (HCL). Met Terraform definieert u de sjablonen die het proces voor het maken van netwerk-, opslag- en VM-resources voor een bepaalde toepassingsoplossing automatiseren. U kunt uw bestaande Terraform-sjablonen voor andere platforms met Azure gebruiken om te zorgen voor consistentie en vereenvoudigen van de Infrastructuurimplementatie hoeft te converteren naar een Azure Resource Manager-sjabloon.

Leer hoe u het volgende doet:

- [Installeer en configureer terraform met Azure](../articles/terraform/terraform-install-configure.md).
- [Een Azure-infra structuur maken met terraform](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) gebruikt runbooks voor het verwerken van een reeks taken op de vm's die u wilt richten. Azure Automation wordt gebruikt voor het beheren van bestaande VM's in plaats van te maken van een infrastructuur. Azure Automation kunt uitvoeren voor zowel Linux en Windows-VM's, evenals on-premises virtuele of fysieke computers met een hybrid runbook worker. Runbooks kunnen worden opgeslagen in een opslagplaats voor bronbeheer, zoals GitHub. Deze runbooks kunnen vervolgens handmatig uitvoeren of op een ingesteld schema.

Azure Automation biedt ook een Desired State Configuration (DSC)-service waarmee u kunt het maken van definities voor de wijze waarop een bepaalde set van virtuele machines moet zijn geconfigureerd. Vervolgens zorgt de DSC dat de vereiste configuratie is toegepast en de virtuele machine consistent blijft. Azure Automation DSC wordt uitgevoerd op Windows en Linux-computers.

Leer hoe u het volgende doet:

- [Een Power shell-Runbook maken](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Gebruik Hybrid Runbook worker om on-premises resources te beheren](../articles/automation/automation-hybrid-runbook-worker.md).
- [Gebruik Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) is een suite met hulpprogram ma's die u helpen bij het delen en volgen van code, het gebruik van geautomatiseerde builds en het maken van een volledige, doorlopende CI/cd-pijp lijn voor integratie en ontwikkeling. Azure DevOps-Services kan worden geïntegreerd met Visual Studio en andere editors voor het vereenvoudigen van gebruik. Azure DevOps-Services kan ook maken en configureren van Azure-VM's en code te implementeren.

Meer informatie over:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) is een continue integratie server die helpt toepassingen te implementeren en testen en om automatische pijp lijnen te maken voor het leveren van code. Er zijn honderden van invoegtoepassingen uit te breiden het Jenkins-kernplatform en u kunt ook worden geïntegreerd met veel andere producten en oplossingen op basis van webhooks. U kunt handmatig Jenkins installeren op een Azure-VM, Jenkins uit binnen een Docker-container uitvoeren of een vooraf gemaakte Azure Marketplace-installatiekopie gebruiken.

Leer hoe u het volgende doet:

- [Maak een ontwikkel infrastructuur op een virtuele Linux-machine in azure met Jenkins, github en docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Volgende stappen
Er zijn veel verschillende opties voor het gebruik van hulpprogramma's voor werkstroomautomatisering infrastructuur in Azure. Hebt u de vrijheid om te gebruiken van de oplossing die het beste past bij uw behoeften en de omgeving. Zie de aanpassing van een [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) -of [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) -VM automatiseren om aan de slag te gaan en enkele van de hulpprogram ma's in azure uit te proberen.
