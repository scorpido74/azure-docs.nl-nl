---
title: Zimperium Mobile Threat Defense verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Zimperium Mobile Threat Defense met Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587937"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Verbind uw Zimperium Mobile Threat Defense met Azure Sentinel


> [!IMPORTANT]
> De Zimperium Mobile Threat Defense-gegevensconnector in Azure Sentinel is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.



Zimperium Mobile Threat Defense-connector biedt u de mogelijkheid om het Zimperium-bedreigingslogboek te verbinden met Azure Sentinel om dashboards te bekijken, aangepaste waarschuwingen te maken en onderzoek te verbeteren. Dit geeft u meer inzicht in het mobiele bedreigingslandschap van uw organisatie en verbetert uw beveiligingsmogelijkheden.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Zimperium Mobile Threat Defense configureren en verbinden

Zimperium Mobile Threat Defense kan logboeken rechtstreeks integreren en exporteren naar **Azure Sentinel.**

1. Klik in de Azure Sentinel-portal op Gegevensconnectoren en selecteer **Zimperium Mobile Threat Defense**.
2. Selecteer **Connectorpagina openen**.
3. Volg de instructies op de **Zimperium Mobile Threat Defense** connector pagina, samengevat als volgt.
 1. Klik in zConsole op **Beheren** op de navigatiebalk.
 2. Klik op het tabblad **Integrations**.
 3. Klik op de knop **Bedreigingsrapportage** en vervolgens op de knop **Integratietoevoegen.**
 4. Maak de integratie door **Microsoft Azure Sentinel** te selecteren op basis van de beschikbare integraties en voer werkruimte-id en primaire sleutel in om verbinding te maken met Azure Sentinel.
 5. Optie om een filterniveau te selecteren voor de bedreigingsgegevens die u naar Azure Sentinel wilt pushen, is ook beschikbaar. 

4. Voor meer informatie verwijzen wij u naar de [Klantenservice van Zimperium.](https://support.zimperium.com)


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat een succesvolle verbinding is gemaakt, worden de gegevens weergegeven in Log Analytics onder CustomLogs ZimperiumThreatLog_CL en ZimperiumMitigationLog_CL.

Als u het relevante schema wilt gebruiken in Log Analytics voor de Zimperium Mobile Threat Defense, zoekt u naar ZimperiumThreatLog_CL en ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Zimperium Mobile Threat Defense verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)

- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)

- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

Zie het volgende voor meer informatie over Zimperium:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Security Blog](https://blog.zimperium.com)

- [Zimperium Customer Support Portal](https://support.zimperium.com)

