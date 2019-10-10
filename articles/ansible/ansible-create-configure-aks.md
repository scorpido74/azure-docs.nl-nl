---
title: Zelf studie-clusters van Azure Kubernetes service (AKS) configureren in azure met behulp van Ansible
description: Leer hoe u Ansible gebruikt om een Azure Kubernetes Service-cluster in Azure te maken en beheren
keywords: ansible, azure, devops, bash, Cloud shell, Playbook, AKS, container, AKS, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9b70a9c364768322a3eae6ef5b92c87b6839c540
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242093"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Zelf studie: Azure Kubernetes service (AKS)-clusters configureren in azure met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan worden geconfigureerd om [Azure Active Directory (AD)](/azure/active-directory/) te gebruiken voor gebruikers verificatie. Eenmaal geconfigureerd, gebruikt u uw Azure AD-verificatie token om u aan te melden bij het AKS-cluster. De RBAC kan worden gebaseerd op de identiteit of het lidmaatschap van de Directory groep van een gebruiker.

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

Met de voorbeeld Playbook maakt u een resource groep en een AKS-cluster in de resource groep.

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

- De eerste sectie in `tasks` definieert een resource groep met de naam `myResourceGroup` binnen de locatie van de `eastus`.
- De tweede sectie binnen `tasks` definieert een AKS-cluster met de naam `myAKSCluster` binnen de resource groep `myResourceGroup`.
- Voor de tijdelijke aanduiding `your_ssh_key` voert u uw openbare RSA-sleutel in in de indeling met één regel, beginnend met 'ssh-rsa' (zonder aanhalingstekens).

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook azure_create_aks.yml
```

Als de Playbook wordt uitgevoerd, worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende uitvoer:

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

Het voorbeeld-playbook in de vorige sectie definieert twee knooppunten. U kunt het aantal knoop punten aanpassen door de `count`-waarde in het `agent_pool_profiles`-blok te wijzigen.

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

- Voor de tijdelijke aanduiding `your_ssh_key` voert u uw openbare RSA-sleutel in in de indeling met één regel, beginnend met 'ssh-rsa' (zonder aanhalingstekens).

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook azure_configure_aks.yml
```

Als de Playbook wordt uitgevoerd, worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende uitvoer:

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

De voor beeld-Playbook verwijdert een AKS-cluster.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook azure_delete_aks.yml
```

Als de Playbook wordt uitgevoerd, worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende uitvoer:

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