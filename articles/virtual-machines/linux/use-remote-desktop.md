---
title: Extern bureaublad gebruiken voor een Linux-vm in Azure
description: Meer informatie over het installeren en configureren van Extern bureaublad (xrdp) om verbinding te maken met een Linux-vm in Azure met behulp van grafische hulpprogramma's
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 2b1b708618c60153b8dbce69b26d832fa18b25aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476600"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Extern bureaublad installeren en configureren om verbinding te maken met een Linux-vm in Azure
Linux virtuele machines (VM's) in Azure worden meestal beheerd vanaf de opdrachtregel met behulp van een Secure Shell (SSH) verbinding. Wanneer het gebruik van extern bureaublad eenvoudiger is voor Linux of voor scenario's voor snelle probleemoplossing, is het gebruik van extern bureaublad eenvoudiger. In dit artikel wordt beschreven hoe u een desktopomgeving[(xfce)](https://www.xfce.org)en extern bureaublad[(xrdp)](https://www.xrdp.org)voor uw Linux-VM installeert en configureert met behulp van het implementatiemodel Resource Manager.


## <a name="prerequisites"></a>Vereisten
Dit artikel vereist een bestaande Ubuntu 18.04 LTS VM in Azure. Als u een vm wilt maken, gebruikt u een van de volgende methoden:

- De [Azure CLI](quick-create-cli.md)
- De [Azure-portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Een desktopomgeving installeren op uw Linux-vm
De meeste Linux VM's in Azure hebben niet standaard een desktopomgeving geïnstalleerd. Linux VM's worden vaak beheerd met behulp van SSH-verbindingen in plaats van een desktop-omgeving. Er zijn verschillende desktop omgevingen in Linux die u kiezen. Afhankelijk van de desktopomgeving kan het 5 tot 10 minuten duren voordat alle vereiste pakketten zijn geïnstalleerd en geconfigureerd.

In het volgende voorbeeld wordt de lichtgewicht [xfce4-desktopomgeving](https://www.xfce.org/) geïnstalleerd op een Ubuntu 18.04 LTS VM. Opdrachten voor andere distributies variëren `yum` enigszins (gebruik om te installeren `selinux` op Red `zypper` Hat Enterprise Linux en de juiste regels te configureren, of te gebruiken om te installeren op SUSE, bijvoorbeeld).

Eerst SSH naar uw VM. In het volgende voorbeeld wordt verbinding gemaakt met de VM met de naam *myvm.westus.cloudapp.azure.com* met de gebruikersnaam van *azureuser*. Gebruik je eigen waarden:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u Windows gebruikt en meer informatie nodig hebt over het gebruik van SSH, raadpleegt u [SSH-sleutels gebruiken met Windows.](ssh-from-windows.md)

Installeer vervolgens xfce `apt` met als volgt:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Een externe desktopserver installeren en configureren
Nu u een bureaubladomgeving hebt geïnstalleerd, configureert u een externe desktopservice om te luisteren naar binnenkomende verbindingen. [xrdp](http://xrdp.org) is een open source Remote Desktop Protocol (RDP) server die beschikbaar is op de meeste Linux-distributies en goed werkt met xfce. Installeer xrdp op uw Ubuntu VM als volgt:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Vertel xrdp welke desktopomgeving u moet gebruiken wanneer u uw sessie start. Configureer xrdp om xfce als bureaubladomgeving als volgt te gebruiken:

```bash
echo xfce4-session >~/.xsession
```

Start de xrdp-service opnieuw op om de wijzigingen als volgt in werking te laten treden:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Een lokaal gebruikersaccountwachtwoord instellen
Als u een wachtwoord voor uw gebruikersaccount hebt gemaakt toen u uw vm maakte, slaat u deze stap over. Als u alleen SSH-sleutelverificatie gebruikt en geen lokaal accountwachtwoord hebt ingesteld, geeft u een wachtwoord op voordat u xrdp gebruikt om u aan te melden bij uw VM. xrdp kan ssh-sleutels niet accepteren voor verificatie. In het volgende voorbeeld wordt een wachtwoord opgegeven voor *azureuser*van het gebruikersaccount:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Als u een wachtwoord opgeeft, wordt uw SSHD-configuratie niet bijgewerkt om wachtwoordaanmeldingen mogelijk te maken als dit momenteel niet het geval is. Vanuit beveiligingsperspectief u verbinding maken met uw VM met een SSH-tunnel met behulp van sleutelverificatie en vervolgens verbinding maken met xrdp. Als dat het zo is, slaat u de volgende stap over bij het maken van een regel voor netwerkbeveiliging om extern bureaublad-verkeer toe te staan.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Een regel voor netwerkbeveiligingsgroep maken voor Extern bureaublad-verkeer
Om Extern bureaublad-verkeer uw Linux-VM te laten bereiken, moet een code voor netwerkbeveiliging worden gemaakt waarmee TCP op poort 3389 uw VM kan bereiken. Zie [Wat is een netwerkbeveiligingsgroep voor](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) meer informatie over regels voor netwerkbeveiliging? U [de Azure-portal ook gebruiken om een regel voor netwerkbeveiligingsgroepen te maken.](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

In het volgende voorbeeld wordt een regel voor netwerkbeveiligingen met [de az vm-openpoort](/cli/azure/vm#az-vm-open-port) op poort *3389*. Open de volgende regel voor netwerkbeveiligingsgroepen, van de Azure CLI en niet van de SSH-sessie tot uw VM:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Uw Linux-vm verbinden met een Extern bureaublad-client
Open uw lokale externe desktopclient en maak verbinding met het IP-adres of de DNS-naam van uw Linux-vm. Voer als volgt de gebruikersnaam en het wachtwoord voor het gebruikersaccount op uw VM in:

![Verbinding maken met xrdp met uw Extern bureaublad-client](./media/use-remote-desktop/remote-desktop-client.png)

Na het verifiëren wordt de xfce-bureaubladomgeving geladen en lijkt het op het volgende voorbeeld:

![xfce-desktopomgeving via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Als uw lokale RDP-client verificatie op netwerkniveau (NLA) gebruikt, moet u mogelijk de verbindingsinstelling uitschakelen. XRDP ondersteunt momenteel geen NLA. U ook kijken naar alternatieve RDP-oplossingen die NLA ondersteunen, zoals [FreeRDP.](https://www.freerdp.com)


## <a name="troubleshoot"></a>Problemen oplossen
Als u geen verbinding maken met uw `netstat` Linux-VM via een Extern bureaublad-client, gebruikt u op uw Linux-vm om te controleren of uw VM als volgt naar RDP-verbindingen luistert:

```bash
sudo netstat -plnt | grep rdp
```

In het volgende voorbeeld wordt het VM-luisteren op TCP-poort 3389 weergegeven zoals verwacht:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Als de *xrdp-sesman-service* niet luistert, start u op een Ubuntu VM de service als volgt opnieuw op:

```bash
sudo service xrdp restart
```

Bekijk de *log-in/var/log* op uw Ubuntu VM op voor aanwijzingen waarom de service mogelijk niet reageert. U de syslog ook controleren tijdens een poging tot verbinding met extern bureaublad om eventuele fouten te bekijken:

```bash
tail -f /var/log/syslog
```

Andere Linux-distributies zoals Red Hat Enterprise Linux en SUSE kunnen verschillende manieren hebben om services opnieuw op te starten en alternatieve logbestandslocaties te bekijken.

Als u geen antwoord ontvangt in uw externe desktopclient en geen gebeurtenissen in het systeemlogboek ziet, geeft dit gedrag aan dat extern desktopverkeer de VM niet kan bereiken. Controleer de regels van uw netwerkbeveiligingsgroep om ervoor te zorgen dat u een regel hebt om TCP toe te staan op poort 3389. Zie [Problemen met de toepassingsconnectiviteit oplossen](../windows/troubleshoot-app-connection.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
Zie [SSH-sleutels voor Linux-vm's maken in Azure voor](mac-create-ssh-keys.md)meer informatie over het maken en gebruiken van SSH-sleutels met Linux VM's.

Zie [SSH-sleutels gebruiken met Windows](ssh-from-windows.md)voor informatie over het gebruik van SSH-toetsen.

