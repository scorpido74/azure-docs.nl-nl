---
title: Zelfstudie - AKS-clusters (Azure Kubernetes Service) configureren in Azure met Ansible
description: Leer hoe u Ansible gebruikt om een Azure Kubernetes Service-cluster in Azure te maken en beheren
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156678"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Zelfstudie: AKS-clusters (Azure Kubernetes Service) configureren in Azure met Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan worden geconfigureerd om [Azure Active Directory (AD)](/azure/active-directory/) te gebruiken voor gebruikersverificatie. Nadat u bent geconfigureerd, gebruikt u uw Azure AD-verificatietoken om u aan te melden bij het AKS-cluster. De RBAC kan worden gebaseerd op de identiteit van een gebruiker of directory group membership.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een AKS-cluster maken
> * Een AKS-cluster configureren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Een beheerd AKS-cluster maken

In het voorbeeldwerkboek wordt een resourcegroep en een AKS-cluster gemaakt binnen de brongroep.

Sla het volgende playbook op als `azure_create_aks.yml`:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

- De eerste `tasks` sectie binnen definieert een resourcegroep met de naam `myResourceGroup` binnen de `eastus` locatie.
- De tweede `tasks` sectie binnen definieert `myAKSCluster` een `myResourceGroup` AKS-cluster met de naam binnen de resourcegroep.
- Voor de tijdelijke aanduiding `your_ssh_key` voert u uw openbare RSA-sleutel in in de indeling met één regel, beginnend met 'ssh-rsa' (zonder aanhalingstekens).
- Voor `aks_version` de tijdelijke aanduiding, gebruik maken van de [az aks get-versies](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) commando.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook azure_create_aks.yml
```

Als u het draaiboek uitvoert, worden resultaten weergegeven die vergelijkbaar zijn met de volgende uitvoer:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>AKS-knooppunten schalen

Het voorbeeld-playbook in de vorige sectie definieert twee knooppunten. U past het aantal knooppunten `count` aan door `agent_pool_profiles` de waarde in het blok te wijzigen.

Sla het volgende playbook op als `azure_configure_aks.yml`:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

- Voor de tijdelijke aanduiding `your_ssh_key` voert u uw openbare RSA-sleutel in in de indeling met één regel, beginnend met 'ssh-rsa' (zonder aanhalingstekens).

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook azure_configure_aks.yml
```

Als u het draaiboek uitvoert, worden resultaten weergegeven die vergelijkbaar zijn met de volgende uitvoer:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Wis een beheerd AKS-cluster

In de voorbeeldplaybook wordt een AKS-cluster verwijderd.

Sla het volgende playbook op als `azure_delete_aks.yml`:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook azure_delete_aks.yml
```

Als u het draaiboek uitvoert, worden resultaten weergegeven die vergelijkbaar zijn met de volgende uitvoer:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een toepassing schalen in AKS (Azure Kubernetes Service)](/azure/aks/tutorial-kubernetes-scale)