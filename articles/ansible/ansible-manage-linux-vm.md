---
title: Quickstart - Linux-virtuele machines beheren in Azure met Ansible
description: In deze quickstart leert u hoe u een Virtuele Linux-machine in Azure beheert met Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239534"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Snelstart: Linux-virtuele machines beheren in Azure met Ansible

U kunt Ansible ook gebruiken om de implementatie en configuratie van resources in uw omgeving te automatiseren. In dit artikel gebruik je een Ansible playbook om een Linux virtuele machine te starten en te stoppen. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Een virtuele machine stoppen

In deze sectie gebruikt u Ansible om een virtuele Azure-machine te de-toewijzen (stoppen).

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](/azure/cloud-shell/overview).

1. Maak een `azure-vm-stop.yml`bestand met de naam en open het in de editor:

    ```bash
    code azure-vm-stop.yml
    ```

1. Plak de volgende voorbeeldcode in de editor:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Vervang `{{ resource_group_name }}` de `{{ vm_name }}` tijdelijke aanduidingen en tijdelijke aanduidingen door uw waarden.

1. Sla het bestand op en sluit de editor af.

1. Voer de playbook `ansible-playbook` uit met de opdracht:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Een virtuele machine starten

In deze sectie gebruikt u Ansible om een gedealde (gestopte) Azure-virtuele machine te starten.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](/azure/cloud-shell/overview).

1. Maak een `azure-vm-start.yml`bestand met de naam en open het in de editor:

    ```bash
    code azure-vm-start.yml
    ```

1. Plak de volgende voorbeeldcode in de editor:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Vervang `{{ resource_group_name }}` de `{{ vm_name }}` tijdelijke aanduidingen en tijdelijke aanduidingen door uw waarden.

1. Sla het bestand op en sluit de editor af.

1. Voer de playbook `ansible-playbook` uit met de opdracht:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Zelfstudie: Dynamische azure-inventarissen beheren met Ansible](./ansible-manage-azure-dynamic-inventories.md)