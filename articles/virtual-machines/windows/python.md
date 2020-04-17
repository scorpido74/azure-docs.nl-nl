---
title: Een Windows-vm maken en beheren in Azure met Python
description: Leer python gebruiken om een Windows VM in Azure te maken en te beheren.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 670c369e25639c859f6a8d8b3c65e329b5cf7f04
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458178"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Windows VM's maken en beheren in Azure met Python

Een [Azure Virtual Machine](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) heeft verschillende ondersteunende Azure-bronnen nodig. In dit artikel wordt betrekking op het maken, beheren en verwijderen van VM-resources met Behulp van Python. Procedures voor:

> [!div class="checklist"]
> * Een Visual Studio-project maken
> * Pakketten installeren
> * Referenties maken
> * Resources maken
> * Beheertaken uitvoeren
> * Resources verwijderen
> * De toepassing uitvoeren

Het duurt ongeveer 20 minuten om deze stappen te doen.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

1. Als u dit nog niet hebt gedaan, installeert u [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecteer **Python-ontwikkeling** op de pagina Workloads en klik op **Installeren**. In het overzicht u zien dat **Python 3 64-bits (3.6.0)** automatisch voor u wordt geselecteerd. Als u Visual Studio al hebt geïnstalleerd, u de Python-werkbelasting toevoegen met behulp van de Visual Studio Launcher.
2. Klik na het installeren en starten van Visual Studio op **Nieuw project** > **New** > **bestand**.
3. Klik op **Templates** > **Python** > **Python Application,** voer *myPythonProject* in voor de naam van het project, selecteer de locatie van het project en klik op **OK**.

## <a name="install-packages"></a>Pakketten installeren

1. Klik in Solution Explorer onder *myPythonProject*met de rechtermuisknop op **Python-omgevingen**en selecteer **Virtuele omgeving toevoegen**.
2. Accepteer in het scherm Virtuele omgeving toevoegen de standaardnaam van *env,* controleer of *Python 3.6 (64-bits)* is geselecteerd voor de basistolk en klik vervolgens op **Maken**.
3. Klik met de rechtermuisknop op de *env-omgeving* die u hebt gemaakt, klik op **Python-pakket installeren,** voer *azure* in het zoekvak in en druk op Enter.

U moet in de uitvoervensters zien dat de azure-pakketten zijn geïnstalleerd. 

## <a name="create-credentials"></a>Referenties maken

Controleer voordat u met deze stap begint of u een [Active Directory-serviceprincipal hebt.](../../active-directory/develop/howto-create-service-principal-portal.md) U moet ook de toepassings-ID, de verificatiesleutel en de tenant-id registreren die u in een latere stap nodig hebt.

1. Open *myPythonProject.py* bestand dat is gemaakt en voeg deze code toe om uw toepassing uit te voeren:

    ```python
    if __name__ == "__main__":
    ```

2. Als u de benodigde code wilt importeren, voegt u deze instructies toe aan de bovenkant van het .py-bestand:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Voeg vervolgens in het .py-bestand variabelen toe na de importinstructies om algemene waarden op te geven die in de code worden gebruikt:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Vervang **abonnement-id** door uw abonnements-id.

4. Als u de Active Directory-referenties wilt maken die u nodig hebt om aanvragen in te dienen, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Vervang **toepassings-id,** **verificatiesleutel**en **tenant-id** door de waarden die u eerder hebt verzameld toen u de principal van uw Azure Active Directory-service maakte.

5. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Resources maken
 
### <a name="initialize-management-clients"></a>Itize management clients

Beheerclients zijn nodig om resources te maken en te beheren met behulp van de Python SDK in Azure. Als u de beheerclients wilt maken, voegt u deze code toe onder de **instructie als** aan het einde van het .py-bestand:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>De VM maken en ondersteunende resources

Alle resources moeten zijn opgenomen in een [resourcegroep](../../azure-resource-manager/management/overview.md).

1. Als u een resourcegroep wilt maken, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Beschikbaarheidssets](tutorial-availability-sets.md) maken het eenvoudiger voor u om de virtuele machines te onderhouden die door uw toepassing worden gebruikt.

1. Als u een beschikbaarheidsset wilt maken, voegt u deze functie toe na de variabelen in het .py-bestand:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

Een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) is nodig om te communiceren met de virtuele machine.

1. Als u een openbaar IP-adres voor de virtuele machine wilt maken, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Een virtuele machine moet zich in een subnet van een [virtueel netwerk bevinden.](../../virtual-network/virtual-networks-overview.md)

