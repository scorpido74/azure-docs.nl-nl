---
title: MediaServices bijwerken na toegang tot de opslag | Microsoft Documenten
description: In deze artikelen vindt u richtlijnen voor het bijwerken van Media Services na het rollen van toegangssleutels voor opslag.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 2a0d1c5af572c88dc11bed950b46706f0a2f081f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981963"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Media Services bijwerken na gebruik toegangssleutels voor opslag 

Wanneer u een nieuw AMS-account (Azure Media Services) maakt, wordt u ook gevraagd een Azure Storage-account te selecteren dat wordt gebruikt om uw media-inhoud op te slaan. U meer dan één opslagaccount toevoegen aan uw Media Services-account. In dit artikel ziet u hoe u opslagsleutels roteren. Het laat ook zien hoe je opslagaccounts toevoegt aan een media-account. 

Als u de in dit artikel beschreven acties wilt uitvoeren, moet u [Azure Resource Manager API's](/rest/api/media/operations/azure-media-services-rest-api-reference) en [Powershell](https://docs.microsoft.com/powershell/module/az.media)gebruiken.  Zie [Azure-resources beheren met PowerShell en Resource Manager](../../azure-resource-manager/management/manage-resource-groups-powershell.md)voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

Wanneer een nieuw opslagaccount wordt gemaakt, genereert Azure twee 512-bits opslagtoegangssleutels, die worden gebruikt om toegang tot uw opslagaccount te verifiëren. Om uw opslagverbindingen veiliger te houden, wordt aanbevolen om uw opslagtoegangssleutel periodiek te regenereren en te roteren. Er zijn twee toegangssleutels (primair en secundair) beschikbaar om u in staat te stellen verbindingen met het opslagaccount te onderhouden met één toegangssleutel terwijl u de andere toegangssleutel regenereert. Deze procedure wordt ook wel "rolling access keys" genoemd.

Media Services is afhankelijk van een opslagsleutel die eraan wordt geleverd. Met name de locators die worden gebruikt om uw assets te streamen of te downloaden, zijn afhankelijk van de opgegeven opslagtoegangssleutel. Wanneer een AMS-account wordt gemaakt, is het standaard afhankelijk van de primaire opslagtoegangssleutel, maar als gebruiker u de opslagsleutel die AMS heeft bijwerken. U moet Media Services laten weten welke sleutel u moet gebruiken door de volgende stappen in dit artikel te volgen.  

>[!NOTE]
> Als u meerdere opslagaccounts hebt, voert u deze procedure uit bij elk opslagaccount. De volgorde waarin u opslagsleutels draait, is niet vastgesteld. U eerst de secundaire sleutel draaien en vervolgens de primaire sleutel of vice versa.
>
> Voordat u stappen uitvoert die in dit artikel worden beschreven op een productieaccount, moet u deze testen op een preproductieaccount.
>

## <a name="steps-to-rotate-storage-keys"></a>Stappen om opslagsleutels te roteren 
 
 1. Wijzig de primaire sleutel voor het opslagaccount via de powershell-cmdlet of [Azure-portal.](https://portal.azure.com/)
 2. Call Sync-AzMediaServiceStorageKeys cmdlet met de juiste params om media-account te dwingen om opslagaccountsleutels op te halen
 
    In het volgende voorbeeld ziet u hoe u sleutels synchroniseert met opslagaccounts.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Wacht een uur of zo. Controleer of de streamingscenario's werken.
 4. Wijzig de secundaire sleutel voor opslagaccount via de powershell-cmdlet of Azure-portal.
 5. Bel Sync-AzMediaServiceStorageKeys powershell met de juiste params om media-account te dwingen om nieuwe opslagaccountsleutels op te halen. 
 6. Wacht een uur of zo. Controleer of de streamingscenario's werken.
 
### <a name="a-powershell-cmdlet-example"></a>Een powershell cmdlet voorbeeld 

In het volgende voorbeeld wordt uitgelegd hoe u het opslagaccount krijgen en synchroniseren met het AMS-account.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Stappen om opslagaccounts toe te voegen aan uw AMS-account

In het volgende artikel ziet u hoe u opslagaccounts toevoegt aan uw AMS-account: [meerdere opslagaccounts koppelen aan een Media Services-account](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Erkenningen
We willen graag de volgende mensen erkennen die hebben bijgedragen aan de oprichting van dit document: Cenk Dingiloglu, Milan Gada, Seva Titov.
