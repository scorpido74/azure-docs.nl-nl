---
title: 'Quick Start: virtuele Linux-machines beheren in azure met behulp van Ansible'
description: In deze Quick Start leert u hoe u een virtuele Linux-machine beheert in azure met behulp van Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 90113373535c835e5cddf707bcb520789e596aec
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202401"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Snelstartgids: virtuele Linux-machines beheren in azure met behulp van Ansible

U kunt Ansible ook gebruiken om de implementatie en configuratie van resources in uw omgeving te automatiseren. In dit artikel gebruikt u een Ansible Playbook om een virtuele Linux-machine te starten en te stoppen. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Een virtuele machine stoppen

In deze sectie gebruikt u Ansible om de toewijzing van een virtuele Azure-machine ongedaan te maken (stop).

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](/azure/cloud-shell/overview).

1. Maak een bestand met de naam `azure-vm-stop.yml`en open het in de editor:

    ```azurecli-interactive
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

1. Vervang de tijdelijke aanduidingen `{{ resource_group_name }}` en `{{ vm_name }}` door uw waarden.

1. Sla het bestand op en sluit de editor af.

1. Voer de Playbook uit met de opdracht `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

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

In deze sectie gebruikt u Ansible om een niet-toegewezen (gestopte) virtuele machine van Azure te starten.

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](/azure/cloud-shell/overview).

1. Maak een bestand met de naam `azure-vm-start.yml`en open het in de editor:

    ```azurecli-interactive
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

1. Vervang de tijdelijke aanduidingen `{{ resource_group_name }}` en `{{ vm_name }}` door uw waarden.

1. Sla het bestand op en sluit de editor af.

1. Voer de Playbook uit met de opdracht `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

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
> [Zelf studie: dynamische voor raden van Azure beheren met Ansible](./ansible-manage-azure-dynamic-inventories.md)