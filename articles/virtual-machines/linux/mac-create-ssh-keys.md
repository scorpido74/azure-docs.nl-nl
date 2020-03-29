---
title: Een SSH-sleutelpaar maken en gebruiken voor Linux VM's in Azure
description: Een SSH-sleutelpaar voor Linux-vm's in Azure maken en gebruiken om de beveiliging van het verificatieproces te verbeteren.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969044"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snelle stappen: maak en gebruik een SSH-sleutelpaar voor Linux-vm's in Azure

Met een secure shell (SSH) sleutelpaar u virtuele machines (VM's) maken in Azure die SSH-sleutels gebruiken voor verificatie, waardoor wachtwoorden niet meer hoeven te worden aangemeld. In dit artikel ziet u hoe u snel een SSH-sleutelbestandspaar voor Linux-vm's genereren en gebruiken. U deze stappen uitvoeren met de Azure Cloud Shell, een macOS- of Linux-host, het Windows-subsysteem voor Linux en andere hulpprogramma's die OpenSSH ondersteunen. 

> [!NOTE]
> VM's gemaakt met behulp van SSH-sleutels zijn standaard geconfigureerd met wachtwoorden uitgeschakeld, die sterk verhoogt de moeilijkheid van brute-force guessing aanvallen. 

Zie Gedetailleerde stappen om [SSH-sleutelparen te maken voor](create-ssh-keys-detailed.md)meer achtergrondinformatie en voorbeelden.

Zie [SSH-sleutels gebruiken met Windows op Azure](ssh-from-windows.md)voor aanvullende manieren om SSH-sleutels op een Windows-computer te genereren en te gebruiken.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Gebruik `ssh-keygen` de opdracht om ssh-bestanden voor openbare en privésleutels te genereren. Standaard worden deze bestanden gemaakt in de map ~/.ssh. U een andere locatie opgeven en een optioneel wachtwoord *(wachtwoordzin)* om toegang te krijgen tot het privésleutelbestand. Als er op de opgegeven locatie een SSH-sleutelpaar met dezelfde naam bestaat, worden deze bestanden overschreven.

Met de volgende opdracht wordt een SSH-sleutelpaar gemaakt met RSA-versleuteling en een beetje lengte van 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Als u de [Azure CLI](/cli/azure) gebruikt om uw VM te maken met de opdracht AZ VM `--generate-ssh-keys` [Maken,](/cli/azure/vm#az-vm-create) u optioneel SSH-bestanden met openbare en private sleutelbestanden genereren met de optie. De belangrijkste bestanden worden opgeslagen in de ~/.ssh-map, tenzij anders aangegeven met de `--ssh-dest-key-path` optie. De `--generate-ssh-keys` optie overschrijft bestaande sleutelbestanden niet, maar stuurt een fout niet terug. Vervang *vmname* en *RGname* in de volgende opdracht door uw eigen waarden:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Een SSH-openbare sleutel verstrekken bij het implementeren van een VM

Als u een Linux-vm wilt maken die SSH-sleutels voor verificatie gebruikt, geeft u uw SSH-openbare sleutel op wanneer u de VM maakt met de Azure-portal, Azure CLI, Azure Resource Manager-sjablonen of andere methoden:

* [Een virtuele Linux-machine maken met Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een Virtuele Linux-machine maken met de Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een virtuele Linux-machine maken met een Azure-sjabloon](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Als u niet bekend bent met het formaat van een openbare SSH-sleutel, u uw openbare sleutel weergeven met de volgende `cat` opdracht, indien nodig vervangen `~/.ssh/id_rsa.pub` door het pad en de bestandsnaam van uw eigen openbare sleutelbestand:

```bash
cat ~/.ssh/id_rsa.pub
```

Een typische waarde van de openbare sleutel ziet er als volgt uit:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Als u de inhoud van het openbare sleutelbestand kopieert en plakt dat u wilt gebruiken in de Azure-portal of een Resource Manager-sjabloon, controleert u of u geen slepende witruimte kopieert. Als u een openbare sleutel in macOS wilt `pbcopy`kopiëren, u het bestand met openbare sleutels naar. Op dezelfde manier u in Linux het `xclip`openbare sleutelbestand doorsluisen naar programma's zoals.

De openbare sleutel die u op uw Linux-vm in Azure plaatst, wordt standaard opgeslagen in ~/.ssh/id_rsa.pub, tenzij u een andere locatie hebt opgegeven wanneer u het sleutelpaar hebt gemaakt. Als u de [Azure CLI 2.0](/cli/azure) wilt gebruiken om uw VM te maken met een bestaande openbare sleutel, `--ssh-key-values` geeft u de waarde en eventueel de locatie van deze openbare sleutel op met behulp van de opdracht AZ VM [Create](/cli/azure/vm#az-vm-create) met de optie. Vervang *vmname,* *RGname*en *keyFile* in de volgende opdracht door uw eigen waarden:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Als u meerdere SSH-sleutels met uw VM wilt gebruiken, u `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`deze invoeren in een lijst met ruimtescheidingen, zoals deze.


## <a name="ssh-into-your-vm"></a>SSH in uw virtuele machine

Met de openbare sleutel die is geïmplementeerd op uw Azure VM en de privésleutel op uw lokale systeem, wordt SSH in uw VM gebruikt met behulp van het IP-adres of de DNS-naam van uw VM. Vervang in de volgende opdracht *azureuser* en *myvm.westus.cloudapp.azure.com* door de gebruikersnaam van de beheerder en de volledig gekwalificeerde domeinnaam (of IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u een wachtwoordzin hebt opgegeven toen u uw sleutelpaar maakte, voert u die wachtwoordzin in wanneer u daarom wordt gevraagd tijdens het aanmeldingsproces. De VM wordt toegevoegd aan uw bestand ~/.ssh/known_hosts en u wordt niet gevraagd opnieuw verbinding te maken totdat de openbare sleutel op uw Azure VM wordt gewijzigd of de servernaam is verwijderd uit ~/.ssh/known_hosts.

Als de VM het just-in-time toegangsbeleid gebruikt, moet u toegang aanvragen voordat u verbinding maken met de VM. Zie Toegang [tot virtuele machines beheren met behulp van het just-in-time-beleid](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.

## <a name="next-steps"></a>Volgende stappen

* Zie Gedetailleerde stappen voor het maken [en beheren van SSH-sleutelparen voor](create-ssh-keys-detailed.md)meer informatie over het werken met SSH-sleutelparen.

* Zie [Problemen met SSH-verbindingen met een Azure Linux VM](troubleshoot-ssh-connection.md)als u problemen hebt met SSH-verbindingen met Azure Linux VM.
