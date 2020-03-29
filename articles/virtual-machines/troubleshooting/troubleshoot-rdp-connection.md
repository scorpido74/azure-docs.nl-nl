---
title: Kan geen verbinding maken met RDP met een Windows VM in Azure | Microsoft Documenten
description: Problemen oplossen wanneer u geen verbinding maken met uw virtuele Windows-machine in Azure met Extern bureaublad
keywords: Extern bureaublad-fout, fout in externe bureaubladverbinding, kan geen verbinding maken met VM, probleemoplossing voor extern bureaublad
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916605"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Problemen met Extern bureaublad-verbindingen met een virtuele Azure-machine oplossen
De RDP-verbinding (Remote Desktop Protocol) met uw Windows Azure VM (Virtual Machine) kan om verschillende redenen worden onderbroken, waardoor u geen toegang hebt tot uw VM. Het probleem kan in Extern bureaublad in de VM zitten, in de netwerkverbinding of in de Extern bureaublad-client in de hostcomputer. In dit artikel wordt u door sommige van de meest voorkomende methoden geleid om RDP-verbindingsfouten op te lossen. 

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snelle stappen voor het oplossen van problemen
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met de VM:

1. Configuratie van Extern bureaublad opnieuw instellen.
2. Controleer de regels van de netwerkbeveiligingsgroep / de eindpunten van cloudservices.
3. VM-consolelogboeken bekijken.
4. De NIC voor de VM opnieuw instellen.
5. Controleer de VM-bronstatus.
6. Uw VM-wachtwoord opnieuw instellen.
7. Start uw VM opnieuw.
8. Uw VM opnieuw implementeren.

