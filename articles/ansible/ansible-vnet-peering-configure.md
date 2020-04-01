---
title: Zelfstudie - Azure virtual network peering configureren met Ansible
description: Meer informatie over het gebruik van Ansible om virtuele netwerken te verbinden met virtuele netwerkpeering.
keywords: ansible, azure, devops, bash, playbook, netwerken, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155724"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Zelfstudie: Azure virtual network peering configureren met Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Met Virtual Network (VNet)](/azure/virtual-network/virtual-network-peering-overview) u twee virtuele Azure-netwerken naadloos met elkaar verbinden. Eenmaal peered, de twee virtuele netwerken verschijnen als een voor connectiviteit doeleinden. 

Verkeer wordt omgeleid tussen VM's in hetzelfde virtuele netwerk via privé-IP-adressen. Op dezelfde manier wordt het verkeer tussen VM's in een peered virtueel netwerk omgeleid via de Microsoft-backbone-infrastructuur. Hierdoor kunnen VM's in verschillende virtuele netwerken met elkaar communiceren.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Twee virtuele netwerken maken
> * Peer de twee virtuele netwerken
> * Het peering tussen de twee netwerken verwijderen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Twee resourcegroepen maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Twee resourcegroepen maken 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Het eerste virtuele netwerk maken

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Een virtueel netwerk maken
- Een subnet maken binnen het virtuele netwerk

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Het tweede virtuele netwerk maken

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Een virtueel netwerk maken
- Een subnet maken binnen het virtuele netwerk

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Peer de twee virtuele netwerken

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Tuurwerk van virtuele netwerken
- Peer twee eerder gemaakt virtuele netwerken

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Het virtuele netwerk peering verwijderen

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Verwijder het peering tussen de twee eerder gemaakte virtuele netwerken

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Download het voorbeeld van het draaiboek

Er zijn twee manieren om het volledige voorbeeld playbook te krijgen:

- [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) en `vnet_peering.yml`sla het op in .
- Maak een nieuw `vnet_peering.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Het voorbeeldvan het voorbeelddraaiboek uitvoeren

De voorbeeld playbook code in deze sectie wordt gebruikt om verschillende functies te testen die tijdens deze zelfstudie worden weergegeven.

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Vervang `vars` in de `{{ resource_group_name }}` sectie de tijdelijke aanduiding door de naam van uw resourcegroep.

Voer het draaiboek uit met de opdracht ansible-playbook:

```bash
ansible-playbook vnet_peering.yml
```

Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- De twee eerder gemaakte resourcesgroepen verwijderen

Sla het volgende playbook op als `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Vervang `{{ resource_group_name-1 }}` de tijdelijke aanduiding door de naam van de eerste resourcegroep die is gemaakt.
- Vervang `{{ resource_group_name-2 }}` de tijdelijke aanduiding door de naam van de tweede resourcegroep die is gemaakt.
- Alle bronnen binnen de twee opgegeven resourcegroepen worden verwijderd.

Voer het draaiboek uit met de opdracht ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)