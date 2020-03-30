---
title: Windows-beveiligingsgebeurtenisgegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Windows-beveiligingsgebeurtenisgegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124972"
---
# <a name="connect-windows-security-events"></a>Verbinding maken met Windows-beveiligingsgebeurtenissen 

Met de connector Beveiligingsgebeurtenissen u alle beveiligingsgebeurtenissen streamen van uw Windows-systemen (servers en werkstations, fysiek en virtueel) naar uw Azure Sentinel-werkruimte. Hierdoor u Windows-beveiligingsgebeurtenissen in uw dashboards bekijken, deze gebruiken bij het maken van aangepaste waarschuwingen en erop vertrouwen om uw onderzoeken te verbeteren, zodat u meer inzicht krijgt in het netwerk van uw organisatie en uw beveiligingsactiviteiten uitbreidt Mogelijkheden. U selecteren uit de volgende sets welke gebeurtenissen u wilt streamen:<a name="event-sets"></a>

- **Alle evenementen** - Alle Windows-beveiligings- en AppLocker-gebeurtenissen.
- **Algemeen** - Een standaardset gebeurtenissen voor controledoeleinden. Een volledige gebruikerscontroletrail is opgenomen in deze set. Het bevat bijvoorbeeld zowel aanmeldings- als gebruikersafmeldingsgebeurtenissen (gebeurtenis-id's 4624, 4634). Er zijn ook controleacties zoals wijzigingen in beveiligingsgroepen, Kerberos-bewerkingen voor belangrijke domeinenen en andere soorten gebeurtenissen in overeenstemming met geaccepteerde aanbevolen procedures.

    De **algemene** gebeurtenisset kan bepaalde soorten gebeurtenissen bevatten die niet zo vaak voorkomen.  Dit komt omdat het belangrijkste punt van de **gemeenschappelijke** set is om het volume van de gebeurtenissen te verminderen tot een meer beheersbaar niveau, met behoud van volledige audit trail vermogen.

- **Minimaal** - Een kleine set gebeurtenissen die kunnen wijzen op potentiële bedreigingen. Deze set bevat geen volledig controlespoor. Het heeft alleen betrekking op gebeurtenissen die kunnen wijzen op een succesvolle schending, en andere belangrijke gebeurtenissen die zeer lage percentages van voorkomen hebben. Het bevat bijvoorbeeld geslaagde en mislukte gebruikersaanmeldingen (gebeurtenis-id's 4624, 4625), maar bevat geen uitloginformatie (4634) die, hoewel belangrijk voor controle, niet zinvol is voor inbreukdetectie en relatief veel volume heeft. Het grootste deel van het gegevensvolume van deze set bestaat uit aanmeldingsgebeurtenissen en gebeurtenisgebeurtenissen voor procescreatie (gebeurtenis-id 4688).

- **Geen** - Geen beveiligings- of AppLocker-gebeurtenissen. (Deze instelling wordt gebruikt om de connector uit te schakelen.)

    In de volgende lijst vindt u een volledig overzicht van de beveiligings- en applocker-gebeurtenis---gegevens voor elke set:

    | Gebeurtenisset | Verzamelde gebeurtenis--geïdentificeerde gegevens |
    | --- | --- |
    | **Minimaal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Gemeenschappelijk** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> Het verzamelen van beveiligingsgebeurtenissen in de context van één werkruimte kan worden geconfigureerd vanuit Azure Security Center of Azure Sentinel, maar niet beide. Als u Azure Sentinel invoert in een werkruimte die al Azure Security Center uitvoert en is ingesteld op het verzamelen van beveiligingsgebeurtenissen, hebt u twee opties:
> - Laat de verzameling Beveiligingsgebeurtenissen in Azure Security Center zoals het is. U deze gebeurtenissen zowel in Azure Sentinel als in Azure Security Center opvragen en analyseren. U echter niet de verbindingsstatus van de connector controleren of de configuratie ervan wijzigen in Azure Sentinel. Als dit belangrijk voor u is, overweeg dan de tweede optie.
>
> - [Schakel de verzameling Beveiligingsgebeurtenissen uit](../security-center/security-center-enable-data-collection.md) in Azure Security Center en voeg vervolgens alleen de connector Beveiligingsgebeurtenissen toe in Azure Sentinel. Net als bij de eerste optie u gebeurtenissen in zowel Azure Sentinel als Azure Security Center opvragen en analyseren, maar u nu de verbindingsstatus van de connector controleren of de configuratie wijzigen in - en alleen in - Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>De Windows Security Events-connector instellen

Ga als verkenner met het verzamelen van uw Windows-beveiligingsgebeurtenissen in Azure Sentinel:

1. Selecteer **gegevensconnectoren**in het navigatiemenu van Azure Sentinel . Klik in de lijst met connectors op **Beveiligingsgebeurtenissen**en klik vervolgens op de knop **Connector openen** rechtsonder. Volg vervolgens de instructies op het scherm onder het tabblad **Instructies,** zoals beschreven in de rest van deze sectie.

1. Controleer of u over de juiste machtigingen beschikt zoals beschreven **onder Voorwaarden**.

1. Download en installeer de [Log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) (ook bekend als de Microsoft Monitoring Agent of MMA) op de machines waarvoor u beveiligingsgebeurtenissen naar Azure Sentinel wilt streamen.

    Voor Azure Virtual Machines:
    
    1. Klik op **De agent installeren op Azure Windows Virtual Machine**en vervolgens op de onderstaande koppeling.
    1. Voor elke virtuele machine die u wilt verbinden, klikt u op de naam in de lijst die aan de rechterkant wordt weergegeven en klikt u vervolgens op **Verbinden**.

    Voor niet-Azure Windows-machines (fysieke, virtuele on-prem of virtueel in een andere cloud):

    1. Klik op **De agent installeren op niet-Azure Windows Machine**en vervolgens op de onderstaande koppeling.
    1. Klik op de juiste downloadkoppelingen die rechts, onder **Windows Computers**, worden weergegeven.
    1. Installeer de agent met het gedownloade uitvoerbare bestand op de Windows-systemen van uw keuze en configureer deze met behulp van de **Werkruimte-id en sleutels** die onder de hierboven genoemde downloadkoppelingen worden weergegeven.

    > [!NOTE]
    >
    > Als u Windows-systemen zonder de benodigde internetverbinding wilt toestaan om gebeurtenissen nog steeds naar Azure Sentinel te streamen, downloaden en installeren we de **OMS Gateway** op een aparte machine, met behulp van de koppeling rechtsonder, om als proxy op te treden.  U moet nog steeds de loganalyse-agent installeren op elk Windows-systeem waarvan u de gebeurtenissen wilt verzamelen.
    >
    > Zie de [ **gatewaydocumentatie log Analytics** ](../azure-monitor/platform/gateway.md)voor meer informatie over dit scenario.

    Zie de documentatie van de [ **log-analyse-agent voor** ](../azure-monitor/platform/agent-windows.md)extra installatieopties en meer informatie.

1. Selecteer welke gebeurtenisset[(Alles, Algemeen of Minimaal](#event-sets)) u wilt streamen.

1. Klik op **Update**.

1. Als u het relevante schema wilt gebruiken `SecurityEvent` in beveiligingsgebeurtenissen log Analytics voor Windows, typt u het queryvenster.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan ongeveer 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Windows-beveiligingsgebeurtenissen koppelen aan Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp van [ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.

