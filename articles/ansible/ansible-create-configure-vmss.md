---
title: Zelfstudie - Virtuele machineschaalsets configureren in Azure met Ansible
description: Meer informatie over het gebruik van Ansible voor het maken en configureren van virtuele machineschaalsets in Azure
keywords: ansible, azure, devops, bash, playbook, virtuele machine, schaalset voor virtuele machines, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156550"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Zelfstudie: Virtuele machineschaalsets configureren in Azure met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * De resources voor een virtuele machine configureren
> * Een schaalset configureren
> * Schaal de schaal die is ingesteld door de VM-exemplaren te verhogen 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Een schaalset configureren

De playbookcode in deze sectie definieert de volgende bronnen:

* **Resourcegroep** waarin al uw resources worden geïmplementeerd.
* **Virtueel netwerk** in de adresruimte 10.0.0.0/16
* **Subnet** binnen het virtuele netwerk
* **Openbaar IP-adres** waarmee u toegang krijgt tot resources via internet
* **Netwerkbeveiligingsgroep** die de stroom van netwerkverkeer in en uit uw schaalset regelt
* **Load balancer** waarmee verkeer wordt verdeeld over een reeks gedefinieerde virtuele machines met behulp van load balancer-regels
* **Schaalset voor virtuele machines** waarin alle gemaakte resources worden gebruikt

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) en `vmss-create.yml`sla het op in .
* Maak een nieuw `vmss-create.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

* Vervang `vars` in de `{{ admin_password }}` sectie de tijdelijke aanduiding door uw eigen wachtwoord.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook vmss-create.yml
```

Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Het aantal VM-exemplaren weergeven

De [geconfigureerde schaalset](#configure-a-scale-set) heeft momenteel twee exemplaren. De volgende stappen worden gebruikt om die waarde te bevestigen:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navigeer naar de schaalset die u hebt geconfigureerd.

1. U ziet de naam van de schaalset met het aantal exemplaren tussen haakjes:`Standard_DS1_v2 (2 instances)`

1. U ook het aantal exemplaren verifiëren met de [Azure Cloud Shell](https://shell.azure.com/) door de volgende opdracht uit te voeren:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    De resultaten van het uitvoeren van de opdracht Azure CLI in Cloud Shell tonen aan dat er nu drie exemplaren bestaan: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Een schaalset schalen

De playbook-code in deze sectie haalt informatie op over de schaalset en wijzigt de capaciteit van twee naar drie.

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) en `vmss-scale-out.yml`sla het op in .
* Maak een nieuw `vmss-scale-out.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook vmss-scale-out.yml
```

Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>De resultaten verifiëren

Controleer uw resultaten van uw werk via de Azure-portal:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navigeer naar de schaalset die u hebt geconfigureerd.

1. U ziet de naam van de schaalset met het aantal exemplaren tussen haakjes:`Standard_DS1_v2 (3 instances)` 

1. U kunt de wijziging ook verifiëren in [Azure Cloud Shell](https://shell.azure.com/) door de volgende opdracht uit te voeren:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    De resultaten van het uitvoeren van de opdracht Azure CLI in Cloud Shell tonen aan dat er nu drie exemplaren bestaan: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Zelfstudie: Apps implementeren voor virtuele machineschaalsets in Azure met Ansible](./ansible-deploy-app-vmss.md)