Lees verder als u meer gedetailleerde stappen en uitleg nodig hebt. Controleer of lokale netwerkapparatuur zoals routers en firewalls uitgaande TCP-poort 3389 niet blokkeert, zoals vermeld in [gedetailleerde rdp-probleemoplossingsscenario's.](detailed-troubleshoot-rdp.md)

> [!TIP]
> Als de **connect-knop** voor uw VM grijs is weergegeven in de portal en u niet bent verbonden met Azure via een [Express Route](../../expressroute/expressroute-introduction.md) of [Site-to-Site VPN-verbinding,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) moet u uw VM een openbaar IP-adres maken en toewijzen voordat u RDP gebruiken. Er is meer informatie over [openbare IP-adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) beschikbaar.


## <a name="ways-to-troubleshoot-rdp-issues"></a>Manieren om RDP-problemen op te lossen
U vm's die zijn gemaakt met behulp van het implementatiemodel ResourceBeheer oplossen met een van de volgende methoden:

* Azure-portal - geweldig als u de RDP-configuratie of gebruikersreferenties snel opnieuw wilt instellen en u de Azure-hulpprogramma's niet hebt geïnstalleerd.
* Azure PowerShell - als u zich comfortabel voelt met een PowerShell-prompt, moet u de RDP-configuratie of gebruikersreferenties snel opnieuw instellen met de Azure PowerShell-cmdlets.

U ook stappen vinden voor het oplossen van vm's die zijn gemaakt met behulp van het [klassieke implementatiemodel.](#troubleshoot-vms-created-using-the-classic-deployment-model)

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met de Azure-portal
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw virtuele machine. Als u nog steeds geen verbinding maken, probeert u de volgende stap.

1. **Uw RDP-verbinding opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen zijn uitgeschakeld of windows firewallregels bijvoorbeeld RDP blokkeren.
   
    Selecteer uw VM in de Azure-portal. Schuif naar beneden in het instellingenvenster naar de sectie **Ondersteuning + Probleemoplossing** onder aan de lijst. Klik op de knop **Wachtwoord opnieuw instellen.** Stel de **modus** **in op Alleen de configuratie opnieuw instellen** en klik op de knop **Bijwerken:**
   
    ![De RDP-configuratie in de Azure-portal opnieuw instellen](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Regels voor netwerkbeveiligingsgroepen verifiëren**. Gebruik [IP-stroom controleren](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) om te bevestigen of verkeer naar of van een virtuele machine wordt geblokkeerd door een regel in een netwerkbeveiligingsgroep. U ook effectieve regels voor beveiligingsgroepen controleren om ervoor te zorgen dat de NSG-regel 'Toestaan' bestaat en prioriteit krijgt voor rdp-poort (standaard389). Zie [Effectieve beveiligingsregels gebruiken om de vm-verkeersstroom op te lossen voor](../../virtual-network/diagnose-network-traffic-filter-problem.md)meer informatie .

3. **Vm-opstartdiagnose bekijken**. Met deze stap voor het oplossen van problemen worden de VM-consolelogboeken geherziet om te bepalen of de VM een probleem meldt. Niet alle VM's hebben opstartdiagnose ingeschakeld, dus deze probleemoplossingsstap kan optioneel zijn.
   
    Specifieke stappen voor het oplossen van problemen vallen buiten het bereik van dit artikel, maar kunnen duiden op een breder probleem dat van invloed is op de RDP-connectiviteit. Zie [Diagnostische gegevens voor VM's opstarten voor virtuele](boot-diagnostics.md)gegevens voor meer informatie over het bekijken van de consolelogboeken en vm-schermafbeelding.

4. **De NIC voor de VM opnieuw instellen**. Zie voor meer informatie [hoe u NIC voor Azure Windows VM opnieuw instellen.](../windows/reset-network-interface.md)
5. **Controleer de VM-resourcestatus**. Met deze stap voor het oplossen van problemen worden er geen bekende problemen met het Azure-platform vastgesteld die van invloed kunnen zijn op de connectiviteit met de VM.
   
    Selecteer uw VM in de Azure-portal. Schuif naar beneden in het instellingenvenster naar de sectie **Ondersteuning + Probleemoplossing** onder aan de lijst. Klik op de knop **Resourcestatus.** Een gezonde VM meldt als **beschikbaar:**
   
    ![Vm-bronstatus controleren in de Azure-portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Gebruikersreferenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wachtwoord opnieuw ingesteld op een lokaal beheerdersaccount wanneer u niet zeker bent of de referenties bent vergeten.  Zodra u bent aangemeld bij de VM, moet u het wachtwoord voor die gebruiker opnieuw instellen.
   
    Selecteer uw VM in de Azure-portal. Schuif naar beneden in het instellingenvenster naar de sectie **Ondersteuning + Probleemoplossing** onder aan de lijst. Klik op de knop **Wachtwoord opnieuw instellen.** Zorg ervoor dat de **modus** is ingesteld op **Wachtwoord opnieuw instellen** en voer vervolgens uw gebruikersnaam en een nieuw wachtwoord in. Klik tot slot op de knop **Bijwerken:**
   
    ![De gebruikersreferenties in de Azure-portal opnieuw instellen](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Start uw VM opnieuw.** Deze probleemoplossingsstap kan eventuele onderliggende problemen die de VM zelf heeft, verhelpen.
   
    Selecteer uw VM in de Azure-portal en klik op het tabblad **Overzicht.** Klik op de knop **Opnieuw starten:**
   
    ![De VM opnieuw starten in de Azure-portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Uw VM opnieuw implementeren**. Met deze stap voor het oplossen van problemen wordt uw VM opnieuw geïmplementeerd naar een andere host binnen Azure om onderliggende platform- of netwerkproblemen te verhelpen.
   
    Selecteer uw VM in de Azure-portal. Schuif naar beneden in het instellingenvenster naar de sectie **Ondersteuning + Probleemoplossing** onder aan de lijst. Klik **op** de knop Opnieuw implementeren en klik vervolgens op **Opnieuw implementeren:**
   
    ![De VM opnieuw implementeren in de Azure-portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Nadat deze bewerking is voltooid, gaan tijdelijke schijfgegevens verloren en worden dynamische IP-adressen die aan de VM zijn gekoppeld, bijgewerkt.

9. **Routering verifiëren**. Gebruik de [next-hopmogelijkheid](../../network-watcher/network-watcher-check-next-hop-portal.md) van Network Watcher om te bevestigen dat een route niet voorkomt dat verkeer wordt doorgestuurd naar of van een virtuele machine. U ook effectieve routes bekijken om alle effectieve routes voor een netwerkinterface te bekijken. Zie [Effectieve routes gebruiken om de vm-verkeersstroom op te lossen voor](../../virtual-network/diagnose-network-routing-problem.md)meer informatie.

10. Zorg ervoor dat elke on-premises firewall of firewall op uw computer uitgaand TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds rdp-problemen ondervindt, u [een ondersteuningsverzoek openen](https://azure.microsoft.com/support/options/) of meer gedetailleerde concepten en stappen voor [het oplossen van problemen met RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)lezen.

## <a name="troubleshoot-using-azure-powershell"></a>Problemen oplossen met behulp van Azure PowerShell
Als u dit nog niet hebt gedaan, [installeert en configureert u de nieuwste Azure PowerShell.](/powershell/azure/overview)

In de volgende voorbeelden worden `myResourceGroup` `myVM`variabelen `myVMAccessExtension`gebruikt zoals , en . Vervang deze variabele namen en locaties door uw eigen waarden.

> [!NOTE]
> U reset de gebruikersreferenties en de RDP-configuratie met de [PowerDlet Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. In de volgende `myVMAccessExtension` voorbeelden is een naam die u opgeeft als onderdeel van het proces. Als u eerder met de VMAccessAgent hebt gewerkt, u `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` de naam van de bestaande extensie krijgen door de eigenschappen van de VM te controleren. Als u de naam wilt bekijken, kijkt u onder het gedeelte 'Extensies' van de uitvoer.

Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw virtuele machine. Als u nog steeds geen verbinding maken, probeert u de volgende stap.

1. **Uw RDP-verbinding opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen zijn uitgeschakeld of windows firewallregels bijvoorbeeld RDP blokkeren.
   
    In het volgende voorbeeld wordt de RDP-verbinding opnieuw ingesteld op een VM met de naam `myVM` op de `WestUS` locatie en in de resourcegroep met de naam `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Regels voor netwerkbeveiligingsgroepen verifiëren**. Met deze stap voor het oplossen van problemen wordt gecontroleerd of u een regel in uw netwerkbeveiligingsgroep hebt om RDP-verkeer toe te staan. De standaardpoort voor RDP is TCP-poort 3389. Een regel om RDP-verkeer toe te staan, wordt mogelijk niet automatisch gemaakt wanneer u uw vm maakt.
   
    Wijs eerst alle configuratiegegevens voor uw `$rules` netwerkbeveiligingsgroep toe aan de variabele. In het volgende voorbeeld wordt informatie `myNetworkSecurityGroup` verkregen over `myResourceGroup`de netwerkbeveiligingsgroep die is genoemd in de resourcegroep met de naam :
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Bekijk nu de regels die zijn geconfigureerd voor deze netwerkbeveiligingsgroep. Controleer of er een regel bestaat om TCP-poort 3389 als volgt toe te staan voor binnenkomende verbindingen:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    In het volgende voorbeeld wordt een geldige beveiligingsregel weergegeven die RDP-verkeer toestaat. U kunt `Protocol` `DestinationPortRange`zien `Access`, `Direction` , en correct zijn geconfigureerd:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Als u geen regel hebt die RDP-verkeer toestaat, [maakt u een regel netwerkbeveiligingsgroep](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tcp-poort 3389 toestaan.
3. **Gebruikersreferenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wachtwoord opnieuw ingesteld op het lokale beheerdersaccount dat u opgeeft wanneer u de referenties niet zeker bent of bent vergeten.
   
    Geef eerst de gebruikersnaam en een nieuw wachtwoord `$cred` op door referenties als volgt aan de variabele toe te wijsen:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Werk nu de referenties van uw VM bij. In het volgende voorbeeld worden de `myVM` referenties `WestUS` bijgewerkt op een `myResourceGroup`vm die is genoemd in de locatie en in de resourcegroep met de naam:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Start uw VM opnieuw.** Deze probleemoplossingsstap kan eventuele onderliggende problemen die de VM zelf heeft, verhelpen.
   
    In het volgende voorbeeld `myVM` wordt de vm `myResourceGroup`opnieuw gestart met de naam in de resourcegroep met de naam:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Uw VM opnieuw implementeren**. Met deze stap voor het oplossen van problemen wordt uw VM opnieuw geïmplementeerd naar een andere host binnen Azure om onderliggende platform- of netwerkproblemen te verhelpen.
   
    In het volgende voorbeeld wordt `myVM` de `WestUS` VM opnieuw geïmplementeerd `myResourceGroup`die is vernoemd naar de locatie en in de resourcegroep met de naam :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Routering verifiëren**. Gebruik de [next-hopmogelijkheid](../../network-watcher/network-watcher-check-next-hop-portal.md) van Network Watcher om te bevestigen dat een route niet voorkomt dat verkeer wordt doorgestuurd naar of van een virtuele machine. U ook effectieve routes bekijken om alle effectieve routes voor een netwerkinterface te bekijken. Zie [Effectieve routes gebruiken om de vm-verkeersstroom op te lossen voor](../../virtual-network/diagnose-network-routing-problem.md)meer informatie.

7. Zorg ervoor dat elke on-premises firewall of firewall op uw computer uitgaand TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds rdp-problemen ondervindt, u [een ondersteuningsverzoek openen](https://azure.microsoft.com/support/options/) of meer gedetailleerde concepten en stappen voor [het oplossen van problemen met RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)lezen.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Problemen met VM's die zijn gemaakt met het implementatiemodel Classic oplossen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Probeer na elke probleemoplossingsstap opnieuw verbinding te maken met de virtuele machine.

1. **Uw RDP-verbinding opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen zijn uitgeschakeld of windows firewallregels bijvoorbeeld RDP blokkeren.
   
    Selecteer uw VM in de Azure-portal. Klik op de **... Meer** knop en klik vervolgens op **Rastoegang opnieuw instellen:**
   
    ![De RDP-configuratie in de Azure-portal opnieuw instellen](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Eindpunten van Cloud Services verifiëren**. Met deze stap voor het oplossen van problemen wordt gecontroleerd of u eindpunten in uw Cloud Services hebt om RDP-verkeer mogelijk te maken. De standaardpoort voor RDP is TCP-poort 3389. Een regel om RDP-verkeer toe te staan, wordt mogelijk niet automatisch gemaakt wanneer u uw vm maakt.
   
   Selecteer uw VM in de Azure-portal. Klik op de knop **Eindpunten** om de eindpunten weer te geven die momenteel voor uw vm zijn geconfigureerd. Controleer of er eindpunten zijn die RDP-verkeer op TCP-poort 3389 toestaan.
   
   In het volgende voorbeeld worden geldige eindpunten weergegeven die RDP-verkeer toestaan:
   
   ![Eindpunten van Cloud Services verifiëren in de Azure-portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Als u geen eindpunt hebt waarmee RDP-verkeer wordt toegestaan, [maakt u een eindpunt voor Cloud Services.](../windows/classic/setup-endpoints.md) Tcp toestaan om privépoort 3389 toe te staan.
3. **Vm-opstartdiagnose bekijken**. Met deze stap voor het oplossen van problemen worden de VM-consolelogboeken geherziet om te bepalen of de VM een probleem meldt. Niet alle VM's hebben opstartdiagnose ingeschakeld, dus deze probleemoplossingsstap kan optioneel zijn.
   
    Specifieke stappen voor het oplossen van problemen vallen buiten het bereik van dit artikel, maar kunnen duiden op een breder probleem dat van invloed is op de RDP-connectiviteit. Zie [Diagnostische gegevens voor VM's opstarten voor virtuele](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)gegevens voor meer informatie over het bekijken van de consolelogboeken en vm-schermafbeelding.
4. **Controleer de VM-resourcestatus**. Met deze stap voor het oplossen van problemen worden er geen bekende problemen met het Azure-platform vastgesteld die van invloed kunnen zijn op de connectiviteit met de VM.
   
    Selecteer uw VM in de Azure-portal. Schuif naar beneden in het instellingenvenster naar de sectie **Ondersteuning + Probleemoplossing** onder aan de lijst. Klik op de knop **Resourcestatus.** Een gezonde VM meldt als **beschikbaar:**
   
    ![Vm-bronstatus controleren in de Azure-portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Gebruikersreferenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wachtwoord opnieuw ingesteld op het lokale beheerdersaccount dat u opgeeft wanneer u de referenties niet zeker bent of bent vergeten.  Zodra u bent aangemeld bij de VM, moet u het wachtwoord voor die gebruiker opnieuw instellen.
   
    Selecteer uw VM in de Azure-portal. Schuif naar beneden in het instellingenvenster naar de sectie **Ondersteuning + Probleemoplossing** onder aan de lijst. Klik op de knop **Wachtwoord opnieuw instellen.** Voer uw gebruikersnaam en een nieuw wachtwoord in. Klik tot slot op de knop **Opslaan:**
   
    ![De gebruikersreferenties in de Azure-portal opnieuw instellen](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Start uw VM opnieuw.** Deze probleemoplossingsstap kan eventuele onderliggende problemen die de VM zelf heeft, verhelpen.
   
    Selecteer uw VM in de Azure-portal en klik op het tabblad **Overzicht.** Klik op de knop **Opnieuw starten:**
   
    ![De VM opnieuw starten in de Azure-portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Zorg ervoor dat elke on-premises firewall of firewall op uw computer uitgaand TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds rdp-problemen ondervindt, u [een ondersteuningsverzoek openen](https://azure.microsoft.com/support/options/) of meer gedetailleerde concepten en stappen voor [het oplossen van problemen met RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)lezen.

## <a name="troubleshoot-specific-rdp-errors"></a>Specifieke RDP-fouten oplossen
U een specifiek foutbericht tegenkomen wanneer u via RDP verbinding probeert te maken met uw VM. De volgende zijn de meest voorkomende foutmeldingen:

* [De externe sessie is verbroken omdat er geen Extern bureaublad-licentieservers beschikbaar zijn om een licentie te verstrekken.](troubleshoot-specific-rdp-errors.md#rdplicense)
* [Extern bureaublad kan de naam van de computer niet vinden.](troubleshoot-specific-rdp-errors.md#rdpname)
* [Er is een verificatiefout opgetreden. Er kan geen contact worden opgenomen met de lokale veiligheidsautoriteit.](troubleshoot-specific-rdp-errors.md#rdpauth)
* [Windows-beveiligingsfout: uw referenties hebben niet gewerkt.](troubleshoot-specific-rdp-errors.md#wincred)
* [Deze computer kan geen verbinding maken met de externe computer.](troubleshoot-specific-rdp-errors.md#rdpconnect)

## <a name="additional-resources"></a>Aanvullende bronnen
Als er geen van deze fouten is opgetreden en u nog steeds geen verbinding met de virtuele band met de virtuele machine maken via Extern bureaublad, leest u de gedetailleerde [handleiding voor probleemoplossing voor Extern bureaublad.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Zie [Toegang tot een toepassing die op](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)een Azure VM wordt uitgevoerd voor het oplossen van stappen voor het openen van toepassingen die op een vm worden uitgevoerd.
* Zie Problemen met [SSH-verbindingen met een Linux-vm in Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)oplossen als u problemen ondervindt met Secure Shell (SSH) om verbinding te maken met een Linux-vm in Azure.


