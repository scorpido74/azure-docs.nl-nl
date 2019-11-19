---
title: 'Zelf studie: virtuele-machine schaal sets automatisch schalen in azure met behulp van Ansible'
description: Meer informatie over hoe u Ansible kunt gebruiken om schaal sets voor virtuele machines te schalen met automatisch schalen in azure
keywords: ansible, azure, devops, bash, playbook, schalen, automatische schaalaanpassing, virtuele machine, virtuele-machineschaalset, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156816"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Zelf studie: schaal sets voor virtuele machines automatisch schalen in azure met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

De functie van het automatisch aanpassen van het aantal VM-exemplaren wordt [AutoScale](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)genoemd. Het voor deel van automatisch schalen is dat het de beheer overhead vermindert om de prestaties van uw toepassing te controleren en te optimaliseren. Automatisch schalen kan worden geconfigureerd als reactie op de vraag of op basis van een gedefinieerd schema. Met Ansible kunt u de regels voor automatisch schalen opgeven waarmee de aanvaard bare prestaties voor een positieve klant ervaring worden gedefinieerd.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een profiel voor automatisch schalen definiëren
> * Automatisch schalen op basis van een terugkerend schema
> * Automatisch schalen op basis van de prestaties van de app
> * Informatie over instellingen voor automatisch schalen ophalen 
> * Een instelling voor automatisch schalen uitschakelen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatisch schalen op basis van een planning

Als u automatisch schalen wilt inschakelen voor een schaalset, moet u eerst een profiel voor automatisch schalen definiëren. In dit profiel worden de minimum-, maximum- en standaardcapaciteit ingesteld voor de schaalset. Met behulp van deze limieten kunt u de kosten in de hand houden doordat er niet steeds VM-exemplaren hoeven te worden gemaakt, en kunt u aanvaardbare prestaties realiseren met een minimum aantal exemplaren die altijd kunnen worden ingeschaald. 

Met Ansible kunt u uw schaal sets op een specifieke datum of een terugkerend schema schalen.

De Playbook-code in deze sectie verhoogt het aantal VM-exemplaren tot drie op 10:00 elke maandag.

Sla het volgende playbook op als `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatisch schalen op basis van prestatie gegevens

Als de vraag voor een toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. Met Ansible kunt u bepalen welke metrische gegevens moeten worden bewaakt, zoals CPU-gebruik, schijf gebruik en app-laad tijd. U kunt in schaal sets schalen en uitschalen op basis van de drempel waarden voor prestatie waarden, een terugkerend schema of een bepaalde datum. 

Met de Playbook-code in deze sectie wordt de CPU-belasting voor de afgelopen 10 minuten op 18:00 elke maandag gecontroleerd. 

Op basis van de metrische gegevens van het CPU-percentage voert de Playbook een van de volgende acties uit:

- Hiermee wordt het aantal VM-exemplaren naar vier geschaald
- Schaalt in het aantal VM-exemplaren naar een

Sla het volgende playbook op als `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Informatie over instellingen voor automatisch schalen ophalen 

In de Playbook-code in deze sectie wordt gebruikgemaakt van de module `azure_rm_autoscale_facts` om de details van de instelling voor automatisch schalen op te halen.

Sla het volgende playbook op als `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Instellingen voor automatisch schalen uitschakelen

Er zijn twee manieren om instellingen voor automatisch schalen uit te scha kelen. Een manier is om de `enabled` sleutel te wijzigen van `true` in `false`. De tweede manier is de instelling verwijderen.

De Playbook-code in deze sectie verwijdert de instelling voor automatisch schalen. 

Sla het volgende playbook op als `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Zelf studie: aangepaste installatie kopie van virtuele-machine schaal sets van Azure bijwerken met behulp van Ansible](./ansible-vmss-update-image.md)