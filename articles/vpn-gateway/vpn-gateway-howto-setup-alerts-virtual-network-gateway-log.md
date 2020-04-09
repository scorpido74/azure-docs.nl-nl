---
title: 'Azure VPN-gateway: waarschuwingen configureren voor diagnostische logboekgebeurtenissen'
description: Stappen om waarschuwingen te configureren voor diagnostische logboekgebeurtenissen van VPN-gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878898"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Waarschuwingen instellen voor diagnostische logboekgebeurtenissen van VPN Gateway

Met dit artikel u waarschuwingen instellen op basis van diagnostische logboekgebeurtenissen van Azure VPN Gateway met Azure Log Analytics. 

De volgende logboeken zijn beschikbaar in Azure:

|***Naam*** | ***Beschrijving*** |
|---        | ---               |
|GatewayDiagnosticLog | Bevat diagnostische logboeken voor gatewayconfiguratiegebeurtenissen, primaire wijzigingen en onderhoudsgebeurtenissen |
|TunnelDiagnosticLog | Bevat wijzigingsgebeurtenissen in de tunnelstatus. Gebeurtenissen tussen tunnelverbinding en verbinding maken hebben een samengevatte reden voor de statuswijziging indien van toepassing |
|RouteDiagnoslog | Registreert wijzigingen in statische routes en BGP-gebeurtenissen die zich voordoen op de gateway |
|IKEDiagnosticLog | Ike-controleberichten en gebeurtenissen op de gateway logboeken |
|P2SDiagnosticLog | Registreert point-to-site controleberichten en gebeurtenissen op de gateway. Verbindingsbroninfo wordt alleen beschikbaar voor IKEv2-verbindingen |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Waarschuwingen instellen in de Azure-portal

In de volgende voorbeeldstappen wordt een waarschuwing gegenereerd voor een gebeurtenis waarbij een VPN-tunnel van site-to-site wordt betrokken:


1. Zoek in de Azure-portal naar **Log Analytics** onder **Alle services** en selecteer **Log Analytics-werkruimten**.

   ![Selecties voor het gaan naar Log Analytics-werkruimten](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Maken")

2. Selecteer **Maken** op de pagina **Log Analytics.**

   ![Pagina Logboekanalyse met knop Maken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecteer")

3. Selecteer **Nieuw maken** en vul de details in.

   ![Gegevens voor het maken van een Log Analytics-werkruimte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecteer")

4. Zoek uw VPN-gateway op het blade met de**instellingen voor monitordiagnostiek.** **Monitor** > 

   ![Selecties voor het vinden van de VPN-gateway in diagnostische instellingen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecteer")

5. Als u diagnostische gegevens wilt inschakelen, dubbelklikt u op de gateway en selecteert u **Diagnostische gegevens inschakelen**.

   ![Selecties voor het inschakelen van diagnostiek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecteer")

6. Vul de gegevens in en zorg ervoor dat **Send to Log Analytics** en **TunnelDiagnosticLog** zijn geselecteerd. Kies de Logboekanalysewerkruimte die u in stap 3 hebt gemaakt.

   ![Geselecteerde selectievakjes](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecteer")

   > [!NOTE]
   > Het kan een paar uur duren voordat de gegevens in eerste instantie worden weergegeven.

7. Ga naar het overzicht voor de bron van de virtuele netwerkgateway en selecteer **Waarschuwingen** op het tabblad **Controle.** Maak vervolgens een nieuwe waarschuwingsregel of bewerk een bestaande waarschuwingsregel.

   ![Selecties voor het maken van een nieuwe waarschuwingsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecteer")

   ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecteer")
8. Selecteer de werkruimte Log Analytics en de bron.

   ![Selecties voor werkruimte en resource](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecteer")

9. Selecteer **Aangepaste logboekzoekopdracht** als de signaallogica onder **Voorwaarde toevoegen**.

   ![Selecties voor een aangepaste logboekzoekopdracht](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecteer")

10. Voer in het tekstvak **Zoekquery** te volgende query in. Vervang de waarden in <> en TimeGenerated naar gelang van het geval.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Stel de drempelwaarde in op 0 en selecteer **Gereed**.

    ![Een query invoeren en een drempelwaarde selecteren](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecteer")

11. Selecteer **op** de pagina Regel maken de optie **Nieuw maken** onder de sectie **ACTIEGROEPEN.** Vul de details in en selecteer **OK**.

    ![Details voor een nieuwe actiegroep](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecteer")

12. Vul op de pagina **Regel maken** de details voor **Acties aanpassen** in en controleer of de juiste naam wordt weergegeven in de sectie **ACTIEGROEPNAAM.** Selecteer **Waarschuwingsregel maken** om de regel te maken.

    ![Selecties voor het maken van een regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecteer")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Waarschuwingen instellen met PowerShell

In de volgende voorbeeldstappen wordt een waarschuwing gemaakt voor een verbinding waarbij een vpn-tunnel van site-to-site wordt betrokken.

1. Maak een log analytics-werkruimte:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Schakel diagnostische gegevens in voor de VPN-gateway:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Maak een actiegroep.

   Met deze code wordt een actiegroep gegenereerd die een e-mailmelding verzendt wanneer een waarschuwing wordt geactiveerd:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Maak een waarschuwingsregel op basis van een aangepaste logboekzoekopdracht:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Volgende stappen

Zie [Waarschuwingen instellen op VPN Gateway-statistieken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)voor het configureren van waarschuwingen voor tunnelstatistieken.
