---
title: Leadmanagement configureren in Marketo | Azure Marketplace
description: Configureer leadbeheer voor Marketo voor Azure-marktplaatsklanten.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 90f42954fd4d7324a7684795fca6ec302411790c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731104"
---
# <a name="configure-lead-management-in-marketo"></a>Leadmanagement configureren in Marketo

In dit artikel wordt beschreven hoe u uw Marketo CRM-systeem instelt om verkoopleads te verwerken vanuit uw marktplaatsaanbieding.

## <a name="set-up-your-marketo-crm-system"></a>Uw Marketo CRM-systeem instellen

1. Log hier in

2. Selecteer **Ontwerpstudio**.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Selecteer **Nieuw formulier**.

    ![Marketo nieuwe vorm](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Vul de vereiste velden in het nieuwe formulier en selecteer **Vervolgens Maken**.

    ![Marketo maakt nieuwe vorm](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Selecteer **Voltooien**in Velddetails .

    ![Marketo finishformulier](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Goedkeuren en sluiten.

7. Selecteer op het tabblad *MarketplaceLeadBacked* de optie **Code insluiten**. 

    ![Insluitcode](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo Embed Code geeft code weer die vergelijkbaar is met het volgende voorbeeld.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Kopieer de waarden voor de onderstaande velden in het formulier Code insluiten. U gebruikt deze waarden om uw aanbieding te configureren om leads te ontvangen in de volgende stap. Gebruik het volgende voorbeeld als een handleiding voor het verkrijgen van de id's die u nodig hebt in het voorbeeld Marketo Embed Code.

    - Server-id = **ys12**
    - Munchkin ID = **123-PQR-789**
    - Formulier-ID = **1179**

    **Een andere manier om erachter te komen deze waarden**

    - Server-id is te vinden in de URL van`serverID.marketo.com`uw Marketo-exemplaar, bijvoorbeeld " ".
    - Ontvang de Munching ID van uw abonnement door naar uw>Munchkin-menu te gaan in het veld "Munchkin `https://{Munchkin ID}.mktorest.com`Account ID" of vanaf het eerste deel van uw Subdomein van de Marketo REST API-host: .
    - Formulier-ID is de id van het formulier Embed Code dat u in stap 7 hebt gemaakt om leads van onze marktplaats te routeren.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Uw aanbieding configureren om leads naar Marketo te verzenden

Wanneer u klaar bent om de leadmanagementgegevens voor uw aanbieding in de publicatieportal te configureren, voert u de volgende stappen uit: 

1. Navigeer naar de pagina **Installatie van aanbieding** voor uw aanbieding.

1. Selecteer **Verbinding maken** onder de sectie Leadbeheer. 

    ![Leadmanagement - Connect](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Selecteer **Marketo** voor de hoofdbestemming in het pop-upvenster Verbindingsdetails.

    ![Een hoofdbestemming kiezen](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Geef de **server-id,** **de munching-account-id**en **formulier-id op**.

    > [!Note]
    > U moet de rest van de aanbieding voltooien en publiceren voordat u leads voor de aanbieding ontvangen. 

5. **E-mail contact** opnemen : e-mails verstrekken aan mensen in uw bedrijf die e-mailmeldingen moeten ontvangen wanneer een nieuwe lead wordt ontvangen. U meerdere e-mails verstrekken door ze te scheiden met een puntkomma.

6. Selecteer **OK**.

   Klik op de knop Valideren om ervoor te zorgen dat u met succes verbinding hebt gemaakt met een leadbestemming. Als dit lukt, heb je een testlead in de hoofdbestemming.

   ![Verbindingsdetails](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
