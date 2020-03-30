---
title: Een service-SAS maken voor een container of blob met .NET
titleSuffix: Azure Storage
description: Meer informatie over het maken van een service shared access signature (SAS) voor een container of blob met behulp van de .NET-clientbibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 10045a760d7e0fcb02a754bc9bb52a5ebca970f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137208"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Een service-SAS maken voor een container of blob met .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel ziet u hoe u de opslagaccountsleutel gebruiken om een serviceSAS voor een container of blob te maken met de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-a-service-sas-for-a-blob-container"></a>Een service-SAS maken voor een blobcontainer

Als u een serviceSAS voor een container wilt maken, roept u de methode [CloudBlobContainer.GetSharedAccessSignature aan.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature)

In het volgende codevoorbeeld wordt een SAS op een container gesmaakt. Als de naam van een bestaand beleid voor opgeslagen toegang wordt opgegeven, is dat beleid gekoppeld aan de SAS. Als er geen opgeslagen toegangsbeleid wordt opgegeven, wordt er door de code een ad hoc SAS op de container gesmaakt.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

## <a name="create-a-service-sas-for-a-blob"></a>Een service-SAS voor een blob maken

Als u een serviceSAS voor een blob wilt maken, roept u de methode [CloudBlob.GetSharedAccessSignature aan.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)

In het volgende codevoorbeeld wordt een SAS op een blob gemaakt. Als de naam van een bestaand beleid voor opgeslagen toegang wordt opgegeven, is dat beleid gekoppeld aan de SAS. Als er geen opgeslagen toegangsbeleid is opgegeven, wordt er door de code een ad hoc SAS op de blob gesmaakt.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Volgende stappen

- [Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)
- [Een service-SAS maken](/rest/api/storageservices/create-service-sas)
