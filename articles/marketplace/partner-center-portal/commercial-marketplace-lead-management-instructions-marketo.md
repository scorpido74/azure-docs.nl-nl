---
title: Lead beheer in Marketo-micro soft Commercial Marketplace
description: Meer informatie over het gebruik van een Marketo CRM-systeem voor het beheer van leads van Microsoft AppSource en Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 1f3a097629f8c5f4a3f0ecefa5ee50f2d3e62162
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789875"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Marketo gebruiken om leads voor commerciële Marketplace te beheren

In dit artikel wordt beschreven hoe u uw Marketo CRM-systeem zo instelt dat verkoop leads van uw aanbiedingen in Microsoft AppSource en Azure Marketplace kunnen worden verwerkt.

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

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecteer uw aanbieding en ga naar het tabblad **installatie** van de aanbieding.

1. Selecteer in het gedeelte **lead beheer** de optie **verbinding maken**. 

    ![De knop verbinding maken in de sectie Lead beheer](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Selecteer in het pop-upvenster **verbindings Details** de optie **marketo** voor de **doel locatie**van de lead.

    ![Kies een doel voor een potentiële klant](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Geef de **Server-id**, de **Munchkin-account-id**en de **formulier-id**op.

    > [!NOTE]
    > U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen. 

1. Voer onder **contact-e-mail**de e-mail adressen in voor personen in uw bedrijf die e-mail meldingen moeten ontvangen wanneer er een nieuwe lead wordt ontvangen. U kunt meerdere e-mail adressen opgeven door deze te scheiden met een punt komma.

1. Selecteer **OK**.

   Selecteer **valideren**om ervoor te zorgen dat u verbinding hebt gemaakt met een doel van de lead. Als dat lukt, hebt u een test lead in de doel locatie van de lead.

   ![Pop-upvenster voor verbindings Details](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
