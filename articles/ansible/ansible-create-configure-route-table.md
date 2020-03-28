---
title: Zelfstudie - Azure-routetabellen configureren met Ansible
description: Meer informatie over het maken, beheren en verwijderen van Azure-routetabellen met Ansible. Lees ook hoe u routes maken en verwijderen.
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659794"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Zelfstudie: Azure-routetabellen configureren met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u meer controle nodig hebt over de routering van uw omgeving, u een [routetabel maken.](/azure/virtual-network/virtual-networks-udr-overview) 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Een routetabel maken Een virtueel netwerk maken en een routetabel koppelen aan een subnet Een routetabel loskoppelen van een subnet Routes maken en verwijderen Query een routetabel Een routetabel verwijderen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Een routetabel maken

De playbook-code in deze sectie maakt een routetabel. Zie [Azure-limieten](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)voor informatie over routetabellimieten. 

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

De playbookcode in deze sectie:

* Een virtueel netwerk maken
* Maakt een subnet binnen het virtuele netwerk
* Koppelt een routetabel aan het subnet

Routetabellen zijn niet gekoppeld aan virtuele netwerken. Routetabellen zijn eerder gekoppeld aan het subnet van een virtueel netwerk.

De virtuele netwerk- en routetabel moet naast elkaar bestaan in dezelfde Azure-locatie en -abonnement.

Subnetten en routetabellen hebben een één-op-één-relatie. Een subnet kan worden gedefinieerd zonder bijbehorende routetabel of één routetabel. Routetabellen kunnen worden gekoppeld aan geen, één of veel subnetten. 

Verkeer van het subnet wordt omgeleid op basis van:

- routes gedefinieerd in routetabellen
- [standaardroutes](/azure/virtual-network/virtual-networks-udr-overview#default)
- routes die worden gepropageerd vanuit een on-premises netwerk

Het virtuele netwerk moet zijn verbonden met een virtuele Azure-netwerkgateway. De gateway kan ExpressRoute zijn, of VPN als het BGP gebruikt met een VPN-gateway.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Een routetabel ontkoppelen van een subnet

De playbookcode in deze sectie scheidt een routetabel van een subnet.

Wanneer u een routetabel van een subnet `route_table` scheidt, stelt `None`u het subnet in op . 

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Een route maken

De playbookcode in deze sectie een route binnen een routetabel. 

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

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

* `virtual_network_gateway`wordt gedefinieerd `next_hop_type`als . Zie [Routeringsoverzicht](/azure/virtual-network/virtual-networks-udr-overview)voor meer informatie over hoe Azure routes selecteert.
* `address_prefix`wordt gedefinieerd `10.1.0.0/16`als . Het voorvoegsel kan niet worden gedupliceerd in de routetabel.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Een route verwijderen

De playbook-code in deze sectie verwijdert een route uit een routetabel.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Informatie over routetabel

De playbookcode in deze sectie gebruikt `azure_rm_routetable_facts` de Ansible-module om routetabelgegevens op te halen.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Een routetabel verwijderen

De playbook code in deze sectie een route tabel.

Wanneer een routetabel wordt verwijderd, worden ook alle routes verwijderd.

Een routetabel kan niet worden verwijderd als deze is gekoppeld aan een subnet. [De routetabel scheiden van subnetten](#dissociate-a-route-table-from-a-subnet) voordat u probeert de routetabel te verwijderen. 

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)