---
title: Azure Blob Storage gebruiken voor modelconversie
description: Beschrijft algemene stappen voor het instellen en gebruiken van blobopslag voor modelconversie.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681647"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Azure Blob Storage gebruiken voor modelconversie

De [modelconversieservice](model-conversion.md) vereist toegang tot Azure blob-opslag, zodat deze invoergegevens kan ophalen en uitvoergegevens kan opslaan. In dit artikel wordt beschreven hoe u de meest voorkomende stappen uitvoeren.

## <a name="prepare-azure-storage-accounts"></a>Azure Storage-accounts voorbereiden

- Een opslagaccount maken (StorageV2)
- Een invoerblobcontainer maken in het opslagaccount (bijvoorbeeld 'arrinput' genoemd)
- Een uitvoerblobcontainer maken in het opslagaccount (bijvoorbeeld 'arroutput' genoemd)

> [!TIP]
> Voor stapsgewijze instructies voor het instellen van uw opslagaccount, u [snel beginnen: een model converteren voor rendering](../../quickstarts/convert-model.md)

Het aanmaken van het opslagaccount en de blobcontainers kan met een van de volgende hulpprogramma's:

- [Azure Portal](https://portal.azure.com)
- [az-opdrachtregel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Ervoor zorgen dat Azure Remote Rendering toegang heeft tot uw opslagaccount

Azure Remote Rending moet modelgegevens ophalen uit uw opslagaccount en gegevens terugschrijven.

U Azure Remote Rendering op de volgende twee manieren toegang verlenen tot uw opslagaccount:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Uw Azure Storage-account verbinden met uw Azure Remote Rendering-account

Volg de stappen in de sectie [Een account maken.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>SAS ophalen voor de opslagcontainers

Opgeslagen toegangshandtekeningen (SAS) worden gebruikt om leestoegang te verlenen voor invoer en schrijftoegang voor uitvoer. We raden u aan elke keer dat een model wordt geconverteerd nieuwe URI's te genereren. Aangezien URI's na enige tijd verlopen, kan het risico lopen dat uw aanvraag onverwacht wordt afgebroken.

Details over SAS zijn te vinden op de [SAS-documentatie.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

Een SAS URI kan worden gegenereerd met een van:

- az PowerShell-module
  - zie het [voorbeeld PowerShell-scripts](../../samples/powershell-example-scripts.md)
- [az-opdrachtregel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - klik met de rechtermuisknop op de container "Get Shared Access Signature" (lezen, lijsttoegang voor invoercontainer, schrijftoegang voor uitvoercontainer)
- SDKs (C#, Python ... )

Een voorbeeld van het gebruik van Gedeelde toegangshandtekeningen in assetconversie wordt weergegeven in Conversion.ps1 van de [Powershell Example Scripts](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Een invoermodel uploaden

Als u wilt beginnen met het converteren van een model, moet u het uploaden met een van de volgende opties:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - een handige gebruikersinterface voor het uploaden/downloaden/beheren van bestanden op azure blob-opslag
- [Azure-opdrachtregel](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - zie het [voorbeeld PowerShell-scripts](../../samples/powershell-example-scripts.md)
- [Met behulp van een opslag SDK (Python, C # ... )](https://docs.microsoft.com/azure/storage/)
- [De AZURE Storage REST API's gebruiken](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Voor een voorbeeld van het uploaden van gegevens voor conversie verwijzen naar Conversion.ps1 van de [Powershell Example Scripts](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Een SAS URI voor het geconverteerde model

Deze stap is vergelijkbaar met [het ophalen van SAS voor de opslagcontainers.](#retrieve-sas-for-the-storage-containers) Deze keer moet u echter een SAS URI ophalen voor het modelbestand, dat naar de uitvoercontainer is geschreven.

Als u bijvoorbeeld een SAS URI wilt ophalen via de [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)klikt u met de rechtermuisknop op het modelbestand en selecteert u 'Gedeelde toegangshandtekening ophalen'.

Een SAS (Shared Access Signature) voor het laden van modellen is nodig als u uw opslagaccount niet hebt gekoppeld aan uw Azure Remote Rendering-account. Je leren hoe je je account koppelen in [Een account maken.](../create-an-account.md#link-storage-accounts)

## <a name="next-steps"></a>Volgende stappen

- [De modelconversie configureren](configure-model-conversion.md)
- [De API voor de modelconversie REST](conversion-rest-api.md)
