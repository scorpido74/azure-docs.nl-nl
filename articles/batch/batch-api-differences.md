---
title: Verschillen tussen beheer-Api's en service-Api's
description: Api's werken op de verschillende lagen van de Azure Batch service.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115394"
---
# <a name="service-level-and-management-level-apis"></a>Api's voor service niveau en beheer niveau

Azure Batch heeft twee sets Api's: één voor het service niveau en één voor het beheer niveau. De naam is vaak vergelijkbaar, maar retourneert verschillende resultaten. Als u activiteiten logboeken wilt, moet u de beheer-Api's gebruiken. Api's op service niveau negeren de Azure Resource Management-laag en worden niet geregistreerd.


Batch-beheer en batch-service hebben beide Api's voor de groep, bijvoorbeeld. 
- Deze API voor het verwijderen van een groep is rechtstreeks gericht op het batch-account:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Deze API voor het verwijderen https://docs.microsoft.com/rest/api/batchmanagement/pool/delete van de groep is gericht op de Management.Azure.com-laag.

