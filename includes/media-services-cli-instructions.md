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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79461211"
---
## <a name="use-cli-shell"></a>CLI-shell gebruiken

U kunt het beste [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) gebruiken om CLI-opdrachten uit te voeren. **Cloud shell** is een gratis, interactieve shell die u kunt gebruiken om de stappen in dit artikel uit te voeren. Er zijn vooraf algemene Azure-hulpprogramma's geïnstalleerd en geconfigureerd in Cloud Shell die u kunt gebruiken bij uw account. Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken. Linux-gebruikers kunnen kiezen voor een Bash-ervaring en Windows-gebruikers kunnen kiezen voor PowerShell.

U kunt de CLI ook lokaal installeren. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor instructies voor uw platform.

### <a name="sign-in"></a>Aanmelden

Als u een lokale installatie van de CLI wilt gebruiken, moet u zich aanmelden bij Azure. Deze stap is niet vereist voor Azure Cloud Shell. Meld u aan met `az login` de opdracht.

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser pagina openen en de instructies op de opdracht regel volgen om een autorisatie code in te voeren nadat u https://aka.ms/devicelogin in uw browser hebt genavigeerd.

### <a name="specify-location-of-files"></a>Locatie van bestanden opgeven

Met veel Media Services CLI-opdrachten kunt u een para meter door geven aan de naam van een bestand. Als u **Cloud shell**gebruikt, kunt u uw bestand uploaden naar uw clouddrive (met behulp van bash of Power shell). 

![Bestanden uploaden]

Of u nu gebruikmaakt van een lokale CLI of **Cloud shell**, u moet het bestandspad opgeven volgens het besturings systeem of Cloud shell (bash of Power shell) dat u gebruikt. Hieronder ziet u enkele voor beelden:

Relatief pad naar het bestand (alle besturings systemen)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absoluut bestandspad in Linux/Mac en Windows-besturings systeem

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Gebruik `{file}` deze opdracht als u wordt gevraagd een pad naar het bestand op te vragen. Bijvoorbeeld `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Gebruiken `@{file}` als de opdracht het opgegeven bestand gaat laden. Bijvoorbeeld `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Bestanden uploaden]: ./media/media-services-cli/upload-download-files.png
