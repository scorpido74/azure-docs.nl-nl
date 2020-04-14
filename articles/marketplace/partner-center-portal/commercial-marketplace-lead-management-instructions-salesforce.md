---
title: Leadbeheer configureren voor Salesforce | Azure Marketplace
description: Configureer leadbeheer op Salesforce voor Azure Marketplace-klanten.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263004"
---
# <a name="configure-lead-management-for-salesforce"></a>Leadmanagement configureren voor Salesforce

In dit artikel wordt beschreven hoe u uw Salesforce-systeem instelt om verkoopleads te verwerken vanuit uw commerciële marktplaatsaanbieding.

> [!Note]
> Marketplace biedt geen ondersteuning voor vooraf ingevulde lijsten, zoals een lijst met waarden voor het veld **Land.** Zorg ervoor dat er geen lijsten zijn ingesteld voordat u verdergaat. U ook een [HTTPS-eindpunt](./commercial-marketplace-lead-management-instructions-https.md) of een [Azure-tabel](./commercial-marketplace-lead-management-instructions-azure-table.md) configureren om leads te ontvangen.

## <a name="set-up-your-salesforce-system"></a>Uw Salesforce-systeem instellen

1. Meld u aan bij Salesforce.
2. Als u gebruik maakt van de Salesforce-verlichtingservaring.
    1. Selecteer **Setup** op de startpagina van Salesforce.
    ![Salesforce-installatie](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Navigeer vanaf de pagina Setup via de linkernavigatie naar **Platform Tools->Feature Settings->Marketing->Web-to-Lead.**

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Als u de Salesforce Classic-ervaring gebruikt:
    1. Selecteer **Setup** op de startpagina van Salesforce.
    ![Klassieke Salesforce-setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Navigeer vanaf de pagina Setup via de linkernavigatie naar **Build->Customize->Leads->Web-to-Lead.**

        ![Salesforce klassieke web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

De rest van de instructies zijn hetzelfde, ongeacht welke Salesforce-ervaring u gebruikt.

4. Selecteer op de **pagina Web-to-Lead Setup**de knop **Web-to-Lead-formulier maken.**
5. Selecteer **Web-to-Lead-formulier**maken **bij web-to-lead-instelling**.
    ![Salesforce - Web-to-Lead Setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Controleer in **het formulier Een web-naar-lead maken**of de `the Include reCAPTCHA in HTML` instelling niet is ingeschakeld en selecteer **Genereren**. 
    ![Salesforce - Een web-naar-leadformulier maken](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. U krijgt wat HTML-tekst te zien. Zoek naar de tekst "oid" en kopieer de **oid waarde** uit de HTML-tekst (alleen de tekst tussen aanhalingstekens) en sla deze op. U plakt deze waarde in het veld **Organisatie-id** op de publicatieportal.
    ![Salesforce - Een web-naar-leadformulier maken](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **Geselecteerd voltooid**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Uw aanbieding configureren om leads naar Salesforce te verzenden

Wanneer u klaar bent om de leadmanagementgegevens voor uw aanbieding in de publicatieportal te configureren, volgt u de volgende stappen:

1. Navigeer naar de pagina **Installatie van aanbieding** voor uw aanbieding.
1. Selecteer **Verbinding maken** onder de sectie Leadbeheer.
    ![Leadmanagement - Connect](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Selecteer **Salesforce** in het pop-upvenster Verbindingsdetails voor de `oid` **leadbestemming** en plak in het formulier van web-naar-lead dat u hebt gemaakt door eerdere stappen in het veld **Organisatie-id** te volgen.

1. **E-mail contact** opnemen : e-mails verstrekken aan mensen in uw bedrijf die e-mailmeldingen moeten ontvangen wanneer een nieuwe lead wordt ontvangen. U meerdere e-mails verstrekken door ze te scheiden met puntkomma.

1. Selecteer **Ok**.

Klik op de knop Valideren om ervoor te zorgen dat u met succes verbinding hebt gemaakt met een leadbestemming. Als dit lukt, heb je een testlead in de hoofdbestemming.

>[!Note]
>U moet de rest van de aanbieding voltooien en publiceren voordat u leads voor de aanbieding ontvangen.

![Verbindingsgegevens - Kies een leadbestemming](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Verbindingsgegevens - Kies een leadbestemming](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
