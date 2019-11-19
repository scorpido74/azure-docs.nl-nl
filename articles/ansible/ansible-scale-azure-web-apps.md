---
title: 'Zelf studie: apps schalen in Azure App Service met behulp van Ansible'
description: Meer informatie over het opschalen van een app in Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, web-app, schalen, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155911"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Zelf studie: apps schalen in Azure App Service met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Gegevens van een bestaand App Service-plan ophalen
> * Het App Service plan omhoog schalen naar S2 met drie werk rollen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure app service-app** : als u geen Azure app service app hebt, [configureert u een app in azure app service met behulp van Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Een app omhoog schalen

Er zijn twee werk stromen voor schalen: *Omhoog schalen* en *uitschalen*.

**Omhoog schalen:** Zo kunt u meer resources opschalen. Deze resources zijn onder andere CPU, geheugen, schijf ruimte, Vm's en meer. U kunt een app omhoog schalen door de prijs categorie te wijzigen van het App Service plan waartoe de app behoort. 
**Uitschalen:** Als u wilt uitschalen, kunt u het aantal VM-exemplaren verhogen waarop uw app wordt uitgevoerd. Afhankelijk van de prijs categorie van uw App Service-abonnement kunt u uitschalen tot Maxi maal 20 exemplaren. Automatisch [schalen](/azure/azure-monitor/platform/autoscale-get-started) biedt u de mogelijkheid om het aantal exemplaren te schalen op basis van vooraf gedefinieerde regels en schema's.

De Playbook-code in deze sectie definieert de volgende bewerking:

* Gegevens van een bestaand App Service-plan ophalen
* Het App service-plan bijwerken naar S2 met drie workers

Sla het volgende playbook op als `webapp_scaleup.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook webapp_scaleup.yml
```

Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)