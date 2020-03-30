---
title: Ansible gebruiken met Azure
description: Leer Ansible gebruiken om cloudinrichting, configuratiebeheer en toepassingsimplementaties te automatiseren.
keywords: ansible, azure, devops, overzicht, cloudinrichting, configuratiebeheer, implementatie van toepassing, ansible-modules, ansible-playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193562"
---
# <a name="using-ansible-with-azure"></a>Ansible gebruiken met Azure

[Ansible](https://www.ansible.com) is een open-sourceproduct waarmee het inrichten van clouds, configuratiebeheer en toepassingsimplementatie kan worden geautomatiseerd. U kunt Ansible gebruiken om virtuele machines, containers, netwerken en volledige cloudinfrastructuren in te richten. Met Ansible u ook de implementatie en configuratie van resources in uw omgeving automatiseren.

Dit artikel biedt een eenvoudig overzicht van de voordelen van het gebruik van Ansible in combinatie met Azure.

## <a name="ansible-playbooks"></a>Ansible-playbooks

[Met ansible playbooks](https://docs.ansible.com/ansible/latest/playbooks.html) u Ansible regisseren om uw omgeving te configureren. Playbooks zijn gecodeerd met behulp van YAML om te kunnen worden menselijk leesbaar. De sectie Zelfstudies geeft veel voorbeelden van het gebruik van playbooks om Azure-bronnen te installeren en te configureren. 

## <a name="ansible-modules"></a>Ansible-modules

Ansible bevat een suite van [Ansible modules](https://docs.ansible.com/ansible/latest/modules_by_category.html) die direct worden uitgevoerd op externe hosts of via [playbooks.](https://docs.ansible.com/ansible/latest/playbooks.html) Gebruikers kunnen hun eigen modules maken. Modules worden gebruikt om systeembronnen te beheren - zoals services, pakketten of bestanden - of om systeemopdrachten uit te voeren.

Voor interactie met Azure-services bevat Ansible een reeks [Ansible-cloudmodules.](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) Met deze modules u uw infrastructuur op Azure maken en orkestreren. 

## <a name="migrate-existing-workload-to-azure"></a>Bestaande workloads migreren naar Azure

Zodra u Ansible gebruikt om uw infrastructuur te definiëren, u de playbook van uw toepassing toepassen, zodat Azure uw omgeving automatisch kan schalen als dat nodig is. 

## <a name="automate-cloud-native-application-in-azure"></a>Cloudeigen toepassingen automatiseren in Azure

Met Ansible kunt u cloud-apps in Azure automatiseren met behulp van Azure-microservices zoals [Azure Functions](https://azure.microsoft.com//services/functions/) en [Kubernetes on Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Implementaties met een dynamische voorraad beheren

Via de functie voor [dynamische voorraad](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) biedt Ansible de mogelijkheid om inventarisgegevens voor Azure-resources op te halen. Vervolgens kunt u uw bestaande Azure-implementaties labelen en deze gelabelde implementaties beheren via Ansible.

## <a name="additional-azure-marketplace-options"></a>Aanvullende opties voor Azure Marketplace

De [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) is een Azure Marketplace-afbeelding van Red Hat. 

Ansible Tower is een webgebaseerde gebruikersinterface en dashboard voor Ansible met de volgende functies:

* Hiermee u op rollen gebaseerd toegangscontrole, taakplanning en grafisch voorraadbeheer definiëren. 
* Bevat een REST API en CLI, zodat u Tower invoegen in bestaande tools en processen. 
* Ondersteunt real-time uitvoer van playbook-uitvoeringen. 
* Versleutelt referenties - zoals Azure- en SSH-sleutels - zodat u taken delegeren zonder referenties bloot te leggen.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Overzicht met Ansible-modules en -versies voor Azure

Ansible bevat een reeks modules voor gebruik bij het inrichten en configureren van Azure-resources. Deze bronnen omvatten virtuele machines, schaalsets, netwerkservices en containerservices. De [Ansible matrix](./ansible-matrix.md) bevat de Ansible-modules voor Azure en de Ansible-versies waarin ze worden verzonden.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: de ansible-oplossingssjabloon voor Azure implementeren in CentOS](./ansible-deploy-solution-template.md)
- [Snelstart: Linux virtuele machines configureren in Azure met Ansible](./ansible-install-configure.md)