---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - met intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329595"
---
### <a name="set-up-local-azure-credentials"></a>Lokale Azure-referenties instellen

In dit voorbeeld wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Voor meer informatie over verschillende manieren waarop een client-app kan verifiëren met Azure Digital Twins, raadpleegt u [*Instructies: Verificatiecode voor app schrijven*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]