---
title: Zelfstudie - Apps implementeren voor virtuele machineschaalsets in Azure met Ansible
description: Meer informatie over het gebruik van Ansible om azure-set's voor virtuele machineschalen te configureren en toepassingen op de schaalset te implementeren
keywords: ansible, azure, devops, bash, playbook, virtuele machine, schaalset voor virtuele machines, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940864"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Zelfstudie: Apps implementeren voor virtuele machineschaalsets in Azure met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hostgegevens ophalen voor een groep Azure VM's
> * De voorbeeld-app klonen en bouwen
> * Installeer de JRE (Java Runtime Environment) op een schaalset
> * De Java-toepassing implementeren op een schaalset

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) wordt gebruikt om een Java-sample te downloaden die in deze zelfstudie wordt gebruikt.
- **JDK (Java SE Development Kit)**: de [JDK](https://aka.ms/azure-jdks) wordt gebruikt om het Java-voorbeeldproject te maken.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) wordt gebruikt om het voorbeeld Java project te bouwen.

## <a name="get-host-information"></a>Hostgegevens ophalen

De playbook-code in deze sectie haalt hostinformatie op voor een groep virtuele machines. De code krijgt de openbare IP-adressen en load balancer binnen `scalesethosts` een opgegeven resourcegroep en maakt een hostgroep met de naam in de voorraad.

Sla het volgende voorbeeld-playbook op als `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Een toepassing voorbereiden voor implementatie

De playbook-code in `git` deze sectie wordt gebruikt om een Java-voorbeeldproject van GitHub te klonen en bouwt het project. 

Sla het volgende playbook op als `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Voer het voorbeeld-Ansible-playbook uit met de volgende opdracht:

  ```bash
  ansible-playbook app.yml
  ```

Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>De toepassing implementeren op een schaalset

De playbookcode in deze sectie wordt gebruikt om:

* De JRE installeren op een hostgroep met de naam`saclesethosts`
* De Java-toepassing implementeren in een hostgroep met de naam`saclesethosts`

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) en `vmss-setup-deploy.yml`sla het op in .
* Maak een nieuw `vmss-setup-deploy.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

* Vervang `vars` in de `{{ admin_password }}` sectie de tijdelijke aanduiding door uw eigen wachtwoord.
* Als u het ssh-verbindingstype met wachtwoorden wilt gebruiken, installeert u het sshpass-programma:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    Centos:

    ```bash
    yum install sshpass
    ```

* In sommige omgevingen ziet u mogelijk een fout over het gebruik van een SSH-wachtwoord in plaats van een sleutel. Als u die fout ontvangt, u de controle `/etc/ansible/ansible.cfg` van `~/.ansible.cfg`de hostsleutel uitschakelen door de volgende regel toe te voegen aan of:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Voer het playbook uit met de volgende opdracht:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

De uitvoer van het uitvoeren van de opdracht ansible-playbook geeft aan dat de Java-toepassing van de steekproef is geïnstalleerd op de hostgroep van de schaalset:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>De resultaten verifiëren

Controleer de resultaten van uw werk door te navigeren naar de URL van de load balancer voor uw schaalset:

![Java-app wordt uitgevoerd in een schaalset in Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Seten voor virtuele machineschalen automatisch schalen in Azure met Ansible](./ansible-auto-scale-vmss.md)