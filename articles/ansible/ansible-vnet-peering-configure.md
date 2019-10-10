---
title: Zelf studie-de peering van het virtuele Azure-netwerk configureren met behulp van Ansible
description: Meer informatie over het gebruik van Ansible voor het verbinden van virtuele netwerken met peering op virtueel netwerk.
keywords: ansible, azure, devops, bash, Playbook, netwerken, peering
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 180bdff48a2ace4dfee1d1cb10eb75a33d360f4c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241240"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Zelf studie: Azure Virtual Network-peering configureren met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

Met [peering voor virtuele netwerken (VNet)](/azure/virtual-network/virtual-network-peering-overview) kunt u naadloos verbinding maken tussen twee virtuele netwerken van Azure. Als de twee virtuele netwerken zijn gekoppeld, worden deze als één voor connectiviteits doeleinden weer gegeven. 

Verkeer wordt gerouteerd tussen virtuele machines in hetzelfde virtuele netwerk via privé-IP-adressen. Ook verkeer tussen virtuele machines in een gekoppeld virtueel netwerk wordt doorgestuurd via de micro soft backbone-infra structuur. Als gevolg hiervan kunnen Vm's in verschillende virtuele netwerken met elkaar communiceren.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Twee virtuele netwerken maken
> * De twee virtuele netwerken peeren
> * De peering tussen de twee netwerken verwijderen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Twee resource groepen maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Twee resource groepen maken 

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

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Maak een virtueel netwerk
- Een subnet binnen het virtuele netwerk maken

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

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Maak een virtueel netwerk
- Een subnet binnen het virtuele netwerk maken

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

## <a name="peer-the-two-virtual-networks"></a>De twee virtuele netwerken peeren

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Peering op virtueel netwerk initialiseren
- Peer twee eerder gemaakte virtuele netwerken

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

## <a name="delete-the-virtual-network-peering"></a>De peering voor het virtuele netwerk verwijderen

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- De peering tussen de twee eerder gemaakte virtuele netwerken verwijderen

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>De voor beeld-Playbook ophalen

Er zijn twee manieren om de volledige voorbeeld Playbook te verkrijgen:

- [Down load de Playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) en sla deze op `vnet_peering.yml`.
- Maak een nieuw bestand met de naam `vnet_peering.yml` en kopieer het naar de volgende inhoud:

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

## <a name="run-the-sample-playbook"></a>De voorbeeld Playbook uitvoeren

De voor beeld-Playbook-code in deze sectie wordt gebruikt om verschillende functies te testen die in deze zelf studie worden weer gegeven.

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Vervang in het gedeelte `vars` de tijdelijke aanduiding `{{ resource_group_name }}` door de naam van uw resource groep.

Voer de Playbook uit met behulp van de ansible-Playbook opdracht:

```bash
ansible-playbook vnet_peering.yml
```

Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

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

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- De twee eerder gemaakte resource groepen verwijderen

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

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Vervang de tijdelijke aanduiding `{{ resource_group_name-1 }}` door de naam van de eerste resource groep die is gemaakt.
- Vervang de tijdelijke aanduiding `{{ resource_group_name-2 }}` door de naam van de tweede resource groep die is gemaakt.
- Alle resources binnen de twee opgegeven resource groepen worden verwijderd.

Voer de Playbook uit met behulp van de ansible-Playbook opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)