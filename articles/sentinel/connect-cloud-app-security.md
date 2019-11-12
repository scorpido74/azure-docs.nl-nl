---
title: Cloud App Security gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Cloud App Security gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: 32e7e79465b68caca9a636019f65b9f08284fc72
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928222"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Verbinding maken met gegevens van Microsoft Cloud App Security 



U kunt Logboeken van [Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) streamen naar Azure Sentinel met één klik. Met deze verbinding kunt u de waarschuwingen van Cloud App Security naar Azure Sentinel streamen. 

## <a name="prerequisites"></a>Vereisten

- Gebruiker met de machtigingen globale beheerder of beveiligings beheerder
- Als u Cloud Discovery-Logboeken in azure Sentinel wilt streamen, [schakelt u Azure Sentinel in als uw Siem in Microsoft Cloud app Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Opname van Cloud Discovery Logboeken is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
 
## <a name="connect-to-cloud-app-security"></a>Verbinding maken met Cloud App Security

Als u Cloud App Security al hebt, controleert u of deze is [ingeschakeld in uw netwerk](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Als Cloud App Security is geïmplementeerd en opnamet van uw gegevens, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. In azure Sentinel selecteert u **Data connectors**, klikt u op de tegel **Cloud app Security** en selecteert u de **pagina connector openen**.

1. Selecteer de logboeken die u wilt streamen naar Azure Sentinel, u kunt **waarschuwingen** en **Cloud Discovery logboeken** kiezen (preview-versie). 

1. Klik op **Verbinding maken**.

1. Als u het relevante schema in Log Analytics voor de Cloud App Security waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Cloud App Security kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
