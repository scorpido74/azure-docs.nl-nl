---
title: Azure Storage accounts met Azure Media Services accounts | Microsoft Docs
description: Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. De opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account.
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
ms.openlocfilehash: 4bbadd7e10f0fd6896932dd79a5ca42d9906d2a2
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602134"
---
# <a name="azure-storage-accounts"></a>Azure Storage accounts

Als u media-inhoud in azure wilt beheren, versleutelen, coderen, analyseren en streamen, moet u een Media Services-account maken. Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. De opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account. 

Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt ten zeerste aanbevolen om opslag accounts te gebruiken op dezelfde locatie als het Media Services-account om extra latentie en de kosten voor gegevens uitvoer te voor komen

U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. Media Services ondersteunt **GPv2**-accounts (General-purpose v2) of **GPv1**-accounts (General-purpose v1). <br/>Blob-accounts kunt u niet instellen als **primaire** account. 

We raden u aan GPv2 te gebruiken, zodat u kunt profiteren van de nieuwste functies en prestaties. Zie [Azure Storage account overview](../../storage/common/storage-account-overview.md)(Engelstalig) voor meer informatie over opslag accounts.

> [!NOTE]
> Alleen de laag Hot Access wordt ondersteund voor gebruik met Azure Media Services, hoewel de andere toegangs lagen kunnen worden gebruikt om de opslag kosten te verlagen voor inhoud die niet actief wordt gebruikt.

Er zijn verschillende Sku's die u kunt kiezen voor uw opslag account. Zie [Opslagaccounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) voor meer informatie. Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Als u echter een SKU voor productie selecteert, kunt u overwegen om `--sku Standard_RAGRS` te gebruiken. Deze biedt geografische replicatie voor bedrijfscontinuïteit. 

## <a name="assets-in-a-storage-account"></a>Assets in een opslag account

In Media Services v3 worden de opslag-Api's gebruikt voor het uploaden van bestanden naar assets. Zie voor meer informatie [assets-concept](assets-concept.md).

> [!Note]
> U moet niet proberen om de inhoud te wijzigen van BLOB-containers die zijn gegenereerd door de Media Services SDK zonder gebruik te maken van Media Services-Api's.
 
## <a name="storage-side-encryption"></a>Versleuteling van opslag aan de serverzijde

Ter bescherming van uw activa in rust, moeten de activa van de versleuteling van opslag aan de serverzijde worden versleuteld. De volgende tabel laat zien hoe de versleuteling van opslag aan de serverzijde in Media Services v3 werkt:

|Optie voor opslagversleuteling|Beschrijving|Media Services v3|
|---|---|---|
|Media Services-Storage-versleuteling| AES-256-codering, sleutel beheerd door Media Services|Niet ondersteund<sup>(1)</sup>|
|[Storage Service Encryption voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Versleuteling op de server die worden aangeboden door Azure Storage, sleutel beheerd door Azure of door de klant|Ondersteund|
|[Versleuteling van de opslag aan de client zijde](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Clientversleuteling die worden aangeboden door Azure-opslag, beheerd door de klant in Key Vault sleutel|Niet ondersteund|

<sup>1</sup> in Media Services V3 wordt opslag VERSLEUTELING (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2. Dit betekent dat v3 werkt met bestaande opslag versleuteld activa, maar staat niet toe dat het maken van nieuwe labels.

## <a name="storage-account-errors"></a>Fouten in het opslag account

De status 'Verbinding verbroken' voor een Media Services-account geeft aan dat het account geen toegang meer heeft tot een of meer van de gekoppelde opslagaccounts vanwege een wijziging van de toegangssleutels voor opslag. Bijgewerkte toegangssleutels voor opslag zijn noodzakelijk voor Media Services om allerlei taken te kunnen uitvoeren in het account.

Hieronder vindt u de belangrijkste scenario's waarin een Media Services-account geen toegang krijgt tot gekoppelde opslagaccounts. 

|Probleem|Oplossing|
|---|---|
|Het Media Services-account of een of meer gekoppelde opslagaccounts zijn gemigreerd naar afzonderlijke abonnementen. |Migreer het opslagaccount of de opslagaccounts, of het Media Services-account, zodat deze zich allemaal in hetzelfde abonnement bevinden. |
|Het Media Services-account gebruikt een gekoppeld opslagaccount in een ander abonnement, aangezien het een vroeg Media Services-account betreft waarvoor dit nog werd ondersteund. Alle vroege Media Services-accounts zijn geconverteerd naar moderne ARM-accounts (Azure Resources Manager) en hebben daardoor de status Verbinding verbroken. |Migreer het opslagaccount of Media Services-account, zodat deze zich allemaal in hetzelfde abonnement bevinden.|

## <a name="azure-storage-firewall"></a>Azure Storage firewall

Azure Media Services biedt geen ondersteuning voor opslag accounts waarbij de Azure Storage firewall of het [persoonlijke eind punt](https://docs.microsoft.com/azure/storage/common/storage-network-security) is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Zie [een account maken](create-account-cli-quickstart.md)voor meer informatie over het koppelen van een opslag account aan uw Media Services-account.
