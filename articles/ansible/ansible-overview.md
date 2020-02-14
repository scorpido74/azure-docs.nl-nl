---
title: Ansible gebruiken met Azure
description: Leer Ansible gebruiken om cloudinrichting, configuratiebeheer en toepassingsimplementaties te automatiseren.
keywords: ansible, azure, devops, overzicht, cloudinrichting, configuratiebeheer, implementatie van toepassing, ansible-modules, ansible-playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193562"
---
# <a name="using-ansible-with-azure"></a>Ansible gebruiken met Azure

[Ansible](https://www.ansible.com) is een open-sourceproduct waarmee het inrichten van clouds, configuratiebeheer en toepassingsimplementatie kan worden geautomatiseerd. U kunt Ansible gebruiken om virtuele machines, containers, netwerken en volledige cloudinfrastructuren in te richten. Daarnaast kunt u met Ansible de implementatie en configuratie van resources in uw omgeving automatiseren.

Dit artikel biedt een eenvoudig overzicht van de voordelen van het gebruik van Ansible in combinatie met Azure.

## <a name="ansible-playbooks"></a>Ansible-playbooks

Met [Ansible playbooks](https://docs.ansible.com/ansible/latest/playbooks.html) kunt u Ansible sturen om uw omgeving te configureren. Playbooks worden gecodeerd met behulp van YAML, zodat ze niet kunnen worden gelezen. De sectie zelf studies bevat veel voor beelden van het gebruik van playbooks voor het installeren en configureren van Azure-resources. 

## <a name="ansible-modules"></a>Ansible-modules

Ansible bevat een pakket [Ansible-modules](https://docs.ansible.com/ansible/latest/modules_by_category.html) die rechtstreeks op externe hosts of via [playbooks](https://docs.ansible.com/ansible/latest/playbooks.html)worden uitgevoerd. Gebruikers kunnen hun eigen modules maken. Modules worden gebruikt om systeem bronnen te beheren, zoals Services, pakketten of bestanden, of om systeem opdrachten uit te voeren.

Voor interactie met Azure-Services bevat Ansible een suite met [Ansible-Cloud modules](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Met deze modules kunt u uw infra structuur maken en organiseren in Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Bestaande workloads migreren naar Azure

Wanneer u Ansible gebruikt voor het definiëren van uw infra structuur, kunt u de Playbook van uw toepassing Toep assen zodat Azure uw omgeving automatisch kan schalen wanneer dat nodig is. 

## <a name="automate-cloud-native-application-in-azure"></a>Cloudeigen toepassingen automatiseren in Azure

Met Ansible kunt u cloud-apps in Azure automatiseren met behulp van Azure-microservices zoals [Azure Functions](https://azure.microsoft.com//services/functions/) en [Kubernetes on Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Implementaties met een dynamische voorraad beheren

Via de functie voor [dynamische voorraad](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) biedt Ansible de mogelijkheid om inventarisgegevens voor Azure-resources op te halen. Vervolgens kunt u uw bestaande Azure-implementaties labelen en deze gelabelde implementaties beheren via Ansible.

## <a name="additional-azure-marketplace-options"></a>Aanvullende opties voor Azure Marketplace

De [Ansible-Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) is een Azure Marketplace-installatie kopie van Red Hat. 

Ansible Tower is een op het web gebaseerde gebruikers interface en dash board voor Ansible met de volgende functies:

* Hiermee kunt u toegangs beheer op basis van rollen, taak planning en grafisch voorraad beheer definiëren. 
* Bevat een REST API en CLI, zodat u een Tower kunt invoegen in bestaande hulpprogram ma's en processen. 
* Ondersteunt real-time uitvoer van Playbook-uitvoeringen. 
* Versleutelt referenties, zoals Azure-en SSH-sleutels, zodat u taken kunt delegeren zonder referenties weer te geven.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Overzicht met Ansible-modules en -versies voor Azure

Ansible bevat een suite met modules die u kunt gebruiken bij het inrichten en configureren van Azure-resources. Deze resources omvatten virtuele machines, schaal sets, netwerk services en container Services. De [Ansible-matrix](./ansible-matrix.md) bevat de Ansible-modules voor Azure en de versies van de Ansible waarin ze worden geleverd.

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: de Ansible-oplossings sjabloon voor Azure implementeren op CentOS](./ansible-deploy-solution-template.md)
- [Snelstartgids: virtuele Linux-machines configureren in azure met behulp van Ansible](./ansible-install-configure.md)