---
title: UNH 2.5 segmenten in EDIFACT-berichten
description: EDIFACT-berichten oplossen met UNH2.5-segmenten in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792533"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>EDIFACT-documenten verwerken met UNH2.5-segmenten in Azure Logic Apps

Als er een UNH2.5-segment bestaat in een EDIFACT-document, wordt het segment gebruikt voor schemaopzoeken. In dit voorbeeld van EDIFACT is `EAN008`het veld UNH bijvoorbeeld:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Volg de volgende stappen die hieronder worden beschreven om dit bericht te verwerken:

1. Werk het schema bij.

1. Controleer de overeenkomstinstellingen.

## <a name="update-the-schema"></a>Het schema bijwerken

Als u het bericht wilt verwerken, moet u een schema implementeren met de naam UNH2.5 root node. De hoofdnaam van het schema voor het `EFACT_D03B_ORDERS_EAN008`veld UNH is bijvoorbeeld . Voor `D03B_ORDERS` elk dat een ander UNH2.5-segment heeft, moet u een individueel schema implementeren.

## <a name="add-schema-to-edifact-agreement"></a>Schema toevoegen aan EDIFACT-overeenkomst

### <a name="edifact-decode"></a>EDIFACT Decoderen

Als u het binnenkomende bericht wilt decoderen, stelt u het schema in de ontvangstinstellingen van de EDIFACT-overeenkomst in:

1. Open uw integratieaccount in de [Azure-portal.](https://portal.azure.com)

1. Voeg het schema toe aan uw integratieaccount.

1. Configureer het schema in de ontvangstinstellingen van de EDIFACT-overeenkomst.

1. Selecteer de EDIFACT-overeenkomst en selecteer **Bewerken als JSON**. Voeg de UNH2.5-waarde toe `schemaReferences` aan de sectie Ontvangstovereenkomst:

   ![UnH2.5 toevoegen om een overeenkomst te ontvangen](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT Coderen

Als u het binnenkomende bericht wilt coderen, configureert u het schema in de instellingen voor het verzenden van edifact-overeenkomst

1. Open uw integratieaccount in de [Azure-portal.](https://portal.azure.com)

1. Voeg het schema toe aan uw integratieaccount.

1. Configureer het schema in de verzendinstellingen van de EDIFACT-overeenkomst.

1. Selecteer EDIFACT-overeenkomst en klik op **Bewerken als JSON**.  UNH2.5-waarde toevoegen aan het schema Voor de overeenkomst **verzendenReferenties**

1. Selecteer de EDIFACT-overeenkomst en selecteer **Bewerken als JSON**. Voeg de UNH2.5-waarde toe `schemaReferences` aan de sectie Verzendovereenkomst:

   ![UnH2.5 toevoegen om een overeenkomst te verzenden](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [integratieaccountovereenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md)