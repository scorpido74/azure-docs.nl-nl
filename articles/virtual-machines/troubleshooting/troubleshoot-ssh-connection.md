---
title: Problemen met SSH-verbindingen oplossen met een Azure VM | Microsoft Documenten
description: Problemen zoals 'SSH-verbinding is mislukt' of 'SSH-verbinding geweigerd' oplossen voor een Azure VM met Linux.
keywords: ssh-verbinding geweigerd, ssh-fout, azure ssh, SSH-verbinding mislukt
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060668"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Het oplossen van problemen met SSH-verbindingen naar een virtuele Azure Linux-machine waarop zich fouten voordoen, die afsluit vanwege fouten of die wordt geweigerd
In dit artikel u de problemen vinden en corrigeren die optreden als gevolg van Secure Shell-fouten (SSH), ssh-verbindingsfouten of Wordt SSH geweigerd wanneer u probeert verbinding te maken met een Virtuele Linux-machine (VM). U de Azure-portal, Azure CLI of VM Access Extension voor Linux gebruiken om verbindingsproblemen op te lossen en op te lossen.


Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning krijgen**. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.

## <a name="quick-troubleshooting-steps"></a>Snelle stappen voor het oplossen van problemen
Probeer na elke probleemoplossingsstap opnieuw verbinding te maken met de virtuele machine.