1. Als u een virtueel netwerk wilt maken, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Als u een subnet aan het virtuele netwerk wilt toevoegen, voegt u deze functie toe na de variabelen in het .py-bestand:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Een virtuele machine heeft een netwerkinterface nodig om te communiceren op het virtuele netwerk.

1. Als u een netwerkinterface wilt maken, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Nu u alle ondersteunende bronnen hebt gemaakt, u een virtuele machine maken.

1. Als u de virtuele machine wilt maken, voegt u deze functie toe na de variabelen in het .py-bestand:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Met deze zelfstudie wordt een virtuele machine gemaakt waarop een versie van het Windows Server-besturingssysteem wordt uitgevoerd. Zie Navigeren en selecteren van [Azure-afbeeldingen voor virtuele machines met Windows PowerShell en azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie over het selecteren van andere afbeeldingen.
    > 
    > 

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Beheertaken uitvoeren

Tijdens de levenscyclus van een virtuele machine wilt u mogelijk beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een virtuele machine. Daarnaast u code maken om repetitieve of complexe taken te automatiseren.

### <a name="get-information-about-the-vm"></a>Informatie over de VM

1. Als u informatie wilt krijgen over de virtuele machine, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>De virtuele machine stoppen

U een virtuele machine stoppen en alle instellingen behouden, maar er nog steeds kosten voor in rekening worden gebracht, of u een virtuele machine stoppen en detoewijzing ervan doen. Wanneer een virtuele machine is toegewezen, worden alle resources die eraan zijn gekoppeld ook deallocatie en eindigt de facturering ervoor.

1. Als u de virtuele machine wilt stoppen zonder deze te dealeren, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Als u de virtuele machine wilt detoewijzen, wijzigt u de power_off aanroepen naar deze code:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>De virtuele machine starten

1. Als u de virtuele machine wilt starten, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Het formaat van de vm wijzigen

Veel aspecten van de implementatie moeten worden overwogen bij de beslissing over een grootte voor uw virtuele machine. Zie [VM-formaten voor](sizes.md)meer informatie .

1. Als u de grootte van de virtuele machine wilt wijzigen, voegt u deze functie toe na de variabelen in het .py-bestand:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Een gegevensschijf toevoegen aan de virtuele machine

Virtuele machines kunnen een of meer [gegevensschijven](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bevatten die als VHD's zijn opgeslagen.

1. Als u een gegevensschijf aan de virtuele machine wilt toevoegen, voegt u deze functie toe na de variabelen in het .py-bestand: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Resources verwijderen

Omdat er kosten in rekening worden gebracht voor resources die in Azure worden gebruikt, is het altijd een goede gewoonte om resources te verwijderen die niet langer nodig zijn. Als u de virtuele machines en alle ondersteunende resources wilt verwijderen, hoeft u alleen de brongroep te verwijderen.

1. Als u de brongroep en alle resources wilt verwijderen, voegt u deze functie toe na de variabelen in het .py-bestand:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Als u de functie wilt aanroepen die u eerder hebt toegevoegd, voegt u deze code toe onder de **instructie if** aan het einde van het .py-bestand:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Sla *myPythonProject.py*op.

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Als u de consoletoepassing wilt uitvoeren, klikt u op **Start** in Visual Studio.

2. Druk op **Enter** nadat de status van elke resource is geretourneerd. In de statusinformatie ziet u een **status geslaagde** inrichting. Nadat de virtuele machine is gemaakt, hebt u de mogelijkheid om alle resources die u maakt te verwijderen. Voordat u op **Enter** drukt om resources te verwijderen, u enkele minuten de tijd nemen om de creatie ervan in de Azure-portal te verifiëren. Als u de Azure-portal hebt geopend, moet u mogelijk het blad vernieuwen om nieuwe bronnen weer te geven.  

    Het duurt ongeveer vijf minuten voordat deze consoletoepassing volledig van begin tot eind wordt uitgevoerd. Het kan enkele minuten duren voordat de toepassing is voltooid voordat alle bronnen en de brongroep zijn verwijderd.


## <a name="next-steps"></a>Volgende stappen

- Als er problemen met de implementatie zijn, raadpleegt u als volgende stap [Problemen met resourcegroepimplementaties in Azure Portal oplossen](../../resource-manager-troubleshoot-deployments-portal.md).
- Meer informatie over de [Azure Python-bibliotheek](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

