---
title: Beheer van leads in de Market configureren | Azure Marketplace
description: Configureer het beheer van leads voor de klanten van Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133654"
---
# <a name="configure-lead-management-in-marketo"></a>Beheer van leads in de Market configureren

In dit artikel wordt beschreven hoe u uw Marketo CRM-systeem zo instelt dat verkoop leads van uw aanbieding voor commerciële Marketplace kunnen worden verwerkt.

## <a name="set-up-your-marketo-crm-system"></a>Uw Marketo CRM-systeem instellen

1. Meld u aan bij Marketo.

1. Selecteer **Design Studio**.

    ![Marketo-ontwerp studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selecteer **nieuw formulier**.

    ![Nieuw formulier voor marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Vul de vereiste velden in het dialoog venster **nieuw formulier** in en selecteer vervolgens **maken**.

    ![Marketo-nieuw formulier maken](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Selecteer op de pagina **veld Details** de optie **volt ooien**.

    ![Formulier marketo-afwerking](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Goed keuren en sluiten.

1. Selecteer **code insluiten**op het tabblad **MarketplaceLeadBackend** . 

    ![Marketo-invoeg code](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Met marketo-insluit code wordt code weer gegeven die vergelijkbaar is met het volgende voor beeld.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Kopieer de waarden voor de volgende velden die worden weer gegeven in het formulier voor de insluit code. U gebruikt deze waarden om uw aanbieding te configureren voor het ontvangen van leads in de volgende stap. Gebruik het volgende voor beeld als richt lijn voor het ophalen van de Id's die u nodig hebt via het code voorbeeld van de Marketo-invoeg toepassing.

    - Server-ID = **ys12**
    - Munchkin-ID = **123-PQR-789**
    - Formulier-ID = **1179**

    Een andere manier om deze waarden te berekenen:

    - De server-ID bevindt zich in de URL van uw Marketo- `serverID.marketo.com`exemplaar, bijvoorbeeld.
    - Als u de Munchkin-id van uw abonnement wilt **Admin** > ophalen, gaat u naar het**Munchkin** menu van de **Munchkin-account-id** of vanuit het eerste deel van uw Market- `https://{Munchkin ID}.mktorest.com`rest API host-subdomein:.
    - De formulier-ID is de ID van het insluit code formulier dat u in stap 7 hebt gemaakt om leads van de Marketplace te routeren.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Uw aanbieding configureren voor het verzenden van leads naar Marketo

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, voert u de volgende stappen uit. 

1. Ga naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.

1. Selecteer **verbinding maken** in het gedeelte **lead beheer** . 

    ![De knop verbinding maken in de sectie Lead beheer](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Selecteer in het pop-upvenster **verbindings Details** de optie **marketo** voor de **doel locatie**van de lead.

    ![Kies een doel voor een potentiële klant](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Geef de **Server-id**, de **Munchkin-account-id**en de **formulier-id**op.

    > [!NOTE]
    > U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen. 

1. Voer onder **contact-e-mail**de e-mail adressen in voor personen in uw bedrijf die e-mail meldingen moeten ontvangen wanneer er een nieuwe lead wordt ontvangen. U kunt meerdere e-mail adressen opgeven door deze te scheiden met een punt komma.

1. Selecteer **OK**.

   Selecteer de knop **valideren** om ervoor te zorgen dat u verbinding hebt gemaakt met een doel van een lead. Als dat lukt, hebt u een test lead in de doel locatie van de lead.

   ![Pop-upvenster voor verbindings Details](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
