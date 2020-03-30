---
title: Automatische schaal van een Azure API Management-exemplaar configureren | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u automatisch schaalgedrag instelt voor een Azure API Management-exemplaar.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018265"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Exemplaar van Azure API Management automatisch schalen  

Azure API Management-serviceinstantie kan automatisch worden geschaald op basis van een set regels. Dit gedrag kan worden ingeschakeld en geconfigureerd via Azure Monitor en wordt alleen ondersteund in **standaard-** en **Premium-lagen** van de Azure API Management-service.

Het artikel loopt door het proces van het configureren van autoscale en suggereert een optimale configuratie van autoscale regels.

> [!NOTE]
> API-beheerservice in de **verbruikslaag** wordt automatisch geschaald op basis van het verkeer - zonder dat extra configuratie nodig is.

## <a name="prerequisites"></a>Vereisten

Als u de stappen uit dit artikel wilt volgen, moet u het volgende doen:

+ Een actief Azure-abonnement hebben.
+ Een Azure API Management-exemplaar hebben. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
+ Inzicht in het concept [capaciteit van een Azure API Management-instantie.](api-management-capacity.md)
+ Inzicht in [handmatig schalen proces van een Azure API Management instantie,](upgrade-and-scale.md)met inbegrip van kostengevolgen.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Azure API Management-beperkingen voor automatisch schalen

Bepaalde beperkingen en gevolgen van schaalbeslissingen moeten worden overwogen voordat autoscale gedrag wordt geconfigureerd.

+ Autoscale kan alleen worden ingeschakeld voor **standaard-** en **Premium-lagen** van de Azure API Management-service.
+ Prijslagen geven ook het maximumaantal eenheden voor een serviceexemplaar op.
+ Het schalingproces duurt ten minste 20 minuten.
+ Als de service is vergrendeld door een andere bewerking, mislukt de schalingsaanvraag en wordt deze automatisch opnieuw geprobeerd.
+ In het geval van een service met multiregionale implementaties kunnen alleen eenheden op de **primaire locatie** worden geschaald. Eenheden op andere locaties kunnen niet worden geschaald.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Automatische schaal voor Azure API Management-service inschakelen en configureren

Volg de onderstaande stappen om automatisch schalen voor een Azure API Management-service te configureren:

1. Navigeer naar **Monitor-instantie** in de Azure-portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Selecteer **Automatisch schalen** in het menu aan de linkerkant.

    ![Azure Monitor-bron voor automatisch schalen](media/api-management-howto-autoscale/02.png)

3. Zoek uw Azure API Management-service op basis van de filters in vervolgkeuzemenu's.
4. Selecteer de gewenste Azure API Management-serviceinstantie.
5. Klik in de onlangs geopende sectie op de knop **Automatisch schalen inschakelen.**

    ![Azure Monitor automatisch schalen inschakelen](media/api-management-howto-autoscale/03.png)

6. Klik **in** de sectie Regels op **+ Een regel toevoegen**.

    ![Voegregel voor automatisch schalen van Azure Monitor automatisch](media/api-management-howto-autoscale/04.png)

7. Definieer een nieuwe scale out-regel.

   Een uitschaingsregel kan bijvoorbeeld leiden tot een toevoeging van een Azure API Management-eenheid, wanneer de gemiddelde capaciteitsstatistiek in de afgelopen 30 minuten meer dan 80% bedraagt. De onderstaande tabel biedt configuratie voor een dergelijke regel.

    | Parameter             | Waarde             | Opmerkingen                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Bron van metrische gegevens         | Huidige resource  | Definieer de regel op basis van de huidige Azure API Management-resourcestatistieken.                                                                                                                                                                                                     |
    | *Criteria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Tijdverzameling      | Average           |                                                                                                                                                                                                                                                                                 |
    | Naam van meetwaarde           | Capaciteit          | Capaciteitsstatistiek is een Azure API Management-statistiek die het gebruik van resources van een Azure API Management-instantie weerspiegelt.                                                                                                                                                            |
    | Tijdsintervalstatistieken  | Average           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Groter dan      |                                                                                                                                                                                                                                                                                 |
    | Drempelwaarde             | 80%               | De drempelwaarde voor de statistiek gemiddelde capaciteit.                                                                                                                                                                                                                                 |
    | Duur (in minuten) | 30                | De tijdspanne om de capaciteitsstatistiek te gemiddelde over is specifiek voor gebruikspatronen. Hoe langer de periode is, hoe soepeler de reactie zal zijn - intermitterende pieken zullen minder effect hebben op de scale-out beslissing. Het zal echter ook de scale-out trigger vertragen. |
    | *Actie*              |                   |                                                                                                                                                                                                                                                                                 |
    | Bewerking             | Aantal verhogen met |                                                                                                                                                                                                                                                                                 |
    | Aantal exemplaren        | 1                 | Schaal de instantie Azure API Management uit met 1 eenheid.                                                                                                                                                                                                                          |
    | Afkoelen (minuten)   | 60                | Het duurt minstens 20 minuten voordat de Azure API Management-service is uitgeschaald. In de meeste gevallen voorkomt de afkoelperiode van 60 minuten veel scale-outs.                                                                                                  |

8. Klik **op Toevoegen** om de regel op te slaan.

    ![Azure Monitor-scale-outregel](media/api-management-howto-autoscale/05.png)

9. Klik nogmaals op **+ Een regel toevoegen**.

    Deze keer moet een schaal in regel worden gedefinieerd. Het zal ervoor zorgen dat resources niet worden verspild, wanneer het gebruik van API's afneemt.

10. Definieer een nieuwe schaal in regel.

    Een schaal regel kan bijvoorbeeld leiden tot het verwijderen van een Azure API Management-eenheid, wanneer de gemiddelde capaciteitsstatistiek in de afgelopen 30 minuten lager is dan 35%. De onderstaande tabel biedt configuratie voor een dergelijke regel.

    | Parameter             | Waarde             | Opmerkingen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Bron van metrische gegevens         | Huidige resource  | Definieer de regel op basis van de huidige Azure API Management-resourcestatistieken.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Criteria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tijdverzameling      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Naam van meetwaarde           | Capaciteit          | Dezelfde statistiek als die welke wordt gebruikt voor de uitschalijnregel.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Tijdsintervalstatistieken  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Kleiner dan         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Drempelwaarde             | 35%               | Net als bij de uitschaderegel is deze waarde sterk afhankelijk van de gebruikspatronen van het Azure API Management. |
    | Duur (in minuten) | 30                | Dezelfde waarde als die welke wordt gebruikt voor de uitschalijnregel.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Actie*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Bewerking             | Aantal verlagen met | Tegenover wat werd gebruikt voor de scale out regel.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Aantal exemplaren        | 1                 | Dezelfde waarde als die welke wordt gebruikt voor de uitschalijnregel.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Afkoelen (minuten)   | 90                | Schaal in moet conservatiever zijn dan een schaal uit, dus de afkoelperiode moet langer zijn.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klik **op Toevoegen** om de regel op te slaan.

    ![Azure Monitor-schaal in regel](media/api-management-howto-autoscale/06.png)

12. Stel het **maximum** aantal Azure API Management-eenheden in.

    > [!NOTE]
    > Azure API Management heeft een limiet van eenheden waarnaar een instantie kan worden uitgeschaald. De limiet is afhankelijk van een servicelaag.

    ![Azure Monitor-schaal in regel](media/api-management-howto-autoscale/07.png)

13. Klik op **Opslaan**. Uw automatische weegschaal is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

+ [Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's](api-management-howto-deploy-multi-region.md)
