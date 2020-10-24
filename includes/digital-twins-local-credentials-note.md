---
author: baanders
description: bestand insluiten voor DefaultAzureCredential in azure Digital Apparaatdubbels-voor beelden-Opmerking
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494493"
---
>[!NOTE]
> In dit voor beeld wordt [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (onderdeel van de `Azure.Identity` bibliotheek) gebruikt voor het verifiëren van gebruikers met het Azure Digital apparaatdubbels-exemplaar wanneer u het op uw lokale computer uitvoert. Met dit type verificatie zoekt het voor beeld naar Azure-referenties in uw lokale omgeving, zoals een aanmelding vanuit een lokale [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) of in Visual Studio/Visual Studio code.
>
> `DefaultAzureCredential`Zie [*How to: app-verificatie code schrijven*](../articles/digital-twins/how-to-authenticate-client.md)voor meer informatie over het gebruik van en andere verificatie opties.