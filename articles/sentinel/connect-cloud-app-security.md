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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240110"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Verbinding maken met gegevens van Microsoft Cloud App Security 



U kunt Logboeken van [Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) streamen naar Azure Sentinel met één klik. Met deze verbinding kunt u de waarschuwingen van Cloud App Security naar Azure Sentinel streamen. 

## <a name="prerequisites"></a>Vereisten

- Gebruiker met de machtigingen globale beheerder of beveiligings beheerder

## <a name="connect-to-cloud-app-security"></a>Verbinding maken met Cloud App Security

Als u Cloud App Security al hebt, controleert u of deze is [ingeschakeld in uw netwerk](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Als Cloud App Security is geïmplementeerd en opnamet van uw gegevens, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Cloud app Security** .

1. Selecteer de logboeken die u wilt streamen naar Azure Sentinel, u kunt **waarschuwingen**kiezen. 

1. U kunt selecteren of u wilt dat de waarschuwingen van Microsoft Cloud App Security automatisch incidenten in azure-Sentinel automatisch genereren. Selecteer **inschakelen** onder **incidenten maken** om de standaard analyse regel in te scha kelen waarmee incidenten automatisch worden gemaakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. U kunt deze regel vervolgens bewerken onder **analyse** en vervolgens op **actieve regels**.

1. Klik op**Verbinden**.

1. Als u het relevante schema in Log Analytics voor de Cloud App Security waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Cloud App Security kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
