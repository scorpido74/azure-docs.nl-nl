---
title: SSH-sleutels gebruiken met Windows voor virtuele Linux-machines
description: Meer informatie over het genereren en gebruiken van SSH-sleutels op een Windows-computer om verbinding te maken met een virtuele Linux-machine in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 4fc411d3833fcd47477e7e0fe11c6da9e2114143
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502024"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-sleutels gebruiken met Windows op Azure

In dit artikel worden de manieren beschreven waarop u SSH-sleutels ( *Secure Shell* ) kunt genereren en gebruiken op een Windows-computer om een virtuele Linux-machine (VM) te maken en te verbinden in Azure. Als u SSH-sleutels wilt gebruiken vanuit een Linux-of macOS-client, raadpleegt u de [snelle](mac-create-ssh-keys.md) of [gedetailleerde](create-ssh-keys-detailed.md) richt lijnen.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-pakketten en SSH-clients
U maakt verbinding met virtuele Linux-machines in Azure en beheert deze met behulp van een *SSH-client*. Computers met Linux of macOS hebben meestal een suite SSH-opdrachten voor het genereren en beheren van SSH-sleutels en het maken van SSH-verbindingen. 

Op Windows-computers zijn niet altijd vergelijk bare SSH-opdrachten geïnstalleerd. Recente versies van Windows 10 bieden [openssh-client opdrachten](https://devblogs.microsoft.com/commandline/windows10v1803/) voor het maken en beheren van SSH-sleutels en het maken van ssh-verbindingen vanaf een opdracht prompt. Recente Windows 10-versies omvatten ook het [Windows-subsysteem voor Linux](/windows/wsl/about) voor het uitvoeren en openen van hulpprogram ma's zoals een SSH-client in een bash-shell. 

Andere veelgebruikte Windows SSH-clients die u lokaal kunt installeren, zijn opgenomen in de volgende pakketten:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git voor Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

U kunt de SSH-hulpprogram ma's die beschikbaar zijn in bash ook gebruiken in de [Azure Cloud shell](../../cloud-shell/overview.md). 

* Open Cloud Shell in uw webbrowser op [https://shell.azure.com](https://shell.azure.com) of in de [Azure Portal](https://portal.azure.com). 
* Toegang Cloud Shell als een Terminal vanuit Visual Studio code door de extensie van het [Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)te installeren.

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken
In de volgende secties worden twee opties beschreven voor het maken van een SSH-sleutel paar in Windows. U kunt een shell opdracht gebruiken ( `ssh-keygen` ) of een GUI-hulp programma (PuTTYgen). Houd er ook rekening mee dat wanneer u Power shell gebruikt om een sleutel te maken, de open bare sleutel uploadt als SSH. com-indeling (SECSH). Wanneer u CLI gebruikt, moet u de sleutel converteren naar de OpenSSH-indeling voordat u deze uploadt. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>SSH-sleutels maken met ssh-keygen

Als u een opdracht shell uitvoert in Windows die SSH-client hulpprogramma's ondersteunt (of als u Azure Cloud Shell gebruikt), maakt u een SSH-sleutel paar met behulp van de `ssh-keygen` opdracht. Typ de volgende opdracht en beantwoord de prompts. Als er een SSH-sleutel paar op de gekozen locatie bestaat, worden deze bestanden overschreven. 

```bash
ssh-keygen -t rsa -b 2048
```

Zie [de volgende](mac-create-ssh-keys.md) [stappen voor](create-ssh-keys-detailed.md) meer informatie over het maken van SSH-sleutels met behulp van `ssh-keygen` .

### <a name="create-ssh-keys-with-puttygen"></a>SSH-sleutels maken met PuTTYgen

Als u liever een GUI-hulp programma gebruikt om SSH-sleutels te maken, kunt u de PuTTYgen-sleutel generator gebruiken, opgenomen in het pakket voor het [downloaden van putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Een SSH RSA-sleutel paar maken met PuTTYgen:

1. PuTTYgen starten.

2. Klik op **Genereren**. Standaard genereert PuTTYgen een RSA-sleutel van 2048 bits (SSH-2).

3. Beweeg de muis aanwijzer in het lege gebied om wille keurigheid voor de sleutel op te geven.

4. Nadat de open bare sleutel is gegenereerd, voert u optioneel een wachtwoordzin in en bevestigt u deze. U wordt gevraagd om de wachtwoordzin wanneer u zich bij de virtuele machine verifieert met uw persoonlijke SSH-sleutel. Zonder een wachtwoordzin kunnen gebruikers zich aanmelden bij elke virtuele machine of service die gebruikmaakt van die sleutel als iemand uw persoonlijke sleutel verkrijgt. U wordt aangeraden een wachtwoordzin te maken. Als u de wachtwoordzin vergeet, is er echter geen manier om deze te herstellen.

5. De open bare sleutel wordt bovenaan het venster weer gegeven. U kunt deze volledige open bare sleutel kopiëren en deze vervolgens in de Azure Portal of in een Azure Resource Manager sjabloon plakken wanneer u een virtuele Linux-machine maakt. U kunt ook **open bare sleutel opslaan** selecteren om een kopie op uw computer op te slaan. Houd er rekening mee dat bij het opslaan naar een bestand de open bare sleutel wordt geconverteerd naar een andere indeling, [RFC4716](https://tools.ietf.org/html/rfc4716). De RFC4716-indeling is mogelijk niet compatibel met alle Api's. Daarom wordt u aangeraden om de open bare sleutel die wordt weer gegeven in het venster PuTTy, te kopiëren voor gebruik in de Azure Portal.

    ![Bestand met open bare sleutel voor PuTTy opslaan](./media/ssh-from-windows/save-public-key.png)

6. Selecteer desgewenst **persoonlijke sleutel opslaan**om de persoonlijke sleutel op te slaan in de indeling van de persoonlijke sleutel voor putty (. ppk-bestand). U hebt het. ppk-bestand later nodig voor het gebruik van PuTTy om een SSH-verbinding met de virtuele machine te maken.

    ![Bestand met persoonlijke sleutel voor PuTTy opslaan](./media/ssh-from-windows/save-ppk-file.png)

    Als u de persoonlijke sleutel in de OpenSSH-indeling wilt opslaan, selecteert u in de indeling van de persoonlijke sleutel die door veel SSH-clients wordt gebruikt, de export OpenSSH-sleutel **conversies**  >  **Export OpenSSH key**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Een open bare SSH-sleutel opgeven bij het implementeren van een virtuele machine

Als u een virtuele Linux-machine wilt maken die gebruikmaakt van SSH-sleutels voor verificatie, geeft u uw open bare SSH-sleutel op wanneer u de virtuele machine maakt met behulp van de Azure Portal of andere methoden

In het volgende voor beeld ziet u hoe u deze open bare sleutel kopieert en plakt in de Azure Portal wanneer u een virtuele Linux-machine maakt. De open bare sleutel wordt doorgaans opgeslagen in de map ~/.ssh/authorized_key op de nieuwe virtuele machine.

   ![Open bare sleutel gebruiken bij het maken van een virtuele machine in de Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Een van de manieren om een SSH-verbinding met uw Linux-VM vanuit Windows te maken, is met behulp van een SSH-client. Dit is de aanbevolen methode als u een SSH-client op uw Windows-systeem hebt geïnstalleerd of als u de SSH-hulpprogram ma's in bash in Azure Cloud Shell gebruikt. Als u liever een GUI-hulp programma hebt, kunt u verbinding maken met PuTTy.  

### <a name="use-an-ssh-client"></a>Een SSH-client gebruiken
Met de open bare sleutel die is geïmplementeerd op uw Azure-VM en de persoonlijke sleutel op uw lokale systeem, SSH naar uw virtuele machine met het IP-adres of de DNS-naam van uw virtuele machine. Vervang *azureuser* en *myvm.westus.cloudapp.Azure.com* in de volgende opdracht door de gebruikers naam van de beheerder en de Fully Qualified Domain Name (of het IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u tijdens het maken van uw sleutel paar een wachtwoordzin hebt geconfigureerd, voert u de wachtwoordzin in wanneer u hierom wordt gevraagd tijdens het aanmeldings proces.

Als de virtuele machine gebruikmaakt van het just-in-time-toegangs beleid, moet u toegang aanvragen voordat u verbinding kunt maken met de virtuele machine. Zie [toegang tot virtuele machines beheren met de just-in-time-beleids regels](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.

### <a name="connect-with-putty"></a>Verbinding maken met PuTTy

Als u het [putty-download pakket](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) hebt geïnstalleerd en eerder een putty persoonlijke sleutel bestand (. ppk) hebt gegenereerd, kunt u verbinding maken met een virtuele Linux-machine met PuTTY.

1. PuTTy starten.

2. Vul de hostnaam of het IP-adres van uw virtuele machine in via de Azure Portal:

    ![Nieuwe PuTTy-verbinding openen](./media/ssh-from-windows/putty-new-connection.png)

3. Selecteer de categorie **verbindings**-  >  **SSH**-  >  **verificatie** . Blader naar en selecteer uw persoonlijke sleutel voor PuTTy (. ppk-bestand):

    ![Selecteer uw persoonlijke sleutel voor de validatie van de PuTTy](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klik op **openen** om verbinding te maken met uw virtuele machine.

## <a name="next-steps"></a>Volgende stappen

* Zie voor gedetailleerde stappen, opties en geavanceerde voor beelden van het werken met SSH-sleutels [gedetailleerde stappen voor het maken van SSH-sleutel paren](create-ssh-keys-detailed.md).

* U kunt Power shell ook in Azure Cloud Shell gebruiken om SSH-sleutels te genereren en SSH-verbindingen te maken met virtuele Linux-machines. Zie de [Power shell-Snelstartgids](../../cloud-shell/quickstart-powershell.md#ssh).

* Zie [problemen met ssh-verbindingen met een virtuele machine van Azure Linux oplossen](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)als u problemen hebt met het gebruik van SSH om verbinding te maken met uw virtuele Linux-machines.
