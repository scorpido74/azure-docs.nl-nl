---
title: Beheer van leads voor Sales Force configureren | Azure Marketplace
description: Configureer het beheer van leads voor Sales Force voor Azure Marketplace-klanten.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812155"
---
# <a name="configure-lead-management-for-salesforce"></a>Beheer van leads voor Sales Force configureren

In dit artikel wordt beschreven hoe u uw Sales Force-systeem zo kunt instellen dat verkoop leads van uw Marketplace-aanbieding worden verwerkt.

## <a name="set-up-your-salesforce-system"></a>Uw Sales Force-systeem instellen

1. Meld u aan bij Sales Force.
2. Als u gebruikmaakt van de Sales Force-belichtings ervaring.
    1. Selecteer **Setup** van Sales Force-start pagina.
    Setup van ![Sales Force](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Ga op de pagina Setup naar de linkernavigatiebalk op basis van de **Hulpprogram ma's voor het platform-> onderdelen-> marketing-> Web-to-lead**.
    ![Sales Force-](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png) voor webtoepassingen

3. Als u de klassieke Sales Force-ervaring gebruikt:
    1. Selecteer **Setup** van Sales Force-start pagina.
    Setup van ![Sales Force](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Ga op de pagina Setup naar de linkernavigatiebalk om **> aanpassen-> leads-> Web-to-lead**.
    ![Sales Force-klassiek, webto-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

De overige instructies zijn hetzelfde, ongeacht de Sales Force-ervaring die u gebruikt.

4. Selecteer op de **pagina voor het instellen van de web-naar-lead**de knop voor het maken van een **Web-naar-lead-formulier** .
5. Selecteer op de **pagina Web**-to-lead instellen de optie **Web-naar-lead-formulier maken**.
    ![Sales Force-installatie van webtoepassingen](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Zorg er in het **formulier een web-naar-lead maken**voor dat `the Include reCAPTCHA in HTML` instelling is uitgeschakeld en selecteer **genereren**. 
    ![Sales Force: Maak een web-to-lead-formulier](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Er wordt een aantal HTML-tekst weer gegeven. Zoek naar de tekst "OID" en kopieer de **OID-waarde** uit de HTML-tekst (alleen de tekst tussen aanhalings tekens) en sla deze op. Plak deze waarde in het veld **organisatie-id** op de portal voor publiceren.
    ![Sales Force: Maak een web-to-lead-formulier](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Geselecteerd **.**

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Uw aanbieding configureren voor het verzenden van leads naar Sales Force

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, volgt u de onderstaande stappen:

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
1. Selecteer **verbinding maken** in het gedeelte Lead beheer.
    ![Lead beheer-verbinding maken](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Selecteer in het pop-upvenster verbindings Details de optie **Sales Force** voor het doel van de **lead** en plak het formulier `oid` van web-naar-lead dat u hebt gemaakt door eerdere stappen in het veld **organisatie-id** te volgen.

1. Selecteer **Opslaan**. 

    >[!Note]
    >U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.

    ![Verbindings Details: Kies een doel voor de lead](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Verbindings Details: Kies een doel voor de lead](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)