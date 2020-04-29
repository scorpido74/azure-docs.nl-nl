---
title: 'Zelf studie: communicatie problemen oplossen tussen netwerken met behulp van de Azure Portal'
titleSuffix: Azure Network Watcher
description: In deze zelf studie leert u hoe u een communicatie probleem kunt vaststellen tussen een virtueel Azure-netwerk dat is verbonden met een on-premises of een ander virtueel netwerk, via een virtuele Azure-netwerk gateway, met behulp van de VPN-functie voor diagnostische gegevens van Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 974e45b761fb45e4bc1c451fa6755e16cab49e11
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76834678"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Zelfstudie: Communicatieproblemen tussen netwerken onderzoeken met Azure Portal

De gateway van een virtueel netwerk koppelt een virtueel Azure-netwerk aan een on-premises netwerk of een ander virtueel netwerk. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een probleem met de gateway van een virtueel netwerk onderzoeken met de VPN-diagnosemogelijkheden van Network Watcher
> * Een probleem met een gatewayverbinding onderzoeken
> * Een probleem met een gateway oplossen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor het gebruik van diagnostische gegevens van VPN moet u een bestaande, actieve VPN-gateway hebben. Als u geen VPN-gateway hebt om te onderzoeken, kunt u er een implementeren met een [PowerShell-script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). U kunt het PowerShell-script uitvoeren vanuit:
- **Een lokale Power shell-installatie**: voor het script `Az` is de module Azure PowerShell vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u een upgrade moet uitvoeren, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- **De Azure Cloud Shell**: in de [Azure Cloud Shell](https://shell.azure.com/powershell) is de meest recente versie van PowerShell geïnstalleerd en geconfigureerd en wordt u aangemeld bij Azure.

Het script heeft ongeveer een uur nodig om een VPN-gateway te maken. In de overige stappen wordt ervan uitgegaan dat de gateway die u wilt onderzoeken de gateway is die door dit script is geïmplementeerd. Als u in plaats daarvan uw eigen gateway onderzoekt, zullen de resultaten anders zijn.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Network Watcher inschakelen

Als u al een netwerk-watcher hebt ingeschakeld in de regio VS - oost, ga dan verder met [Een gateway onderzoeken](#diagnose-a-gateway).

1. Selecteer in de portal de optie **Alle services**. Typ *Network Watcher* in het vak **Filteren**. Selecteer **Network Watcher** in de resultaten.
2. Selecteer **Regio's** om dit item uit te vouwen en selecteer vervolgens **...** rechts van **VS - oost**, zoals wordt weergegeven in de volgende afbeelding:

    ![Network Watcher inschakelen](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Selecteer **Network Watcher inschakelen**.

## <a name="diagnose-a-gateway"></a>Een gateway onderzoeken

1. Selecteer aan de linkerkant van de portal de optie **Alle services**.
2. Begin *network watcher* te typen in het vak **Filter**. Selecteer **Network Watcher** in de zoekresultaten.
3. Selecteer onder **DIAGNOSTISCHE HULPPROGRAMMA'S VOOR NETWERK**, de optie **Diagnostische gegevens van VPN**.
4. Selecteer **Opslagaccount** en selecteer vervolgens het opslagaccount waarnaar u de diagnostische gegevens wilt schrijven.
5. Selecteer in de lijst met **opslagaccounts** het opslagaccount dat u wilt gebruiken. Als u geen bestaand opslagaccount hebt, selecteert u **+ Opslagaccount**, Enter, of selecteert u de vereiste gegevens en selecteert u **Maken** om er een te maken. Als u een VPN-gateway hebt gemaakt met het script in [Vereisten](#prerequisites), wilt u het opslagaccount misschien maken in dezelfde resourcegroep, *TestRG1*, als de gateway.
6. Selecteer in de lijst **Containers** de container die u wilt gebruiken en selecteer **Selecteren**. Als u geen containers hebt, selecteert u **+ Container**, voert u een naam voor de container in en selecteert u **OK**.
7. Selecteer een gateway en selecteer **Probleemoplossing starten**. Zoals u in de volgende afbeelding ziet, wordt de test uitgevoerd voor een gateway met de naam **Vnet1GW**:

    ![Diagnostische gegevens van VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Terwijl de test wordt uitgevoerd, wordt **In uitvoering** weergegeven in de kolom **STATUS VAN PROBLEEMOPLOSSING** waar in de vorige afbeelding **Niet gestart** werd weergegeven. De test kan enkele minuten duren.
9. Bekijk de status van een voltooide test. In de volgende afbeelding ziet u de statusresultaten van een voltooide diagnostische test:

    ![Status](./media/diagnose-communication-problem-between-networks/status.png)

    U ziet dat de **STATUS VAN PROBLEEMOPLOSSING****Niet in orde** is. U ziet ook een **samenvatting** en de **details** van het probleem op het tabblad **Status**.
10. Wanneer u het tabblad **Actie** ziet, vindt u in de diagnostische gegevens van VPN aanvullende informatie. In het voorbeeld, dat in de volgende afbeelding wordt weergegeven, ziet u in de diagnostische gegevens van VPN dat u wordt geadviseerd om de status van elke verbinding te controleren:

    ![Bewerking](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Een gatewayverbinding onderzoeken

Een gateway is via een gatewayverbinding verbonden met andere netwerken. Zowel de gateway als de gatewayverbindingen moeten in orde zijn voor een geslaagde communicatie tussen een virtueel netwerk en een verbonden netwerk.

1. Voer stap 7 van [Een gateway onderzoeken](#diagnose-a-gateway) nogmaals uit, maar selecteer deze keer een verbinding. In het volgende voorbeeld wordt de verbinding **VNet1toSite1** getest:

    ![Verbinding](./media/diagnose-communication-problem-between-networks/connection.png)

    De test wordt gedurende enkele minuten uitgevoerd.
2. Nadat de test van de verbinding is voltooid, worden op de tabbladen **Status** en **Actie** resultaten weergegeven die vergelijkbaar zijn met die in de volgende afbeeldingen:

    ![Verbindingsstatus](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Verbindingsactie](./media/diagnose-communication-problem-between-networks/connection-action.png)

    Op het tabblad **Status** van de diagnostische gegevens van VPN vindt u informatie over het probleem. Op het tabblad **Actie** vindt u ook een aantal suggesties van de mogelijke oorzaak van het probleem.

    Als de gateway die u hebt getest, een van de gateways is die is geïmplementeerd met het [script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) in [Vereisten](#prerequisites), geven het probleem op het tabblad **Status** en de eerste twee items op het tabblad **Acties** precies aan wat het probleem is. Het script configureert u een tijdelijke aanduiding voor IP-adres, 23.99.221.164 voor het on-premises VPN-gatewayapparaat.

    Om het probleem op te lossen, moet u controleren of de on-premises VPN-gateway [correct is geconfigureerd](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Wijzig daarom het IP-adres dat door het script voor de lokale netwerkgateway is geconfigureerd in het werkelijke openbare adres van uw lokale VPN-gateway.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alleen voor deze zelfstudie een VPN-gateway hebt gemaakt met het script in [Vereisten](#prerequisites) en deze gateway verder niet meer nodig hebt, verwijdert u de resourcegroep en alle resources daarin:

1. Typ *TestRG1* in het vak **Zoeken** bovenaan de portal. Selecteer **TestRG1** in de zoekresultaten.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ *TestRG1* in **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een probleem met een virtuele netwerkgateway onderzoekt. Misschien wilt u netwerkcommunicatie naar en van een virtuele machine in een logboek vastleggen zodat u het logboek kunt controleren op afwijkingen. Ga naar de volgende zelfstudie als u wilt weten hoe u dat doet.

> [!div class="nextstepaction"]
> [Netwerkverkeer van en naar een VM in een logboek vastleggen](network-watcher-nsg-flow-logging-portal.md)
