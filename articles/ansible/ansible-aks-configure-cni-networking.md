---
title: Zelfstudie - Azure CNI-netwerken configureren in Azure Kubernetes Service (AKS) met Ansible
description: Meer informatie over het gebruik van Ansible om kubenet-netwerken te configureren in het AKS-cluster (Azure Kubernetes Service)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156910"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Zelfstudie: Azure CNI-netwerken configureren in Azure Kubernetes Service (AKS) met Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Met AKS u een cluster implementeren met behulp van de volgende netwerkmodellen:

- [Kubenet-netwerken](/azure/aks/configure-kubenet) - Netwerkbronnen worden meestal gemaakt en geconfigureerd wanneer het AKS-cluster wordt geïmplementeerd.
- [Azure CNI-netwerken](/azure/aks/configure-azure-cni) - AKS-cluster is verbonden met bestaande VNET-bronnen en -configuraties (Virtual Network).

Zie [Netwerkconcepten voor toepassingen in AKS voor](/azure/aks/concepts-network)meer informatie over netwerken met uw toepassingen in AKS.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een AKS-cluster maken
> * Azure CNI-netwerken configureren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Een virtueel netwerk maken
- Een subnet maken binnen het virtuele netwerk

Sla het volgende playbook op als `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Een AKS-cluster maken in het virtuele netwerk

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Maak een AKS-cluster binnen een virtueel netwerk.

Sla het volgende playbook op als `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Gebruik `azure_rm_aks_version` de module om de ondersteunde versie te vinden.
- Het `vnet_subnet_id` is het subnet dat in de vorige sectie is gemaakt.
- Het draaiboek `ssh_key` `~/.ssh/id_rsa.pub`laadt van . Als u het wijzigt, gebruikt u de single-line indeling - te beginnen met "ssh-rsa" (zonder de aanhalingstekens).
- De `client_id` `client_secret` waarden en waarden `~/.azure/credentials`worden geladen uit , dat is de standaard referentiebestand. U deze waarden instellen op uw serviceprincipal of deze waarden laden vanuit omgevingsvariabelen:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Het voorbeeldvan het voorbeelddraaiboek uitvoeren

De voorbeeld playbook code in deze sectie wordt gebruikt om verschillende functies te testen die tijdens deze zelfstudie worden weergegeven.

Sla het volgende playbook op als `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Wijzig `aksansibletest` de waarde in de naam van uw resourcegroep.
- Wijzig `aksansibletest` de waarde in uw AKS-naam.
- Wijzig `eastus` de waarde in de locatie van uw resourcegroep.

Voer het draaiboek uit met de opdracht ansible-playbook:

```bash
ansible-playbook aks-azure-cni.yml
```

Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- Een brongroep verwijderen waarnaar in de `vars` sectie wordt verwezen.

Sla het volgende playbook op als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Vervang `{{ resource_group_name }}` de tijdelijke aanduiding door de naam van uw resourcegroep.
- Alle bronnen binnen de opgegeven resourcegroep worden verwijderd.

Voer het draaiboek uit met de opdracht ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Active Directory configureren in AKS met Ansible](./ansible-aks-configure-rbac.md)