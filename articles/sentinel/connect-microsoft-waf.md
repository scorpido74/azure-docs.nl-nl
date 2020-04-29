---
title: Web Application firewall-gegevens verbinden met Azure Sentinel
description: Meer informatie over het verbinden van micro soft Web Application Firewall-gegevens met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588175"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Verbinding maken met gegevens van micro soft Web Application Firewall



U kunt Logboeken streamen vanuit de micro soft Web Application Firewall van de Azure-toepassing-gateway (WAF). Met deze WAF worden uw toepassingen beschermd tegen veelvoorkomende beveiligings problemen, zoals SQL-injectie en cross-site scripting, en kunt u regels aanpassen om fout-positieven te verminderen. Volg deze instructies voor het streamen van uw micro soft Web Application firewall-logboeken naar Azure Sentinel.


## <a name="prerequisites"></a>Vereisten

- Een bestaande toepassings gateway resource

## <a name="connect-to-microsoft-web-application-firewall"></a>Verbinding maken met micro soft Web Application Firewall

Als u al micro soft Web Application Firewall hebt, zorg er dan voor dat u een bestaande gateway resource hebt.
Zodra uw micro soft-Web Application Firewall is geïmplementeerd en gegevens heeft opgehaald, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.
    
1. Selecteer in de Azure-Sentinel-Portal de optie **Data connectors**.
1. Selecteer op de pagina Data connectors de tegel **WAF** .
1. Ga naar [Application Gateway resource](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) en kies uw WAF.
    1. Selecteer **Diagnostische instellingen**.
    1. Selecteer **+ Diagnostische instelling toevoegen** onder de tabel.
    1. Typ op de pagina **Diagnostische instellingen** een **naam** en selecteer **verzenden naar log Analytics**.
    1. Selecteer onder **log Analytics werk ruimte** de Azure Sentinel-werk ruimte.
    1. Selecteer de logboek typen die u wilt analyseren. We raden het volgende aan: ApplicationGatewayAccessLog en ApplicationGatewayFirewallLog.
1. Als u het relevante schema in Log Analytics voor de micro soft Web Application Firewall-waarschuwingen wilt gebruiken, zoekt u naar **AzureDiagnostics**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u micro soft Web Application Firewall verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
