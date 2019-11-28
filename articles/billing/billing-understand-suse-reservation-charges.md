---
title: Korting softwareabonnement - Azure | Microsoft Docs
description: Lees hier alles over kortingen voor softwareabonnementen worden toegepast op software op virtuele machines.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 76fc2d782571818d14a3008c10a0c3fae07a2b7d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225729"
---
# <a name="azure-software-plan-discount"></a>Korting voor abonnement op Azure-software

Abonnementen voor Azure-software voor SUSE en RedHat zijn reserveringen die van toepassing zijn op geïmplementeerde VM's. De korting voor het softwareabonnement wordt toegepast op het softwaregebruik van geïmplementeerde VM's die overeenkomen met de reservering.

Wanneer u een VM afsluit, wordt de korting automatisch toegepast op een andere overeenkomende VM, indien beschikbaar. Een softwareabonnement dekt de kosten voor het uitvoeren van de software op een VM. Andere kosten, zoals die voor berekeningen, opslag en netwerken, worden afzonderlijk in rekening gebracht.

U kunt alleen het juiste abonnement kopen als u een goed beeld hebt van het VM-gebruik en het aantal vCPU's dat op deze VM's wordt uitgevoerd. Gebruik de volgende secties om te bepalen welk abonnement u het beste kunt kopen, op basis van uw gebruiksgegevens.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Gebruik van Red Hat-VM's controleren voordat u een aankoop doet

Zoek de productnaam op in uw gebruiksgegevens en koop een RedHat-abonnement met hetzelfde type en dezelfde grootte.

Als in de gebruiksgegevens bijvoorbeeld **Red Hat Enterprise Linux - 1-4 vCPU VM License** wordt vermeld, moet u **Red Hat Enterprise Linux** kopen voor **1-4 vCPU VM**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Gebruik van SUSE-VM's controleren voordat u een aankoop doet

Zoek de productnaam op in uw gebruiksgegevens en koop een SUSE-abonnement met hetzelfde type en dezelfde grootte.

Als in de gebruiksgegevens bijvoorbeeld het product **SUSE Linux Enterprise Server Priority - 2-4 vCPU VM Support** wordt vermeld, koopt u **SUSE Linux Enterprise Server Priority** voor **2-4 vCPU**.

![Voorbeeld van het selecteren van het product dat u wilt kopen](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Korting is van toepassing op verschillende VM-grootten voor SUSE-abonnementen

Net als bij gereserveerde VM-instanties, biedt het kopen van een SUSE-abonnement flexibiliteit van instantiegrootte. Dit betekent dat uw korting ook van toepassing is wanneer u een VM implementeert met een ander aantal vCPU's. De korting geldt voor verschillende VM-grootten binnen het softwareabonnement.

De korting is afhankelijk van de verhouding die in de volgende tabellen wordt weergegeven. De verhouding vergelijkt de relatieve footprint voor elke meter in die groep. De verhouding is afhankelijk van het aantal VM-vCPU's. Gebruik de verhoudingswaarde om te berekenen hoeveel VM-exemplaren de korting van het SUSE Linux-abonnement krijgen.

Als u bijvoorbeeld een abonnement voor SUSE Linux Enterprise Server for HPC Priority koopt voor een VM met 3 of 4 vCPU's, is de verhouding voor die reservering 2. De korting dekt de kosten van de SUSE-software voor:

- 2 geïmplementeerde VM's met 1 of 2 vCPU's
- 1 geïmplementeerde VM met 3 of 4 vCPU's
- of 0,77 of ongeveer 77% van een VM machine met 5 of meer vCPU's

De verhouding voor 5 of meer vCPU's is 2,6. Een reservering voor SUSE met een VM met 5 of meer vCPU's dekt dus slechts een gedeelte van de softwarekosten, ongeveer 77%.

In de volgende tabellen ziet u de softwareabonnementen waarvoor u een reservering kunt kopen, de bijbehorende gebruiksmeters en de bijbehorende verhoudingen.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Naam op Marketplace van Azure-portal:

- SLES 12 SP3 for HPC (Priority)

|SUSE-VM | MeterId| Verhouding| Voorbeeld van VM-grootte|
| -------| ------------------------| --- |--- |
|SLES for HPC 1-2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC 3-4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC 5+ vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Naam op Marketplace van Azure-portal:

- SLES 12 SP3 for HPC

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- | --- | ------------------------| --- |
|SLES for HPC 1-2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC 3-4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES for HPC 5+ vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Namen op Marketplace van Azure-portal:

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority 1-2 vCPUs|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority 3-4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority 5+ vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Namen op Marketplace van Azure-portal:

- SLES 15 (PRIORITY)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2-4 vCPUs |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2-4 vCPUs |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vCPUs |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPUs |e11331a8-fd32-4e71-b60e-4de2a818c67a|3,2|D8s_v3|
|SLES 12 core vCPUs |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3,2||
|SLES 16 vCPUs |bb21066f-fe46-46d3-8006-b326b1663e52|3,2| D16s_v3|
|SLES 20 vCPUs |c5228804-1de6-4bd4-a61c-501d9003acc8|3,2| |
|SLES 24 cores vCPUs |-005d-4075-ac11-822ccde9e8f6|3,2| ND24s|
|SLES 32 vCPUs |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3,2| D32s_v3|
|SLES 40 cores vCPUs |a161d3d3-0592-4956-9b64-6829678b6506|3,2||
|SLES 64 vCPUs |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3,2| D64s_v3|
|SLES 72 cores vCPUs |93329a72-24d7-4faa-93d9-203f367ed334|3,2|F72s_v2|
|SLES 96 cores vCPUs |2018c3a8-ff13-41f8-b64d-9558c5206547|3,2||
|SLES 128 cores vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3,2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Namen op Marketplace van Azure-portal:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- |--- |
|SLES 1-2 cores vCPUs |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 cores vCPUs |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES 5+ vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over reserveringen:

- [Wat zijn Azure-reserveringen?](billing-save-compute-costs-reservations.md)
- [Vooruitbetalen voor Azure-softwareabonnementen](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](billing-understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
