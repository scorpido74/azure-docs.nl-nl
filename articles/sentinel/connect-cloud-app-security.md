---
title: Cloud App-beveiligingsgegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Cloud App Security-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588362"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Gegevens van Microsoft Cloud App Security verbinden 



U logboeken van [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) met één klik naar Azure Sentinel streamen. Met deze verbinding u de waarschuwingen van Cloud App Security streamen naar Azure Sentinel. 

## <a name="prerequisites"></a>Vereisten

- Gebruiker met machtigingen voor globale beheerders of beveiligingsbeheerders
- Als u Cloud Discovery-logboeken wilt streamen naar Azure Sentinel, [schakelt u Azure Sentinel in als uw SIEM in Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> Inname van Cloud Discovery-logboeken is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
 
## <a name="connect-to-cloud-app-security"></a>Verbinding maken met cloud-appbeveiliging

Als u al cloud-appbeveiliging hebt, controleert u of deze is [ingeschakeld in uw netwerk.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
Als Cloud App Security wordt geïmplementeerd en uw gegevens wordt opgenomen, kunnen de waarschuwingsgegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in Azure Sentinel **gegevensconnectors,** klik op de tegel **Beveiliging van cloudapps** en selecteer **Connectorpagina openen**.

1. Selecteer welke logboeken u wilt streamen naar Azure Sentinel, u **waarschuwingen** en **logboeken voor clouddetectie** kiezen (voorbeeld). 

1. Klik op **Verbinden**.

1. Als u het relevante schema wilt gebruiken in Log Analytics voor de beveiligingswaarschuwingen voor cloudapps, zoekt u **naar SecurityAlert**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Cloud App Security verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
