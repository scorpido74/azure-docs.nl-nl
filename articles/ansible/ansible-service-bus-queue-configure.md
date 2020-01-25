---
title: 'Zelf studie: wacht rijen configureren in Azure Service Bus met behulp van Ansible'
description: Meer informatie over het gebruik van Ansible voor het maken van een Azure Service Bus wachtrij
keywords: ansible, azure, devops, bash, Playbook, service bus, Queue
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713231"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Zelf studie: wacht rijen configureren in Azure Service Bus met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een wachtrij maken
> * Een SAS-plicy maken
> * Naam ruimte gegevens ophalen
> * Wachtrij gegevens ophalen
> * Het SAS-beleid van de wachtrij intrekken

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>De Service Bus wachtrij maken

De voorbeeld code Playbook maakt de volgende resources:
- Azure-resourcegroep
- Naam ruimte in de resource groep Service Bus
- Wachtrij Service Bus met de naam ruimte

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

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Het SAS-beleid maken

Een [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) is een autorisatie mechanisme op basis van claims met behulp van tokens. 

De voorbeeld code Playbook maakt twee SAS-beleids regels voor een Service Bus wachtrij met andere bevoegdheden.

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:
- De `rights` waarde vertegenwoordigt de bevoegdheid die een gebruiker heeft met de wachtrij. Geef een van de volgende waarden op: `manage`, `listen`, `send`of `listen_send`.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Naam ruimte gegevens ophalen

De voor beeld-Playbook-code voert een query uit op de naam ruimte gegevens.

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:
- De waarde `show_sas_policies` geeft aan of de SAS-beleids regels onder de opgegeven naam ruimte moeten worden weer gegeven. Standaard is de waarde `False` om extra netwerk overhead te voor komen.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Wachtrij gegevens ophalen

De voorbeeld code van de Playbook-query heeft betrekking op de wachtrij gegevens. 

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:
- De waarde `show_sas_policies` geeft aan of de SAS-beleids regels in de opgegeven wachtrij moeten worden weer gegeven. Deze waarde is standaard ingesteld op `False` om extra netwerk overhead te voor komen.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Het SAS-beleid van de wachtrij intrekken

Met de voorbeeld code Playbook wordt een BEVEILIGINGS beleid voor de wachtrij verwijderd.

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

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla de volgende code op als `cleanup.yml`:

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

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Zelf studie: een onderwerp in Azure Service Bus configureren met behulp van Ansible](ansible-service-bus-topic-configure.md)