---
title: DNS-gegevens verbinden in Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van DNS-gegevens in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588311"
---
# <a name="connect-your-domain-name-server"></a>Uw domeinnaamserver verbinden

> [!IMPORTANT]
> De DNS-gegevensconnector in Azure Sentinel bevindt zich momenteel in een openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

U elke DNS (Domain Name Server) die op Windows wordt uitgevoerd, verbinden met Azure Sentinel. Dit wordt gedaan door het installeren van een agent op de DNS-machine. Met BEHULP van DNS-logboeken u beveiligings-, prestatie- en operationsgerelateerde inzichten verkrijgen in de DNS-infrastructuur van uw organisatie door analytische en auditlogboeken en andere gerelateerde gegevens van de DNS-servers te verzamelen, analyseren en correleren.

Wanneer u DNS-logboekverbinding inschakelt, u:
- Clients identificeren die schadelijke domeinnamen proberen op te lossen
- Verouderde bronrecords identificeren
- Veelgevraagde domeinnamen en spraakzame DNS-clients identificeren
- Het laden van aanvragen op DNS-servers weergeven
- Dynamische DNS-registratiefouten weergeven

## <a name="connected-sources"></a>Verbonden bronnen

In de volgende tabel worden de verbonden bronnen beschreven die door deze oplossing worden ondersteund:

| **Verbonden bron** | **Ondersteuning** | **Beschrijving** |
| --- | --- | --- |
| [Windows-agents](../azure-monitor/platform/agent-windows.md) | Ja | De oplossing verzamelt DNS-informatie van Windows-agents. |
| [Linux-agents](../azure-monitor/learn/quick-collect-linux-computer.md) | Nee | De oplossing verzamelt geen DNS-informatie van directe Linux-agents. |
| [Beheergroep System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Ja | De oplossing verzamelt DNS-informatie van agents in een verbonden Operations Manager-beheergroep. Een directe verbinding van de operations manager-agent met Azure Monitor is niet vereist. Gegevens worden doorgestuurd van de beheergroep naar de werkruimte Log Analytics. |
| [Azure-opslagaccount](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nee | Azure-opslag wordt niet gebruikt door de oplossing. |

### <a name="data-collection-details"></a>Gegevensverzamelingsgegevens

De oplossing verzamelt DNS-voorraad- en DNS-gebeurtenisgerelateerde gegevens van de DNS-servers waar een Log Analytics-agent is geïnstalleerd. Voorraadgerelateerde gegevens, zoals het aantal DNS-servers, -zones en resourcerecords, worden verzameld door de DNS PowerShell-cmdlets uit te voeren. De gegevens worden eens in de twee dagen bijgewerkt. De gebeurtenisgerelateerde gegevens worden in de buurt van realtime verzameld uit de [analytische en auditlogboeken](https://technet.microsoft.com/library/dn800669.aspx#enhanc) die worden geleverd door verbeterde DNS-logboekregistratie en -diagnose in Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Uw DNS-toestel verbinden

1. Selecteer in de Azure Sentinel-portal **gegevensconnectors** en kies de tegel **DNS (Preview).**
1. Als uw DNS-machines zich in Azure bevinden:
    1. Klik **op Agent installeren op de virtuele machine van Azure Windows**.
    1. Selecteer **in** de lijst Virtuele machines de DNS-machine die u naar Azure Sentinel wilt streamen. Zorg ervoor dat dit een Windows VM is.
    1. Klik in het venster dat voor die virtuele machine wordt geopend op **Verbinden**.  
    1. Klik **op Inschakelen** in het venster **DNS-connector.** 

2. Als uw DNS-machine geen Azure VM is:
    1. Klik **op Installatieagent op niet-Azure-machines**.
    1. Selecteer in het venster **Direct-agent** de optie **Windows-agent downloaden (64-bits)** of **Windows-agent downloaden (32-bits).**
    1. Installeer de agent op uw DNS-machine. Kopieer de **werkruimte-id**, **primaire toets**en **secundaire sleutel** en gebruik deze wanneer deze wordt gevraagd tijdens de installatie.

3. Als u het relevante schema in Log Analytics wilt gebruiken voor de DNS-logboeken, zoekt u **naar DnsEvents**.

## <a name="validate"></a>Valideren 

Zoek in Log Analytics naar het schema **DnsEvents** en zorg ervoor dat er gebeurtenissen zijn.

## <a name="troubleshooting"></a>Problemen oplossen

Als opzoekquery's niet worden weergegeven in Azure Sentinel, voert u de volgende stappen uit, zodat de query's correct worden weergegeven:
1. Schakel de [DNS Analytics-logboeken in op uw servers.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))
2. Controleer of DNS-gebeurtenissen worden weergegeven in de lijst met logboekanalyses.
3. Azure [DNS Analytics](../azure-monitor/insights/dns-analytics.md)inschakelen .
4. Wijzig in Azure DNS Analytics onder **Configuratie**een van de instellingen, sla deze op, wijzig deze vervolgens terug als dat nodig is en sla het vervolgens opnieuw op.
5. Controleer Azure DNS-analyses om te controleren of de query's nu worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u dns-on-premises apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