1. [De SSH-configuratie opnieuw instellen](#reset-config).
2. De referenties voor de gebruiker [opnieuw instellen.](#reset-credentials)
3. Controleer of de [regels voor netwerkbeveiliging](../../virtual-network/security-overview.md) ssh-verkeer mogelijk maken.
   * Zorg ervoor dat er een [netwerkbeveiligingsgroepregel](#security-rules) bestaat om SSH-verkeer toe te staan (standaard TCP-poort 22).
   * U poortomleiding/ toewijzing niet gebruiken zonder een Azure-load balancer te gebruiken.
4. Controleer de [vm-resourcestatus](../../resource-health/resource-health-overview.md).
   * Zorg ervoor dat de VM als gezond rapporteert.
   * Als u [opstartdiagnoses hebt ingeschakeld,](boot-diagnostics.md)controleert u of de VM geen opstartfouten in de logboeken rapporteert.
5. [Start de VM opnieuw](#restart-vm).
6. [De VM opnieuw implementeren](#redeploy-vm).

Lees verder voor meer gedetailleerde stappen en uitleg over het oplossen van problemen.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Beschikbare methoden om problemen met ssh-verbindingen op te lossen
U referenties of SSH-configuratie opnieuw instellen met een van de volgende methoden:

* [Azure-portal](#use-the-azure-portal) - geweldig als u de SSH-configuratie of SSH-sleutel snel opnieuw moet instellen en u de Azure-hulpprogramma's niet hebt geïnstalleerd.
* [Azure VM Serial Console](https://aka.ms/serialconsolelinux) - de VM-seriële console werkt ongeacht de SSH-configuratie en biedt u een interactieve console voor uw vm. In feite, "kan niet SSH" situaties zijn specifiek wat de seriële console is ontworpen om te helpen oplossen. Zie hieronder voor meer informatie.
* [Azure CLI](#use-the-azure-cli) - als u zich al op de opdrachtregel bevindt, moet u de SSH-configuratie of referenties snel opnieuw instellen. Als u met een klassieke VM werkt, u de [Azure-klassieke CLI](#use-the-azure-classic-cli)gebruiken.
* [Azure VMAccessForLinux-extensie](#use-the-vmaccess-extension) - Json-definitiebestanden maken en opnieuw gebruiken om de SSH-configuratie of gebruikersreferenties opnieuw in te stellen.

Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw virtuele machine. Als u nog steeds geen verbinding maken, probeert u de volgende stap.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
De Azure-portal biedt een snelle manier om de SSH-configuratie of gebruikersreferenties opnieuw in te stellen zonder hulpprogramma's op uw lokale computer te installeren.

Selecteer om te beginnen uw VM in de Azure-portal. Blader omlaag naar de sectie **Ondersteuning + Probleemoplossing** en selecteer **Wachtwoord opnieuw instellen** zoals in het volgende voorbeeld:

![SSH-configuratie of referenties opnieuw instellen in de Azure-portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />De SSH-configuratie opnieuw instellen
Als u de SSH-configuratie opnieuw wilt instellen, selecteert u `Reset configuration only` in de sectie **Modus** zoals in de vorige schermafbeelding en selecteert u **Bijwerken**. Zodra deze actie is voltooid, probeert u opnieuw toegang te krijgen tot uw vm.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />SSH-referenties voor een gebruiker opnieuw instellen
Als u de referenties van een `Reset SSH public key` bestaande `Reset password` gebruiker opnieuw wilt instellen, selecteert u een van beide of in de sectie **Modus** zoals in de vorige schermafbeelding. Geef de gebruikersnaam en een SSH-sleutel of nieuw wachtwoord op en selecteer **Bijwerken**.

U in dit menu ook een gebruiker met sudo-bevoegdheden maken in de VM. Voer een nieuwe gebruikersnaam en bijbehorend wachtwoord of SSH-sleutel in en selecteer **Bijwerken**.

### <a name="check-security-rules"></a><a id="security-rules" />Beveiligingsregels controleren

Gebruik [IP-stroomcontrole](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) om te controleren of een regel in een netwerkbeveiligingsgroep verkeer van of naar een virtuele machine blokkeert. U ook effectieve regels voor beveiligingsgroepen controleren om ervoor te zorgen dat de NSG-regel 'Toestaan' bestaat en prioriteit krijgt voor de SSH-poort (standaard 22). Zie [Effectieve beveiligingsregels gebruiken om de vm-verkeersstroom op te lossen voor](../../virtual-network/diagnose-network-traffic-filter-problem.md)meer informatie.

### <a name="check-routing"></a>Routering controleren

Gebruik de [next-hopmogelijkheid](../../network-watcher/network-watcher-check-next-hop-portal.md) van Network Watcher om te bevestigen dat een route niet voorkomt dat verkeer wordt doorgestuurd naar of van een virtuele machine. U ook effectieve routes bekijken om alle effectieve routes voor een netwerkinterface te bekijken. Zie [Effectieve routes gebruiken om de vm-verkeersstroom op te lossen voor](../../virtual-network/diagnose-network-routing-problem.md)meer informatie.

## <a name="use-the-azure-vm-serial-console"></a>De Seriële console van Azure VM gebruiken
De [Azure VM Serial Console](./serial-console-linux.md) biedt toegang tot een tekstgebaseerde console voor virtuele Linux-machines. U de console gebruiken om problemen op te lossen in een interactieve shell. Zorg ervoor dat u aan de [vereisten voor](./serial-console-linux.md#prerequisites) het gebruik van Serial Console hebt voldaan en probeer de onderstaande opdrachten om uw SSH-connectiviteit verder op te lossen.

### <a name="check-that-ssh-is-running"></a>Controleren of SSH actief is
U de volgende opdracht gebruiken om te controleren of SSH op uw vm wordt uitgevoerd:

```console
ps -aux | grep ssh
```

Als er een uitvoer is, is SSH operationeel.

### <a name="check-which-port-ssh-is-running-on"></a>Controleren op welke poort SSH draait

U de volgende opdracht gebruiken om te controleren op welke poort SSH wordt uitgevoerd:

```console
sudo grep Port /etc/ssh/sshd_config
```

Uw uitvoer ziet er ongeveer zo uit:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken
Als u dit nog niet hebt gedaan, installeert u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) en meldt u zich aan bij een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index)

Als u een aangepaste Linux-schijfafbeelding hebt gemaakt en geüpload, controleert u of de [Microsoft Azure Linux Agent-versie](../extensions/agent-linux.md) 2.0.5 of hoger is geïnstalleerd. Voor VM's die zijn gemaakt met galerijafbeeldingen, is deze toegangsextensie al voor u geïnstalleerd en geconfigureerd.

### <a name="reset-ssh-configuration"></a>SSH-configuratie opnieuw instellen
U in eerste instantie proberen de SSH-configuratie opnieuw in te stellen op standaardwaarden en de SSH-server op de VM opnieuw op te starten. Dit verandert niets aan de naam, het wachtwoord of de SSH-sleutels van het gebruikersaccount.
In het volgende voorbeeld wordt gebruik gemaakte [az vm-user reset-ssh](/cli/azure/vm/user) om de SSH-configuratie op de vm met de naam `myVM` in `myResourceGroup`. Gebruik je eigen waarden als volgt:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
In het volgende voorbeeld wordt gebruik gebruikt [az vm-gebruikersupdate](/cli/azure/vm/user) om de referenties voor `myUsername` de waarde die is opgegeven in `myPassword`de VM met de naam `myVM` in `myResourceGroup`. Gebruik je eigen waarden als volgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Als u SSH-sleutelverificatie gebruikt, u de SSH-sleutel voor een bepaalde gebruiker opnieuw instellen. In het volgende voorbeeld wordt gebruik gemaakte **az vm access set-linux-user** om de SSH-sleutel `~/.ssh/id_rsa.pub` bij te werken die is opgeslagen voor de gebruiker met de `myUsername`naam , op de vm met de naam `myVM` in `myResourceGroup`. Gebruik je eigen waarden als volgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>De VMAccess-extensie gebruiken
De VM Access Extension voor Linux leest in een json-bestand dat acties definieert die moeten worden uitgevoerd. Deze acties omvatten het opnieuw instellen van SSHD, het opnieuw instellen van een SSH-toets of het toevoegen van een gebruiker. U gebruikt de Azure CLI nog steeds om de VMAccess-extensie aan te roepen, maar u de json-bestanden desgewenst opnieuw gebruiken in meerdere VM's. Met deze aanpak u een opslagplaats van json-bestanden maken die vervolgens kunnen worden opgeroepen voor bepaalde scenario's.

### <a name="reset-sshd"></a>SSHD opnieuw instellen
Maak een `settings.json` bestand met de volgende inhoud:

```json
{
    "reset_ssh":"True"
}
```

Met de Azure CLI belt `VMAccessForLinux` u vervolgens de extensie om uw SSHD-verbinding opnieuw in te stellen door uw json-bestand op te geven. In het volgende voorbeeld wordt az [vm-extensie](/cli/azure/vm/extension) ingesteld `myVM` `myResourceGroup`om SSHD opnieuw in te stellen op de VM met de naam in . Gebruik je eigen waarden als volgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
Als SSHD correct lijkt te functioneren, u de referenties voor een gebruikersgebruiker opnieuw instellen. Als u het wachtwoord voor een `settings.json`gebruiker opnieuw wilt instellen, maakt u een bestand met de naam . In het volgende voorbeeld worden `myUsername` de referenties `myPassword`voor de waarde die is opgegeven in . Voer de volgende `settings.json` regels in uw bestand in met uw eigen waarden:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Of om de SSH-toets opnieuw in te `settings.json`stellen voor een gebruiker, maakt u eerst een bestand met de naam . In het volgende voorbeeld worden `myUsername` de referenties `myPassword`voor de waarde `myVM` `myResourceGroup`die is opgegeven op de vm in . Voer de volgende `settings.json` regels in uw bestand in met uw eigen waarden:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Nadat u uw json-bestand hebt gemaakt, gebruikt u de Azure CLI om de `VMAccessForLinux` extensie aan te roepen om uw SSH-gebruikersreferenties opnieuw in te stellen door uw json-bestand op te geven. In het volgende voorbeeld worden referenties `myVM` `myResourceGroup`opnieuw ingesteld op de vm met de naam in . Gebruik je eigen waarden als volgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>De Azure-klassieker CLI gebruiken
Als u dit nog niet hebt gedaan, [installeert u de klassieke CLI van Azure en maakt u verbinding met uw Azure-abonnement.](../../cli-install-nodejs.md) Zorg ervoor dat u de resourcebeheermodus als volgt gebruikt:

```azurecli
azure config mode arm
```

Als u een aangepaste Linux-schijfafbeelding hebt gemaakt en geüpload, controleert u of de [Microsoft Azure Linux Agent-versie](../extensions/agent-linux.md) 2.0.5 of hoger is geïnstalleerd. Voor VM's die zijn gemaakt met galerijafbeeldingen, is deze toegangsextensie al voor u geïnstalleerd en geconfigureerd.

### <a name="reset-ssh-configuration"></a>SSH-configuratie opnieuw instellen
De SSHD-configuratie zelf kan verkeerd zijn geconfigureerd of er is een fout opgetreden bij de service. U SSHD opnieuw instellen om ervoor te zorgen dat de SSH-configuratie zelf geldig is. Het opnieuw instellen van SSHD moet de eerste stap zijn voor het oplossen van problemen die u neemt.

In het volgende voorbeeld wordt SSHD opnieuw ingesteld op een vm met de naam `myVM` in de resourcegroep met de naam `myResourceGroup`. Gebruik als volgt uw eigen namen van vm- en resourcegroepen:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
Als SSHD correct lijkt te functioneren, u het wachtwoord voor een gebruikersgebruiker opnieuw instellen. In het volgende voorbeeld worden `myUsername` de referenties `myPassword`voor de waarde `myVM` `myResourceGroup`die is opgegeven op de vm in . Gebruik je eigen waarden als volgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Als u SSH-sleutelverificatie gebruikt, u de SSH-sleutel voor een bepaalde gebruiker opnieuw instellen. In het volgende voorbeeld wordt `~/.ssh/id_rsa.pub` de SSH-sleutel bijgewerkt die is opgeslagen voor de gebruiker met de naam `myUsername`, op de vm die is genoemd `myVM` in `myResourceGroup`. Gebruik je eigen waarden als volgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Een virtuele machine opnieuw opstarten
Als u de SSH-configuratie en gebruikersreferenties opnieuw hebt ingesteld of als u een fout hebt ondervonden, u proberen de VM opnieuw te starten om onderliggende rekenproblemen op te lossen.

### <a name="azure-portal"></a>Azure Portal
Als u een VM opnieuw wilt starten met de Azure-portal, selecteert u uw VM en selecteert u **Opnieuw starten** als in het volgende voorbeeld:

![Een VM opnieuw starten in de Azure-portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure-CLI
In het volgende voorbeeld wordt de `myVM` vm opnieuw gestart `myResourceGroup`met [az vm](/cli/azure/vm) om de vm met de naam in de resourcegroep met de naam . Gebruik je eigen waarden als volgt:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

In het volgende voorbeeld `myVM` wordt de vm `myResourceGroup`opnieuw gestart met de naam in de resourcegroep met de naam . Gebruik je eigen waarden als volgt:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Een VM opnieuw implementeren
U een VM opnieuw implementeren naar een ander knooppunt binnen Azure, waardoor onderliggende netwerkproblemen kunnen worden verholpen. Zie Virtuele machine opnieuw implementeren [naar een nieuw Azure-knooppunt voor](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)informatie over het opnieuw implementeren van een virtuele machine.

> [!NOTE]
> Nadat deze bewerking is voltooid, gaan tijdelijke schijfgegevens verloren en worden dynamische IP-adressen die aan de virtuele machine zijn gekoppeld, bijgewerkt.
>
>

### <a name="azure-portal"></a>Azure Portal
Als u een vm opnieuw wilt implementeren met de Azure-portal, selecteert u uw VM en schuift u omlaag naar de sectie **Ondersteuning + Probleemoplossing.** Selecteer **Opnieuw implementeren** als in het volgende voorbeeld:

![Een VM opnieuw implementeren in de Azure-portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure-CLI
In het volgende voorbeeld wordt [az vm opnieuw geïmplementeerd](/cli/azure/vm) om de VM met de naam `myVM` in de resourcegroep met de naam `myResourceGroup`. Gebruik je eigen waarden als volgt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

In het volgende voorbeeld wordt `myVM` de vm `myResourceGroup`opnieuw geïmplementeerd met de naam in de resourcegroep met de naam . Gebruik je eigen waarden als volgt:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VM's die zijn gemaakt met het implementatiemodel Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Probeer deze stappen om de meest voorkomende SSH-verbindingsfouten voor VM's op te lossen die zijn gemaakt met behulp van het klassieke implementatiemodel. Probeer na elke stap opnieuw verbinding te maken met de virtuele machine.

* Externe toegang opnieuw instellen vanuit de [Azure-portal](https://portal.azure.com). Selecteer op de Azure-portal uw VM en selecteer **Extern opnieuw instellen...**.
* Start de VM opnieuw. Selecteer uw VM in de [Azure-portal](https://portal.azure.com)en selecteer **Opnieuw starten**.

* Implementeer de VM opnieuw naar een nieuw Azure-knooppunt. Zie [Virtuele machine opnieuw implementeren naar een nieuw Azure-knooppunt voor](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)informatie over het opnieuw implementeren van een virtuele machine.

    Nadat deze bewerking is voltooid, gaan tijdelijke schijfgegevens verloren en worden dynamische IP-adressen die aan de virtuele machine zijn gekoppeld, bijgewerkt.
* Volg de instructies in [Hoe u een wachtwoord of SSH voor virtuele Linux-machines opnieuw instelt](../linux/classic/reset-access-classic.md) om:

  * Stel het wachtwoord of de SSH-sleutel opnieuw in.
  * Maak een *sudo-gebruikersaccount* aan.
  * De SSH-configuratie opnieuw instellen.
* Controleer de resourcestatus van de VM op platformproblemen.<br>
     Selecteer uw VM en scrol naar beneden **Instellingen** > **Controleer De status**.

## <a name="additional-resources"></a>Aanvullende bronnen
* Als u nog steeds niet in staat bent om uw VM naar uw VM te leiden nadat u de [nastappen](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hebt gevolgd, raadpleegt u meer gedetailleerde stappen voor het oplossen van problemen om aanvullende stappen te controleren om het probleem op te lossen.
* Zie Toegang [tot een toepassing op een virtuele azure-machine oplossen voor](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) meer informatie over het oplossen van problemen met toepassingen
* Zie [Een wachtwoord of SSH voor virtuele machines op Linux opnieuw instellen voor](../linux/classic/reset-access-classic.md)meer informatie over het oplossen van problemen met virtuele machines.
