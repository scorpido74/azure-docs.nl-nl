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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814736"
---
Deze functie is beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u een preview-extensie of-module installeren.

### <a name="install-extension-for-azure-cli"></a>Extensie voor Azure CLI installeren

Voor Azure CLI hebt u de [extensie Event grid](/cli/azure/azure-cli-extensions-list)nodig.

In [Cloud shell](/azure/cloud-shell/quickstart):

* Als u de extensie eerder hebt geïnstalleerd, werkt u deze bij`az extension update -n eventgrid`
* Als u de extensie nog niet eerder hebt geïnstalleerd, installeert u deze`az extension add -n eventgrid`

Voor een lokale installatie:

1. [Installeer de Azure cli](/cli/azure/install-azure-cli). Zorg ervoor dat u de nieuwste versie hebt door te controleren met `az --version`.
1. Eerdere versies van de uitbrei ding verwijderen`az extension remove -n eventgrid`
1. Installeer de `eventgrid` extensie met`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Module voor Power Shell installeren

Voor Power shell hebt u de [module AzureRM. EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)nodig.

In [Cloud shell](/azure/cloud-shell/quickstart-powershell):

* De module installeren`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Voor een lokale installatie:

1. Power shell-console openen als Administrator
1. De module installeren`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Als de `-AllowPrerelease` para meter niet beschikbaar is, gebruikt u de volgende stappen:

1. Voer `Install-Module PowerShellGet -Force` uit.
1. Voer `Update-Module PowerShellGet` uit.
1. De Power shell-console sluiten
1. Start Power shell als Administrator opnieuw op
1. De module installeren`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
