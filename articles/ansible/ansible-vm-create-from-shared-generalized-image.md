---
title: 'Zelf studie: een VM of virtuele-machine schaalset maken vanuit de galerie met gedeelde Azure-installatie kopieën met Ansible'
description: Meer informatie over het gebruik van Ansible voor het maken van VM-of virtuele-machine schaal sets op basis van een gegeneraliseerde installatie kopie in de galerie met gedeelde afbeeldingen.
keywords: ansible, azure, devops, bash, Playbook, virtual machine, virtuele-machine schaalset, Galerie voor gedeelde afbeeldingen
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 814be49c972e444f2a4e4a703501e88fa1272b89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392143"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Zelf studie: een VM of virtuele-machine schaalset maken vanuit de galerie met gedeelde Azure-installatie kopieën met Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

De [Galerie met gedeelde installatie kopieën](/azure/virtual-machines/windows/shared-image-galleries) is een service waarmee u eenvoudig aangepaste, beheerde installatie kopieën kunt beheren, delen en ordenen. Deze functie is handig voor scenario's waarin veel installatie kopieën worden bewaard en gedeeld. Aangepaste installatie kopieën kunnen worden gedeeld tussen abonnementen en tussen Azure Active Directory tenants. Installatie kopieën kunnen ook worden gerepliceerd naar meerdere regio's voor een snellere implementatie van schalen.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een gegeneraliseerde VM en aangepaste installatie kopie maken
> * Een galerie met gedeelde afbeeldingen maken
> * Een gedeelde installatie kopie en installatie kopie versie maken
> * Een virtuele machine maken met behulp van de gegeneraliseerde installatie kopie
> * Een schaalset voor virtuele machines maken met behulp van de gegeneraliseerde installatie kopie
> * Krijg informatie over uw gedeelde afbeeldings galerie, afbeelding en versie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>De voor beeld-playbooks ophalen

Er zijn twee manieren om de volledige set voor beeld-playbooks op te halen:

- [Down load de SIG-map](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) en sla deze op uw lokale computer op.
- Maak een nieuw bestand voor elke sectie en kopieer de voorbeeld Playbook hierin.

Het `vars.yml`-bestand bevat de variabelen die worden gebruikt door alle voorbeeld playbooks voor deze zelf studie. U kunt het bestand bewerken om unieke namen en waarden op te geven.

Het eerste voor beeld Playbook `00-prerequisites.yml` maakt wat u nodig hebt om deze zelf studie te volt ooien:
- Een resource groep, een logische container waarin Azure-resources worden geïmplementeerd en beheerd.
- Een virtueel netwerk; subnetrouter het open bare IP-adres en de netwerk interface kaart voor de virtuele machine.
- Een virtuele bron machine die wordt gebruikt voor het maken van de gegeneraliseerde installatie kopie.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 00-prerequisites.yml
```

Controleer in de [Azure Portal](https://portol.azure.com)de resource groep die u in `vars.yml` hebt opgegeven om de nieuwe virtuele machine en de verschillende resources die u hebt gemaakt weer te geven.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>De virtuele machine generaliseren en een aangepaste installatie kopie maken

De volgende Playbook, `01a-create-generalized-image.yml`, generaliseert de bron-VM die in de vorige stap is gemaakt en maakt vervolgens een aangepaste installatie kopie op basis hiervan.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Controleer de resource groep en zorg ervoor dat `testimagea` wordt weer gegeven.

## <a name="create-the-shared-image-gallery"></a>De galerie met gedeelde afbeeldingen maken

De afbeeldings galerie is de opslag plaats voor het delen en beheren van installatie kopieën. Met de voorbeeld code Playbook in `02-create-shared-image-gallery.yml` wordt een galerie met gedeelde afbeeldingen gemaakt in de resource groep.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

U ziet nu een nieuwe galerie, `myGallery`, in de resource groep.

## <a name="create-a-shared-image-and-image-version"></a>Een gedeelde installatie kopie en installatie kopie versie maken

De volgende Playbook, `03a-create-shared-image-generalized.yml`, maakt een definitie van de installatie kopie en een installatie kopie versie.

Afbeeldings definities zijn onder andere het type installatie kopie (Windows of Linux), de release opmerkingen en de minimale en maximale geheugen vereisten. De versie van de installatie kopie is de versie van de installatie kopie. Met de galerie, afbeeldings definitie en afbeeldings versie kunt u afbeeldingen in logische groepen indelen. 

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

De resource groep heeft nu een definitie van de installatie kopie en een afbeeldings versie voor uw galerie.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Een virtuele machine maken op basis van de gegeneraliseerde installatie kopie

Voer ten slotte `04a-create-vm-using-generalized-image.yml` uit om een virtuele machine te maken op basis van de gegeneraliseerde installatie kopie die u in de vorige stap hebt gemaakt.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Een schaalset voor virtuele machines maken op basis van de gegeneraliseerde installatie kopie

U kunt ook een schaalset voor virtuele machines maken op basis van de gegeneraliseerde installatie kopie. Voer `05a-create-vmss-using-generalized-image.yml` uit.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Informatie over de galerie ophalen

U kunt informatie over de galerie, de definitie van de installatie kopie en de versie ophalen door `06-get-info.yml` uit te voeren.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>De gedeelde installatie kopie verwijderen

Als u de galerie resources wilt verwijderen, raadpleegt u voor beeld Playbook `07-delete-gallery.yml`. Verwijder resources in omgekeerde volg orde. Begin met het verwijderen van de installatie kopie versie. Nadat u alle versies van de installatie kopie hebt verwijderd, kunt u de definitie van de installatie kopie verwijderen. Nadat u alle afbeeldings definities hebt verwijderd, kunt u de galerie verwijderen.

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

De voorbeeld code van de Playbook in deze sectie wordt gebruikt voor het volgende:

- De twee eerder gemaakte resource groepen verwijderen

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

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Vervang de tijdelijke aanduiding `{{ resource_group_name }}` door de naam van uw resource groep.
- Alle resources binnen de twee opgegeven resource groepen worden verwijderd.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)