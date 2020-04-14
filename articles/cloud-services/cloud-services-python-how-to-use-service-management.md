---
title: De handleiding Service Management API (Python) gebruiken -
description: Meer informatie over het programmatisch uitvoeren van algemene servicebeheertaken van Python.
services: cloud-services
documentationcenter: python
author: tanmaygore
manager: vashan
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: tagore
ms.openlocfilehash: 135dd92f7af4397f2053ea0bdc15d98dfad93914
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253353"
---
# <a name="use-service-management-from-python"></a>Servicebeheer van Python gebruiken
In deze handleiding ziet u hoe u programmatisch algemene servicebeheertaken van Python uitvoeren. De klasse **ServiceManagementService** in de [Azure SDK voor Python](https://github.com/Azure/azure-sdk-for-python) ondersteunt programmatische toegang tot een groot deel van de functionaliteit die betrekking heeft op servicebeheer die beschikbaar is in de [Azure-portal.][management-portal] U deze functionaliteit gebruiken om cloudservices, implementaties, gegevensbeheerservices en virtuele machines te maken, bij te werken en te verwijderen. Deze functionaliteit kan nuttig zijn bij het bouwen van toepassingen die programmatische toegang tot servicebeheer nodig hebben.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Wat is servicemanagement?
De Azure Service Management API biedt programmatische toegang tot een groot deel van de functionaliteit voor servicebeheer die beschikbaar is via de [Azure-portal.][management-portal] U de Azure SDK voor Python gebruiken om uw cloudservices en opslagaccounts te beheren.

Als u de API voor servicebeheer wilt gebruiken, moet u [een Azure-account maken.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="concepts"></a><a name="Concepts"> </a>Concepten
De Azure SDK voor Python omsluit de [Api voor servicebeheer][svc-mgmt-rest-api], een REST-API. Alle API-bewerkingen worden uitgevoerd via TLS en wederzijds geverifieerd met behulp van X.509 v3-certificaten. De beheerservice is toegankelijk vanuit een service die in Azure wordt uitgevoerd. Het kan ook rechtstreeks via het internet worden geopend vanaf elke toepassing die een HTTPS-verzoek kan verzenden en een HTTPS-antwoord kan ontvangen.

## <a name="installation"></a><a name="Installation"> </a>Installeren
Alle functies beschreven in dit artikel `azure-servicemanagement-legacy` zijn beschikbaar in het pakket, die u installeren met behulp van pip. Zie [Python installeren en de Azure SDK](/azure/developer/python/azure-sdk-install)voor meer informatie over de installatie (bijvoorbeeld als u nieuw bent bij Python).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Verbinding maken met servicebeheer
Als u verbinding wilt maken met het eindpunt voor servicebeheer, hebt u uw Azure-abonnements-id en een geldig beheercertificaat nodig. U uw abonnements-id verkrijgen via de [Azure-portal.][management-portal]

> [!NOTE]
> U nu certificaten gebruiken die met OpenSSL zijn gemaakt wanneer u op Windows wordt uitgevoerd. Python 2.7.4 of hoger is vereist. We raden u aan OpenSSL te gebruiken in plaats van .pfx, omdat ondersteuning voor .pfx-certificaten in de toekomst waarschijnlijk zal worden verwijderd.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Beheercertificaten op Windows/Mac/Linux (OpenSSL)
U [OpenSSL](https://www.openssl.org/) gebruiken om uw beheercertificaat te maken. U moet twee certificaten maken, één `.cer` voor de server (een `.pem` bestand) en één voor de client (een bestand). Voer het `.pem` bestand uit om het bestand te maken:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Voer het `.cer` certificaat uit om het certificaat te maken:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Zie Overzicht van certificaten [voor Azure Cloud Services voor](cloud-services-certs-create.md)meer informatie over Azure-certificaten. Zie de documentatie op [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html).

Nadat u deze bestanden `.cer` hebt gemaakt, uploadt u het bestand naar Azure. Selecteer **uploaden**in de [Azure-portal][management-portal]op het tabblad **Instellingen** . Houd er rekening `.pem` mee waar u het bestand hebt opgeslagen.

Nadat u uw abonnements-id hebt verkregen, maakt u een certificaat en uploadt u het `.cer` bestand naar Azure en maakt u verbinding met het eindpunt voor Azure-beheer. Maak verbinding door de abonnements-id `.pem` en het pad naar het bestand door te geven aan **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

In het voorgaande `sms` voorbeeld is een **ServiceManagementService-object.** De klasse **ServiceManagementService** is de primaire klasse die wordt gebruikt voor het beheer van Azure-services.

### <a name="management-certificates-on-windows-makecert"></a>Beheercertificaten op Windows (MakeCert)
U een zelfondertekend beheercertificaat op `makecert.exe`uw machine maken met behulp van. Open een **opdrachtprompt voor Visual Studio** als **beheerder** en gebruik de volgende opdracht, waarbij *AzureCertificate* wordt vervangen door de certificaatnaam die u wilt gebruiken:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

De opdracht `.cer` maakt het bestand en installeert het in het **opslagarchief Persoonlijk** certificaat. Zie [Overzicht van certificaten voor Azure Cloud Services voor](cloud-services-certs-create.md)meer informatie.

Nadat u het certificaat `.cer` hebt gemaakt, uploadt u het bestand naar Azure. Selecteer **uploaden**in de [Azure-portal][management-portal]op het tabblad **Instellingen** .

Nadat u uw abonnements-id hebt verkregen, maakt u een certificaat en uploadt u het `.cer` bestand naar Azure en maakt u verbinding met het eindpunt voor Azure-beheer. Maak verbinding door de abonnements-id en de locatie van het certificaat in uw **persoonlijke** certificaatarchief door te geven aan **ServiceManagementService** (vervang *AzureCertificate* opnieuw door met de naam van uw certificaat).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

In het voorgaande `sms` voorbeeld is een **ServiceManagementService-object.** De klasse **ServiceManagementService** is de primaire klasse die wordt gebruikt voor het beheer van Azure-services.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Beschikbare locaties aanbieden
Als u de locaties wilt weergeven die beschikbaar zijn voor hostingservices, gebruikt u de **methode lijstlocaties.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Wanneer u een cloudservice of -opslagservice maakt, moet u een geldige locatie bieden. De **\_methode lijstlocaties** retourneert altijd een up-to-date lijst met de momenteel beschikbare locaties. Met betrekking tot dit schrijven, de beschikbare locaties zijn:

* Europa -west
* Europa - noord
* Azië - zuidoost
* Azië - oost
* VS - centraal
* VS - noord-centraal
* VS - zuid-centraal
* VS - west
* VS - oost
* Japan - oost
* Japan - west
* Brazilië - zuid
* Australië - oost
* Australië - zuidoost

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Een cloudservice maken
Wanneer u een toepassing maakt en deze uitvoert in Azure, worden de code en configuratie samen een [Azure-cloudservice][cloud service]genoemd. (Het stond bekend als een *gehoste service* in eerdere Azure-releases.) U de **methode voor gehoste\_\_service maken** gebruiken om een nieuwe gehoste service te maken. Maak de service door een gehoste servicenaam op te geven (die uniek moet zijn in Azure), een label (automatisch gecodeerd naar base64), een beschrijving en een locatie.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

U alle gehoste services voor uw abonnement weergeven met de **methode voor gehoste\_\_services** van de lijst.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Als u informatie wilt krijgen over een bepaalde gehoste service, geeft u de naam van de gehoste service door aan de methode **voor gehoste\_\_serviceeigenschappen.\_**

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Nadat u een cloudservice hebt gemaakt, implementeert u uw code naar de service met de **implementatiemethode voor\_maken.**

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Een cloudservice verwijderen
U een cloudservice verwijderen door de servicenaam door te geven aan de **gehoste\_\_servicemethode verwijderen.**

    sms.delete_hosted_service('myhostedservice')

Voordat u een service verwijderen, moeten alle implementaties voor de service eerst worden verwijderd. Zie [Een implementatie verwijderen](#DeleteDeployment)voor meer informatie.

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Een implementatie verwijderen
Als u een implementatie wilt verwijderen, gebruikt u de **implementatiemethode verwijderen.\_** In het volgende voorbeeld ziet `v1`u hoe u een implementatie met de naam verwijdert:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Een opslagservice maken
Met [een opslagservice](../storage/common/storage-create-storage-account.md) hebt u toegang tot [Azure-blobs,](../storage/blobs/storage-python-how-to-use-blob-storage.md) [tabellen](../cosmos-db/table-storage-how-to-use-python.md)en [wachtrijen.](../storage/queues/storage-python-how-to-use-queue-storage.md) Als u een opslagservice wilt maken, hebt u een naam voor de service nodig (tussen 3 en 24 kleine letters en uniek binnen Azure). U hebt ook een beschrijving, een label (maximaal 100 tekens, automatisch gecodeerd naar base64) en een locatie nodig. In het volgende voorbeeld ziet u hoe u een opslagservice maakt door een locatie op te geven:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

In het voorgaande voorbeeld kan de status van de bewerking **\_opslagaccount\_maken** worden opgehaald door het resultaat door te geven door **een opslagaccount\_\_te maken** aan de methode voor de **bewerkingsstatus\_\_ophalen.** 

U uw opslagaccounts en hun eigenschappen aanbieden met de methode **voor opslagaccounts\_\_van** de lijst.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Een opslagservice verwijderen
Als u een opslagservice wilt verwijderen, geeft u de naam van de opslagservice door aan de **methode voor\_opslagaccount\_verwijderen.** Als u een opslagservice verwijdert, worden alle gegevens die in de service zijn opgeslagen (blobs, tabellen en wachtrijen) verwijderd.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Beschikbare besturingssystemen weergeven
Als u de besturingssystemen wilt weergeven die beschikbaar zijn voor hostingservices, gebruikt u de methode van de **lijstbesturingssystemen.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

U ook de **\_methode\_\_voor het gezinsbesturingssysteem** van de lijst gebruiken, waarbij de besturingssystemen per gezin worden groepeel.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Een afbeelding van het besturingssysteem maken
Als u een afbeelding van het besturingssysteem wilt toevoegen aan de afbeeldingsopslagplaats, gebruikt u de methode **voor het toevoegen van afbeeldingsafbeeldingen.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Als u de beschikbare afbeeldingen van het besturingssysteem wilt weergeven, gebruikt u de methode **afbeeldingen van het lijstbesturingssysteem.\_\_** Het bevat alle platformafbeeldingen en gebruikersafbeeldingen.

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Een afbeelding van een besturingssysteem verwijderen
Als u een gebruikersafbeelding wilt verwijderen, gebruikt u de methode **voor het verwijderen van de afbeelding van\_het besturingssysteem.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Een virtuele machine maken
Als u een virtuele machine wilt maken, moet u eerst een [cloudservice](#CreateCloudService)maken. Maak vervolgens de implementatie van de virtuele machine met behulp van de **implementatiemethode voor virtuele\_\_machines\_maken.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Een virtuele machine verwijderen
Als u een virtuele machine wilt verwijderen, verwijdert u eerst de implementatie met behulp van de **implementatiemethode verwijderen.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

De cloudservice kan vervolgens worden verwijderd met behulp van de **gehoste\_\_servicemethode verwijderen.**

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Een virtuele machine maken op basis van een vastgelegd virtueel machinebeeld
Als u een VM-afbeelding wilt vastleggen, roept u eerst de **methode voor het vastleggen van vm-afbeelding\_\_** aan.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Gebruik de **API voor\_lijstvm-afbeeldingen\_** om ervoor te zorgen dat u de afbeelding hebt vastgelegd. Zorg ervoor dat uw afbeelding wordt weergegeven in de resultaten.

    images = sms.list_vm_images()

Als u de virtuele machine uiteindelijk wilt maken met behulp van de vastgelegde afbeelding, gebruikt u de **implementatiemethode voor virtuele\_\_machines\_maken** zoals voorheen, maar deze tijd gaat in de vm_image_name.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Zie [Een Virtuele Linux-machine](../virtual-machines/linux/classic/capture-image-classic.md)vastleggen voor meer informatie over het vastleggen van een Virtuele Linux-machine in het klassieke implementatiemodel.

Zie [Een virtuele windows-machine](../virtual-machines/windows/classic/capture-image-classic.md)vastleggen voor meer informatie over het vastleggen van een virtuele Windows-machine in het klassieke implementatiemodel.

## <a name="next-steps"></a><a name="What's Next"> </a>Volgende stappen
Nu u de basisbeginselen van servicebeheer hebt geleerd, hebt u toegang tot de [volledige API-referentiedocumentatie voor de Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) en u complexe taken eenvoudig uitvoeren om uw Python-toepassing te beheren.

Raadpleeg het [Python Developer Center](https://azure.microsoft.com/develop/python/) voor meer informatie.

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
