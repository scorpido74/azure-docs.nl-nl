---
title: Azure-opslagaccounts
titleSuffix: Azure Media Services
description: Meer informatie over het maken van een Azure-opslagaccount voor gebruik met Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499836"
---
# <a name="azure-storage-accounts"></a>Azure Storage-accounts

Als u wilt beginnen met het beheren, versleutelen, coderen, analyseren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. Het opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account.

Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt ten zeerste aanbevolen om opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om extra latentie- en gegevensverwijderingskosten te voorkomen.

U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. Media Services ondersteunt **GPv2**-accounts (General-purpose v2) of **GPv1**-accounts (General-purpose v1). Alleen blobaccounts zijn niet toegestaan als **primair**.

We raden u aan GPv2 te gebruiken, zodat u profiteren van de nieuwste functies en prestaties. Zie overzicht van Azure [Storage-account](../../storage/common/storage-account-overview.md)voor meer informatie over opslagaccounts.

> [!NOTE]
> Alleen de hot access-laag wordt ondersteund voor gebruik met Azure Media Services, hoewel de andere toegangslagen kunnen worden gebruikt om de opslagkosten te verlagen voor inhoud die niet actief wordt gebruikt.

Er zijn verschillende SKU's die u kiezen voor uw opslagaccount. Zie [Opslagaccounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) voor meer informatie. Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Echter, bij het kiezen van een `--sku Standard_RAGRS`SKU voor productie, moet u overwegen , die geografische replicatie biedt voor bedrijfscontinuïteit.

## <a name="assets-in-a-storage-account"></a>Activa in een opslagrekening

In Media Services v3 worden de opslag-API's gebruikt om bestanden te uploaden naar assets. Zie [Assets in Azure Media Services v3](assets-concept.md)voor meer informatie.

> [!Note]
> Probeer de inhoud van blobcontainers die zijn gegenereerd door de Media Services SDK niet te wijzigen zonder API's van Media Services te gebruiken.

## <a name="storage-side-encryption"></a>Versleuteling aan de opslagzijde

Om uw assets in rust te beschermen, moeten de assets worden versleuteld door de opslagzijdeversleuteling. In de volgende tabel ziet u hoe de versleuteling aan de opslagzijde werkt in Media Services v3:

|Versleuteling, optie|Beschrijving|Media Services v3|
|---|---|---|
|Media Services-opslagversleuteling| AES-256 encryptie, sleutel beheerd door Media Services. |Niet ondersteund. <sup>De lid 1, onder a), van verordening nr.</sup>|
|[Opslagserviceversleuteling voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Server-side encryptie aangeboden door Azure Storage, sleutel beheerd door Azure of door de klant.|Ondersteund.|
|[Versleuteling aan de clientzijde van opslagclient](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client-side encryptie aangeboden door Azure storage, sleutel beheerd door de klant in Key Vault.|Wordt niet ondersteund.|

<sup>1</sup> In Media Services v3 wordt opslagversleuteling (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2, wat betekent dat v3 werkt met bestaande opslagversleutelde assets, maar geen nieuwe kan worden gemaakt.

## <a name="storage-account-errors"></a>Fouten in opslagaccount

De status 'Verbinding verbroken' voor een Media Services-account geeft aan dat het account geen toegang meer heeft tot een of meer van de gekoppelde opslagaccounts vanwege een wijziging van de toegangssleutels voor opslag. Bijgewerkte toegangssleutels voor opslag zijn noodzakelijk voor Media Services om allerlei taken te kunnen uitvoeren in het account.

Hieronder vindt u de belangrijkste scenario's waarin een Media Services-account geen toegang krijgt tot gekoppelde opslagaccounts.

|Probleem|Oplossing|
|---|---|
|Het Media Services-account of een of meer gekoppelde opslagaccounts zijn gemigreerd naar afzonderlijke abonnementen. |Migreer het opslagaccount(en) of Media Services-account, zodat ze allemaal in hetzelfde abonnement zitten. |
|Het Media Services-account gebruikt een gekoppeld opslagaccount in een ander abonnement, aangezien het een vroeg Media Services-account betreft waarvoor dit nog werd ondersteund. Alle vroege Media Services-accounts zijn geconverteerd naar moderne Azure Resources Manager-accounts en hebben een verbroken status. |Migreer het opslagaccount of het Media Services-account, zodat ze allemaal in hetzelfde abonnement zitten.|

## <a name="azure-storage-firewall"></a>Azure Storage-firewall

Azure Media Services biedt geen ondersteuning voor opslagaccounts met de Azure Storage-firewall of [Privéeindpunten](https://docs.microsoft.com/azure/storage/common/storage-network-security) ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Zie [Een account maken](create-account-cli-quickstart.md)voor meer informatie over het koppelen van een opslagaccount aan uw Media Services-account.
