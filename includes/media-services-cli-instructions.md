---
title: bestand opnemen
description: bestand opnemen
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461211"
---
## <a name="use-cli-shell"></a>CLI Shell gebruiken

Het wordt aanbevolen om [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) te gebruiken om CLI-opdrachten uit te voeren. **Cloud Shell** is een gratis, interactieve shell die u gebruiken om de stappen in dit artikel uit te voeren. Er zijn vooraf algemene Azure-hulpprogramma's geïnstalleerd en geconfigureerd in Cloud Shell die u kunt gebruiken bij uw account. Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken. Linux-gebruikers kunnen kiezen voor een Bash-ervaring en Windows-gebruikers kunnen kiezen voor PowerShell.

U de CLI ook lokaal installeren. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor instructies voor uw platform.

### <a name="sign-in"></a>Aanmelden

Als u een lokale installatie van de CLI gebruikt, moet u zich aanmelden bij Azure. Deze stap is niet vereist voor Azure Cloud Shell. Meld u `az login` aan met de opdracht.

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browserpagina openen en de instructies op de opdrachtregel https://aka.ms/devicelogin volgen om een autorisatiecode in te voeren nadat u in uw browser bent genavigeerd.

### <a name="specify-location-of-files"></a>Locatie van bestanden opgeven

Met veel CLI-opdrachten van Media Services u een parameter met een bestandsnaam doorgeven. Als u **Cloud Shell**gebruikt, u uw bestand uploaden naar uw clouddrive (met Behulp van Bash of PowerShell). 

![Bestanden uploaden]

Of u nu een lokale CLI of **Cloud Shell**gebruikt, u moet het bestandspad opgeven volgens het besturingssysteem of cloudshell (Bash of PowerShell) dat u gebruikt. Hieronder vindt u enkele voorbeelden:

Relatief pad naar het bestand (alle besturingssysteem)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absoluut bestandspad op Linux/Mac en Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Gebruik `{file}` als de opdracht om een pad naar het bestand vraagt. Bijvoorbeeld `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Gebruik `@{file}` als de opdracht het opgegeven bestand gaat laden. Bijvoorbeeld `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Bestanden uploaden]: ./media/media-services-cli/upload-download-files.png
