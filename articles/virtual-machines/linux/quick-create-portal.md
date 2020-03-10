---
title: 'Snelstartgids: een virtuele Linux-machine maken in de Azure Portal'
description: In deze Quick Start leert u hoe u de Azure Portal kunt gebruiken om een virtuele Linux-machine te maken.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 894fa2f3cda798a409db3ee8e9761c1702baf955
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372629"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Snelstart: Een virtuele Linux-machine maken op de Azure Portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. De Azure Portal is een gebruikers interface op basis van een browser voor het maken van Azure-resources. In deze Quick start ziet u hoe u de Azure Portal gebruikt voor het implementeren van een virtuele Linux-machine (VM) met Ubuntu 18,04 LTS. Wanner u uw virtuele machine in actie wilt zien, voert u ook SSH voor de virtuele machine uit en installeert u de NGINX-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U hebt een SSH-sleutelpaar nodig om deze snelstart te volgen. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.

Open een bash-shell en gebruik [ssh-keygen](https://www.ssh.com/ssh/keygen/) om een SSH-sleutelpaar te maken. Als u geen een bash-shell op uw lokale computer hebt, kunt u de [Azure Cloud Shell](https://shell.azure.com/bash) gebruiken.


1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het menu boven aan de pagina het `>_` pictogram om Cloud Shell te openen.
1. Zorg ervoor dat de Cloud shell **bash** in de linkerbovenhoek aangeeft. Als het Power shell is, gebruikt u de vervolg keuzelijst om **bash** te selecteren en selecteert u **bevestigen** dat u wilt overschakelen naar de bash-shell.
1. Typ `ssh-keygen -t rsa -b 2048` om de SSH-sleutel te maken. 
1. U wordt gevraagd een bestand op te geven waarin het sleutel paar moet worden opgeslagen. Druk op **Enter** om op te slaan op de standaard locatie, die wordt weer gegeven tussen vier Kante haken. 
1. U wordt gevraagd om een wachtwoordzin in te voeren. U kunt een wachtwoordzin voor uw SSH-sleutel typen of op **Enter** drukken om zonder een wachtwoordzin door te gaan.
1. Met de `ssh-keygen` opdracht worden open bare en persoonlijke sleutels gegenereerd met de standaard naam van `id_rsa` in de `~/.ssh directory`. Met de opdracht wordt het volledige pad naar de openbare sleutel geretourneerd. Gebruik het pad naar de open bare sleutel om de inhoud ervan weer te geven met `cat` door `cat ~/.ssh/id_rsa.pub`te typen.
1. Kopieer de uitvoer van deze opdracht en sla deze ergens op om deze later in dit artikel te gebruiken. Dit is uw open bare sleutel en u hebt deze nodig bij het configureren van uw beheerders account om u aan te melden bij uw VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure Portal](https://portal.azure.com) als u dat nog niet hebt gedaan.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Typ **virtuele machines** in de zoek opdracht.
1. Selecteer onder **Services**de optie **virtuele machines**.
1. Selecteer op de pagina **virtuele machines** de optie **toevoegen**. De pagina **een virtuele machine maken** wordt geopend.
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myResourceGroup* voor de naam. *. 

    ![Een nieuwe resourcegroep maken voor uw VM](./media/quick-create-portal/project-details.png)

1. Typ *myVM* voor de naam van de **virtuele machine**onder Details van het **exemplaar**, kies *VS-oost* voor uw **regio**en kies *Ubuntu 18,04 LTS* voor uw **installatie kopie**. Houd voor de rest de standaardinstellingen aan.

    ![Sectie Exemplaardetails](./media/quick-create-portal/instance-details.png)

1. Onder **Administrator-account**selecteert u **open bare SSH-sleutel**, typt u uw gebruikers naam en plakt u deze in uw open bare sleutel. Verwijder eventuele voorloop- en volgspaties van uw openbare sleutel.

    ![Administrator-account](./media/quick-create-portal/administrator-account.png)

1. Onder **Regels voor binnenkomende poort** > **Openbare binnenkomende poorten**, kiest u **​​Geselecteerde poorten toestaan** en selecteert u vervolgens **SSH (22)** en  **HTTP (80)** in de vervolgkeuzelijst. 

    ![Open poorten voor RDP en HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

1. Op de pagina **Een virtuele machine maken** ziet u de details van de virtuele machine die u gaat maken. Wanneer u klaar bent, selecteert u **Maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. Wanneer de implementatie is voltooid, gaat u verder naar de volgende sectie.

    
## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

Maak een SSH-verbinding met de VM.

1. Selecteer de knop **Verbinden** op de overzichtspagina van uw VM. 

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Laat op de pagina **Verbinding maken met virtuele machine** de standaardopties staan om verbinding te maken met een IP-adres via poort 22. In **Aanmelden met lokaal VM-account** wordt een verbindingsopdracht weergegeven. Selecteer de knop om de opdracht te kopiëren. Het volgende voorbeeld laat zien hoe de SSH-verbindingsopdracht eruitziet:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Met dezelfde bash-shell die u hebt gebruikt om uw SSH-sleutel paar te maken (u kunt de Cloud Shell opnieuw openen door `>_` opnieuw te selecteren of naar https://shell.azure.com/bash)te gaan, plakt u de opdracht SSH-verbinding in de shell om een SSH-sessie te maken.

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de NGINX-webserver. Werk vanuit uw SSH-sessie de pakketbronnen bij en installeer het meest recente NGINX-pakket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Wanneer u klaar bent, typt u `exit` om de SSH-sessie te verlaten.


## <a name="view-the-web-server-in-action"></a>De webserver in actie zien

Gebruik een webbrowser naar keuze om de standaard NGINX-welkomstpagina weer te geven. Typ het open bare IP-adres van de virtuele machine als Internet adres. Het openbare IP-adres kan worden gevonden op de overzichtspagina van de virtuele machine of als onderdeel van de SSH-verbindingsreeks die u eerder hebt gebruikt.

![Standaardsite van NGINX](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkbeveiligingsgroep en -regel gemaakt en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Linux-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Linux-machines](./tutorial-manage-vm.md)
