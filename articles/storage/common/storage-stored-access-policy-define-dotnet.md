---
title: Een opgeslagen toegangs beleid definiëren met .NET-Azure Storage
description: Meer informatie over het definiëren van een opgeslagen toegangs beleid met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990739"
---
# <a name="define-a-stored-access-policy-with-net"></a>Een opgeslagen toegangs beleid definiëren met .NET

Een opgeslagen toegangs beleid biedt een extra controle niveau voor Shared Access signatures (SAS) op service niveau aan de server zijde. Als u een opgeslagen toegangs beleid definieert, worden de hand tekeningen voor gedeelde toegang gegroepeerd en worden er aanvullende beperkingen geboden voor de hand tekeningen voor gedeelde toegang die door het beleid zijn gebonden. U kunt een opgeslagen toegangs beleid gebruiken om de start tijd, de verloop tijd of de machtigingen voor een SAS te wijzigen, of om deze in te trekken nadat deze is uitgegeven.
  
 De volgende opslag bronnen ondersteunen opgeslagen toegangs beleid:  
  
- Blobcontainers  
- Bestandsshares  
- Wachtrijen  
- Tabellen  
  
> [!NOTE]
> Een opgeslagen toegangs beleid voor een container kan worden gekoppeld aan een gedeelde toegangs handtekening die machtigingen verleent aan de container zelf of aan de blobs die deze bevat. Op dezelfde manier kan een opgeslagen toegangs beleid voor een bestands share worden gekoppeld aan een gedeelde toegangs handtekening waarmee machtigingen worden verleend aan de share zelf of aan de bestanden die deze bevat.  
>
> Opgeslagen toegangs beleid wordt alleen ondersteund voor een service-SAS. Opgeslagen toegangs beleid wordt niet ondersteund voor account-SAS of SAS voor gebruikers overdracht.  

## <a name="create-a-stored-access-policy"></a>Een opgeslagen toegangs beleid maken

Met de volgende code wordt een opgeslagen toegangs beleid voor een container gemaakt. U kunt het toegangs beleid gebruiken om beperkingen voor een service-SA'S op te geven voor de container of de blobs.

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

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
- [Opgeslagen toegangsbeleid definiëren](/rest/api/storageservices/define-stored-access-policy)

