---
title: SSH-sleutels gebruiken met Windows voor virtuele Linux-machines
description: Meer informatie over het genereren en gebruiken van SSH-sleutels op een Windows-computer om verbinding te maken met een virtuele Linux-machine op Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: cdf901ca56c150cfed6ba3d462ce493d40bd2488
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757986"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-sleutels gebruiken met Windows op Azure

In dit artikel worden manieren beschreven om *SSH-sleutels (Secure Shell)* op een Windows-computer te genereren en te gebruiken om een Virtuele Linux-machine (VM) in Azure te maken en te verbinden. Zie de [snelle](mac-create-ssh-keys.md) of [gedetailleerde](create-ssh-keys-detailed.md) richtlijnen om SSH-sleutels van een Linux- of macOS-client te gebruiken.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-pakketten en SSH-clients
U maakt verbinding met Linux VM's in Azure en beheert deze met behulp van een *SSH-client.* Computers met Linux of macOS hebben meestal een reeks SSH-opdrachten om SSH-sleutels te genereren en te beheren en SSH-verbindingen te maken. 

Windows-computers hebben niet altijd vergelijkbare SSH-opdrachten geïnstalleerd. Recente versies van Windows 10 bieden [OpenSSH-clientopdrachten](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) om SSH-sleutels te maken en te beheren en SSH-verbindingen te maken vanaf een opdrachtprompt. Recente Windows 10-versies bevatten ook het [Windows-subsysteem voor Linux](https://docs.microsoft.com/windows/wsl/about) om hulpprogramma's uit te voeren en toegang te krijgen tot hulpprogramma's, zoals een SSH-client die native binnen een Bash-shell zit. 

Andere veelvoorkomende Windows SSH-clients die u lokaal installeren, zijn opgenomen in de volgende pakketten:

* [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git voor Windows](https://git-for-windows.github.io/)
* [MobaXterm MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

U ook de SSH-hulpprogramma's gebruiken die beschikbaar zijn in Bash in de [Azure Cloud Shell.](../../cloud-shell/overview.md) 

* Toegang tot Cloud Shell [https://shell.azure.com](https://shell.azure.com) in uw webbrowser op of in de [Azure-portal.](https://portal.azure.com) 
* Toegang tot Cloud Shell als een terminal vanuit Visual Studio Code door de [Azure Account-extensie te](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)installeren.

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken
In de volgende secties worden twee opties beschreven om een SSH-sleutelpaar op Windows te maken. U een shell`ssh-keygen`opdracht ( ) of een GUI-tool (PuTTYgen) gebruiken. Houd er ook rekening mee dat wanneer u Powershell gebruikt om een sleutel te maken, de openbare sleutel uploadt als ssh.com(SECSH)-indeling. Wanneer u CLI gebruikt, converteert u de sleutel naar openSSH-indeling voordat u het uploadt. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>SSH-toetsen maken met ssh-keygen

Als u een opdrachtshell uitvoert op Windows die SSH-clienthulpprogramma's ondersteunt (of `ssh-keygen` als u Azure Cloud Shell gebruikt), maakt u een SSH-sleutelpaar met de opdracht. Typ de volgende opdracht en beantwoord de prompts. Als er een SSH-sleutelpaar bestaat op de gekozen locatie, worden deze bestanden overschreven. 

```bash
ssh-keygen -t rsa -b 2048
```

Zie voor meer achtergrond informatie de [snelle](mac-create-ssh-keys.md) of [gedetailleerde](create-ssh-keys-detailed.md) `ssh-keygen`stappen om SSH-toetsen te maken met behulp van .

### <a name="create-ssh-keys-with-puttygen"></a>SSH-toetsen maken met PuTTYgen

Als u liever een GUI-gebaseerde tool gebruikt om SSH-sleutels te maken, u de PuTTYgen-sleutelgenerator gebruiken, meegeleverd met het [PuTTY-downloadpakket.](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 

Ga als lid van het serini-sleutelpaar met PuTTYgen:

1. Start PuTTYgen.

2. Klik op **Genereren**. PuTTYgen genereert standaard een 2048-bits SSH-2 RSA-toets.

4. Beweeg de muis rond in het lege gebied om willekeur voor de sleutel te bieden.

5. Nadat de openbare sleutel is gegenereerd, voert u optioneel een wachtwoordzin in en bevestigt u deze. U wordt gevraagd om de wachtwoordzin wanneer u zich verifieert naar de VM met uw privé-SSH-sleutel. Zonder een wachtwoordzin kan als iemand uw privésleutel verkrijgt, zich aanmelden bij elke virtuele machine of service die die sleutel gebruikt. We raden u aan een wachtwoordzin te maken. Als u de wachtwoordzin vergeet, is er echter geen manier om deze te herstellen.

6. De openbare sleutel wordt boven aan het venster weergegeven. U deze volledige openbare sleutel kopiëren en vervolgens in de Azure-portal of een Azure Resource Manager-sjabloon plakken wanneer u een Linux-vm maakt. U ook **openbare sleutel opslaan** selecteren om een kopie op uw computer op te slaan:

    ![PuTTY-bestand voor openbare sleutels opslaan](./media/ssh-from-windows/save-public-key.png)

7. Als u de privésleutel wilt opslaan in de privésleutelindeling (.ppk-bestand) selecteert u de **privésleutel opslaan**om de privésleutel op te slaan in de privésleutel van PuTTy . U hebt het .ppk-bestand later nodig om PuTTY te gebruiken om een SSH-verbinding met de VM te maken.

    ![PuTTY-privésleutelbestand opslaan](./media/ssh-from-windows/save-ppk-file.png)

    Als u de privésleutel wilt opslaan in de OpenSSH-indeling, de private key-indeling die door veel SSH-clients wordt gebruikt, selecteert u **De** > **openssh-sleutel conversies exporteren**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Een SSH-openbare sleutel verstrekken bij het implementeren van een VM

Als u een Linux-VM wilt maken die SSH-sleutels gebruikt voor verificatie, geeft u uw SSH-openbare sleutel op wanneer u de VM maakt met behulp van de Azure-portal of andere methoden.

In het volgende voorbeeld ziet u hoe u deze openbare sleutel in de Azure-portal kopieert en plakt wanneer u een Linux-vm maakt. De openbare sleutel wordt meestal dan opgeslagen in de map ~/.ssh/authorized_key op uw nieuwe VM.

   ![Openbare sleutel gebruiken wanneer u een vm maakt in de Azure-portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Een manier om een SSH-verbinding met uw Linux VM van Windows te maken, is door een SSH-client te gebruiken. Dit is de voorkeursmethode als u een SSH-client op uw Windows-systeem hebt geïnstalleerd of als u de SSH-hulpprogramma's in Bash in Azure Cloud Shell gebruikt. Als u de voorkeur geeft aan een GUI-gebaseerde tool, u verbinding maken met PuTTY.  

### <a name="use-an-ssh-client"></a>Een SSH-client gebruiken
Met de openbare sleutel die is geïmplementeerd op uw Azure VM en de privésleutel op uw lokale systeem, gebruikt SSH naar uw VM met het IP-adres of de DNS-naam van uw VM. *Azureuser* en *myvm.westus.cloudapp.azure.com* in de volgende opdracht vervangen door de gebruikersnaam van de beheerder en de volledig gekwalificeerde domeinnaam (of IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u een wachtwoordzin hebt geconfigureerd toen u uw sleutelpaar maakte, voert u de wachtwoordzin in wanneer u daarom wordt gevraagd tijdens het aanmeldingsproces.

Als de VM het just-in-time toegangsbeleid gebruikt, moet u toegang aanvragen voordat u verbinding maken met de VM. Zie Toegang [tot virtuele machines beheren met behulp van het just-in-time-beleid](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.

### <a name="connect-with-putty"></a>Maak verbinding met PuTTY

Als u het [PuTTY-downloadpakket hebt](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) geïnstalleerd en eerder een PuTTY-privésleutelbestand (.ppk) hebt gegenereerd, u verbinding maken met een Linux-VM met PuTTY.

1. Start PuTTy.

2. Vul de hostnaam of het IP-adres van uw VM in vanuit de Azure-portal:

    ![Nieuwe PuTTY-verbinding openen](./media/ssh-from-windows/putty-new-connection.png)

3. Selecteer de categorie **Verbinding** > **SSH** > **Auth.** Blader naar en selecteer uw PuTTY-privésleutel (.ppk-bestand):

    ![Selecteer uw PuTTY-privésleutel voor verificatie](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klik **op Openen** om verbinding te maken met uw virtuele machine.

## <a name="next-steps"></a>Volgende stappen

* Zie [Gedetailleerde stappen om SSH-sleutelparen te maken](create-ssh-keys-detailed.md)voor gedetailleerde stappen, opties en geavanceerde voorbeelden van het werken met SSH-toetsen.

* U PowerShell ook gebruiken in Azure Cloud Shell om SSH-sleutels te genereren en SSH-verbindingen met Linux-VM's te maken. Zie de [PowerShell quickstart](../../cloud-shell/quickstart-powershell.md#ssh).

* Zie [SSH-verbindingen](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)met een Azure Linux VM oplossen als u problemen hebt met het gebruik van SSH om verbinding te maken met uw Linux-VM.If you have d using SSH to connect to your Linux VM, see Troubleshoot SSH connections to an Azure Linux VM .
