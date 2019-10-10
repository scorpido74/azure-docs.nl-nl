---
title: Zelf studie-Azure Cosmos DB accounts configureren met Ansible
description: Meer informatie over het gebruik van Ansible voor het maken en configureren van een Azure Cosmos DB
keywords: ansible, azure, devops, bash, Playbook, Cosmo DB, data base
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 82cb29cfe3aeb7b6ca43fceca4c900b2eeb148c2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242076"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>Zelf studie: Azure Cosmos DB accounts configureren met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure Cosmos DB](/azure/cosmos-db/) is een database service die ondersteuning biedt voor verschillende database typen. Deze database typen zijn onder andere document, sleutel waarde, brede kolom en grafiek. Met Ansible kunt u de implementatie en configuratie van resources in uw omgeving automatiseren.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een account maken
> * De account sleutels ophalen
> * Het account verwijderen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>Een wille keurige achtervoegsel maken

Het Playbook-voorbeeld fragment maakt een wille keurige achtervoegsel. De achtervoegsel wordt gebruikt als onderdeel van de naam van het Azure Cosmos DB-account.

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>Een resourcegroep maken 

Het Playbook-voorbeeld fragment maakt een Azure-resource groep. Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>Virtueel netwerk en subnet maken

Met de volgende code maakt u een virtueel netwerk en een subnet voor het Azure Cosmos DB account:

```yml
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name }}"
      address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
      dns_servers:
        - 127.0.0.1
        - 127.0.0.3

  - name: Add subnet
    azure_rm_subnet:
      name: "{{ subnet_name }}"
      virtual_network_name: "{{ vnet_name }}"
      resource_group: "{{ resource_group }}"
      address_prefix_cidr: "10.1.0.0/24"
```

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Met de volgende code wordt het Cosmos DB-account gemaakt:

```yml
  - name: Create instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      location: eastus
      kind: global_document_db
      geo_rep_locations:
        - name: eastus
          failover_priority: 0
        - name: westus
          failover_priority: 1
      database_account_offer_type: Standard
      is_virtual_network_filter_enabled: yes
      virtual_network_rules:
        - subnet:
            resource_group: "{{ resource_group }}"
            virtual_network_name: "{{ vnet_name }}"
            subnet_name: "{{ subnet_name }}"
          ignore_missing_vnet_service_endpoint: yes
      enable_automatic_failover: yes
```

Het kan enkele minuten duren voordat het account is gemaakt.

## <a name="retrieve-the-keys"></a>De sleutels ophalen

Met de volgende code worden de sleutels opgehaald die u in uw app kunt gebruiken.

```yml
  - name: Get Cosmos DB Account facts with keys
    azure_rm_cosmosdbaccount_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      retrieve_keys: all
    register: output

  - name: Display Cosmos DB Acccount facts output
    debug:
      var: output
```

## <a name="delete-the-azure-cosmos-db-account"></a>Het Azure Cosmos DB account verwijderen

Ten slotte ziet u hoe een Azure Cosmos DB account moet worden verwijderd in het laatste fragment.

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>De voor beeld-Playbook ophalen

Er zijn twee manieren om de volledige voorbeeld Playbook te verkrijgen:
- [Down load de Playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml) en sla deze op `cosmosdb.yml`.
- Maak een nieuw bestand met de naam `cosmosdb.yml` en kopieer het naar de volgende inhoud:

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
#  roles:
#    - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cosmosdbaccount_name: cosmos{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name }}"
        address_prefixes_cidr:
          - 10.1.0.0/16
          - 172.100.0.0/16
        dns_servers:
          - 127.0.0.1
          - 127.0.0.3

    - name: Add subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"

    - name: Create instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        location: eastus
        kind: global_document_db
        geo_rep_locations:
          - name: eastus
            failover_priority: 0
          - name: westus
            failover_priority: 1
        database_account_offer_type: Standard
        is_virtual_network_filter_enabled: yes
        virtual_network_rules:
          - subnet:
              resource_group: "{{ resource_group }}"
              virtual_network_name: "{{ vnet_name }}"
              subnet_name: "{{ subnet_name }}"
            ignore_missing_vnet_service_endpoint: yes
        enable_automatic_failover: yes

    - name: Get Cosmos DB Account facts with keys
      azure_rm_cosmosdbaccount_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        retrieve_keys: all
      register: output

    - name: Display Cosmos DB Account facts output
      debug:
        var: output

    - name: Delete instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>De voorbeeld Playbook uitvoeren

In deze sectie voert u de Playbook uit om verschillende functies te testen die in dit artikel worden weer gegeven.

Voordat u de Playbook uitvoert, moet u de volgende wijzigingen aanbrengen:
- Vervang in het gedeelte `vars` de tijdelijke aanduiding `{{ resource_group_name }}` door de naam van uw resource groep.
- Zorg ervoor dat de ' cosmosdbaccount_name alleen kleine letters bevat en wereld wijd uniek is.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla de volgende code op als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)