---
title: Zelfstudie - De aangepaste afbeelding van Azure-eenvoudigmaassets voor virtuele machines bijwerken met Ansible
description: Meer informatie over het gebruik van Ansible om virtuele machineschaalsets in Azure bij te werken met aangepaste afbeelding
keywords: ansible, azure, devops, bash, playbook, virtuele machine, schaalset voor virtuele machines, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b7d3053c09d2dcb667a4fc407035f4814f786932
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155845"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Zelfstudie: De aangepaste afbeelding van Azure-waarden voor virtuele machines bijwerken met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Nadat een VM is geïmplementeerd, configureert u de VM met de software die uw app nodig heeft. In plaats van deze configuratietaak voor elke virtuele machine uit te voeren, u een aangepaste afbeelding maken. Een aangepaste afbeelding is een momentopname van een bestaande VM die alle geïnstalleerde software bevat. Wanneer u [een schaalset](./ansible-create-configure-vmss.md)configureert, geeft u de afbeelding op die u moet gebruiken voor de VM's van die schaalset. Met behulp van een aangepaste afbeelding is elke VM-instantie identiek geconfigureerd voor uw app. Soms moet u de aangepaste afbeelding van uw schaalset bijwerken. Die taak is de focus van deze tutorial.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Twee VM's configureren met HTTPD
> * Een aangepaste afbeelding maken op basis van een bestaande virtuele machine
> * Een schaalset maken op basis van een afbeelding
> * De aangepaste afbeelding bijwerken

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Twee VM's configureren

De playbook code in deze sectie maakt twee virtuele machines met HTTPD geïnstalleerd op beide. 

Op `index.html` de pagina voor elke virtuele machine wordt een testtekenreeks weergegeven:

* Eerste virtuele machine geeft de waarde weer`Image A`
* Tweede vm geeft de waarde weer`Image B`

Deze tekenreeks is bedoeld om het configureren van elke VM met verschillende software na te bootsen.

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) en `create_vms.yml`sla het op in .
* Maak een nieuw `create_vms.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Voer de draaimap `ansible-playbook` uit met `myrg` de opdracht en vervang de naam van uw resourcegroep:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Vanwege de `debug` secties van het `ansible-playbook` draaiboek drukt de opdracht het IP-adres van elke vm af. Kopieer deze IP-adressen voor later gebruik.

![IP-adressen van virtuele machines](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Verbinding maken met de twee VM's

In deze sectie maakt u verbinding met elke virtuele machine. Zoals vermeld in de vorige `Image A` `Image B` sectie, de snaren en na te bootsen met twee verschillende VM's met verschillende configuraties.

Maak met de IP-adressen uit de vorige sectie verbinding met beide VM's:

![Schermafbeelding van virtuele machine A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Schermafbeelding van virtuele machine B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Afbeeldingen maken van elke virtuele machine

Op dit punt hebt u twee VM's met `index.html` iets verschillende configuraties (hun bestanden).

De playbookcode in deze sectie maakt een aangepaste afbeelding voor elke virtuele machine:

* `image_vmforimageA`- Aangepaste afbeelding die is `Image A` gemaakt voor de vm die wordt weergegeven op de startpagina.
* `image_vmforimageB`- Aangepaste afbeelding die is `Image B` gemaakt voor de vm die wordt weergegeven op de startpagina.

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) en `capture-images.yml`sla het op in .
* Maak een nieuw `capture-images.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Voer de draaimap `ansible-playbook` uit met `myrg` de opdracht en vervang de naam van uw resourcegroep:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Schaalset maken met Afbeelding A

In deze sectie wordt een draaiboek gebruikt om de volgende Azure-bronnen te configureren:

* Openbaar IP-adres
* Load balancer
* Schaalset waarnaar wordt verwezen`image_vmforimageA`

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) en `create-vmss.yml`sla het op in .
* Een nieuw bestand `create-vmss.yml` maken met de naam en de volgende inhoud kopiëren:"

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Voer de draaimap `ansible-playbook` uit met `myrg` de opdracht en vervang de naam van uw resourcegroep:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Vanwege het `debug` gedeelte van het `ansible-playbook` draaiboek drukt de opdracht het IP-adres van de schaalset af. Kopieer dit IP-adres voor later gebruik.

![Openbaar IP-adres](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Verbinding maken met de schaalset

In deze sectie maakt u verbinding met de schaalset. 

Maak met het IP-adres uit de vorige sectie verbinding met de schaalset.

Zoals vermeld in de vorige `Image A` `Image B` sectie, de snaren en na te bootsen met twee verschillende VM's met verschillende configuraties.

De schaalset verwijst naar `image_vmforimageA`de aangepaste afbeelding met de naam . Aangepaste `image_vmforimageA` afbeelding is gemaakt op de `Image A`vm waarvan de startpagina wordt weergegeven.

Als gevolg hiervan ziet u een `Image A`startpagina met:

![De schaalset is gekoppeld aan de eerste VM.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Laat uw browservenster open terwijl u doorgaat naar de volgende sectie.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Aangepaste afbeelding wijzigen in schaalset en upgrade-exemplaren

De playbookcode in deze sectie wijzigt de `image_vmforimageA` `image_vmforimageB`afbeelding van de schaalset - van . Ook worden alle huidige virtuele machines die door de schaalset worden geïmplementeerd, bijgewerkt.

Er zijn twee manieren om het voorbeeld playbook te krijgen:

* [Download het playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) en `update-vmss-image.yml`sla het op in .
* Maak een nieuw `update-vmss-image.yml` bestand met de naam en kopieer de volgende inhoud:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Voer de draaimap `ansible-playbook` uit met `myrg` de opdracht en vervang de naam van uw resourcegroep:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Ga terug naar de browser en vernieuw de pagina. 

U ziet dat de onderliggende aangepaste afbeelding van de virtuele machine wordt bijgewerkt.

![De schaalset is gekoppeld aan de tweede VM](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

Sla de volgende `cleanup.yml`code op als :

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible)