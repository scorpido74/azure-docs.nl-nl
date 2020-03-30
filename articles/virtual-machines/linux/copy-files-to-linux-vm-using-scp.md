---
title: Bestanden van en naar Azure Linux VM's verplaatsen met SCP
description: Bestanden veilig verplaatsen van en naar een Linux-VM in Azure met behulp van SCP en een SSH-sleutelpaar.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a0837790b70de42073338bf085ee0f3976b866f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969608"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Bestanden van en naar een Linux-VM verplaatsen met SCP

In dit artikel ziet u hoe u bestanden verplaatst van uw werkstation naar een Azure Linux VM of van een Azure Linux VM naar uw werkstation, met behulp van Secure Copy (SCP). Het snel en veilig verplaatsen van bestanden tussen uw werkstation en een Linux-VM is essentieel voor het beheer van uw Azure-infrastructuur. 

Voor dit artikel hebt u een Linux VM nodig die in Azure is geïmplementeerd met behulp van [openbare en private sleutelbestanden van SSH.](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) U hebt ook een SCP-client nodig voor uw lokale computer. Het is gebouwd op de top van SSH en opgenomen in de standaard Bash shell van de meeste Linux en Mac computers en sommige Windows schelpen.

## <a name="quick-commands"></a>Snelle opdrachten

Een bestand kopiëren naar de Linux-VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

Een bestand van de Linux-VM kopiëren

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Gedetailleerd overzicht

Als voorbeeld verplaatsen we een Azure-configuratiebestand naar een Linux-vm en halen we een logboekbestandmap naar beneden, zowel met behulp van SCP- als SSH-sleutels.   

## <a name="ssh-key-pair-authentication"></a>SSH-sleutelpaarverificatie

SCP gebruikt SSH voor de transportlaag. SSH verwerkt de verificatie op de doelhost en verplaatst het bestand in een versleutelde tunnel die standaard wordt geleverd met SSH. Voor SSH-authenticatie kunnen gebruikersnamen en wachtwoorden worden gebruikt. SSH-verificatie met openbare en private sleutels wordt echter aanbevolen als best practice voor beveiliging. Zodra SSH de verbinding heeft geverifieerd, begint SCP het bestand te kopiëren. Met behulp van `~/.ssh/config` een goed geconfigureerde en SSH openbare en private sleutels, kan de SCP-verbinding worden vastgesteld door alleen met behulp van een servernaam (of IP-adres). Als u slechts één SSH-toets hebt, `~/.ssh/` zoekt SCP deze in de map en gebruikt deze standaard om in te loggen op de VM.

Zie [SSH-sleutels](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) `~/.ssh/config` maken voor meer informatie over het configureren van uw en SSH-openbare en privésleutels.

## <a name="scp-a-file-to-a-linux-vm"></a>SCP een bestand naar een Linux VM

Voor het eerste voorbeeld kopiëren we een Azure-configuratiebestand tot een Linux-vm die wordt gebruikt om automatisering te implementeren. Omdat dit bestand Azure API-referenties bevat, die geheimen bevatten, is beveiliging belangrijk. De versleutelde tunnel van SSH beschermt de inhoud van het bestand.

Met de volgende opdracht wordt het lokale *azure/config-bestand* overgenomen naar een Azure VM met FQDN-myserver.eastus.cloudapp.azure.com . *myserver.eastus.cloudapp.azure.com* De beheerdersgebruikersnaam op de Azure VM is *azureuser*. Het bestand is gericht op de */home/azureuser/directory.* Vervang uw eigen waarden in deze opdracht.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP een directory van een Linux VM

In dit voorbeeld kopiëren we een map met logbestanden van de Linux-VM naar uw werkstation. Een logbestand kan al dan niet gevoelige of geheime gegevens bevatten. Het gebruik van SCP zorgt er echter voor dat de inhoud van de logbestanden wordt versleuteld. Het gebruik van SCP om de bestanden over te zetten is de eenvoudigste manier om de logboekmap en bestanden naar uw werkstation te brengen terwijl u ook veilig bent.

Met de volgende opdracht worden bestanden in de map */home/azureuser/logs/logs/op* de Azure VM gekopieerd naar de lokale /tmp-map:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

De `-r` vlag instrueert SCP om de bestanden en mappen opnieuw te kopiëren vanaf het punt van de map die in de opdracht wordt vermeld.  Merk ook op dat de syntaxis `cp` van de opdracht opdracht is vergelijkbaar met een kopie opdracht.

## <a name="next-steps"></a>Volgende stappen

* [Gebruikers beheren, SSH en schijven controleren of repareren op Azure Linux VM's met behulp van de VMAccess-extensie](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
