---
title: UNH 2,5-segmenten in EDIFACT-berichten
description: EDIFACT-berichten oplossen met UNH 2.5-segmenten in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792533"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>EDIFACT documenten met UNH 2.5-segmenten in Azure Logic Apps verwerken

Als er een UNH 2.5-segment bestaat in een EDIFACT-document, wordt het segment gebruikt voor het opzoeken van het schema. In dit voor beeld van een EDIFACT-bericht is `EAN008`het veld UNH:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Voer de volgende stappen uit om dit bericht af te handelen:

1. Het schema bijwerken.

1. Controleer de instellingen van de overeenkomst.

## <a name="update-the-schema"></a>Het schema bijwerken

Als u het bericht wilt verwerken, moet u een schema implementeren dat de naam van het UNH 2.5-hoofd knooppunt bevat. Bijvoorbeeld, de naam van het schema root voor het veld UNH- `EFACT_D03B_ORDERS_EAN008`voor beeld is. Voor elk `D03B_ORDERS` met een ander unh 2.5-segment moet u een afzonderlijk schema implementeren.

## <a name="add-schema-to-edifact-agreement"></a>Schema toevoegen aan EDIFACT-overeenkomst

### <a name="edifact-decode"></a>EDIFACT decoderen

Als u het binnenkomende bericht wilt decoderen, moet u het schema instellen in de instellingen voor het ontvangen van de EDIFACT-overeenkomst:

1. Open uw integratie account in de [Azure Portal](https://portal.azure.com).

1. Voeg het schema toe aan uw integratie account.

1. Configureer het schema in de receive-instellingen van de EDIFACT-overeenkomst.

1. Selecteer de EDIFACT-overeenkomst en selecteer **bewerken als JSON**. Voeg de UNH 2.5-waarde toe aan de sectie `schemaReferences` van de ontvangst overeenkomst:

   ![UNH 2.5 toevoegen om overeenkomst te ontvangen](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT-code ring

Als u het binnenkomende bericht wilt coderen, configureert u het schema in de EDIFACT-overeenkomst instellingen verzenden

1. Open uw integratie account in de [Azure Portal](https://portal.azure.com).

1. Voeg het schema toe aan uw integratie account.

1. Configureer het schema in de instellingen voor verzenden van de EDIFACT-overeenkomst.

1. Selecteer EDIFACT Agreement en klik op **bewerken als JSON**.  UNH 2.5-waarde toevoegen in de overeenkomst **SchemaReferences** verzenden

1. Selecteer de EDIFACT-overeenkomst en selecteer **bewerken als JSON**. Voeg de UNH 2.5-waarde toe aan de sectie `schemaReferences` voor het verzenden van de overeenkomst:

   ![UNH 2.5 toevoegen aan het verzenden van een overeenkomst](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [integratie-account overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md)