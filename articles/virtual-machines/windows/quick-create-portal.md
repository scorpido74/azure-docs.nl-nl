---
title: Snelstart - Een Windows-vm maken in de Azure-portal
description: In deze snelstartgids leert u hoe u de Azure Portal  gebruikt om een virtuele Windows-machine te maken
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a304e400d193eb4b2d0c2e8ec30ea03ea41977f8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606641"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snelstart: Een virtuele Windows-machine maken op de Azure Portal

Virtuele Azure-machines (VM's)kunnen worden gemaakt via Azure Portal. Deze methode biedt een gebruikersinterface op basis van een browser voor het maken van VM's en van alle verwante resources. Met deze quickstart u zien hoe u de Azure-portal gebruiken om een virtuele machine (VM) te implementeren in Azure waarop Windows Server 2019 wordt uitgevoerd. Om uw virtuele machine in actie te zien, voert u een externe bureaubladsessie uit voor de virtuele machine uit en installeert u de IIS-webserver.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Typ **virtuele machines** in de zoekopdracht.
1. Selecteer **onder Services**virtuele **machines**.
1. Selecteer op de pagina **Virtuele machines** de optie **Toevoegen**. 
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myResourceGroup* als de naam. 

    ![Een nieuwe resourcegroep maken voor uw VM](./media/quick-create-portal/project-details.png)

1. Typ **onder Instantiedetails** *myVM* voor de **naam van** de virtuele machine en kies *Oost-VS* voor uw **regio**en kies Vervolgens Windows *Server 2019 Datacenter* voor de **afbeelding**. Houd voor de rest de standaardinstellingen aan.

    ![Sectie Exemplaardetails](./media/quick-create-portal/instance-details.png)

1. Geeft onder **Administrator-account**, een gebruikersnaam op, bijvoorbeeld *azureuser*, en een wachtwoord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Voer uw gebruikersnaam en wachtwoord in](./media/quick-create-portal/administrator-account.png)

1. Kies onder **Inkomende poortregels**De optie **Geselecteerde poorten toestaan** en selecteer vervolgens **RDP (3389)** en **HTTP (80)** in de vervolgkeuzelijst.

    ![Open poorten voor RDP en HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

    ![Controleren en maken](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

Maak via een extern bureaublad verbinding met de virtuele machine. Deze instructies geven aan hoe u vanaf een Windows-computer verbinding maakt met uw VM. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) uit de Mac App Store.

1. Klik op de knop **Verbinding** maken op de overzichtspagina voor uw virtuele machine. 

    ![Verbinding maken met een Azure VM vanaf de portal](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Bewaar op de pagina **Verbinding maken met virtuele machine** de standaardopties om verbinding te maken via IP-adres, via poort 3389 en klik op **RDP-bestand downloaden**.

2. Open het gedownloade RDP-bestand en klik op **Verbinden** wanneer dit wordt gevraagd. 

3. Selecteer in het venster **Windows-beveiliging****Meer opties** en vervolgens **Een ander account gebruiken**. Typ de gebruikersnaam als\\*localhost-gebruikersnaam,* voer het wachtwoord in dat u voor de virtuele machine hebt gemaakt en klik op **OK**. **localhost**

4. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om de verbinding te maken.

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de IIS-webserver. Open een PowerShell-prompt op de virtuele machine en voer de volgende opdracht uit:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Wanneer u klaar bent, sluit u de externe-bureaubladverbinding met de virtuele machine.


## <a name="view-the-iis-welcome-page"></a>De welkomstpagina van IIS weergeven

Selecteer in de portal de VM en in het overzicht van de vm gebruikt u de knop **Klik om** rechts van het IP-adres te kopiëren en deze in een browsertabblad te plakken. De standaard IIS-welkomstpagina wordt geopend en moet er als volgt uitzien:

![Standaardsite van IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. 

Selecteer de brongroep voor de virtuele machine en selecteer **Verwijderen**. Bevestig de naam van de resourcegroep om de resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkpoort geopend voor internetverkeer en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Windows-machines](./tutorial-manage-vm.md)