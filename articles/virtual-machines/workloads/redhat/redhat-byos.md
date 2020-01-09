---
title: Red Hat Enterprise Linux uw eigen abonnement-installatie kopieën maken | Microsoft Docs
description: Meer informatie over het opnemen van uw eigen abonnements installatie kopieën voor Red Hat Enterprise Linux op Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486506"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux uw eigen abonnements installatie kopieën in azure
Red Hat Enterprise Linux-installatie kopieën (RHEL) zijn beschikbaar in azure via een PAYG (betalen per gebruik) of een BYOS-model (uw eigen abonnement). Dit document bevat een overzicht van de BYOS-installatie kopieën in Azure.

## <a name="important-points-to-consider"></a>Belang rijke punten om rekening mee te houden

- De RHEL BYOS-installatie kopieën die in dit programma zijn opgenomen, zijn RHEL installatie kopieën in de productie kant, vergelijkbaar met de RHEL PAYG-installatie kopieën in de Azure Gallery/Marketplace. Het registratie proces voor het verkrijgen van de installatie kopieën is in preview.

- De afbeeldingen volgen onze huidige beleids regels die worden beschreven in [Red Hat Enterprise Linux installatie kopieën op Azure](./redhat-images.md)

- Standaard ondersteunings beleid is van toepassing op Vm's die zijn gemaakt op basis van deze installatie kopieën

- De Vm's die zijn ingericht vanuit RHEL BYOS-installatie kopieën bevatten geen RHEL-kosten voor RHEL PAYG-installatie kopieën

- De installatie kopieën hebben geen recht, dus moet u abonnement-manager gebruiken om de Vm's te registreren en te abonneren om updates van Red Hat direct te ontvangen

- Het is momenteel niet mogelijk om dynamisch te scha kelen tussen BYOS-en PAYG-facturerings modellen voor Linux-installatie kopieën. Het opnieuw implementeren van de VM vanaf de betreffende installatie kopie is vereist om te scha kelen tussen het facturerings model

- Azure Disk Encryption (ADE) wordt ondersteund op deze RHEL BYOS-installatie kopieën. ADE-ondersteuning is momenteel beschikbaar als preview-versie. U moet zich registreren bij Red Hat met behulp van abonnement-manager voordat u ADE configureert. Wanneer u de optie voor het configureren van ADE hebt geregistreerd, gaat u naar het volgende: [Azure Disk Encryption inschakelen voor vm's van Linux IaaS](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- Hoewel de installatie kopieën niet worden gewijzigd (naast de standaard updates en patches), is het registratie proces een preview-versie en wordt de stroom verder verbeterd om het proces te stroom lijnen

- U hebt volledige controle over de virtuele machines die al zijn ingericht op basis van deze installatie kopieën of de moment opnamen ervan, ongeacht de uiteindelijke implementatie

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Vereisten en voor waarden voor toegang tot de RHEL BYOS-installatie kopieën

1. U kunt vertrouwd raken met de voor waarden van het [Red Hat Cloud Access-programma](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) en registreren bij de [inschrijvings pagina voor Cloud toegang van Red Hat](https://access.redhat.com/cloude/manager/image_imports/new).

1. Vul het [formulier voor de RHEL BYOS-toegangs aanvraag](https://aka.ms/rhel-byos)in. U moet beschikken over uw Red Hat-account nummer en uw Azure-abonnement (en) die u hebt om toegang te krijgen tot de RHEL BYOS-installatie kopieën met.

1. Bij controle en goed keuring door zowel Red Hat als micro soft worden uw Azure-abonnement (en) ingeschakeld voor toegang tot de installatie kopie.

### <a name="expected-time-for-image-access"></a>Verwachte tijd voor toegang tot de installatie kopie

Bij het volt ooien van het RHEL BYOS-formulier en het accepteren van de voor waarden, valideert Red Hat uw geschiktheid voor de BYOS-installatie kopieën binnen drie werk dagen en ontvangt u, indien geldig, de BYOS-installatie kopieën binnen één werkdag.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>De RHEL BYOS-installatie kopieën van de Azure Portal gebruiken

1. Nadat uw abonnement is ingeschakeld voor RHEL BYOS-installatie kopieën, kunt u het vinden in de [Azure Portal](https://portal.azure.com) door te navigeren om **een resource te maken** en vervolgens **alle weer te geven**.

1. Boven aan de pagina ziet u dat u persoonlijke aanbiedingen hebt.

    ![Privé aanbiedingen voor Marketplace](./media/rhel-byos-privateoffers.png)

1. U kunt klikken op de paarse koppeling of omlaag schuiven naar de onderkant van de pagina om uw persoonlijke aanbiedingen te bekijken.

1. De rest van het inrichten in de gebruikers interface is niet anders dan andere bestaande Red Hat-installatie kopieën. Kies uw RHEL-versie en volg de aanwijzingen om uw VM in te richten. Met dit proces kunt u ook de voor waarden van de afbeelding in de laatste stap accepteren.

>[!NOTE]
>Met deze stappen wordt de RHEL BYOS-installatie kopie niet ingeschakeld voor programmatische implementatie. er is een extra stap vereist, zoals beschreven in de sectie ' aanvullende informatie ' hieronder.

De rest van dit document richt zich op de CLI-methode om de voor waarden van de installatie kopie in te richten en te accepteren. De gebruikers interface en CLI zijn volledig uitwisselbaar, voor zover het uiteindelijke resultaat (een ingerichte RHEL BYOS VM) betreft.

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>De RHEL BYOS-installatie kopieën van de Azure CLI gebruiken
De volgende reeks instructies begeleiden u bij het eerste implementatie proces voor een RHEL-VM met behulp van de Azure CLI. Bij deze instructies wordt ervan uitgegaan dat u de [Azure cli hebt geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Zorg ervoor dat u alle kleine letters in de Publisher-, aanbiedings-, plan-en afbeeldings verwijzingen gebruikt voor de volgende opdrachten:

1. Controleer of u zich in het gewenste abonnement bevindt:
    ```azurecli
    az account show -o=json
    ```

1. Maak een resource groep voor uw RHEL BYOS-VM:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. De voor waarden van de installatie kopie accepteren:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Deze voor waarden moeten eenmaal worden geaccepteerd *per Azure-abonnement, per afbeeldings-SKU*.

1. Beschrijving Valideer uw VM-implementatie met de volgende opdracht:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Richt uw VM in door dezelfde opdracht uit te voeren als hierboven zonder het argument `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH in uw virtuele machine en controleer of u een onrechtse installatie kopie hebt. U kunt dit doen door `sudo yum repolist`uit te voeren. Met de uitvoer wordt u gevraagd om abonnement-manager te gebruiken voor het registreren van de virtuele machine met Red Hat.

## <a name="additional-information"></a>Aanvullende informatie
- Als u probeert een virtuele machine in te richten op een abonnement dat niet is ingeschakeld voor deze aanbieding, wordt de volgende fout weer geven en moet u contact opnemen met micro soft of Red Hat om uw abonnement in te scha kelen.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Als u een moment opname maakt van de RHEL BYOS-installatie kopie en de installatie kopie in de [Galerie met gedeelde afbeeldingen](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)publiceert, moet u plan gegevens opgeven die overeenkomen met de oorspronkelijke bron van de moment opname. De opdracht kan er bijvoorbeeld als volgt uitzien (Let op de plan parameters op de laatste regel):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Als u Automation gebruikt om Vm's in te richten vanuit de RHEL BYOS-installatie kopieën, moet u plan parameters opgeven die vergelijkbaar zijn met wat hierboven is weer gegeven. Als u bijvoorbeeld terraform gebruikt, geeft u de plan gegevens op in een [plan blok](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Red Hat-update-infra structuur](./redhat-rhui.md).
* Ga naar de [documentatie pagina](./redhat-images.md)voor meer informatie over de Red Hat-afbeeldingen in Azure.
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .