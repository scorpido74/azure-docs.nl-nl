---
title: 'Snelstart: Een virtuele Linux-machine maken op de Azure Portal | Microsoft Docs'
description: In deze snelstartgids leert u hoe u de Azure Portal  gebruikt om een virtuele Linux-machine te maken
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 8/16/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 189d6d0030264590986d6fe2af47d35705cfb08b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575802"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Quickstart: Een virtuele Linux-machine maken in de Azure Portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. De Azure-portal is een gebruikersinterface op basis van een browser voor het maken van VM's en alle verwante resources. In deze snelstart wordt beschreven hoe u de Azure-portal gebruikt voor het implementeren van een virtuele Linux-machine (VM) waarop Ubuntu 16.04 LTS wordt uitgevoerd. Wanner u uw virtuele machine in actie wilt zien, voert u ook SSH voor de virtuele machine uit en installeert u de NGINX-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U hebt een SSH-sleutelpaar nodig om deze snelstart te volgen. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.

Open een bash-shell en gebruik [ssh-keygen](https://www.ssh.com/ssh/keygen/) om een SSH-sleutelpaar te maken. Hieronder vindt u een voor beeld van een opdracht. Als u geen een bash-shell op uw lokale computer hebt, kunt u de [Azure Cloud Shell](https://shell.azure.com/bash) gebruiken.

```bash
ssh-keygen -t rsa -b 2048
```

U wordt gevraagd een bestand op te geven waarin het sleutel paar moet worden opgeslagen. U kunt een specifieke bestands locatie invoeren of op ENTER drukken om op te slaan op de standaard locatie die wordt aangegeven tussen haakjes. Vervolgens wordt u gevraagd om een wachtwoordzin in te voeren. U kunt een wachtwoordzin invoeren voor uw SSH-sleutel of u kunt op ENTER drukken om zonder een wachtwoordzin door te gaan.

```bash
[root@linuxvm ~]$ ssh-keygen -t rsa -b 2048
Enter the file in which to save the key (home/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your public key has been saved in /home/root/.ssh/id_rsa.pub.
The key fingerprint is: SHA256:kkQS13gbaxevy4ULH0mW6wLIkcFm0twx/rIlSo1fIqU
The key's randomart image is:
+---[RSA 2018]----+
|   +oo=+         |
|  . B=o.+ .      |
|   + o+. + +     |
|    o* o+ = .    |
|   .EoB.S+ =     |
|   .o+.O. * .    |
|    . o. = =     |
|        . *      |
|         .       |
+----[SHA256]-----+
```
Met `ssh-keygen` de opdracht worden open bare en persoonlijke sleutels gegenereerd met de `id_rsa` standaard naam `~/.ssh directory`van in de. Met de opdracht wordt het volledige pad naar de openbare sleutel geretourneerd. Gebruik het pad naar de openbare sleutel om de inhoud ervan weer te geven met `cat`.

```bash
cat ~/.ssh/id_rsa.pub
```

>[!NOTE]
> Als u ervoor hebt gekozen om uw SSH-sleutel op een andere locatie op te slaan dan de standaard waarde, moet u deze locatie gebruiken wanneer u uitvoert`cat`

Sla de uitvoer van deze opdracht op. Dit is uw open bare sleutel en u hebt deze nodig bij het configureren van uw beheerders account om u aan te melden bij uw VM.

Ga naar de [pagina man](https://linux.die.net/man/1/ssh-keygen)(Engelstalig) voor meer informatie over de opdracht ssh-keygen.

Zie [SSH-sleutels gebruiken met Windows](ssh-from-windows.md)als u een Windows-computer gebruikt en meer wilt weten over het maken van SSH-sleutel paren, met inbegrip van het gebruik van putty.

Als u uw SSH-sleutelpaar met behulp van de Cloud Shell maakt, wordt deze opgeslagen in een Azure-bestandsshare die [automatisch wordt gekoppeld door de Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Verwijder deze bestandsshare of dit opslagaccount niet totdat u uw sleutels hebt opgehaald. Anders verliest u toegang tot de virtuele machine.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.

1. Zoek in het zoekvak boven de lijst met Azure Marketplace-resources naar **Ubuntu Server 18,04** en selecteer de Ubuntu 18,04 LTS-aanbieding en kies vervolgens **maken**.

1. Zorg ervoor dat op het tabblad **Basis** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** onder **Resourcegroep**. Typ in het pop-upvenster *myResourceGroup* als naam voor de resourcegroep en kies **OK**.

    ![Een nieuwe resourcegroep maken voor uw VM](./media/quick-create-portal/project-details.png)

1. Typ onder **Exemplaardetails** *myVM* als **Naam van de virtuele machine** en kies *US - oost* als de **Regio**. Houd voor de rest de standaardinstellingen aan.

    ![Sectie Exemplaardetails](./media/quick-create-portal/instance-details.png)

1. Onder **Administrator-account**selecteert u **open bare SSH-sleutel**, typt u uw gebruikers naam en plakt u uw open bare sleutel die u eerder hebt opgeslagen in het tekstvak. Verwijder eventuele voorloop- en volgspaties van uw openbare sleutel.

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

2. Laat op de pagina **Verbinding maken met virtuele machine** de standaardopties staan om verbinding te maken met een IP-adres via poort 22. In **Aanmelden met lokaal VM-account** wordt een verbindingsopdracht weergegeven. Klik op de knop om de opdracht te kopiëren. Het volgende voorbeeld laat zien hoe de SSH-verbindingsopdracht eruitziet:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Gebruik dezelfde bash-shell die u hebt gebruikt om uw SSH-sleutel paar te maken (zoals de [Azure Cloud shell](https://shell.azure.com/bash) of uw lokale bash-shell), plak de opdracht SSH-verbinding in de shell om een SSH-sessie te maken.

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de NGINX-webserver. Werk vanuit uw SSH-sessie de pakketbronnen bij en installeer het meest recente NGINX-pakket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Wanneer u klaar bent, typt u `exit` om de SSH-sessie te verlaten.


## <a name="view-the-web-server-in-action"></a>De webserver in actie zien

Gebruik een webbrowser naar keuze om de standaard NGINX-welkomstpagina weer te geven. Gebruik het openbare IP-adres van uw VM als het webadres. Het openbare IP-adres kan worden gevonden op de overzichtspagina van de virtuele machine of als onderdeel van de SSH-verbindingsreeks die u eerder hebt gebruikt.

![Standaardsite van NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkbeveiligingsgroep en -regel gemaakt en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Linux-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Linux-machines](./tutorial-manage-vm.md)
