---
title: Beheer van leads in de Market configureren | Azure Marketplace
description: Configureer het beheer van leads voor de klanten van Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812141"
---
# <a name="configure-lead-management-in-marketo"></a>Beheer van leads in de Market configureren

In dit artikel wordt beschreven hoe u uw Marketo CRM-systeem zo instelt dat verkoop leads van uw Marketplace-aanbieding kunnen worden verwerkt.

## <a name="set-up-your-marketo-crm-system"></a>Uw Marketo CRM-systeem instellen

1. Meld u aan bij Marketo.
2. Selecteer **Design Studio**.
    ![Marketo-ontwerp Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Selecteer **nieuw formulier**.
    ![Marketo nieuw formulier](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Vul de vereiste velden in het nieuwe formulier in en selecteer vervolgens **maken**.
    ![Marketo nieuw formulier maken](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Selecteer **volt ooien**op veld Details.
    formulier ![Marketo-afwerking](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Goed keuren en sluiten.

7. Selecteer **code insluiten**op het tabblad *MarketplaceLeadBacked* . 

    ![Invoeg code](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Met marketo-insluit code wordt code weer gegeven die vergelijkbaar is met het volgende voor beeld.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Kopieer de waarden voor de onderstaande velden die worden weer gegeven in het formulier voor het insluiten van code. U gebruikt deze waarden om uw aanbieding te configureren voor het ontvangen van leads in de volgende stap. Gebruik het volgende voor beeld als richt lijn voor het ophalen van de Id's die u nodig hebt via het code voorbeeld van de Marketo-invoeg toepassing.

    - Server-ID = **ys12**
    - Munchkin-ID = **123-PQR-789**
    - Formulier-ID = **1179**

    **Een andere manier om deze waarden te berekenen**

    - De server-ID bevindt zich in de URL van uw Marketo-exemplaar, bijvoorbeeld "`serverID.marketo.com`".
    - U kunt de munching-ID van uw abonnement ophalen door naar uw beheerder te gaan > menu Munchkin in het veld Munchkin-account-ID of vanuit het eerste deel van uw Market-REST API host-subdomein: `https://{Munchkin ID}.mktorest.com`.
    - De formulier-ID is de ID van het insluit code formulier dat u in stap 7 hebt gemaakt om leads van onze Marketplace te routeren.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Uw aanbieding configureren voor het verzenden van leads naar Marketo

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, volgt u de onderstaande stappen: 

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
1. Selecteer **verbinding maken** in het gedeelte Lead beheer. 

    ![Lead beheer-verbinding maken](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Selecteer in het pop-upvenster verbindings Details de optie **marketo** voor de doel locatie van de lead.

    ![Kies een doel voor een potentiële klant](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Geef de **Server-id**, de **munching-account-id**en de **formulier-id**op.

    >[!Note]
    >U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen. 

