---
title: Zelfstudie - Wachtrijen configureren in Azure Service Bus met Ansible
description: Meer informatie over het gebruik van Ansible om een Azure Service Bus-wachtrij te maken
keywords: ansible, azure, devops, bash, playbook, service bus, queue
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76713231"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Zelfstudie: Wachtrijen configureren in Azure Service Bus met Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een wachtrij maken
> * Maak een SAS plicy
> * Naamruimte-informatie ophalen
> * Wachtrijgegevens ophalen
> * Het SAS-beleid voor wachtrijen intrekken

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>De wachtrij servicebus maken

Met de voorbeeldcode van de playbook worden de volgende bronnen gemaakt:
- Azure-resourcegroep
- Naamruimte servicebus binnen de resourcegroep
- Wachtrij servicebus met de naamruimte

Sla het volgende playbook op als `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Het SAS-beleid maken

Een [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) is een autorisatiemechanisme op basis van claims met behulp van tokens. 

Met de voorbeeldcode van de playbook worden twee SAS-beleidsregels gemaakt voor een wachtrij voor servicebussen met verschillende bevoegdheden.

Sla het volgende playbook op als `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:
- De `rights` waarde vertegenwoordigt de bevoegdheid die een gebruiker heeft met de wachtrij. Geef een van de `manage` `listen`volgende `send`waarden `listen_send`op: , , of .

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Naamruimte-informatie ophalen

De voorbeeldcode van de playbook vraagt de naamruimte-informatie op.

Sla het volgende playbook op als `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:
- De `show_sas_policies` waarde geeft aan of het SAS-beleid onder de opgegeven naamruimte moet worden weergegeven. Standaard is `False` de waarde om extra netwerkoverhead te voorkomen.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Wachtrijgegevens ophalen

De voorbeeldgroep playbook code query's wachtrij informatie. 

Sla het volgende playbook op als `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:
- De `show_sas_policies` waarde geeft aan of het SAS-beleid onder de opgegeven wachtrij moet worden weergegeven. Deze waarde is standaard `False` ingesteld om extra netwerkoverhead te voorkomen.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Het SAS-beleid voor wachtrijen intrekken

De voorbeeldcode van de playbook verwijdert een SAS-wachtrijbeleid in de wachtrij.

Sla het volgende playbook op als `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

Sla de volgende `cleanup.yml`code op als :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Zelfstudie: Een onderwerp configureren in Azure Service Bus met Ansible](ansible-service-bus-topic-configure.md)