---
title: Aan de slag met Azure Advisor
description: Aan de slag met Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259628"
---
# <a name="get-started-with-azure-advisor"></a>Aan de slag met Azure Advisor

Meer informatie over hoe u toegang krijgt tot Advisor via de Azure-portal, aanbevelingen krijgen en aanbevelingen uitvoeren.

> [!NOTE]
> Azure Advisor wordt automatisch op de achtergrond uitgevoerd om nieuw gemaakte resources te vinden. Het kan tot 24 uur duren om aanbevelingen te doen over deze bronnen.

## <a name="get-recommendations"></a>Aanbevelingen opdoen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik in het linkerdeelvenster op **Adviseur**.  Als u Advisor niet in het linkerdeelvenster ziet, klikt u op **Alle services**.  Klik in het deelvenster Servicemenu onder **Controleren en beheren**op **Adviseur**. Het dashboard van Advisor wordt weergegeven.

   ![Toegang tot Azure Advisor met behulp van de Azure-portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. In het Advisor-dashboard geeft een samenvatting weer van uw aanbevelingen voor alle geselecteerde abonnementen.  U kunt de abonnementen kiezen waarvoor u aanbevelingen wilt weergeven voor het gebruik van de vervolgkeuzelijst met het abonnementsfilter.

1. Als u aanbevelingen voor een specifieke categorie wilt krijgen, klikt u op een van de tabbladen: **Hoge beschikbaarheid,** **beveiliging**, **prestaties**of **kosten**. 

   ![Azure Advisor-dashboard](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Aanbevelingsdetails opvragen en een oplossing implementeren

U een aanbeveling selecteren in Advisor om aanvullende details te bekijken, zoals de aanbevelingsacties en de beïnvloede resources, en om de oplossing voor de aanbeveling te implementeren.  

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

1. Selecteer een aanbevelingscategorie om de lijst met aanbevelingen in die categorie weer te geven of selecteer het tabblad **Alles** om al uw aanbevelingen weer te geven.

1. Klik op een aanbeveling die u in detail wilt bekijken.

1. Bekijk de informatie over de aanbeveling en de bronnen waarop de aanbeveling van toepassing is.

1. Klik op de **aanbevolen actie** om de aanbeveling uit te voeren.

## <a name="filter-recommendations"></a>Filteraanbevelingen

U aanbevelingen filteren om in te zoomen op wat voor u het belangrijkst is.  U filteren op abonnements-, resourcetype of aanbevelingsstatus.  

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

1. Gebruik de vervolgkeuzeresultaten op het Dashboard Advisor om te filteren op abonnement, resourcetype of aanbevelingsstatus.

    ![Criteria voor zoekfilter van adviseurs](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Aanbevelingen uitstellen of afwijzen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

1. Navigeer naar de aanbeveling die u wilt uitstellen of verwijderen.

1. Klik op de aanbeveling.

1. Klik **op Uitstellen**. 

1. Geef een uitstelperiode op of selecteer **Nooit** verwijderen van de aanbeveling.

## <a name="exclude-subscriptions-or-resource-groups"></a>Abonnementen of resourcegroepen uitsluiten

U resourcegroepen of abonnementen hebben waarvoor u geen aanbevelingen van Advisor wilt ontvangen, zoals 'testbronnen'.  U Advisor configureren om alleen aanbevelingen te genereren voor specifieke abonnementen en resourcegroepen.

> [!NOTE]
> Als u een abonnement of resourcegroep wilt opnemen of uitsluiten van Advisor, moet u eigenaar zijn van een abonnement.  Als u niet over de vereiste machtigingen voor een abonnement of resourcegroep beschikt, is de optie om deze op te nemen of uit te sluiten uitgeschakeld in de gebruikersinterface.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

1. Klik **op Configureren** op de actiebalk.

1. Schakel abonnementen of resourcegroepen waarvoor u geen aanbevelingen van Advisor wilt ontvangen, uit.

    ![Voorbeeld van advisor configureren resources](./media/advisor-get-started/advisor-configure-resources.png)

1. Klik op de knop **Toepassen**.

## <a name="configure-low-usage-vm-recommendation"></a>VM-aanbeveling voor laag gebruik configureren

Met deze procedure configureert u de gemiddelde CPU-gebruiksregel voor de aanbeveling voor virtuele machines met een laag gebruik.

Advisor controleert het gebruik van uw virtuele machine gedurende 7 dagen en identificeert vervolgens virtuele machines met een laag gebruik. Virtuele machines worden beschouwd als een laag gebruik als hun CPU-gebruik is 5% of minder en hun netwerkgebruik is minder dan 2% of als de huidige werkbelasting kan worden opgevangen door een kleinere virtuele machine grootte.

Als u agressiever wilt zijn bij het identificeren van virtuele machines met een laag gebruik, u de gemiddelde CPU-gebruiksregel per abonnementsbasis aanpassen.  De CPU-gebruiksregel kan worden ingesteld op 5%, 10%, 15% of 20%.

> [!NOTE]
> Als u de gemiddelde CPU-gebruiksregel wilt aanpassen voor het identificeren van virtuele machines met een laag gebruik, moet u eigenaar zijn *van*een abonnement.  Als u niet over de vereiste machtigingen voor een abonnement of resourcegroep beschikt, wordt de optie om deze op te nemen of uit te sluiten uitgeschakeld in de gebruikersinterface. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

1. Klik **op Configureren** op de actiebalk.

1. Klik op het tabblad **Regels.**

1. Selecteer de abonnementen waarvoor u de gemiddelde CPU-gebruiksregel wilt aanpassen en klik op **Bewerken**.

1. Selecteer de gewenste gemiddelde CPU-gebruikswaarde en klik op **Toepassen**.

1. Klik **op Aanbevelingen vernieuwen** om uw bestaande aanbevelingen bij te werken om de nieuwe regel voor het gemiddelde CPU-gebruik te gebruiken. 

   ![Voorbeeld van het configureren van aanbevelingsregels door adviseur](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Aanbevelingen downloaden

Met Advisor u een samenvatting van uw aanbevelingen downloaden.  U uw aanbevelingen downloaden als PDF-bestand of CSV-bestand.  Het downloaden van uw aanbevelingen stelt u in staat om eenvoudig te delen met uw collega's of uw eigen analyse uit te voeren bovenop de aanbevelingsgegevens.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

1. Klik **op Downloaden als CSV** of Download als **PDF** op de actiebalk.

De downloadoptie respecteert alle filters die u hebt toegepast op het Advisor-dashboard.  Als u de downloadoptie selecteert tijdens het bekijken van een specifieke aanbevelingscategorie of aanbeveling, bevat het gedownloade overzicht alleen informatie voor die categorie of aanbeveling. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor:

- [Inleiding tot Azure Advisor](advisor-overview.md)
- [Aanbevelingen voor hoge beschikbaarheid](advisor-high-availability-recommendations.md)
- [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
- [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
- [Aanbevelingen voor kosten van adviseur](advisor-performance-recommendations.md)
