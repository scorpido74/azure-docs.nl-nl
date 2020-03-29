---
title: Een opgeslagen toegangsbeleid definiëren met .NET - Azure Storage
description: Meer informatie over het definiëren van een opgeslagen toegangsbeleid met behulp van de .NET-clientbibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990739"
---
# <a name="define-a-stored-access-policy-with-net"></a>Een opgeslagen toegangsbeleid definiëren met .NET

Een opgeslagen toegangsbeleid biedt een extra niveau van controle over gedeelde toegangshandtekeningen op serviceniveau (SAS) aan de serverzijde. Het definiëren van een opgeslagen toegangsbeleid dient om gedeelde toegangshandtekeningen te groeperen en om extra beperkingen op te leggen voor gedeelde toegangshandtekeningen die aan het beleid zijn gebonden. U een opgeslagen toegangsbeleid gebruiken om de begintijd, vervaldatum of machtigingen voor een SAS te wijzigen of om het in te trekken nadat deze is uitgegeven.
  
 De volgende opslagbronnen ondersteunen beleid voor opgeslagen toegang:  
  
- Blobcontainers  
- Bestandsshares  
- Wachtrijen  
- Tabellen  
  
> [!NOTE]
> Een opgeslagen toegangsbeleid voor een container kan worden gekoppeld aan een handtekening met gedeelde toegang waarbij machtigingen worden verleend aan de container zelf of aan de blobs die deze bevat. Op dezelfde manier kan een opgeslagen toegangsbeleid voor een bestandsshare worden gekoppeld aan een handtekening met gedeelde toegang waarmee machtigingen worden verleend aan het aandeel zelf of aan de bestanden die het bevat.  
>
> Het beleid voor opgeslagen toegang wordt alleen ondersteund voor een service-SAS. Beleid voor opgeslagen toegang wordt niet ondersteund voor sas- of gebruikersdelegatie SAS.  

## <a name="create-a-stored-access-policy"></a>Een opgeslagen toegangsbeleid maken

Met de volgende code wordt een opgeslagen toegangsbeleid voor een container aanmaken. U het toegangsbeleid gebruiken om beperkingen op te geven voor een service-SAS voor de container of de blobs.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Zie ook

- [Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)](storage-sas-overview.md)
- [Opgeslagen toegangsbeleid definiëren](/rest/api/storageservices/define-stored-access-policy)

