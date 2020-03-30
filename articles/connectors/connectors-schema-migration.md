---
title: Apps migreren naar het nieuwste schema
description: Json-definities voor logische app-werkstroom migreren naar de meest recente schemaversie van de werkstroomdefinitie
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666785"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Logische apps migreren naar de nieuwste schemaversie

Voer de volgende stappen uit om uw bestaande logische apps naar het nieuwste schema te verplaatsen: 

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

2. Kies **Overzicht**in het menu van uw logische app. Kies Op de werkbalk **Schema bijwerken**.

   > [!NOTE]
   > Wanneer u **Schema**bijwerken kiest, voert Azure Logic Apps automatisch de migratiestappen uit en biedt het de code-uitvoer voor u. U deze uitvoer gebruiken voor het bijwerken van de definitie van uw logische app. Zorg er echter voor dat u de aanbevolen procedures volgt zoals beschreven in de volgende sectie **Aanbevolen procedures.**

   ![Schema bijwerken](./media/connectors-schema-migration/update-schema.png)

   De pagina Schema bijwerken wordt weergegeven en toont een koppeling naar een document waarin de verbeteringen in het nieuwe schema worden beschreven.

## <a name="best-practices"></a>Aanbevolen procedures

Hier volgen enkele aanbevolen procedures voor het migreren van uw logische apps naar de nieuwste schemaversie:

* Kopieer het gemigreerde script naar een nieuwe logische app. Overschrijf de oude versie niet totdat u uw tests hebt voltooid en bevestig dat uw gemigreerde app werkt zoals verwacht.

* Test uw logische app **voordat u** de productie invoert.

* Nadat u de migratie hebt voltooid, begint u met het bijwerken van uw logische apps om de [beheerde API's](../connectors/apis-list.md) waar mogelijk te gebruiken. Gebruik bijvoorbeeld Dropbox v2 overal waar je DropBox v1 gebruikt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [handmatig migreren van uw Logic-apps](../logic-apps/logic-apps-schema-2015-08-01.md)
