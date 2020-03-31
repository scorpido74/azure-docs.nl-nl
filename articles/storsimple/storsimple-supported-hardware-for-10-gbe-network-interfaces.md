---
title: Hardware voor StorSimple 10 GbE-interfaces | Microsoft Documenten
description: Beschrijft de ondersteunde small form-factor pluggable (SFP) transceivers, kabels en switches voor de 10 GbE netwerkinterfaces op uw StorSimple-apparaat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965021"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Ondersteunde hardware voor de 10 GbE-netwerkinterfaces op uw StorSimple-apparaat

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
In dit artikel vindt u informatie over aanvullende hardware die werkt met uw Microsoft Azure StorSimple-apparaat.

## <a name="list-of-devices-tested-by-microsoft"></a>Lijst met apparaten die door Microsoft zijn getest
Microsoft heeft de volgende small form-factor pluggable (SFP) transceivers, kabels en switches getest om ervoor te zorgen dat ze optimaal functioneren met apparaten. (De volgende tabellen worden bijgewerkt als nieuwe hardware wordt getest.)

### <a name="sfp-transceivers"></a>SFP+ Transceivers
| Merk | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kabels
| S. Nee. | Merk | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Switches
| S. Nee. | Merk | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lijst van apparaten die in het veld zijn getest
Deze sectie bevat de lijst met apparaten die door Klanten van StorSimple in het veld zijn geïmplementeerd. Deze zijn niet getest door Microsoft, maar werken waarschijnlijk met uw StorSimple-apparaat.

| Parameter | Waarde |
| --- | --- |
| Schakelen maken |Juniper |
| Switch-model |ex4550-32F |
| Switch-versie van het besturingssysteem |JunOS 12.3R9.4 |
| Bladmodel |Poorten aan boord (PIC 0) |
| Transceiver maken |Juniper |
| Transceiver model |Deelnummer 740-021308 <br></br> Deelnummer 740-030658 |
| Transceiver firmware versie |Rev 01 Versie 0.0 (gerapporteerd) |
| Kabelmodel |Duplex jumper LC/LC 50/125μ, OM3, LSZH |
| StorSimple-model |8600 |
| StorSimple softwareversie |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lijst van apparaten die zijn getest door oem-provider (Mellanox)
Mellanox heeft de volgende small form-factor pluggable (SFP) transceivers, kabels en switches getest om ervoor te zorgen dat ze optimaal functioneren met Mellanox netwerkinterfaces zoals de 10 GbE netwerkinterfaces op uw StorSimple-apparaat.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabels en modules ondersteund door Mellanox
In de volgende tabel worden de kabels en modules vermeld die door Mellanox worden ondersteund. Deze zijn niet getest door Microsoft, maar werken waarschijnlijk met uw StorSimple-apparaat.

