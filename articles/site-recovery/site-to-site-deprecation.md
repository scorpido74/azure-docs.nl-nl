---
title: Afschrijving van noodherstel tussen door de klant beheerde sites (met VMM) met Azure Site Recovery | Microsoft Documenten
description: Details over de komende afschaffing van DR tussen sites die eigendom zijn van de klant met Hyper-V en tussen sites die door SCVMM naar Azure worden beheerd en alternatieve opties
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661667"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Afschrijving van noodherstel tussen door de klant beheerde sites (met VMM) met Azure Site Recovery

In dit artikel worden de komende afschaffingsplannen, de bijbehorende implicaties en de alternatieve opties beschreven die beschikbaar zijn voor de klanten voor het volgende scenario:

DR tussen sites die eigendom zijn van klanten die worden beheerd door System Center Virtual Machine Manager (SCVMM) met behulp van Site Recovery

> [!IMPORTANT]
> Klanten wordt geadviseerd om op zijn vroegst de herstelstappen te nemen om verstoring van hun omgeving te voorkomen. 

## <a name="what-changes-should-you-expect"></a>Welke veranderingen mag u verwachten?

- Vanaf maart 2020 ontvangt u Azure-portalmeldingen & e-mailcommunicatie met de komende afschaffing van site-to-site-replicatie van Hyper-V VM's. De afschaffing staat gepland voor maart 2023.

- Als u een bestaande configuratie hebt, heeft dit geen invloed op de set-up.

- Zodra de scenario's zijn afgeschaft, tenzij de klant de alternatieve benaderingen volgt, kunnen de bestaande replicaties worden verstoord. Klanten kunnen geen DR-gerelateerde bewerkingen bekijken, beheren of uitvoeren via de Azure Site Recovery-ervaring in Azure-portal.
 
## <a name="alternatives"></a>Alternatieven 

Hieronder staan de alternatieven waaruit de klant kan kiezen om ervoor te zorgen dat hun DR-strategie niet wordt beïnvloed zodra het scenario is afgeschaft. 

- Optie 1 (Aanbevolen): kies ervoor om [Azure te gaan gebruiken als DR-doel](hyper-v-vmm-azure-tutorial.md).


- Optie 2: Kies ervoor om door te gaan met site-to-site-replicatie met behulp van de onderliggende [Hyper-V Replica-oplossing,](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)maar u DR-configuraties niet beheren met Azure Site Recovery in de Azure-portal. 


## <a name="remediation-steps"></a>Herstelstappen

Als u kiest voor optie 1, voert u de volgende stappen uit:

1. [Schakel de beveiliging van alle virtuele machines die aan de VMM's zijn gekoppeld, uit.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario) Gebruik de **optie Replicatie uitschakelen en verwijderen** of voer de genoemde scripts uit om ervoor te zorgen dat de on-premises replicatie-instellingen worden opgeschoond. 

2. [Het registreren van alle VMM-servers](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) uit de replicatieconfiguratie van site-to-site.

3. [Bereid Azure-resources](tutorial-prepare-azure-for-hyperv.md) voor om replicatie van uw VM's mogelijk te maken.
4. [On-premises Hyper-V-servers voorbereiden](hyper-v-prepare-on-premises-tutorial.md)
5. [Replicatie instellen voor de VM's in de VMM-cloud](hyper-v-vmm-azure-tutorial.md)
6. Optioneel maar aanbevolen: [een DR-drill uitvoeren](tutorial-dr-drill-azure.md)

Als u kiest voor optie 2 van het gebruik van Hyper-V-replica, voert u de volgende stappen uit:

1. Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine > **Replicatie uitschakelen**.
2. Selecteer **in Replicatie uitschakelen**de optie **Verwijderen**.

    Hiermee wordt het gerepliceerde item verwijderd uit Azure Site Recovery (facturering wordt gestopt). Replicatieconfiguratie op de on-premises virtuele machine **wordt niet** opgeschoond. 

## <a name="next-steps"></a>Volgende stappen
Plan de afschaffing en kies een alternatieve optie die het meest geschikt is voor uw infrastructuur en bedrijf. Als u hierover vragen hebt, neem dan contact op met Microsoft Support

