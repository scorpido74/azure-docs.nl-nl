---
title: Functies voor klantgegevensaanvragen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over functies voor het aanvragen van klantgegevens in Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/14/2020
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 64660e497bb1765b649e00b07fdb5db8c05910f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368693"
---
# <a name="summary-of-customer-data-request-features"></a>Overzicht van de functies voor het aanvragen van klantgegevens

Azure Time Series Insights is een beheerde cloudservice met opslag-, analyse- en visualisatiecomponenten waarmee u eenvoudig miljarden gebeurtenissen tegelijk innemen, opslaan, verkennen en analyseren.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Als u persoonlijke gegevens wilt bekijken, exporteren en verwijderen die mogelijk onderhevig zijn aan een verzoek van een betrokkene, kan een tenantbeheerder van Azure Time Series Insights de Azure-portal of de REST-API's gebruiken. Het gebruik van de Azure-portal voor serviceverzoeken van gegevensdienaars biedt een minder complexe methode om deze bewerkingen uit te voeren die de meeste gebruikers verkiezen.

## <a name="identifying-customer-data"></a>Klantgegevens identificeren

Azure Time Series Insights beschouwt persoonlijke gegevens als gegevens die zijn gekoppeld aan beheerders en gebruikers van Time Series Insights. Time Series Insights slaat de Azure Active Directory-object-ID op van gebruikers met toegang tot de omgeving. De Azure-portal geeft e-mailadressen van gebruikers weer, maar deze e-mailadressen worden niet opgeslagen in Time Series Insights, ze worden dynamisch opgezocht met behulp van de Azure Active Directory-object-ID in Azure Active Directory.

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen

Een tenantbeheerder kan klantgegevens verwijderen via de Azure-portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Voordat u echter klantgegevens via de portal verwijdert, moet u het toegangsbeleid van de gebruiker verwijderen uit de Time Series Insights-omgeving binnen de Azure-portal. Lees Voor meer informatie [toegang tot gegevens tot een Time Series Insights-omgeving met Azure-portal.](time-series-insights-data-access.md)

U ook verwijderbewerkingen uitvoeren voor toegangsbeleid met behulp van de REST API. Lees [Toegangsbeleid voor](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete)meer informatie .

Time Series Insights is geïntegreerd met het policy blade in de Azure-portal. Met De Inzichten van de tijdreeks en het blad van het Beleid u gebruikersgegevens bekijken, exporteren en verwijderen die binnen de dienst zijn opgeslagen. Elke verwijderactie die wordt uitgevoerd in het beleidsblad van de Azure-portal, resulteert in het verwijderen van gebruikersgegevens in Time Series Insights. Als een gebruiker bijvoorbeeld een opgeslagen persoonlijke query heeft, wordt die query permanent verwijderd uit de Verkenner timeseries Insights. Als de gebruiker een opgeslagen gedeelde query heeft, blijft de query bestaan, maar wordt de gebruikersgegevens permanent verwijderd. De volgende notitie bevat instructies over het uitvoeren van deze taken.

## <a name="exporting-customer-data"></a>Klantgegevens exporteren

Net als bij het verwijderen van gegevens kan een tenantbeheerder gegevens bekijken en exporteren die zijn opgeslagen in time series insights vanuit het beleidsblad in de Azure-portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Als u een tenantbeheerder bent, u het beleid voor gegevenstoegang bekijken in de Time Series Insights-omgeving in de Azure-portal. Lees Voor meer informatie [toegang tot gegevens tot een Time Series Insights-omgeving met Azure-portal.](time-series-insights-data-access.md)

Het is ook mogelijk om exportbewerkingen uit te voeren op toegangsbeleid met behulp van de bewerking 'lijst op omgeving' in de meegeleverde REST API. Lees [Toegangsbeleid voor](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment)meer informatie op omgeving .

## <a name="to-delete-data-stored-within-time-series-insights"></a>Gegevens verwijderen die zijn opgeslagen in Inzichten uit de time-serie

Persoonlijke gegevens kunnen hun weg vinden in de opslag van Time Series Insights, een ander scenario dan gebruikers- en beheerdersgegevens. Als u de gegevens die zijn opgeslagen in Time Series Insights als persoonlijke gegevens beschouwt, u die gegevens exporteren en verwijderen met de volgende stappen:

**Gegevens weergeven en exporteren**

Als u gegevens wilt bekijken en exporteren die zijn opgeslagen in Time Series Insights, moet u naar die gegevens zoeken. U de Time Series Insights explorer of Time Series Insights query API's gebruiken om gegevens weer te geven en te exporteren. Als u gegevens wilt bekijken en exporteren met behulp van de Time Series Insights-verkenner, zoekt u eerst naar de betreffende gebruikersgegevens. Na het zoeken klikt u met de rechtermuisknop op de grafiek en selecteert **u Gebeurtenissen verkennen**. Het gebeurtenisraster wordt weergegeven en biedt opties om de gegevens als CSV en JSON te exporteren.

Lees [Azure Time Series Insights explorer](time-series-insights-explorer.md)voor meer informatie.

**Gegevens verwijderen**

Op dit moment biedt Time Series Insights geen ondersteuning voor het verwijderen van gegevens. Time Series Insights biedt echter de mogelijkheid om klantgegevens te verwijderen die zijn opgeslagen in Time Series Insights door bewaarbeleid te configureren. U de bewaarperiode van de gehele Time Series Insights-omgeving aanpassen aan een aantal dagen om uw verwijderingsvereisten te ondersteunen.

Lees Voor meer informatie [Het configureren van retentie in Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Volgende stappen

* Lees meer over [het verlenen van gegevenstoegang tot uw Azure Time Series Insights-omgeving.](./time-series-insights-data-access.md)

* Bekijk de [Azure Time Series Insights explorer](time-series-insights-explorer.md).

* Meer informatie over [het configureren van retentie in Time Series Insights](time-series-insights-how-to-configure-retention.md).