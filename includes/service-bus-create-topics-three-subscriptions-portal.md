---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67176271"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Een onderwerp maken met de Azure-portal
1. Selecteer in het linkermenu op de pagina **Service Bus-naamruimte** de optie **Onderwerpen**.
2. Selecteer **+ Onderwerp** op de werkbalk. 
4. Voer een **naam** in voor het onderwerp. Houd voor de overige opties de standaardwaarden aan.
5. Selecteer **Maken**.

    ![Onderwerp maken](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Abonnementen maken op het onderwerp
1. Selecteer het **onderwerp** dat u in de vorige sectie hebt gemaakt. 
    
    ![Onderwerp selecteren](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Selecteer in het linkermenu op de pagina **Service Bus-onderwerp** de optie **Abonnementen** en vervolgens op de werkbalk **+ Abonnement**. 
    
    ![Knop Abonnement toevoegen](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Voer op de pagina **abonnement maken** de waarde **S1** in voor de **naam** van het abonnement en selecteer vervolgens **maken**. 

    ![Pagina Abonnement maken](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Herhaal de vorige stap twee keer om abonnementen met de naam **S2** en **S3**te maken.