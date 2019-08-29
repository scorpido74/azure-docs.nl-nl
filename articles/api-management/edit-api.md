---
title: Een API bewerken met Azure Portal | Microsoft Docs
description: Deze zelfstudie laat u zien hoe u een API Management (APIM) moet gebruiken om een API te bewerken.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6be36493fabce07838991c789e111e918a9a826d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072155"
---
# <a name="edit-an-api"></a>Een API bewerken

Deze zelfstudie laat u zien hoe u een API Management (APIM) moet gebruiken om een API te bewerken. 

+ U kunt dit doen door het toevoegen, verwijderen, wijzigen van bewerkingen in het APIM-exemplaar. 
+ U kunt de swagger van uw API bewerken.

## <a name="prerequisites"></a>Vereisten

+ [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ [Uw eerste API importeren en publiceren](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Een API in APIM bewerken

![Een API bewerken](./media/edit-api/edit-api001.png)

1. Klik op het tabblad **API's**.
2. Selecteer een van de API's die u eerder hebt geïmporteerd.
3. Selecteer het tabblad **Ontwerpen**.
4. Selecteer een bewerking die u wilt bewerken.
5. Om de naam van de bewerking te wijzigen, selecteert u een **potlood** in het **Front-end**-venster.

## <a name="update-the-swagger"></a>Werk de swagger bij

U kunt uw back-end-API van Azure Portal bijwerken met de volgende stappen:

1. Selecteer **Alle bewerkingen**
2. Klik op het potlood in het **Front-end**-venster.

    ![Een API bewerken](./media/edit-api/edit-api002.png)

    Uw API swagger wordt weergegeven.

    ![Een API bewerken](./media/edit-api/edit-api003.png)

3. Werk de swagger bij.
4. Klik op **Opslaan**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voorbeelden van APIM beleid](policy-samples.md)
> [Een gepubliceerde API beveiligen transformeren en beveiligen](transform-api.md)