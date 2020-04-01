---
title: Zelfstudie - Apps schalen in Azure App Service met Ansible
description: Meer informatie over het opschalen van een app in Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, web-app, schalen, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155911"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Zelfstudie: Apps schalen in Azure App Service met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Gegevens van een bestaand App Service-plan ophalen
> * Het App-serviceplan opschalen naar S2 met drie werknemers

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service-app** - Als u geen Azure App Service-app hebt, [configureert u een app in Azure App Service met Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Een app opschalen

Er zijn twee werkstromen voor schalen: *opschalen* en *uitschalen.*

**Opschalen:** Opschalen betekent meer middelen verwerven. Deze bronnen omvatten CPU, geheugen, schijfruimte, VM's en meer. U schaalt een app op door de prijscategorie van het App Service-abonnement waartoe de app behoort, te wijzigen. 
**Uitschalen:** Het uitschalen betekent het aantal VM-exemplaren dat uw app uitvoert verhogen. Afhankelijk van de prijscategorie app-service-abonnement u uitschalen naar maar liefst 20 exemplaren. [Met automatisch schalen](/azure/azure-monitor/platform/autoscale-get-started) u het aantal instance's automatisch schalen op basis van vooraf gedefinieerde regels en schema's.

De playbookcode in deze sectie definieert de volgende bewerking:

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook webapp_scaleup.yml
```

Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

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