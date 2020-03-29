---
title: Verschillen tussen beheer-API's en service-API's - Azure Batch | Microsoft Documenten
description: API's werken op de verschillende lagen van de Azure Batch-service.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672766"
---
# <a name="service-level-and-management-level-apis"></a>API's op serviceniveau en beheerniveau

Azure Batch heeft twee sets API's, één voor het serviceniveau en één voor het beheerniveau. De naamgeving is vaak vergelijkbaar, maar ze keren verschillende resultaten. Als u activiteitslogboeken wilt, moet u de beheer-API's gebruiken. API's op serviceniveau omzeilen de laag Azure Resource Management en worden niet geregistreerd.


Batchbeheer en Batchservice hebben bijvoorbeeld API's voor Pool. 
- Deze API voor het verwijderen van de groep is rechtstreeks gericht op het batchaccount:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Deze API voor https://docs.microsoft.com/rest/api/batchmanagement/pool/delete het verwijderen van de groep is gericht op de management.azure.com laag.

