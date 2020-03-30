---
title: ContentKeys maken met .NET
description: In dit artikel wordt uitgelegd hoe u inhoudssleutels maken die veilige toegang tot assets bieden.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: aebd6dee9314d6e5641988767c024790b6b721f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251152"
---
# <a name="create-contentkeys-with-net"></a>ContentKeys maken met .NET 
> [!div class="op_single_selector"]
> * [Rest](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Met Media Services u versleutelde assets maken en leveren. Een **ContentKey** biedt veilige toegang tot uw **Asset**s. 

Wanneer u een nieuw element maakt (bijvoorbeeld voordat u [bestanden uploadt),](media-services-dotnet-upload-files.md)u de volgende versleutelingsopties opgeven: **StorageEncrypted,** **CommonEncryptionProtected**of **EnvelopeEncryptionProtected**. 

Wanneer u assets aan uw clients levert, u [configureren dat assets dynamisch worden versleuteld](media-services-dotnet-configure-asset-delivery-policy.md) met een van de volgende twee versleutelingen: **DynamicEnvelopeEncryption** of **DynamicCommonEncryption.**

Versleutelde assets moeten worden gekoppeld aan **ContentKey**s. In dit artikel wordt beschreven hoe u een inhoudssleutel maakt.

> [!NOTE]
> Bij het maken van een nieuw **StorageEncrypted-asset** met behulp van de Media Services .NET SDK wordt de **ContentKey** automatisch gemaakt en gekoppeld aan het item.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Een van de waarden die u moet instellen bij het maken van een inhoudssleutel, is het type inhoudssleutel. Kies uit een van de volgende waarden. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Envelopetype ContentKey maken
In het volgende codefragment wordt een inhoudssleutel van het versleutelingstype envelop versleuteling. Vervolgens associeert het de sleutel met het opgegeven actief.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Algemeen type ContentKey maken
In het volgende codefragment wordt een inhoudssleutel van het algemene versleutelingstype gesleuteld. Vervolgens associeert het de sleutel met het opgegeven actief.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

