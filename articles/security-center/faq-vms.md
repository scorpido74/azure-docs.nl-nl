---
title: Veelgestelde vragen over Azure Security Center - vragen over virtuele machines
description: Veelgestelde vragen over virtuele machines in Azure Security Center, een product waarmee u bedreigingen voorkomen, detecteren en erop reageren
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599364"
---
# <a name="faq---questions-about-virtual-machines"></a>Veelgestelde vragen - Vragen over virtuele machines


## <a name="what-types-of-virtual-machines-are-supported"></a>Welke soorten virtuele machines worden ondersteund?

Monitoring en aanbevelingen zijn beschikbaar voor virtuele machines (VM's) die zijn gemaakt met behulp van zowel de [klassieke als resource manager implementatiemodellen.](../azure-classic-rm.md)

Zie [Ondersteunde platforms in Azure Security Center](security-center-os-coverage.md) voor een lijst met ondersteunde platforms.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Waarom herkent Azure Security Center de antimalwareoplossing die op mijn Azure VM wordt uitgevoerd niet?

Azure Security Center heeft inzicht in antimalware die is geïnstalleerd via Azure-extensies. Beveiligingscentrum kan bijvoorbeeld geen antimalware detecteren die vooraf is geïnstalleerd op een afbeelding die u hebt geleverd of als u antimalware op uw virtuele machines hebt geïnstalleerd met behulp van uw eigen processen (zoals configuratiebeheersystemen).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Waarom krijg ik het bericht 'Ontbrekende scangegevens' voor mijn vm?

Dit bericht wordt weergegeven wanneer er geen scangegevens zijn voor een VM. Het kan even duren (minder dan een uur) voordat scangegevens beschikbaar komen nadat de functie Gegevensverzameling is ingeschakeld in Azure Security Center. Nadat de eerste scangegevens zijn doorgegeven, kunt u dit bericht ontvangen omdat er helemaal geen scangegevens zijn of geen recente scangegevens. Er worden geen scangegevens aangeboden aan een VM die is gestopt. Dit bericht kan ook worden weergegeven als scangegevens onlangs niet zijn ingevuld (in overeenstemming met het bewaarbeleid voor de Windows-agent, die een standaardwaarde van 30 dagen heeft).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hoe vaak scant Security Center op kwetsbaarheden in het besturingssysteem, systeemupdates en problemen met endpointbeveiliging?

Hieronder staan de latentietijden voor beveiligingscentrumscans van kwetsbaarheden, updates en problemen:

- Beveiligingsconfiguraties van het besturingssysteem – gegevens worden binnen 48 uur bijgewerkt
- Systeemupdates – gegevens worden binnen 24 uur bijgewerkt
- Problemen met endpointbeveiliging – gegevens worden binnen 8 uur bijgewerkt

Security Center scant doorgaans elk uur naar nieuwe gegevens en vernieuwt de aanbevelingen dienovereenkomstig. 

> [!NOTE]
> Security Center gebruikt de Microsoft Monitoring Agent om gegevens te verzamelen en op te slaan. Zie Migratie van [azure security centerplatform voor](security-center-platform-migration.md)meer informatie.


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Waarom krijg ik het bericht 'VM-agent ontbreekt?'

De VM-agent moet op VM's zijn geïnstalleerd om gegevensverzameling in te schakelen. De VM-agent wordt standaard geïnstalleerd op VM's die zijn geïmplementeerd vanuit Azure Marketplace. Zie de blogpost [VM Agent en Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)voor informatie over het installeren van de VM-agent op andere VM's.