| S. Nee. | Snelheid | Model | Beschrijving | Merk |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |passieve koperen kabel SFP+ 10 Gb/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |passieve koperen kabel SFP+ 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |passieve koperen kabel SFP+ 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |passieve koperen kabel SFP+ 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP+ kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP+ kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP+ kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP+ naar SFP+ 1m Direct Attach Copper Cable |Hp |
| 9. |10 GbE |455883-B21 PK BLc |10 Gb SR SFP+ Opt |Hp |
| 10. |10 GbE |455886-B21 PK BLc |10 Gb LR SFP+ Opt |Hp |
| 11. |10 GbE |487649-B21 PK BLc |SFP+ 0,5 m 10GbE koperen kabel |Hp |
| 12. |10 GbE |487652-B21 PK BLc |SFP+ 1m 10GbE koperen kabel |Hp |
| 13. |10 GbE |487655-B21 PK BLc |SFP+ 3m 10GbE koperen kabel |Hp |
| 14. |10 GbE |487658-B21 PK BLc |SFP+ 7m 10GbE koperen kabel |Hp |
| 15. |10 GbE |537963-B21 PK BLc |SFP+ 5m 10GbE koperen kabel |Hp |
| 16. |10 GbE |AP784A HP |3m C-serie Passieve Koperen SFP+ kabel |Hp |
| 17. |10 GbE |AP785A PK |5m C-serie Passieve Koperen SFP+ kabel |Hp |
| 18. |10 GbE |AP818A PK |1m B-serie Active Copper SFP+ kabel |Hp |
| 19. |10 GbE |AP819A PK |3m B-serie Active Copper SFP+ kabel |Hp |
| 20. |10 GbE |J9150A PK |X132 10G SFP+ LC SR Transceiver |Hp |
| 21. |10 GbE |J9151A PK |X132 10G SFP+ LC LR Transceiver |Hp |
| 22. |10 GbE |J9283B PK |X242 10G SFP+ SFP+ 3m DAC-kabel |Hp |
| 23. |10 GbE |J9285B PK |X242 10G SFP+ SFP+ 7m DAC-kabel |Hp |
| 24. |10 GbE |JD095B PK |X240 10G SFP+ SFP+ 0,65m DAC-kabel |Hp |
| 25. |10 GbE |JD096B PK |X240 10G SFP+ SFP+ 1,2 m DAC-kabel |Hp |
| 26. |10 GbE |JD097B PK |X240 10G SFP+ SFP+ 3m DAD-kabel |Hp |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP naar SFP+ adapter |Mellanox-technologieën |
| 28. |10 GbE |MC2309124-006 Mt |Passieve koperen kabel 1x SFP+ tot QSFP 10Gb/s 24awg 7m |Mellanox-technologieën |
| 29. |10 GbE |MC2309124-007 Mt |Passieve koperen kabel 1x SFP+ tot QSFP 10Gb/s 24awg 7m |Mellanox-technologieën |
| 30. |10 GbE |MC2309130-003 Mt |Passieve koperen kabel 1x SFP+ naar QSFP 10Gb/s 30awg 3m |Mellanox-technologieën |
| 31. |10 GbE |MC2309130-00A Mt |Passieve koperen kabel 1x SFP+ naar QSFP 10Gb/s 30awg 0,5 m |Mellanox-technologieën |
| 32. |10 GbE |MC3309124-005 Mt |Passieve koperen kabel 1x SFP+ 10 Gb/s 24awg 5m |Mellanox-technologieën |
| 33. |10 GbE |MC3309124-007 Mt |Passieve koperen kabel 1x SFP+ 10 Gb/s 24awg 7m |Mellanox-technologieën |
| 34. |10 GbE |MC3309130-003 Mt |Passieve koperen kabel 1x SFP+ 10 Gb/s 30awg 3m |Mellanox-technologieën |
| 35. |10 GbE |MC3309130-00A Mt |Passieve koperen kabel 1x SFP+ 10 Gb/s 30awg 0,5 m |Mellanox-technologieën |

### <a name="switches-supported-by-mellanox"></a>Switches ondersteund door Mellanox
In de volgende tabel worden de schakelaars weergegeven die door Mellanox worden ondersteund. Deze zijn niet getest door Microsoft, maar werken waarschijnlijk met uw StorSimple-apparaat.

| S. Nee. | Snelheid | Model | Beschrijving | Merk |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet Blade Switch |Hp |
| 2. |10 GbE |538113-B21 |HP 10GbE Pass-Through Module (PTM) |Hp |
| 3. |10 GbE |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit schaalbare switchmodule |IBM |
| 4. |1 GbE |3020 |Cisco Catalyst 3020 1GbE switch blade |Cisco |
| 5. |1 GbE |3020X |Cisco Catalyst 3020X 1GbE switch blade |Cisco |
| 6. |1 GbE |438030-B21 |HP 1GbE-switchmodule - GbE2c Layer 2/3 Ethernet Blade Switch |Hp |
| 7. |1 GbE |6120G |HP ProCurve 6120G/XG 1GbE-switchblad |Hp |

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over StorSimple-hardwarecomponenten en -status](storsimple-monitor-hardware-status.md).

