---
title: Zelf studie-Azure-route tabellen configureren met Ansible
description: Meer informatie over het maken, beheren en verwijderen van Azure-route tabellen met behulp van Ansible. Meer informatie over het maken en verwijderen van routes.
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d1e44a98405bc1009f6f3d56d90fc1fd655d77d5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156491"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Zelf studie: Azure-route tabellen configureren met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u meer controle nodig hebt over de route ring van uw omgeving, kunt u een [route tabel](/azure/virtual-network/virtual-networks-udr-overview)maken. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Een route tabel maken een virtueel netwerk en subnet verbinden een route tabel koppelen aan een subnet een route tabel loskoppelen van een subnet de koppeling maken en verwijderen routes query a route tabel een route tabel verwijderen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Een routetabel maken

Met de Playbook-code in deze sectie maakt u een route tabel. Zie [Azure-limieten](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)voor meer informatie over route tabel limieten. 

Sla het volgende playbook op als `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

De Playbook-code in deze sectie:

* Hiermee maakt u een virtueel netwerk
* Hiermee maakt u een subnet in het virtuele netwerk
* Hiermee wordt een route tabel gekoppeld aan het subnet

Route tabellen zijn niet gekoppeld aan virtuele netwerken. Route tabellen worden in plaats daarvan gekoppeld aan het subnet van een virtueel netwerk.

De tabel virtueel netwerk en route moet naast elkaar bestaan op dezelfde Azure-locatie en hetzelfde abonnement.

Subnetten en route tabellen hebben een een-op-veel-relatie. Een subnet kan worden gedefinieerd zonder gekoppelde route tabel of één route tabel. Route tabellen kunnen worden gekoppeld aan geen, één of een groot aantal subnetten. 

Verkeer van het subnet wordt gerouteerd op basis van:

- routes die zijn gedefinieerd binnen route tabellen
- [standaard routes](/azure/virtual-network/virtual-networks-udr-overview#default)
- routes die worden door gegeven vanuit een on-premises netwerk

Het virtuele netwerk moet zijn verbonden met een virtuele Azure-netwerk gateway. De gateway kan ExpressRoute of VPN zijn als u BGP gebruikt met een VPN-gateway.

Sla het volgende playbook op als `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Een routetabel ontkoppelen van een subnet

Met de Playbook-code in deze sectie wordt een route tabel van een subnet ontkoppeld.

Als u een route tabel wilt ontkoppelen van een subnet, stelt u de `route_table` voor het subnet in op `None`. 

Sla het volgende playbook op als `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Een route maken

De Playbook-code in deze sectie is een route binnen een route tabel. 

Sla het volgende playbook op als `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

* `virtual_network_gateway` is gedefinieerd als `next_hop_type`. Zie [route ring Overview](/azure/virtual-network/virtual-networks-udr-overview)(Engelstalig) voor meer informatie over hoe Azure routes selecteert.
* `address_prefix` is gedefinieerd als `10.1.0.0/16`. Het voor voegsel kan niet worden gedupliceerd in de route tabel.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Een route verwijderen

Met de Playbook-code in deze sectie wordt een route uit een route tabel verwijderd.

Sla het volgende playbook op als `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Informatie over route tabel ophalen

De Playbook-code in deze sectie maakt gebruik van de Ansible-module `azure_rm_routetable_facts` om informatie over de route tabel op te halen.

Sla het volgende playbook op als `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Een routetabel verwijderen

De Playbook-code in deze sectie is een route tabel.

Wanneer een route tabel wordt verwijderd, worden alle bijbehorende routes ook verwijderd.

Een route tabel kan niet worden verwijderd als deze is gekoppeld aan een subnet. Koppel [de route tabel van alle subnets](#dissociate-a-route-table-from-a-subnet) voordat u probeert de route tabel te verwijderen. 

Sla het volgende playbook op als `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)