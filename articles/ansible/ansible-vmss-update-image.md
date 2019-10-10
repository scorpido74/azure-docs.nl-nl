---
title: 'Zelf studie: de aangepaste installatie kopie van virtuele-machine schaal sets van Azure bijwerken met behulp van Ansible'
description: Meer informatie over het gebruik van Ansible voor het bijwerken van virtuele-machine schaal sets in azure met een aangepaste installatie kopie
keywords: ansible, azure, devops, bash, playbook, virtuele machine, schaalset voor virtuele machines, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 3b7baffe6ce0fadbac2dd56b9c8296c80546fa72
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241331"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Zelf studie: de aangepaste installatie kopie van virtuele-machine schaal sets van Azure bijwerken met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Nadat een virtuele machine is geïmplementeerd, configureert u de virtuele machine met de software die uw app nodig heeft. In plaats van deze configuratie taak te gebruiken voor elke virtuele machine, kunt u een aangepaste installatie kopie maken. Een aangepaste installatie kopie is een moment opname van een bestaande virtuele machine die geïnstalleerde software bevat. Wanneer u [een schaalset configureert](./ansible-create-configure-vmss.md), geeft u de installatie kopie op die moet worden gebruikt voor de virtuele machines van de schaalset. Door een aangepaste installatie kopie te gebruiken, is elk VM-exemplaar gelijk geconfigureerd voor uw app. Soms moet u de aangepaste installatie kopie van uw schaalset mogelijk bijwerken. Deze taak is de focus van deze zelf studie.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Twee Vm's configureren met HTTPD
> * Een aangepaste installatie kopie maken van een bestaande virtuele machine
> * Een schaalset maken op basis van een afbeelding
> * De aangepaste installatie kopie bijwerken

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Twee Vm's configureren

Met de Playbook-code in deze sectie maakt u twee virtuele machines waarop HTTPD is geïnstalleerd. 

Op de pagina @no__t 0 van elke VM wordt een test teken reeks weer gegeven:

* In de eerste VM wordt de waarde `Image A` weer gegeven
* Tweede VM geeft de waarde weer `Image B`

Deze teken reeks is bedoeld om het configureren van elke VM met andere software te simuleren.

Er zijn twee manieren om de voorbeeld Playbook te verkrijgen:

* [Down load de Playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) en sla deze op `create_vms.yml`.
* Maak een nieuw bestand met de naam `create_vms.yml` en kopieer het naar de volgende inhoud:

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

Voer de Playbook uit met de `ansible-playbook`-opdracht, waarbij u `myrg` vervangt door de naam van de resource groep:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Vanwege de `debug`-secties van de Playbook wordt met de `ansible-playbook`-opdracht het IP-adres van elke virtuele machine afgedrukt. Kopieer deze IP-adressen voor later gebruik.

![IP-adressen van virtuele machines](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Verbinding maken met de twee virtuele machines

In deze sectie maakt u verbinding met elke virtuele machine. Zoals vermeld in de vorige sectie, simuleren de teken reeksen `Image A` en `Image B` met twee afzonderlijke Vm's met verschillende configuraties.

Met behulp van de IP-adressen uit de vorige sectie maakt u verbinding met beide Vm's:

![Scherm opname van virtuele machine A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Scherm opname van virtuele machine B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Installatie kopieën maken van elke VM

Op dit moment hebt u twee virtuele machines met iets verschillende configuraties (de `index.html` bestanden).

Met de Playbook-code in deze sectie maakt u een aangepaste installatie kopie voor elke VM:

* `image_vmforimageA`: er is een aangepaste installatie kopie gemaakt voor de virtuele machine die `Image A` op de start pagina van de VM weergeeft.
* `image_vmforimageB`: er is een aangepaste installatie kopie gemaakt voor de virtuele machine die `Image B` op de start pagina van de VM weergeeft.

Er zijn twee manieren om de voorbeeld Playbook te verkrijgen:

* [Down load de Playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) en sla deze op `capture-images.yml`.
* Maak een nieuw bestand met de naam `capture-images.yml` en kopieer het naar de volgende inhoud:

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

Voer de Playbook uit met de `ansible-playbook`-opdracht, waarbij u `myrg` vervangt door de naam van de resource groep:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Een schaalset maken met afbeelding A

In deze sectie wordt een Playbook gebruikt voor het configureren van de volgende Azure-resources:

* Openbaar IP-adres
* Load Balancer
* Schaalset die verwijst naar `image_vmforimageA`

Er zijn twee manieren om de voorbeeld Playbook te verkrijgen:

* [Down load de Playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) en sla deze op `create-vmss.yml`.
* Maak een nieuw bestand met de naam `create-vmss.yml` en kopieer het naar de volgende inhoud: "

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

Voer de Playbook uit met de `ansible-playbook`-opdracht, waarbij u `myrg` vervangt door de naam van de resource groep:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Vanwege de `debug` sectie van de Playbook wordt met de `ansible-playbook`-opdracht het IP-adres van de schaalset afgedrukt. Kopieer dit IP-adres voor later gebruik.

![Openbaar IP-adres](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Verbinding maken met de schaalset

In deze sectie maakt u verbinding met de schaalset. 

Gebruik het IP-adres uit de vorige sectie om verbinding te maken met de schaalset.

Zoals vermeld in de vorige sectie, simuleren de teken reeksen `Image A` en `Image B` met twee afzonderlijke Vm's met verschillende configuraties.

De schaalset verwijst naar de aangepaste installatie kopie met de naam `image_vmforimageA`. Aangepaste installatie kopie `image_vmforimageA` is gemaakt op de VM waarvan de start pagina wordt weer gegeven `Image A`.

Als gevolg hiervan wordt een start pagina met `Image A` weer gegeven:

![De schaalset is gekoppeld aan de eerste VM.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Zorg ervoor dat uw browser venster geopend blijft terwijl u verdergaat met de volgende sectie.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Aangepaste installatie kopie wijzigen in schaalset en upgrade-exemplaren

Met de Playbook-code in deze sectie wordt de afbeelding van de schaalset gewijzigd: van `image_vmforimageA` naar `image_vmforimageB`. Ook worden alle huidige virtuele machines bijgewerkt die worden geïmplementeerd door de schaalset.

Er zijn twee manieren om de voorbeeld Playbook te verkrijgen:

* [Down load de Playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) en sla deze op `update-vmss-image.yml`.
* Maak een nieuw bestand met de naam `update-vmss-image.yml` en kopieer het naar de volgende inhoud:

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

Voer de Playbook uit met de `ansible-playbook`-opdracht, waarbij u `myrg` vervangt door de naam van de resource groep:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Ga terug naar de browser en vernieuw de pagina. 

U ziet dat de onderliggende aangepaste installatie kopie van de virtuele machine is bijgewerkt.

![De schaalset is gekoppeld aan de tweede VM](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla de volgende code op als `cleanup.yml`:

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible)