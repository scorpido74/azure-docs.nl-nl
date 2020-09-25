---
title: Azure Hybrid Benefit-en Linux-Vm's
description: Met de Azure Hybrid Benefit kunt u geld besparen op uw virtuele Linux-machines die worden uitgevoerd op Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: d62eaf96354627e0c1e4e0a31bb16fb3265f66ac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279770"
---
# <a name="preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Voor beeld: Azure Hybrid Benefit: hoe dit geldt voor Linux Virtual Machines

## <a name="overview"></a>Overzicht

Met Azure Hybrid Benefit kunt u uw virtuele machines (Vm's) van on-Red Hat Enterprise Linux premises (RHEL) en SUSE Linux Enterprise Server (SLES) eenvoudiger migreren naar Azure met behulp van uw eigen reeds bestaande Red Hat-of SUSE-software abonnement. Met dit voor deel betaalt u alleen voor de kosten van de infra structuur van uw VM omdat de software kosten worden gedekt door uw RHEL-of SLES-abonnement. Het voor deel is van toepassing op alle RHEL-installatie kopieën (PAYG) van de SLES Marketplace.

> [!IMPORTANT]
> Azure Hybrid Benefit voor Linux-Vm's is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="benefit-description"></a>Beschrijving van voor deel

Via Azure Hybrid Benefit kunt u eenvoudig uw on-premises RHEL-en SLES-servers naar Azure migreren door bestaande RHEL-en SLES PAYG-Vm's te converteren naar Azure om uw eigen abonnement (BYOS) in te zetten. Virtuele machines die zijn geïmplementeerd op basis van PAYG-installatie kopieën in azure, bevatten doorgaans kosten voor de infra structuur en software kosten. Met de Azure Hybrid Benefit kunnen PAYG Vm's worden geconverteerd naar een BYOS facturerings model zonder een herimplementatie, waardoor uitval tijd risico wordt voor komen.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit kosten visualisatie op Linux-Vm's.":::

Als u het voor deel inschakelt op een RHEL-of SLES-VM, worden er geen kosten in rekening gebracht voor de extra software die doorgaans op een PAYG-VM is gemaakt. In plaats daarvan begint uw VM met het uitbrengen van de kosten voor BYOS, die alleen de kosten voor de berekenings-hardware en geen software kosten bevatten.

Indien gewenst kunt u ook een virtuele machine converteren waarvoor het voor deel is ingeschakeld op het PAYG-facturerings model.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Bereik van Azure Hybrid Benefit geschiktheid voor Linux-Vm's

Azure Hybrid Benefit is beschikbaar voor alle RHEL-en SLES Marketplace PAYG-installatie kopieën. Het voor deel is nog niet beschikbaar voor RHEL of SLES Marketplace BYOS-installatie kopieën of aangepaste installatie kopieën.

Gereserveerde instanties, toegewezen hosts en SQL Hybrid bene fits komen niet in aanmerking voor de Azure Hybrid Benefit als u het voor deel van Linux-Vm's al gebruikt.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

Azure Hybrid Benefit bevindt zich momenteel in een preview-fase voor Linux-Vm's. Zodra u toegang hebt tot de preview, kunt u het voor deel van de Azure Portal of de Azure CLI inschakelen.

### <a name="preview"></a>Preview

In deze fase kunt u toegang krijgen tot het voor deel door het formulier [hier](https://aka.ms/ahb-linux-form)in te vullen. Wanneer u het formulier hebt ingevuld, worden uw Azure-abonnement (en) ingeschakeld voor het voor deel en ontvangt u binnen drie werk dagen een bevestiging van micro soft.

### <a name="red-hat-customers"></a>Red Hat-klanten

1.    Vul hierboven het aanvraag formulier voor de voor beeld in
1.    Registreren bij het [Red Hat Cloud Access-programma](https://aka.ms/rhel-cloud-access)
1.    Schakel uw Azure-abonnement (en) in voor Cloud toegang en schakel de abonnementen in die de Vm's bevatten waarvoor u het voor deel wilt gebruiken
1.    Pas het voor deel toe op uw bestaande Vm's via de Azure Portal of Azure CLI
1.    Registreer uw Vm's om het voor deel te ontvangen met een afzonderlijke bron van updates

### <a name="suse-customers"></a>SUSE-klanten

1.    Vul hierboven het aanvraag formulier voor de voor beeld in
1.    Registreren bij het SUSE open bare-Cloud programma
1.    Pas het voor deel toe op uw bestaande Vm's via de Azure Portal of Azure CLI
1.    Registreer uw Vm's om het voor deel te ontvangen met een afzonderlijke bron van updates

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Het voor deel in de Azure Portal in-en uitschakelen

U kunt het voor deel van bestaande Vm's inschakelen door de Blade **configuratie** te bezoeken en de stappen daar te volgen. U kunt het voor deel voor nieuwe Vm's inschakelen tijdens het maken van de VM.

### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Het voor deel in de Azure CLI inschakelen en uitschakelen

U kunt de opdracht AZ VM update gebruiken om bestaande Vm's bij te werken. Voor RHEL Vm's voert u de opdracht uit met de para meter---type licentie van RHEL_BYOS. Voor SLES Vm's voert u de opdracht uit met de para meter---type licentie van SLES_BYOS.

#### <a name="cli-example-to-enable-the-benefit"></a>CLI-voor beeld om het voor deel in te scha kelen:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>CLI-voor beeld om het voor deel uit te scha kelen:
Als u het voor deel wilt uitschakelen, gebruikt u de waarde geen in licentie type
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-voor beeld om het voor deel in te scha kelen voor een groot aantal Vm's
Als u het voor deel voor een groot aantal Vm's wilt inschakelen, kunt u de `--ids` para meter in de Azure CLI gebruiken.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

In de volgende voor beelden ziet u twee methoden voor het ophalen van een lijst met resource-Id's, één op het niveau van de resource groep, één op het abonnements niveau.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>De AHB-status van een virtuele machine controleren
U kunt de status van de AHB van een virtuele machine op drie manieren bekijken: de portal controleren met behulp van Azure CLI of de Azure Instance Metadata Service (Azure IMDS) gebruiken.


### <a name="portal"></a>Portal

Bekijk de Blade configuratie en controleer de licentie status om te zien of AHB is ingeschakeld voor uw virtuele machine.

### <a name="azure-cli"></a>Azure CLI

De `az vm get-instance-view` opdracht kan worden gebruikt voor dit doel einde. Zoek naar een License type veld in het antwoord. Als het veld License type bestaat en de waarde RHEL_BYOS of SLES_BYOS is, is voor uw virtuele machine het voor deel ingeschakeld.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service

Vanuit de VM zelf kunt u een query uitvoeren op de IMDS Attestende meta gegevens om de License type van de virtuele machine te bepalen. De License type-waarde ' RHEL_BYOS ' of ' SLES_BYOS ' geeft aan dat het voor deel van uw virtuele machine is ingeschakeld. Meer informatie over [hier](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service#attested-data) Geattesteerde meta gegevens

## <a name="compliance"></a>Naleving

### <a name="red-hat"></a>Red Hat

Als u Azure Hybrid Benefit voor uw RHEL-Vm's wilt gebruiken, moet u eerst zijn geregistreerd bij het Red Hat Cloud Access-programma. U kunt dit doen via de site voor Red Hat Cloud Access hier. Zodra u het voor deel op uw virtuele machine hebt ingeschakeld, moet u de virtuele machine registreren bij uw eigen bron van updates, met een Red Hat Subscription Manager of een Red Hat Satellite. Bij het registreren voor updates zorgt u ervoor dat u een ondersteunde status behoudt.

### <a name="suse"></a>SUSE

Als u Azure Hybrid Benefit voor uw SLES-Vm's wilt gebruiken, moet u eerst zijn geregistreerd bij het SUSE open bare Cloud-programma. Meer informatie over het programma vindt u hier. Zodra u SUSE-abonnementen hebt aangeschaft, moet u uw virtuele machines met behulp van de abonnementen registreren op uw eigen bron van updates via het SUSE-klanten centrum, de server voor abonnements beheer of de beheerder van SUSE.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
*V: kan ik het licentie type ' RHEL_BYOS ' gebruiken met een SLES-installatie kopie of vice versa?*

A: Nee, dat kan niet. Als u een licentie type opgeeft dat onjuist overeenkomt met de distributie die op uw virtuele machine wordt uitgevoerd, worden er geen meta gegevens voor facturering bijgewerkt. Als u echter per ongeluk het verkeerde licentie type invoert, wordt het voor deel nog steeds mogelijk als u uw VM opnieuw bijwerkt naar het juiste licentie type.

*V: Ik heb een Red Hat Cloud-toegang geregistreerd, maar ik kan het voor deel van mijn RHEL-Vm's nog steeds niet inschakelen. Wat moet ik doen?*

A: het kan enige tijd duren voordat de inschrijving van uw Red Hat Cloud Access-abonnement van Red Hat naar Azure wordt door gegeven. Neem contact op met micro soft ondersteuning als u na één werkdag nog steeds de fout ziet.

## <a name="common-errors"></a>Algemene fouten
Deze sectie bevat een lijst met veelvoorkomende fouten en stappen voor het oplossen van problemen.

| Fout | Oplossing |
| ----- | ---------- |
| Het abonnement is niet geregistreerd voor de Linux-preview van Azure Hybrid Benefit. Voor stapsgewijze instructies raadpleegt u https://aka.ms/ahb-linux ' | Vul het formulier in https://aka.ms/ahb-linux-form om u te registreren voor de Linux-preview van de Azure Hybrid Benefit.
| "De actie kan niet worden voltooid omdat uit onze gegevens blijkt dat u Red Hat Cloud Access niet hebt ingeschakeld voor uw Azure-abonnement............ | Als u het voor deel van RHEL Vm's wilt gebruiken, moet u uw Azure-abonnement (en) eerst registreren bij Red Hat Cloud Access. Bezoek deze koppeling voor meer informatie over het registreren van uw Azure-abonnementen voor Red Hat Cloud Access

## <a name="next-steps"></a>Volgende stappen
* Ga aan de slag met de preview-versie door het formulier [hier](https://aka.ms/ahb-linux-form)in te vullen.
