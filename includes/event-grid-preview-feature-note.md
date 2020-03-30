---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814736"
---
Deze functie is beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u een voorbeeldextensie of -module installeren.

### <a name="install-extension-for-azure-cli"></a>Extensie voor Azure CLI installeren

Voor Azure CLI hebt u de [extensie Gebeurtenisraster](/cli/azure/azure-cli-extensions-list)nodig.

In [CloudShell](/azure/cloud-shell/quickstart):

* Als u de extensie eerder hebt geïnstalleerd, werkt u deze bij`az extension update -n eventgrid`
* Als u de extensie nog niet eerder hebt geïnstalleerd, installeert u deze`az extension add -n eventgrid`

Voor een lokale installatie:

1. [Installeer de Azure CLI](/cli/azure/install-azure-cli). Zorg ervoor dat u de nieuwste `az --version`versie, door te controleren met .
1. Eerdere versies van de extensie verwijderen`az extension remove -n eventgrid`
1. Installeer `eventgrid` de extensie met`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Module installeren voor PowerShell

Voor PowerShell hebt u de [AzureRM.EventGrid-module](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)nodig.

In [CloudShell](/azure/cloud-shell/quickstart-powershell):

* De module installeren`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Voor een lokale installatie:

1. PowerShell-console openen als beheerder
1. De module installeren`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Als `-AllowPrerelease` de parameter niet beschikbaar is, gebruikt u de volgende stappen:

1. Voer `Install-Module PowerShellGet -Force` uit.
1. Voer `Update-Module PowerShellGet` uit.
1. De PowerShell-console sluiten
1. PowerShell opnieuw starten als beheerder
1. De module installeren`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
