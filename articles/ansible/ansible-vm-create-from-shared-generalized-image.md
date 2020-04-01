---
title: Zelfstudie - Een vm- of virtuele machineschaalset maken vanuit de Azure Shared Image Gallery met Ansible
description: Meer informatie over het gebruik van Ansible om VM- of virtuele machineschaalset te maken op basis van een algemene afbeelding in Shared Image Gallery.
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, shared image gallery ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, shared image gallery ansible, devops, bash, playbook, virtual machine scale set, shared image gallery ansible, devop
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155839"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Zelfstudie: Een vm- of virtuele machineschaalset maken vanuit de Azure Shared Image Gallery met Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries) is een service waarmee u eenvoudig aangepaste beheerde afbeeldingen beheren, delen en ordenen. Deze functie is gunstig voor scenario's waarin veel afbeeldingen worden onderhouden en gedeeld. Aangepaste afbeeldingen kunnen worden gedeeld tussen abonnementen en tussen Azure Active Directory-tenants. Afbeeldingen kunnen ook worden gerepliceerd naar meerdere regio's voor snellere implementatieschaling.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een algemene VM en aangepaste afbeelding maken
> * Een gedeelde afbeeldingsgalerie maken
> * Een gedeelde afbeeldingen- en afbeeldingsversie maken
> * Een VM maken met de algemene afbeelding
> * Een virtuele machineschaalset maken met de algemene afbeelding
> * Informatie over uw gedeelde afbeeldingsgalerie, afbeelding en versie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Download de voorbeeldplaybooks

Er zijn twee manieren om de volledige set voorbeeldplaybooks te krijgen:

- [Download de SIG-map](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) en sla deze op in uw lokale machine.
- Maak een nieuw bestand voor elke sectie en kopieer de voorbeeldplaybook erin.

Het `vars.yml` bestand bevat de variabelen die worden gebruikt door alle voorbeeldplaybooks voor deze zelfstudie. U het bestand bewerken om unieke namen en waarden op te geven.

Het eerste voorbeeld `00-prerequisites.yml` playbook creëert wat nodig is om deze tutorial te voltooien:
- Een resourcegroep, een logische container waarin Azure-resources worden geïmplementeerd en beheerd.
- Een virtueel netwerk; subnet; openbare IP-adres en netwerkinterfacekaart voor de VM.
- Een bron virtuele machine, die wordt gebruikt voor het maken van de gegeneraliseerde afbeelding.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 00-prerequisites.yml
```

Controleer in de [Azure-portal](https://portal.azure.com)de `vars.yml` brongroep waarin u hebt opgegeven om de nieuwe virtuele machine en de verschillende resources te zien die u hebt gemaakt.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>De VM generaliseren en een aangepaste afbeelding maken

De volgende playbook, `01a-create-generalized-image.yml`generaliseert de bron VM gemaakt in vorige stap en maak vervolgens een aangepaste afbeelding op basis van het.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Controleer uw resourcegroep `testimagea` en zorg ervoor dat deze wordt weergegeven.

## <a name="create-the-shared-image-gallery"></a>De galerie met gedeelde afbeeldingen maken

De afbeeldingsgalerie is de opslagplaats voor het delen en beheren van afbeeldingen. Met de voorbeeldcode `02-create-shared-image-gallery.yml` van de playbook in maakt u een gedeelde afbeeldingsgalerie in uw resourcegroep.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

U ziet nu een `myGallery`nieuwe galerie in uw resourcegroep.

## <a name="create-a-shared-image-and-image-version"></a>Een gedeelde afbeeldingen- en afbeeldingsversie maken

In het volgende `03a-create-shared-image-generalized.yml` draaiboek wordt een afbeeldingsdefinitie en een afbeeldingsversie gemaakt.

Afbeeldingsdefinities omvatten het beeldtype (Windows of Linux), releasenotes en minimale en maximale geheugenvereisten. Afbeeldingversie is de versie van de afbeelding. Met galerij- en afbeeldingsdefinitie en afbeeldingsversie u afbeeldingen in logische groepen ordenen. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Uw resourcegroep heeft nu een afbeeldingsdefinitie en een afbeeldingsversie voor uw galerie.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Een VM maken op basis van de algemene afbeelding

Voer ten `04a-create-vm-using-generalized-image.yml` slotte uit om een VM te maken op basis van de algemene afbeelding die u in de vorige stap hebt gemaakt.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Een virtuele machineschaalsets maken op basis van de algemene afbeelding

U ook een virtuele machineschaalset maken op basis van de algemene afbeelding. Ren `05a-create-vmss-using-generalized-image.yml` om dat te doen.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Informatie over de galerie

U informatie over de galerie, afbeeldingsdefinitie en versie krijgen door . `06-get-info.yml`

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>De gedeelde afbeelding verwijderen

Als u de galeriebronnen wilt `07-delete-gallery.yml`verwijderen, raadpleegt u voorbeeldvan een voorbeeldvan draaiboek . Resources in omgekeerde volgorde verwijderen. Begin met het verwijderen van de afbeeldingsversie. Nadat u alle afbeeldingsversies hebt verwijderd, u de afbeeldingsdefinitie verwijderen. Nadat u alle afbeeldingsdefinities hebt verwijderd, u de galerie verwijderen.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

De voorbeeldcode van de playbook in deze sectie wordt gebruikt om:

- De twee eerder gemaakte resourcesgroepen verwijderen

Sla het volgende playbook op als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Vervang `{{ resource_group_name }}` de tijdelijke aanduiding door de naam van uw resourcegroep.
- Alle bronnen binnen de twee opgegeven resourcegroepen worden verwijderd.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)