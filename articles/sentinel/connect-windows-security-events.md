---
title: Gegevens van Windows-beveiligings gebeurtenissen verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Windows-beveiligings gebeurtenis gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: c74cc5cc65f16eb38ead7c09b5e662cd2463af35
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555291"
---
# <a name="connect-windows-security-events"></a>Verbinding maken met Windows-beveiligingsgebeurtenissen 



U kunt alle beveiligings gebeurtenissen streamen van de Windows-servers die zijn verbonden met uw Azure Sentinel-werk ruimte. Met deze verbinding kunt u Dash boards weer geven, aangepaste waarschuwingen maken en het onderzoek verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert de mogelijkheden van beveiligings bewerkingen.  U kunt selecteren welke gebeurtenissen u wilt streamen:

- **Alle gebeurtenissen** : alle Windows-beveiligings-en AppLocker-gebeurtenissen.
- **Algemeen** : een standaardset gebeurtenissen voor controle doeleinden. Deze set bevat een volledige controle spoor voor gebruikers. Deze set bevat bijvoorbeeld zowel gebruikers aanmelding als gebruikers registratie gebeurtenissen (gebeurtenis-ID 4634). Er zijn controle acties zoals wijzigingen in de beveiligings groep, Kerberos-bewerkingen van de Key-domein controller en andere gebeurtenissen die worden aanbevolen door organisaties van de branche.

Gebeurtenissen met een zeer laag volume zijn opgenomen in de gemeen schappelijke set als de belangrijkste motivatie om deze te kiezen voor alle gebeurtenissen om het volume te verminderen en om specifieke gebeurtenissen niet uit te filteren.
- **Mini maal** : een kleine set gebeurtenissen die mogelijke dreigingen kunnen aanduiden. Als u deze optie inschakelt, kunt u geen volledige controle spoor hebben.  Deze set geldt alleen voor gebeurtenissen die kunnen wijzen op een geslaagde schending en belang rijke gebeurtenissen met een zeer laag volume. Deze set bevat bijvoorbeeld de gebruiker geslaagde en mislukte aanmeldingen (gebeurtenis-Id's 4624, 4625), maar bevat geen afmeldings informatie die belang rijk is voor de controle, maar niet zinvol voor detectie en relatief hoog volume. Het meren deel van het gegevens volume van deze set bestaat uit de aanmeldings gebeurtenissen en de gebeurtenis voor het maken van een proces (gebeurtenis-ID 4688).
- **Geen** : er zijn geen beveiligings-of AppLocker-gebeurtenissen.

> [!NOTE]
> 
> - Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.
> - Als Azure Security Center en Azure Sentinel worden uitgevoerd op dezelfde werk ruimte, kan de connector voor beveiligings gebeurtenissen alleen worden verbonden vanuit Azure Security Center of met Azure Sentinel. Als u deze gebeurtenissen vanuit Azure Sentinel wilt beheren, raden we u aan om deze te verbreken van Azure Security Center en deze alleen te verbinden met Azure Sentinel.


De volgende lijst bevat een volledige uitsplitsing van de gebeurtenis-Id's van de beveiligings-en app-kluis voor elke set:

| Gegevenslaag | Verzamelde gebeurtenis indicatoren |
| --- | --- |
| Minimale | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Algemeen | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

## <a name="set-up-the-windows-security-events-connector"></a>De Windows-beveiligings gebeurtenissen connector instellen

Uw Windows-beveiligings gebeurtenissen volledig integreren met Azure Sentinel:

1. Selecteer in de Azure-Sentinel-Portal de optie **gegevens connectors** en klik vervolgens op de tegel **beveiligings gebeurtenissen** . 
1. Selecteer welke gegevens typen u wilt streamen.
1. Klik op **Update**.
6. Als u het relevante schema in Log Analytics voor de Windows-beveiligings gebeurtenissen wilt gebruiken, zoekt u naar **SecurityEvent**.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan ongeveer 20 minuten duren totdat uw logboeken in Log Analytics worden weer gegeven. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Windows-beveiligings gebeurtenissen verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

