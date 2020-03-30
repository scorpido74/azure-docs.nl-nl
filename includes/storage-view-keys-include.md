---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208176"
---
Ga als het gaat om het weergeven en kopiëren van uw toegangssleutels voor opslagaccount of verbindingstekenreeks vanuit de Azure-portal:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. Selecteer **Access-toetsen**onder **Instellingen**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Sleutel** onder **key1** en klik op de knop **Kopiëren** om de accountsleutel te kopiëren.
5. U afwisselend de volledige verbindingstekenreeks kopiëren. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren.

    ![Schermafbeelding van het weergeven van toegangssleutels in de Azure-portal](media/storage-view-keys-include/portal-connection-string.png)

U een van beide sleutels gebruiken om toegang te krijgen tot Azure Storage, maar over het algemeen is het een goede gewoonte om de eerste sleutel te gebruiken en het gebruik van de tweede sleutel te reserveren voor wanneer u sleutels draait.
