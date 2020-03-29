---
title: Gedetailleerde stappen om een SSH-sleutelpaar te maken
description: Meer informatie over gedetailleerde stappen voor het maken en beheren van een SSH-openbaar en privésleutelpaar voor Linux-VM's in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969540"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Gedetailleerde stappen: SSH-sleutels maken en beheren voor verificatie naar een Linux-vm in Azure 
Met een secure shell (SSH) sleutelpaar u een Virtuele Linux-machine op Azure maken die standaard SSH-sleutels gebruikt voor verificatie, waardoor wachtwoorden niet meer hoeven te worden aangemeld. Vm's die zijn gemaakt met de Azure-portal, Azure CLI, Resource Manager-sjablonen of andere hulpprogramma's kunnen uw SSH-openbare sleutel bevatten als onderdeel van de implementatie, waarmee SSH-sleutelverificatie voor SSH-verbindingen wordt ingesteld. 

In dit artikel vindt u gedetailleerde achtergrondinformatie en stappen om een SSH RSA-sleutelbestandpaar voor SSH-clientverbindingen te maken en te beheren. Als u snelle opdrachten wilt, raadpleegt [u Hoe u een SSH-sleutelpaar voor privésleutels voor Linux-VM's in Azure wilt maken.](mac-create-ssh-keys.md)

Zie [SSH-sleutels gebruiken met Windows op Azure](ssh-from-windows.md)voor aanvullende manieren om SSH-sleutels op een Windows-computer te genereren en te gebruiken.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Wachtwoordzin persoonlijke sleutel
De SSH private key moet een zeer veilige wachtwoordzin hebben om deze te beschermen. Deze wachtwoordzin is alleen maar om toegang te krijgen tot de prive-SSH key bestand en *is niet* het wachtwoord van het gebruikersaccount. Wanneer u een wachtwoordzin aan de SSH-sleutel toevoegt, wordt de persoonlijke sleutel versleuteld met behulp van 128-bits AES, zodat deze niet kan worden gebruikt zonder de wachtwoordzin om de persoonlijke sleutel te ontsleutelen. Als een aanvaller uw privésleutel heeft gestolen en die sleutel geen wachtwoordzin had, kunnen ze die privésleutel gebruiken om zich aan te melden bij servers die de bijbehorende openbare sleutel hebben. Als een privésleutel wordt beschermd door een wachtwoordzin, kan deze niet door die aanvaller worden gebruikt, waardoor een extra beveiligingslaag voor uw infrastructuur op Azure wordt geboden.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Gebruik en voordelen van SSH-sleutels

Wanneer u een Azure VM maakt door de openbare sleutel op `.pub` te geven, kopieert Azure de openbare sleutel (in de indeling) naar de `~/.ssh/authorized_keys` map op de vm. SSH-sleutels `~/.ssh/authorized_keys` worden gebruikt om de client uit te dagen om de bijbehorende privésleutel op een SSH-verbinding aan te passen. In een Azure Linux VM die SSH-sleutels gebruikt voor verificatie, configureert Azure de SSHD-server om geen aanmelding met wachtwoorden toe te staan, alleen SSH-sleutels. Door een Azure Linux VM met SSH-sleutels te maken, u daarom helpen bij het beveiligen `sshd_config` van de VM-implementatie en uzelf de typische configuratiestap na implementatie opslaan van het uitschakelen van wachtwoorden in het bestand.

Als u geen SSH-sleutels wilt gebruiken, u uw Linux-VM instellen om wachtwoordverificatie te gebruiken. Als uw vm niet wordt blootgesteld aan internet, kan het gebruik van wachtwoorden voldoende zijn. U moet echter nog steeds uw wachtwoorden voor elke Linux-VM beheren en een gezond wachtwoordbeleid en -praktijken bijhouden, zoals minimale wachtwoordlengte en regelmatige updates. Het gebruik van SSH-sleutels vermindert de complexiteit van het beheren van individuele referenties in meerdere VM's.

## <a name="generate-keys-with-ssh-keygen"></a>Sleutels genereren met ssh-keygen

Om de sleutels te maken, is `ssh-keygen`een voorkeursopdracht , die beschikbaar is met OpenSSH-hulpprogramma's in de Azure Cloud Shell, een macOS- of Linux-host, het [Windows-subsysteem voor Linux](https://docs.microsoft.com/windows/wsl/about)en andere hulpprogramma's. `ssh-keygen`stelt een reeks vragen en schrijft vervolgens een privésleutel en een bijpassende openbare sleutel. 

SSH-sleutels worden standaard opgeslagen in de `~/.ssh`-directory.  Als u niet beschikt over de map `~/.ssh`, wordt deze door de opdracht `ssh-keygen` voor u gemaakt met de juiste machtigingen.

### <a name="basic-example"></a>Basisvoorbeeld

Met `ssh-keygen` de volgende opdracht genereert u standaard 2048-bits SSH `~/.ssh` RSA-bestanden met openbare en privésleutelbestanden in de map. Als er een SSH-sleutelpaar bestaat op de huidige locatie, worden deze bestanden overschreven.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Gedetailleerd voorbeeld
In het volgende voorbeeld worden extra opdrachtopties weergegeven om een SSH RSA-sleutelpaar te maken. Als er een SSH-sleutelpaar bestaat op de huidige locatie, worden deze bestanden overschreven. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Uitleg van de opdracht**

`ssh-keygen` = het programma dat wordt gebruikt voor het maken van de sleutels

`-m PEM`= de sleutel opmaken als PEM

`-t rsa`= type sleutel om te maken, in dit geval in rsa-formaat

`-b 4096`= het aantal bits in de sleutel, in dit geval 4096

`-C "azureuser@myserver"` = een opmerking die wordt toegevoegd aan het einde van het openbare sleutelbestand om het gemakkelijk te identificeren. Normaal gesproken wordt een e-mailadres gebruikt als commentaar, maar gebruik wat het beste werkt voor uw infrastructuur.

`-f ~/.ssh/mykeys/myprivatekey`= de bestandsnaam van het privésleutelbestand, als u ervoor kiest de standaardnaam niet te gebruiken. Een bijbehorend bestand met `.pub` openbare sleutels dat wordt toegevoegd in dezelfde map. De map moet bestaan.

`-N mypassphrase`= een extra wachtwoordzin die wordt gebruikt om toegang te krijgen tot het privésleutelbestand. 

### <a name="example-of-ssh-keygen"></a>Voorbeeld van ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Opgeslagen sleutelbestanden

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

De naam van het sleutelpaar voor dit artikel. Het hebben van `id_rsa` een sleutelpaar met de naam is de standaard; sommige tools kunnen `id_rsa` verwachten dat de prive-sleutel bestandsnaam, dus het hebben van een is een goed idee. De map `~/.ssh/` is de standaardlocatie voor SSH-sleutelparen en het SSH-configuratiebestand. Als `ssh-keygen` niet wordt opgegeven met een volledig pad, worden de sleutels in de huidige werkmap gemaakt in plaats van in de standaardmap `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lijst van `~/.ssh` de map

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Trefwoord

`Enter passphrase (empty for no passphrase):`

Het wordt *ten zeerste* aanbevolen om een wachtwoordzin toe te voegen aan uw privésleutel. Zonder een wachtwoordzin om het sleutelbestand te beschermen, kan iedereen met het bestand het gebruiken om zich aan te melden bij elke server die de bijbehorende openbare sleutel heeft. Het toevoegen van een wachtwoordzin biedt meer bescherming voor het geval iemand in staat is om toegang te krijgen tot uw prive-sleutel bestand, waardoor u tijd om de sleutels te veranderen.

## <a name="generate-keys-automatically-during-deployment"></a>Sleutels automatisch genereren tijdens implementatie

Als u de [Azure CLI](/cli/azure) gebruikt om uw VM te maken, u optioneel openbare en private sleutelbestanden van SSH genereren door de opdracht [AZ-vm-maken](/cli/azure/vm) met de `--generate-ssh-keys` optie uit te voeren. De sleutels worden opgeslagen in de ~/.ssh directory. Houd er rekening mee dat deze opdrachtoptie geen sleutels overschrijft als ze al op die locatie bestaan.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>SSH-openbare sleutel leveren bij het implementeren van een VM

Als u een Linux-vm wilt maken die SSH-sleutels voor verificatie gebruikt, geeft u uw SSH-openbare sleutel op wanneer u de VM maakt met behulp van de Azure-portal, CLI- en Resource Manager-sjablonen of andere methoden. Wanneer u het portaal gebruikt, voert u de openbare sleutel zelf in. Als u de [Azure CLI](/cli/azure) gebruikt om uw VM te maken met een bestaande openbare sleutel, geeft u de waarde of locatie van deze openbare sleutel op door de opdracht AZ VM Create met de `--ssh-key-value` optie [uit te](/cli/azure/vm) voeren. 

Als u niet bekend bent met het formaat van een openbare SSH-sleutel, u uw openbare sleutel als volgt bekijken `cat` en vervangen `~/.ssh/id_rsa.pub` door uw eigen openbare sleutelbestandslocatie:

```bash
cat ~/.ssh/id_rsa.pub
```

Uitvoer is vergelijkbaar met de volgende (hier geredigeerd):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Als u de inhoud van het bestand met openbare sleutels kopieert en plakt in de Azure-portal of een Resource Manager-sjabloon, controleert u of u geen extra witruimte kopieert of extra regeleinden introduceert. Als u bijvoorbeeld macOS gebruikt, u het bestand `~/.ssh/id_rsa.pub`met openbare sleutels (standaard) naar **pbcopy** leiden om de inhoud `xclip`te kopiëren (er zijn andere Linux-programma's die hetzelfde doen, zoals ).

Als u liever een openbare sleutel gebruikt die in een multiline-indeling is, u een RFC4716-toets genereren in een pemcontainer van de openbare sleutel die u eerder hebt gemaakt.

Een RFC4716-sleutel maken van een bestaande openbare SSH-sleutel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH naar uw VM met een SSH-client
Met de openbare sleutel die is geïmplementeerd op uw Azure VM en de privésleutel op uw lokale systeem, gebruikt SSH naar uw VM met het IP-adres of de DNS-naam van uw VM. *Azureuser* en *myvm.westus.cloudapp.azure.com* in de volgende opdracht vervangen door de gebruikersnaam van de beheerder en de volledig gekwalificeerde domeinnaam (of IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u een wachtwoordzin hebt opgegeven toen u uw sleutelpaar maakte, voert u de wachtwoordzin in wanneer u daarom wordt gevraagd tijdens het aanmeldingsproces. (De server is toegevoegd aan uw map `~/.ssh/known_hosts` en u wordt pas weer gevraagd om verbinding te maken nadat de openbare sleutel op uw virtuele Azure-machine is gewijzigd of de naam van de server is verwijderd uit `~/.ssh/known_hosts`.)

Als de VM het just-in-time toegangsbeleid gebruikt, moet u toegang aanvragen voordat u verbinding maken met de VM. Zie Toegang [tot virtuele machines beheren met behulp van het just-in-time-beleid](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Gebruik ssh-agent om uw privésleutel wachtwoordzin op te slaan

Om te voorkomen dat uw wachtwoordzin voor privésleutels bij `ssh-agent` elke SSH-aanmelding wordt getypt, u de wachtwoordzin van uw privésleutelbestand in de cache opslaan. Als u een Mac gebruikt, slaat de macOS-sleutelhanger de wachtwoordzin voor privésleutels veilig op wanneer u een beroep doet. `ssh-agent`

Controleer en `ssh-agent` `ssh-add` gebruik en om het SSH-systeem te informeren over de belangrijkste bestanden, zodat u de wachtwoordzin niet interactief hoeft te gebruiken.

```bash
eval "$(ssh-agent -s)"
```

Voeg nu de persoonlijke sleutel toe aan `ssh-agent` met de opdracht `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

De privésleutelwachtwoordzin wordt `ssh-agent`nu opgeslagen in .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Ssh-copy-id gebruiken om de sleutel naar een bestaande virtuele machine te kopiëren
Als u al een VM hebt gemaakt, u de nieuwe Openbare SSH-sleutel voor uw Linux-vm installeren met een opdracht die vergelijkbaar is met de volgende opdracht:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Een SSH-configuratiebestand maken en configureren

U een SSH-config-bestand ()`~/.ssh/config`maken en configureren om aanmeldingen te versnellen en uw SSH-clientgedrag te optimaliseren. 

In het volgende voorbeeld ziet u een eenvoudige configuratie die u gebruiken om u als gebruiker snel aan te melden bij een specifieke virtuele machine met behulp van de standaard-ssh-privésleutel. 

### <a name="create-the-file"></a>Het bestand maken

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Het bestand bewerken om de nieuwe SSH-configuratie toe te voegen

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Voorbeeldconfiguratie

Voeg configuratie-instellingen toe die geschikt zijn voor uw host-vm.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

U configuraties toevoegen voor extra hosts om elk van deze hosts in staat te stellen zijn eigen speciale sleutelpaar te gebruiken. Zie [SSH-config-bestand](https://www.ssh.com/ssh/config/) voor meer geavanceerde configuratieopties.

Nu u een SSH-sleutelpaar en een geconfigureerd SSH-config-bestand hebt, u zich snel en veilig aanmelden bij uw Linux-VM. Wanneer u de volgende opdracht uitvoert, zoekt en `Host myvm` laadt SSH alle instellingen uit het blok in het SSH-config-bestand.

```bash
ssh myvm
```

De eerste keer dat u zich aanmeldt bij een server met een SSH-toets, vraagt de opdracht u om de wachtwoordzin voor dat sleutelbestand.

## <a name="next-steps"></a>Volgende stappen

De volgende stap bestaat uit het maken van virtuele Linux-machines in Azure met de nieuwe openbare SSH-sleutel. Azure VM's die zijn gemaakt met een Openbare SSH-sleutel omdat de aanmelding beter is beveiligd dan VM's die zijn gemaakt met de standaardaanmeldingsmethode, wachtwoorden.

* [Een virtuele Linux-machine maken met Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een Virtuele Linux-machine maken met de Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een virtuele Linux-machine maken met een Azure-sjabloon](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
