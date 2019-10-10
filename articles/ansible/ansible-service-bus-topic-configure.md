---
title: 'Zelf studie: onderwerpen configureren in Azure Service Bus met behulp van Ansible'
description: Meer informatie over het gebruik van Ansible voor het maken van een Azure Service Bus onderwerp
keywords: ansible, azure, devops, bash, Playbook, service bus, topics, abonnementen
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 952779db582f9437f10608bf86b0b80560ded2c0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241219"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Zelf studie: onderwerpen configureren in Azure Service Bus met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een onderwerp maken
> * Een abonnement maken
> * Een SAS-beleid maken
> * Naam ruimte gegevens ophalen
> * Informatie over onderwerpen en abonnementen ophalen
> * Een SAS-beleid intrekken

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Het Service Bus-onderwerp maken

De voorbeeld code Playbook maakt de volgende resources:
- Azure-resourcegroep
- Naam ruimte in de resource groep Service Bus
- Service Bus onderwerp met de naam ruimte

Sla het volgende playbook op als `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Het abonnement maken

Met de voorbeeld code Playbook wordt het abonnement gemaakt onder een Service Bus onderwerp. Azure Service Bus onderwerpen kunnen meerdere abonnementen hebben. Een abonnee van een onderwerp kan een kopie ontvangen van elk bericht dat naar het onderwerp wordt verzonden. Abonnementen zijn benoemde entiteiten die blijvend zijn gemaakt, maar die optioneel kunnen verlopen.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Sla het volgende playbook op als `servicebus_subscription.yml`:

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Het SAS-beleid maken

Een [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) is een autorisatie mechanisme op basis van claims met behulp van tokens. 

De voorbeeld code Playbook maakt twee SAS-beleids regels voor een Service Bus wachtrij met andere bevoegdheden.

Sla het volgende playbook op als `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook servicebus_topic_policy.yml
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
- De waarde @no__t 0 geeft aan of de SAS-beleids regels onder de opgegeven naam ruimte moeten worden weer gegeven. Standaard is de waarde `False` om extra netwerk overhead te voor komen.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Informatie over onderwerpen en abonnementen ophalen

De voorbeeld code van de Playbook voert query's uit op de volgende informatie:
- Informatie over Service Bus onderwerp
- Lijst met abonnements Details voor het onderwerp
 
Sla het volgende playbook op als `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:
- De waarde @no__t 0 geeft aan of de SAS-beleids regels in de opgegeven wachtrij moeten worden weer gegeven. Deze waarde is standaard ingesteld op `False` om extra netwerk overhead te voor komen.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)