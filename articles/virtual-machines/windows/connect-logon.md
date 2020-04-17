---
title: Verbinding maken met een Windows Server-vm
description: Meer informatie over het verbinden en aanmelden bij een Windows VM met behulp van de Azure-portal en het implementatiemodel voor Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: c4f5df4a76792c64ab92bbe8d6e4b84080fbd8fe
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459182"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Verbinding maken en aanmelden bij een virtuele Azure-machine met Windows
U gebruikt de knop **Verbinden** in Azure Portal om een Extern bureaublad-sessie (RDP) te starten vanaf een Windows-computer. Eerst maak je verbinding met de virtuele machine, en dan meld je je aan.

Als u vanaf een Mac verbinding wilt maken met een Windows-vm, moet u een RDP-client voor Mac installeren, zoals [Microsoft Remote Desktop.](https://aka.ms/rdmac)

## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine
1. Ga naar de [Azure-portal](https://portal.azure.com/) om verbinding te maken met een virtuele machine. Zoeken naar virtuele machines en selecteer **deze**.
2. Selecteer de virtuele machine in de lijst.
3. Selecteer Verbinding **maken**aan het begin van de pagina met virtuele machines .
4. Selecteer **rdp**op de pagina **Verbinding maken met virtuele machine** en selecteer vervolgens het juiste **IP-adres** en **poortnummer**. In de meeste gevallen moeten het standaard IP-adres en de standaardpoort worden gebruikt. Selecteer **RDP-bestand downloaden**. Als de VM een just-in-time beleidsset heeft ingesteld, moet u eerst de knop **Toegangs aanvragen** selecteren om toegang aan te vragen voordat u het RDP-bestand downloaden. Zie Toegang [tot virtuele machines beheren met behulp van het just-in-time-beleid](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.
5. Open het gedownloade RDP-bestand en selecteer **Verbinden** wanneer dit wordt gevraagd. U krijgt een waarschuwing `.rdp` dat het bestand afkomstig is van een onbekende uitgever. Dit is normaal gedrag. Selecteer **Verbinding maken** om door te gaan in het venster Verbinding met **extern bureaublad.**
   
    ![Schermafbeelding met waarschuwing over een onbekende uitgever](./media/connect-logon/rdp-warn.png)
3. Selecteer in het venster **Windows-beveiliging****Meer opties** en vervolgens **Een ander account gebruiken**. Voer de referenties voor een account op de virtuele machine in en selecteer **OK**.
   
     **Lokaal account:** dit is meestal de gebruikersnaam en het wachtwoord van het lokale account die u hebt opgegeven toen u de virtuele machine hebt gemaakt. In dit geval is het domein de naam van de virtuele machine en het is ingevoerd als *vmname*&#92;*gebruikersnaam*.  
   
    **Domein samengevoegdvm:** Als de vm tot een domein behoort, voert u de gebruikersnaam in de indeling *Domein*&#92;*Gebruikersnaam*in. Het account moet bovendien in de groep Administrators staan of er moet een machtiging voor externe toegang zijn verleend aan de VM.
   
    **Domeincontroller:** Als de VM een domeincontroller is, voert u de gebruikersnaam en het wachtwoord van een domeinbeheerdersaccount voor dat domein in.
4. Selecteer **Ja** om de identiteit van de virtuele machine te verifiëren en de aanmelding af te sluiten.
   
   ![Schermafbeelding met een bericht over het verifiëren van de identiteit van de virtuele machine](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Als **de** connect-knop in de portal grijs is en u niet verbonden bent met Azure via een [Express Route](../../expressroute/expressroute-introduction.md) of [Site-to-Site VPN-verbinding,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) moet u uw VM een openbaar IP-adres maken en toewijzen voordat u RDP gebruiken. Zie [Openbare IP-adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)voor meer informatie.
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Verbinding maken met de virtuele machine met PowerShell

 

Als u PowerShell gebruikt en de Azure PowerShell-module `Get-AzRemoteDesktopFile` hebt geïnstalleerd, u ook verbinding maken met de cmdlet, zoals hieronder wordt weergegeven.

In dit voorbeeld wordt onmiddellijk de RDP-verbinding gestart, waarbij u vergelijkbare aanwijzingen zoals hierboven wordt weergegeven.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

U het RDP-bestand ook opslaan voor toekomstig gebruik.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Volgende stappen
Zie Problemen met verbindingen [met extern bureaublad oplossen](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als u problemen hebt met verbinding met extern bureaublad. 

