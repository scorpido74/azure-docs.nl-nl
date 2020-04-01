---
title: Zelfstudie - Onderwerpen configureren in Azure Service Bus met Ansible
description: Meer informatie over het gebruik van Ansible om een Azure Service Bus-onderwerp te maken
keywords: ansible, azure, devops, bash, playbook, service bus, onderwerpen, abonnementen
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155792"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Zelfstudie: Onderwerpen configureren in Azure Service Bus met Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een onderwerp maken
> * Een abonnement maken
> * Een SAS-beleid maken
> * Naamruimte-informatie ophalen
> * Onderwerp- en abonnementsgegevens ophalen
> * Een SAS-beleid intrekken

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Het onderwerp Servicebus maken

Met de voorbeeldcode van de playbook worden de volgende bronnen gemaakt:
- Azure-resourcegroep
- Naamruimte servicebus binnen de resourcegroep
- Servicebus-onderwerp met de naamruimte

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Het abonnement maken

Met de voorbeeldcode van de playbook wordt het abonnement gemaakt onder een servicebusonderwerp. Azure Service Bus-onderwerpen kunnen meerdere abonnementen hebben. Een abonnee van een onderwerp kan een kopie ontvangen van elk bericht dat naar het onderwerp wordt verzonden. Abonnementen worden entiteiten genoemd die blijvend zijn gemaakt, maar die optioneel kunnen verlopen.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Het SAS-beleid maken

Een [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) is een autorisatiemechanisme op basis van claims met behulp van tokens. 

Met de voorbeeldcode van de playbook worden twee SAS-beleidsregels gemaakt voor een wachtrij voor servicebussen met verschillende bevoegdheden.

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Onderwerp- en abonnementsgegevens ophalen

De voorbeeldquery's voor playbook-code voor de volgende informatie:
- Informatie over het onderwerp ServiceBus
- Lijst met abonnementsgegevens voor het onderwerp
 
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

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:
- De `show_sas_policies` waarde geeft aan of het SAS-beleid onder de opgegeven wachtrij moet worden weergegeven. Deze waarde is standaard `False` ingesteld om extra netwerkoverhead te voorkomen.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_list.yml
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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)