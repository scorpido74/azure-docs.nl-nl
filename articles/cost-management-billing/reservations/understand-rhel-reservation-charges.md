---
title: Abonnementskortingen voor Red Hat-reserveringen - Azure
description: Meer informatie over hoe Red Hat-abonnementskortingen worden toegepast op Red Hat-software op virtuele machines.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 173967356c17150d5694b48e6ed2acd78155ca4b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199225"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Meer informatie over hoe de abonnementskorting voor Red Hat Linux Enterprise-softwarereservering wordt toegepast voor Azure

Nadat u een Red Hat Linux-abonnement hebt gekocht, wordt de korting automatisch toegepast op geïmplementeerde virtuele machines (VM's) met Red Hat die overeenkomen met de reservering. Een Red Hat Linux-abonnement dekt de kosten voor het uitvoeren van de Red Hat-software op een virtuele Azure-machine.

Als u het juiste Red Hat Linux-abonnement wilt kopen, moet u weten wat voor virtuele Red Hat-machines u uitvoert en hoeveel vCPU's deze VM's gebruiken. Gebruik de volgende secties om met behulp van het CSV-bestand over uw gebruik te bepalen welk abonnement u het beste kunt kopen.

## <a name="discount-applies-to-different-vm-sizes"></a>Korting is van toepassing op verschillende VM-grootten

Net als gereserveerde VM-instanties, biedt het kopen van een Red Hat-abonnement flexibiliteit van instantiegrootte. Dit betekent dat uw korting ook van toepassing is wanneer u een VM implementeert met een ander aantal vCPU's. De korting geldt voor verschillende VM-grootten binnen het softwareabonnement.

De korting is afhankelijk van de verhouding die in de volgende tabellen wordt weergegeven. De verhouding vergelijkt de relatieve footprint voor elke meter in die groep. De verhouding is afhankelijk van het aantal VM-vCPU's. Gebruik de verhoudingswaarde om te berekenen hoeveel VM-exemplaren de Red Hat Linux-abonnementskorting krijgen.

Als u bijvoorbeeld een abonnement voor Red Hat Linux Enterprise Server koopt voor een virtuele machine met 3 of 4 vCPU's, is de verhouding voor die reservering 2. De korting geldt voor de kosten van de Red Hat-software voor:

- 2 geïmplementeerde VM's met 1 of 2 vCPU's,
- 1 geïmplementeerde VM met 3 of 4 vCPU's
- of 0,77 of ongeveer 77% van een VM machine met 5 of meer vCPU's

De verhouding voor 5 of meer vCPU's is 2,6. Een reservering voor Red Hat met een VM met 5 of meer vCPU's dekt dus slechts een gedeelte van de softwarekosten, ongeveer 77%.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Meer informatie over het gebruik van Red Hat-VM's voor u een aankoop doet

In de volgende tabellen ziet u de softwareabonnementen waarvoor u een reservering kunt kopen, de bijbehorende gebruiksmeters en de bijbehorende verhoudingen.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Namen op Marketplace van Azure-portal:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (meest recente lvm)

|Red Hat VM | MeterId| Verhouding| Voorbeeld van VM-grootte|
| -------| ------------------------| --- |--- |
|VM-licentie 1-4 vCPU's|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|VM-licentie 1-4 vCPU's|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|VM-licentie 1-4 vCPU's|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|VM-licentie 5+ vCPU's|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|VM-licentie 5+ vCPU's|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|VM-licentie 44 vCPU's|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|VM-licentie 60 vCPU's|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux voor SAP met hoge beschikbaarheid

Naam op Marketplace van Azure-portal:

|Red Hat VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- | --- | ------------------------| --- | --- |
|VM-licentie 1-4 vCPU's |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|VM-licentie 5+ vCPU's|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux met hoge beschikbaarheid

Namen op Marketplace van Azure-portal:

|Red Hat VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- | --- |
|VM-licentie 1-4 vCPU's|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|VM-licentie 5+ vCPU's|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Namen op Marketplace van Azure-portal:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- |--- |
|VM-licentie 1 vCPU|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|VM-licentie 2 vCPU's|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|VM-licentie 4 vCPU's|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|VM-licentie 6 vCPU's|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|VM-licentie 8 vCPU's|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|VM-licentie 12 vCPU's|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|VM-licentie 16 vCPU's|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|VM-licentie 20 vCPU's|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|VM-licentie 24 vCPU's|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|VM-licentie 32 vCPU's|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|VM-licentie 40 vCPU's|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|VM-licentie 44 vCPU's|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|VM-licentie 60 vCPU's|a22bb342-ba9a-4529-a178-39a92ce770b6|2,166666667||
|VM-licentie 64 vCPU's|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|VM-licentie 72 vCPU's|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|VM-licentie 96 vCPU's|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|VM-licentie 128 vCPU's|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Namen op Marketplace van Azure-portal:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- |--- |
|VM-licentie 1 vCPU|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|VM-licentie 2 vCPU's|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|VM-licentie 4 vCPU's|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|VM-licentie 6 vCPU's|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|VM-licentie 8 vCPU's|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|VM-licentie 12 vCPU's|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|VM-licentie 16 vCPU's|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|VM-licentie 20 vCPU's|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|VM-licentie 24 vCPU's|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|VM-licentie 32 vCPU's|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|VM-licentie 40 vCPU's|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|VM-licentie 44 vCPU's|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|VM-licentie 60 vCPU's|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|VM-licentie 64 vCPU's|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|VM-licentie 72 vCPU's|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|VM-licentie 96 vCPU's|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|VM-licentie 128 vCPU's|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie over reserveringen de volgende artikelen:

- [Wat zijn reserveringen voor Azure](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Red Hat-softwareabonnementen met Azure-reserveringen](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Reserveringen voor Azure beheren](manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
