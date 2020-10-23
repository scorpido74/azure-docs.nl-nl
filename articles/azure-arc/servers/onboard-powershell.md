---
title: Hybride computers verbinden met Azure met behulp van Power shell
description: In dit artikel leert u hoe u de Agent installeert en een machine verbindt met Azure met behulp van Azure Arc-servers met behulp van Power shell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375271"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Hybride computers verbinden met Azure met behulp van Power shell

U kunt servers voor Azure-Arc inschakelen voor een of meer Windows-of Linux-computers in uw omgeving door een reeks stappen hand matig uit te voeren. U kunt ook gebruikmaken van de Power shell-cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Met deze cmdlet worden de volgende acties uitgevoerd:

- Hiermee configureert u de hostcomputer voor het downloaden van de Windows-agent vanuit het micro soft Download centrum en het Linux-agent pakket van packages.microsoft.com.
- Hiermee wordt de verbonden machine agent geïnstalleerd.
- Registreert de machine met Azure Arc

Voor deze methode moet u beheerders rechten op de computer hebben om de agent te installeren en configureren. Op Linux, met behulp van het hoofd account en in Windows, bent u lid van de lokale groep Administrators.

Voordat u aan de slag gaat, moet u de [vereisten](agent-overview.md#prerequisites) controleren en controleren of uw abonnement en resources voldoen aan de vereisten. Zie [ondersteunde Azure-regio's](overview.md#supported-regions)voor meer informatie over ondersteunde regio's en andere gerelateerde overwegingen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een computer met Azure PowerShell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies.

Voordat u Azure PowerShell gebruikt om VM-extensies te beheren op uw hybride server die wordt beheerd door servers met Arc-functionaliteit, moet u de `Az.ConnectedMachine` module installeren. Voer de volgende opdracht uit op de server met Arc-functionaliteit:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Wanneer de installatie is voltooid, wordt het volgende bericht weer gegeven:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>De agent in Windows installeren en valideren

1. Open een Power shell-console als beheerder.

2. Meld u aan bij Azure door de opdracht uit te voeren `Connect-AzAccount` .

3. Als u de verbonden machine-agent wilt installeren, gebruikt u `Connect-AzConnectedMachine` met de `-Name` `-ResourceGroupName` `-Location` para meters, en. Gebruik de `-SubscriptionId` para meter om het standaard abonnement te negeren als gevolg van de Azure-context die is gemaakt na het aanmelden.

    Voer de volgende opdracht uit om de verbonden machine agent te installeren op de doel computer die rechtstreeks kan communiceren met Azure::

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Als de doelmachine communiceert via een proxyserver, voert u de volgende opdracht uit:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Op Windows op *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*en op Linux op */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc controleren

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc-servers, gaat u naar Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw computers in [Azure Portal](https://portal.azure.com).

![Een geslaagde server verbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/insights/vminsights-enable-policy.md)en nog veel meer.

* Meer informatie over de [log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u bewakings gegevens van het besturings systeem en werk belasting wilt verzamelen, deze wilt beheren met Automation-runbooks of-functies zoals Updatebeheer, of om andere Azure-Services zoals [Azure Security Center](../../security-center/security-center-introduction.md)te gebruiken.