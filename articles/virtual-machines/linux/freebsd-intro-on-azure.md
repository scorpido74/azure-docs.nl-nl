---
title: Inleiding tot FreeBSD op Azure
description: Meer informatie over het gebruik van Virtual FreeBSD-machines op Azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: fe64418e254289a29aafd155b92396082bff5b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945085"
---
# <a name="introduction-to-freebsd-on-azure"></a>Inleiding tot FreeBSD op Azure
In dit artikel vindt u een overzicht van het uitvoeren van een Virtual Machine van FreeBSD in Azure.

## <a name="overview"></a>Overzicht
FreeBSD voor Microsoft Azure is een geavanceerd computerbesturingssysteem dat wordt gebruikt om moderne servers, desktops en ingesloten platforms van stroom te voorzien.

Microsoft Corporation maakt afbeeldingen van FreeBSD beschikbaar op Azure met de [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) vooraf geconfigureerd. Momenteel worden de volgende FreeBSD-versies aangeboden als afbeeldingen door Microsoft:

- [FreeBSD 10.4 op de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 op de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12.0 op de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

De agent is verantwoordelijk voor de communicatie tussen de FreeBSD VM en de Azure-fabric voor bewerkingen zoals het inrichten van de VM bij het eerste gebruik (gebruikersnaam, wachtwoord of SSH-sleutel, hostnaam, enz.) en het inschakelen van functionaliteit voor selectieve VM-extensies.

Wat betreft toekomstige versies van FreeBSD, de strategie is om actueel te blijven en de nieuwste releases beschikbaar te maken kort nadat ze zijn gepubliceerd door de FreeBSD release engineering team.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Een FreeBSD-VM maken via Azure CLI op FreeBSD
Eerst moet u [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) installeren, maar de opdracht volgen op een FreeBSD-machine.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Als bash niet is geïnstalleerd op uw FreeBSD-machine, voert u de volgende opdracht uit vóór de installatie. 

```bash
sudo pkg install bash
```

Als python niet is geïnstalleerd op uw FreeBSD-machine, voert u volgende opdrachten uit vóór de installatie. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Tijdens de installatie wordt `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`u gevraagd. Als u `y` antwoord `/etc/rc.conf` `a path to an rc file to update`en voer als, `ERROR: [Errno 13] Permission denied`u het probleem te ontmoeten. Als u dit probleem wilt oplossen, moet u `etc/rc.conf`het schrijfrecht aan de huidige gebruiker tegen het bestand verlenen.

U zich nu aanmelden bij Azure en uw FreeBSD VM maken. Hieronder vindt u een voorbeeld om een FreeBSD 11.0 VM te maken. U de `--public-ip-address-dns-name` parameter ook toevoegen met een wereldwijd unieke DNS-naam voor een nieuw gemaakt openbaar IP-adres. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vervolgens u zich aanmelden bij uw FreeBSD VM via het ip-adres dat is afgedrukt in de uitvoer van bovenstaande implementatie. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-extensies voor FreeBSD
Hieronder volgen ondersteunde VM-extensies in FreeBSD.

### <a name="vmaccess"></a>VMAccess
De [VMAccess-extensie](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) kan:

* Het wachtwoord van de oorspronkelijke sudo-gebruiker opnieuw instellen.
* Maak een nieuwe sudo-gebruiker met het opgegeven wachtwoord.
* Stel de openbare hostsleutel in met de gegeven sleutel.
* De openbare hostsleutel die tijdens de VM-inrichting wordt verstrekt, opnieuw instellen als de hostsleutel niet is opgegeven.
* Open de SSH-poort (22) en herstel de sshd_config als reset_ssh is ingesteld op true.
* Verwijder de bestaande gebruiker.
* Controleer schijven.
* Een toegevoegde schijf herstellen.

### <a name="customscript"></a>CustomScript (CustomScript)
De [CustomScript-extensie](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) kan:

* Download indien aanwezig de aangepaste scripts uit Azure Storage of externe openbare opslag (bijvoorbeeld GitHub).
* Voer het itemscript uit.
* Inline-opdrachten ondersteunen.
* Converteer windows-stijl newline automatisch in shell- en Python-scripts.
* Verwijder BOM in shell- en Python-scripts automatisch.
* Bescherm gevoelige gegevens in CommandToExecute.

> [!NOTE]
> FreeBSD VM ondersteunt nu alleen CustomScript versie 1.x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Verificatie: gebruikersnamen, wachtwoorden en SSH-sleutels
Wanneer u een FreeBSD-virtuele machine maakt met behulp van de Azure-portal, moet u een gebruikersnaam, wachtwoord of SSH-openbare sleutel opgeven.
Gebruikersnamen voor het implementeren van een Virtual FreeBSD-machine op Azure mogen niet overeenkomen met de namen van systeemaccounts (UID <100) die al in de virtuele machine aanwezig zijn ('root').
Momenteel wordt alleen de RSA SSH-toets ondersteund. Een multiline SSH-toets `---- BEGIN SSH2 PUBLIC KEY ----` moet `---- END SSH2 PUBLIC KEY ----`beginnen en eindigen met .

## <a name="obtaining-superuser-privileges"></a>Superuser-rechten verkrijgen
Het gebruikersaccount dat is opgegeven tijdens de implementatie van virtuele machine-instanties op Azure, is een geprivilegieerd account. Het pakket van sudo werd geïnstalleerd in de gepubliceerde FreeBSD afbeelding.
Nadat u bent aangemeld via dit gebruikersaccount, u opdrachten als hoofd uitvoeren met behulp van de syntaxis van de opdracht.

```
$ sudo <COMMAND>
```

U optioneel een wortelshell `sudo -s`verkrijgen met behulp van.

## <a name="known-issues"></a>Bekende problemen
De [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) versie 2.2.2 heeft een bekend [probleem](https://github.com/Azure/WALinuxAgent/pull/517) dat de voorzieningsfout voor FreeBSD VM op Azure veroorzaakt. De oplossing is vastgelegd door [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) versie 2.2.3 en latere releases. 

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) om een FreeBSD VM te maken.
