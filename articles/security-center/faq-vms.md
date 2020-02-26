---
title: 'Azure Security Center Veelgestelde vragen: vragen over virtuele machines'
description: Veelgestelde vragen over virtuele machines in Azure Security Center, een product dat u helpt bedreigingen te voor komen, te detecteren en erop te reageren
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599364"
---
# <a name="faq---questions-about-virtual-machines"></a>Veelgestelde vragen: vragen over virtuele machines


## <a name="what-types-of-virtual-machines-are-supported"></a>Welke typen virtuele machines worden ondersteund?

Bewaking en aanbevelingen zijn beschikbaar voor virtuele machines (Vm's) die zijn gemaakt met behulp van de [klassieke en Resource Manager-implementatie modellen](../azure-classic-rm.md).

Zie de [ondersteunde platforms in azure Security Center](security-center-os-coverage.md) voor een lijst met ondersteunde platforms.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Waarom niet Azure Security Center wordt herkend door de antimalwareoplossing die draait op mijn virtuele machine van Azure?

Azure Security Center heeft inzicht in antimalware geïnstalleerd via de Azure-extensies. Security Center is bijvoorbeeld niet kan detecteren, anti-malware die vooraf worden geïnstalleerd op een installatiekopie die u hebt opgegeven of als u anti-malware op uw virtuele machines met uw eigen processen (zoals systemen voor Configuratiebeheer) geïnstalleerd is.


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Waarom krijg ik het bericht 'Ontbreekt scant gegevens' voor mijn VM?

Dit bericht wordt weergegeven wanneer er geen scangegevens zijn voor een VM. Het kan even duren (minder dan een uur) voordat scangegevens beschikbaar komen nadat de functie Gegevensverzameling is ingeschakeld in Azure Security Center. Nadat de eerste scangegevens zijn doorgegeven, kunt u dit bericht ontvangen omdat er helemaal geen scangegevens zijn of geen recente scangegevens. Er worden geen scangegevens aangeboden aan een VM die is gestopt. Dit bericht kan ook verschijnen als Scangegevens niet is ingevuld onlangs (in overeenstemming met het bewaarbeleid voor de Windows-agent, die een standaardwaarde van 30 dagen heeft).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hoe vaak wordt door Security Center gescand op beveiligingsproblemen van besturingssystemen, systeemupdates en problemen met endpoint protection?

Hieronder vindt u de latentie tijden voor Security Center scans van beveiligings problemen, updates en problemen:

- Besturingssysteem-beveiligingsconfiguraties-gegevens wordt bijgewerkt binnen 48 uur
- Systeemupdates-gegevens wordt bijgewerkt binnen 24 uur
- Problemen met Endpoint Protection: gegevens worden bijgewerkt binnen de 8 uur

Security Center doorgaans scant op nieuwe gegevens elk uur, en Hiermee vernieuwt u de aanbevelingen dienovereenkomstig. 

> [!NOTE]
> Security Center gebruikt de micro soft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie [Azure Security Center platform Migration](security-center-platform-migration.md)(Engelstalig) voor meer informatie.


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Waarom krijg ik het bericht 'VM-Agent is ontbrekende?'

De VM-Agent moet worden geïnstalleerd op VM's om gegevens te verzamelen. De VM-agent wordt standaard geïnstalleerd op VM's die zijn geïmplementeerd vanuit Azure Marketplace. Zie voor meer informatie over het installeren van de VM-agent op andere Vm's de blog post [VM-agent en-extensies](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).