---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - met intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 56c63ee13c5e42350a2b544074a0b0527377ca4c
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494685"
---
### <a name="set-up-local-azure-credentials"></a>Lokale Azure-referenties instellen

In dit voorbeeld wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Voor meer informatie over verschillende manieren waarop een client-app kan verifiëren met Azure Digital Twins, raadpleegt u [*Instructies: Verificatiecode voor app schrijven*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]