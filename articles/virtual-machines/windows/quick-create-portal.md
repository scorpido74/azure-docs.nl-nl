---
title: 'Snelstartgids: een virtuele Windows-machine maken in de Azure Portal'
description: In deze snelstartgids leert u hoe u de Azure Portal  gebruikt om een virtuele Windows-machine te maken
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 002d374f5be606688121ef4a3952383567c43e85
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363494"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snelstart: Een virtuele Windows-machine maken op de Azure Portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. Deze methode biedt een gebruikersinterface op basis van een browser voor het maken van VM's en van alle verwante resources. In deze Quick start ziet u hoe u de Azure Portal kunt gebruiken om een virtuele machine (VM) te implementeren in azure met Windows Server 2019. Om uw VM in actie te zien, voert u een externe bureaubladsessie voor de virtuele machine uit en installeert u de IIS-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Typ **virtuele machines** in de zoek opdracht.
1. Selecteer onder **Services**de optie **virtuele machines**.
1. Selecteer op de pagina **virtuele machines** de optie **toevoegen**. 
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myResourceGroup* als de naam. 

    ![Een nieuwe resourcegroep maken voor uw VM](./media/quick-create-portal/project-details.png)

1. Typ *myVM* voor de naam van de **virtuele machine** onder Details van het **exemplaar**en kies *VS-Oost* voor uw **regio**en kies vervolgens *Windows Server 2019 Data Center* voor de **installatie kopie**. Houd voor de rest de standaardinstellingen aan.

    ![Sectie Exemplaardetails](./media/quick-create-portal/instance-details.png)

1. Geeft onder **Administrator-account**, een gebruikersnaam op, bijvoorbeeld *azureuser*, en een wachtwoord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Voer uw gebruikersnaam en wachtwoord in](./media/quick-create-portal/administrator-account.png)

1. Onder **Binnenkomende poort regels**kiest u **geselecteerde poorten toestaan** en selecteert u vervolgens **RDP (3389)** en **http (80)** in de vervolg keuzelijst.

    ![Open poorten voor RDP en HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

    ![Controleren en maken](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

Maak via een extern bureaublad verbinding met de virtuele machine. Deze instructies geven aan hoe u vanaf een Windows-computer verbinding maakt met uw VM. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) uit de Mac App Store.

1. Klik op de knop **verbinding maken** op de pagina overzicht voor uw virtuele machine. 

    ![Verbinding maken met een Azure VM vanaf de portal](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Behoud op de pagina **verbinding maken met virtuele machine** de standaard opties om verbinding te maken op basis van IP-adres, poort 3389 en klik op **RDP-bestand downloaden**.

2. Open het gedownloade RDP-bestand en klik op **Verbinden** wanneer dit wordt gevraagd. 

3. Selecteer in het venster **Windows-beveiliging** **Meer opties** en vervolgens **Een ander account gebruiken**. Typ de gebruikersnaam als **lokalehost**\\*gebruikersnaam*, voer het wachtwoord in dat u hebt gemaakt voor de virtuele machine, en klik vervolgens op **OK**.

4. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om de verbinding te maken.

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de IIS-webserver. Open een PowerShell-prompt op de virtuele machine en voer de volgende opdracht uit:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Wanneer u klaar bent, sluit u de externe-bureaubladverbinding met de virtuele machine.


## <a name="view-the-iis-welcome-page"></a>De welkomstpagina van IIS weergeven

Selecteer de virtuele machine in de portal en klik in het overzicht van de virtuele machine op de knop **om te kopiëren** rechts van het IP-adres om deze te kopiëren en in een browser tabblad te plakken. De standaard welkomst pagina van IIS wordt geopend en moet er als volgt uitzien:

![Standaardsite van IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. 

Selecteer de resource groep voor de virtuele machine en selecteer vervolgens **verwijderen**. Bevestig de naam van de resource groep om het verwijderen van de resources te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkpoort geopend voor internetverkeer en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Windows-machines](./tutorial-manage-vm.md)