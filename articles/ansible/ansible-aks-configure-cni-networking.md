---
title: 'Zelf studie: Azure CNI-netwerken configureren in azure Kubernetes service (AKS) met behulp van Ansible'
description: Meer informatie over het gebruik van Ansible voor het configureren van kubenet-netwerken in azure Kubernetes service (AKS)-cluster
keywords: ansible, azure, devops, bash, Cloud shell, Playbook, AKS, container, AKS, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 04da0e8fb06d0a32c8e8bdc39d7722fc1c3fcdba
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242036"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Zelf studie: Azure CNI-netwerken configureren in azure Kubernetes service (AKS) met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Met AKS kunt u een cluster implementeren met behulp van de volgende netwerk modellen:

- [Kubenet-netwerken](/azure/aks/configure-kubenet) : netwerk bronnen worden doorgaans gemaakt en geconfigureerd als het AKS-cluster wordt geïmplementeerd.
- [Azure cni Networking](/azure/aks/configure-azure-cni) -AKS-cluster is verbonden met bestaande bronnen en configuraties van virtuele netwerken (VNET).

Zie [Network concepten for Applications in AKS](/azure/aks/concepts-network)(Engelstalig) voor meer informatie over netwerken voor uw toepassingen in AKS.

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

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Maak een virtueel netwerk
- Een subnet binnen het virtuele netwerk maken

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

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Maak een AKS-cluster in een virtueel netwerk.

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

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Gebruik de module `azure_rm_aks_version` om de ondersteunde versie te vinden.
- De `vnet_subnet_id` is het subnet dat in de vorige sectie is gemaakt.
- De Playbook laadt `ssh_key` van `~/.ssh/id_rsa.pub`. Als u het wijzigt, gebruikt u de indeling met één regel, te beginnen met ' ssh-rsa ' (zonder de aanhalings tekens).
- De waarden `client_id` en `client_secret` worden geladen van `~/.azure/credentials`. Dit is het standaard referentie bestand. U kunt deze waarden instellen voor uw service-principal of deze waarden laden vanuit omgevings variabelen:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>De voorbeeld Playbook uitvoeren

De voor beeld-Playbook-code in deze sectie wordt gebruikt om verschillende functies te testen die in deze zelf studie worden weer gegeven.

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

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Wijzig de `aksansibletest`-waarde in de naam van de resource groep.
- Wijzig de `aksansibletest`-waarde in de naam van uw AKS.
- Wijzig de `eastus`-waarde in de locatie van de resource groep.

Voer de Playbook uit met behulp van de ansible-Playbook opdracht:

```bash
ansible-playbook aks-azure-cni.yml
```

Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

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

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- Verwijder een resource groep waarnaar wordt verwezen in de sectie `vars`.

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

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Vervang de tijdelijke aanduiding `{{ resource_group_name }}` door de naam van uw resource groep.
- Alle resources in de opgegeven resource groep worden verwijderd.

Voer de Playbook uit met behulp van de ansible-Playbook opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Azure Active Directory configureren in AKS met behulp van Ansible](./ansible-aks-configure-rbac.md)