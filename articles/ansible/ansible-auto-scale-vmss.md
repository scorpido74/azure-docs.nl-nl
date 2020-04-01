---
title: Zelfstudie - Setsets voor virtuele machineschalen automatisch schalen in Azure met Ansible
description: Meer informatie over het gebruik van Ansible om virtuele machineschaalsets te schalen met automatisch schalen in Azure
keywords: ansible, azure, devops, bash, playbook, schalen, automatische schaalaanpassing, virtuele machine, virtuele-machineschaalset, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156816"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Zelfstudie: Seten voor virtuele machineschalen automatisch schalen in Azure met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

De functie om het aantal VM-exemplaren automatisch aan te passen, wordt [automatisch schalen](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)genoemd. Het voordeel van autoscale is dat het de beheeroverhead vermindert om de prestaties van uw toepassing te controleren en te optimaliseren. Autoscale kan worden geconfigureerd in antwoord op de vraag of volgens een gedefinieerd schema. Met Ansible u de regels voor automatische schaal opgeven die de acceptabele prestaties voor een positieve klantervaring definiëren.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een profiel voor automatisch schalen definiëren
> * Autoschaal op basis van een terugkerend schema
> * Autoscale op basis van app-prestaties
> * Informatie over instellingen voor automatisch schalen ophalen 
> * Een instelling voor automatisch schalen uitschakelen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Autoschaal op basis van een planning

Als u automatisch schalen wilt inschakelen voor een schaalset, moet u eerst een profiel voor automatisch schalen definiëren. In dit profiel worden de minimum-, maximum- en standaardcapaciteit ingesteld voor de schaalset. Met behulp van deze limieten kunt u de kosten in de hand houden doordat er niet steeds VM-exemplaren hoeven te worden gemaakt, en kunt u aanvaardbare prestaties realiseren met een minimum aantal exemplaren die altijd kunnen worden ingeschaald. 

Met Ansible u uw schaalsets schalen op een specifieke datum of terugkerend schema.

De playbook code in deze sectie verhoogt het aantal VM-exemplaren tot drie om 10:00 elke maandag.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Autoschaal op basis van prestatiegegevens

Als de vraag voor een toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. Met Ansible u bepalen welke statistieken u moet controleren, zoals CPU-gebruik, schijfgebruik en app-laadtijd. U schalen in en schalen in schaalsets op basis van prestatiemetrische drempels, op basis van een terugkerend schema of op een bepaalde datum. 

De playbook code in deze sectie controleert de CPU-werkbelasting voor de vorige 10 minuten om 18:00 elke maandag. 

Op basis van de CPU-percentagestatistieken doet het draaiboek een van de volgende acties:

- Hiermee wordt het aantal VM-exemplaren uitgeschaald tot vier
- Schalen in het aantal VM-exemplaren naar één

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Informatie over instellingen voor automatisch schalen 

De playbook-code in `azure_rm_autoscale_facts` deze sectie gebruikt de module om de details van de instelling voor automatisch schalen op te halen.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Instellingen voor automatisch schalen uitschakelen

Er zijn twee manieren om instellingen voor automatisch schalen uit te schakelen. Een manier is `enabled` om `true` de `false`sleutel te veranderen van naar . De tweede manier is om de instelling te verwijderen.

De playbook-code in deze sectie verwijdert de instelling voor automatisch schalen. 

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Zelfstudie: Aangepaste afbeelding van Azure-waarden voor virtuele machines bijwerken met Ansible](./ansible-vmss-update-image.md)