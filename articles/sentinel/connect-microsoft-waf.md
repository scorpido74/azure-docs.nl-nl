---
title: Firewallgegevens voor webtoepassingen verbinden met Azure Sentinel
description: Meer informatie over het verbinden van firewallgegevens van Microsoft-webtoepassingen met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588175"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Gegevens van microsoft-webtoepassingsfirewall verbinden



U logboeken streamen vanaf de Microsoft-firewall (Microsoft Web Application Firewall) van de Azure Application Gateway. Deze WAF beschermt uw toepassingen tegen veelvoorkomende webkwetsbaarheden zoals SQL-injectie en cross-site scripting, en stelt u in staat regels aan te passen om fout-positieven te verminderen. Volg deze instructies om uw Firewall van Microsoft Web-toepassingen te streamen naar Azure Sentinel.


## <a name="prerequisites"></a>Vereisten

- Een bestaande gatewaybron voor toepassingen

## <a name="connect-to-microsoft-web-application-firewall"></a>Verbinding maken met microsoft-webtoepassingsfirewall

Als u al een firewall voor Microsoft-webtoepassingen hebt, controleert u of u een bestaande gatewaybron hebt.
Zodra uw Firewall voor Microsoft-webtoepassingen is geïmplementeerd en gegevens worden opgehaald, kunnen de waarschuwingsgegevens eenvoudig worden gestreamd naar Azure Sentinel.
    
1. Selecteer **gegevensconnectors**in de Azure Sentinel-portal .
1. Selecteer op de pagina Gegevensconnectors de **Tegel WAF.**
1. Ga naar [Application Gateway-bron](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) en kies uw WAF.
    1. Selecteer **Diagnostische instellingen**.
    1. Selecteer **+ Diagnostische instelling toevoegen** onder de tabel.
    1. Typ **op** de pagina Diagnostische instellingen een **naam** en selecteer **Verzenden naar logboekanalyse**.
    1. Selecteer **onder Log Analytics Workspace** de Azure Sentinel-werkruimte.
    1. Selecteer de logboektypen die u wilt analyseren. Wij raden u aan: ApplicationGatewayAccessLog en ApplicationGatewayFirewallLog.
1. Als u het relevante schema wilt gebruiken in Log Analytics voor de firewallwaarschuwingen van Microsoft-webtoepassingen, zoekt u naar **AzureDiagnostics**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de firewall van Microsoft-webtoepassingen verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
