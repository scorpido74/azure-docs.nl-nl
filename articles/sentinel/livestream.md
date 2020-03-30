---
title: Gebruik jachtlivestream in Azure Sentinel om bedreigingen te detecteren| Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de livestream van de jacht in Azure Sentinel gebruiken om gegevens bij te houden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582123"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Gebruik jachtlivestream in Azure Sentinel om bedreigingen te detecteren

> [!IMPORTANT]
> Hunting livestream in Azure Sentinel is momenteel in openbare preview en geleidelijk uitrollen naar huurders.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.


Gebruik de jachtlivestream om interactieve sessies te maken waarmee u nieuw gemaakte query's testen wanneer gebeurtenissen plaatsvinden, meldingen krijgen van de sessies wanneer een overeenkomst wordt gevonden en indien nodig onderzoeken kunnen starten. U snel een livestreamsessie maken met behulp van een Log Analytics-query.

- **Nieuw gemaakte query's testen wanneer gebeurtenissen zich voordoen**
    
    U query's testen en aanpassen zonder conflicten aan de huidige regels die actief worden toegepast op gebeurtenissen. Nadat u hebt bevestigd dat deze nieuwe query's werken zoals verwacht, is het eenvoudig om ze te promoten bij aangepaste waarschuwingsregels door een optie te selecteren die de sessie naar een waarschuwing tilt.

- **Ontvang een melding wanneer er bedreigingen optreden**
    
    U bedreigingsgegevensfeeds vergelijken met geaggregeerde logboekgegevens en een melding ontvangen wanneer een overeenkomst plaatsvindt. Bedreigingsgegevensfeeds zijn doorlopende gegevensstromen die gerelateerd zijn aan potentiële of huidige bedreigingen, dus de melding kan wijzen op een potentiële bedreiging voor uw organisatie. Maak een livestreamsessie in plaats van een aangepaste waarschuwingsregel wanneer u op de hoogte wilt worden gesteld van een mogelijk probleem zonder de overheadkosten van het handhaven van een aangepaste waarschuwingsregel.

- **Start onderzoeken**
    
    Als er een actief onderzoek is waarbij een actief zoals een host of gebruiker betrokken is, u specifieke (of enige) activiteit in de logboekgegevens bekijken wanneer deze zich op dat actief voordoet. U een melding krijgen wanneer die activiteit plaatsvindt.


## <a name="create-a-livestream-session"></a>Een livestreamsessie maken

U een livestreamsessie maken op basis van een bestaande jachtquery of uw sessie helemaal opnieuw maken.

1. Navigeer in de Azure-portal naar **Sentinel** > **Threat management** > **Hunting.**

2. Ga als volgt te werk om een livestreamsessie te maken op basis van een jachtquery:
    
    1. Zoek op het tabblad **Query's** de te gebruiken jachtquery.
    2. Klik met de rechtermuisknop op de query en selecteer **Toevoegen aan livestream**. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![Livestream-sessie maken vanuit Azure Sentinel-jachtquery](./media/livestream/livestream-from-query.png)

3. Ga als volgt te werk om een livestreamsessie te maken: 
    
    1. Het tabblad **Livestream** selecteren
    2. Selecteer **Ga naar livestream**.
    
4. In **Livestream** het livestream-deelvenster:
    
    - Als u een livestream vanuit een query hebt gestart, controleert u de query en voert u eventuele wijzigingen aan die u wilt aanbrengen.
    - Als je vanaf nul bent begonnen met livestream, maak je je zoekopdracht. 

5. Selecteer **Afspelen** op de opdrachtbalk.
    
    De statusbalk onder de opdrachtbalk geeft aan of uw livestreamsessie wordt uitgevoerd of onderbroken. In het volgende voorbeeld wordt de sessie uitgevoerd:
    
    > [!div class="mx-imgBorder"]
    > ![livestreamsessie maken vanuit Azure Sentinel-jacht](./media/livestream/livestream-session.png)

6. Selecteer **Opslaan** op de opdrachtbalk.
    
    Tenzij u **Onderbreken**selecteert, blijft de sessie worden uitgevoerd totdat u bent afgemeld bij de Azure-portal.

## <a name="view-your-livestream-sessions"></a>Bekijk je livestreamsessies

1. Navigeer in de Azure-portal naar het**Hunting** > tabblad **Hunting** > **Livestream** van Sentinel**Threat Management.** > 

2. Selecteer de livestreamsessie die u wilt bekijken of bewerken. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![livestreamsessie maken vanuit Azure Sentinel-jachtquery](./media/livestream/livestream-tab.png)
    
    De geselecteerde livestreamsessie wordt geopend zodat je spelen, pauzeren, bewerken enzovoort.

## <a name="receive-notifications-when-new-events-occur"></a>Meldingen ontvangen wanneer er nieuwe gebeurtenissen plaatsvinden

Omdat livestreammeldingen voor nieuwe gebeurtenissen Azure-portalmeldingen gebruiken, ziet u deze meldingen wanneer u de Azure-portal gebruikt. Bijvoorbeeld:

![Azure-portalmelding voor livestream](./media/livestream/notification.png)

Selecteer de melding om het **deelvenster Livestream te** openen.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Een livestreamsessie naar een waarschuwing verheffen

Je een livestreamsessie promoten bij een nieuwe waarschuwing door Omhoog te selecteren **om te waarschuwen** in de opdrachtbalk van de betreffende livestreamsessie:

> [!div class="mx-imgBorder"]
> ![Livestreamsessie verheffen tot een waarschuwing](./media/livestream/elevate-to-alert.png)

Met deze actie wordt de wizard regelcreatie geopend, die vooraf is gevuld met de query die is gekoppeld aan de livestreamsessie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel heb je geleerd hoe je jachtlivestream gebruiken in Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief op zoek naar bedreigingen](hunting.md)
- [Notitieblokken gebruiken om geautomatiseerde jachtcampagnes uit te voeren](notebooks.md)
