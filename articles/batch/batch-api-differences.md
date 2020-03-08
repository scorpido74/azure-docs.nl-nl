---
title: Verschillen tussen beheer-Api's en service-Api's-Azure Batch | Microsoft Docs
description: Api's werken op de verschillende lagen van de Azure Batch service.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672766"
---
# <a name="service-level-and-management-level-apis"></a>Api's voor service niveau en beheer niveau

Azure Batch heeft twee sets Api's: één voor het service niveau en één voor het beheer niveau. De naam is vaak vergelijkbaar, maar retourneert verschillende resultaten. Als u activiteiten logboeken wilt, moet u de beheer-Api's gebruiken. Api's op service niveau negeren de Azure Resource Management-laag en worden niet geregistreerd.


Batch-beheer en batch-service hebben beide Api's voor de groep, bijvoorbeeld. 
- Deze API voor het verwijderen van een groep is rechtstreeks gericht op het batch-account: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Deze API voor het verwijderen van de groep https://docs.microsoft.com/rest/api/batchmanagement/pool/delete is gericht op de management.azure.com-laag.

