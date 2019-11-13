---
title: Functies voor klant gegevens aanvragen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over functies voor het aanvragen van klant gegevens in Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: d134c7cbe220e6aa6b1471b50aa23e17d09734d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007030"
---
# <a name="summary-of-customer-data-request-features"></a>Overzicht van de klant functies voor aanvraag

Azure Time Series Insights is een beheerde Cloud service met opslag-, analyse-en visualisatie onderdelen waarmee u gemakkelijk miljarden gebeurtenissen tegelijkertijd kunt opnemen, opslaan, verkennen en analyseren.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Voor het weer geven, exporteren en verwijderen van persoonlijke gegevens die kunnen worden onderhevig aan een aanvraag voor een gegevens certificaat, kan een Azure Time Series Insights Tenant beheerder de Azure Portal of de REST-Api's gebruiken. Met behulp van de Azure Portal voor het uitvoeren van aanvragen voor service gegevens, biedt een minder complexe methode om deze bewerkingen uit te voeren die de meeste gebruikers prefereren.

## <a name="identifying-customer-data"></a>Klant gegevens identificeren

Azure Time Series Insights persoons gegevens worden beschouwd als gegevens die zijn gekoppeld aan beheerders en gebruikers van Time Series Insights. Time Series Insights slaat de Azure Active Directory object-ID van de gebruikers die toegang hebben tot de omgeving. In het Azure Portal worden e-mail adressen van gebruikers weer gegeven, maar deze e-mail adressen worden niet opgeslagen in Time Series Insights. ze worden dynamisch gezocht met behulp van de Azure Active Directory object-ID in Azure Active Directory.

## <a name="deleting-customer-data"></a>Verwijderen van klantgegevens

Een Tenant beheerder kan klant gegevens verwijderen met behulp van de Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Voordat u echter klant gegevens verwijdert via de portal, moet u het toegangs beleid van de gebruiker verwijderen uit de Time Series Insights omgeving binnen de Azure Portal. Zie [granting Data Access to a time series Insights environment using Azure Portal](time-series-insights-data-access.md)(Engelstalig) voor meer informatie.

U kunt ook delete-bewerkingen uitvoeren op toegangs beleid met behulp van de REST API. Zie [Access policies-Delete (](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete)Engelstalig) voor meer informatie.

Time Series Insights is geïntegreerd met de Blade beleid in de Azure Portal. Zowel Time Series Insights als de Blade beleid kunt u gebruiken om gebruikers gegevens weer te geven, te exporteren en te verwijderen die zijn opgeslagen in de service. Alle verwijderings acties die zijn uitgevoerd op de Blade beleid van de Azure Portal resulteert in het verwijderen van gebruikers gegevens in Time Series Insights. Als een gebruiker bijvoorbeeld een opgeslagen persoonlijke query heeft, wordt die query permanent verwijderd uit de Time Series Insights Explorer. Als de gebruiker een opgeslagen gedeelde query heeft, blijft de query behouden, maar worden de gebruikers gegevens definitief verwijderd. De volgende notitie bevat instructies voor het uitvoeren van deze taken.

## <a name="exporting-customer-data"></a>Exporteren van gegevens van de klant

Net als bij het verwijderen van gegevens kan een Tenant beheerder gegevens die zijn opgeslagen in Time Series Insights bekijken en exporteren vanuit de Blade beleid in de Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Als u een Tenant beheerder bent, kunt u het beleid voor gegevens toegang weer geven in de Time Series Insights omgeving van de Azure Portal. Zie [granting Data Access to a time series Insights environment using Azure Portal](time-series-insights-data-access.md)(Engelstalig) voor meer informatie.

Het is ook mogelijk om export bewerkingen uit te voeren op toegangs beleid met behulp van de bewerking ' lijst op omgeving ' in de gegeven REST API. Zie [toegangs beleid-lijst per omgeving](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment)voor meer informatie.

## <a name="to-delete-data-stored-within-time-series-insights"></a>Gegevens verwijderen die zijn opgeslagen in Time Series Insights

Persoonlijke gegevens kunnen worden omgezet in Time Series Insights opslag, een ander scenario van gebruikers-en beheerder gegevens. Als u rekening houdt met de gegevens die zijn opgeslagen in Time Series Insights als persoons gegevens, kunt u die gegevens exporteren en verwijderen met behulp van de volgende stappen:

**Gegevens weer geven en exporteren**

Als u gegevens wilt weer geven en exporteren die zijn opgeslagen in Time Series Insights, moet u zoeken naar die gegevens. U kunt de Time Series Insights Explorer-of Time Series Insights query-Api's gebruiken om gegevens weer te geven en te exporteren. Als u gegevens wilt weer geven en exporteren met behulp van de Time Series Insights Explorer, zoekt u eerst naar de betreffende gebruikers gegevens. Klik na het zoeken met de rechter muisknop op de grafiek en selecteer **gebeurtenissen verkennen**. Het raster gebeurtenissen wordt weer gegeven en toont opties voor het exporteren van de gegevens als CSV en JSON.

Zie [Azure time series Insights Explorer](time-series-insights-explorer.md)voor meer informatie.

**Gegevens verwijderen**

Op dit moment ondersteunt Time Series Insights geen gedetailleerde verwijdering van gegevens. Time Series Insights biedt echter de mogelijkheid om klant gegevens te verwijderen die zijn opgeslagen in Time Series Insights door het configureren van Bewaar beleid. U kunt de Bewaar periode van de hele Time Series Insights omgeving aanpassen tot een wille keurig aantal dagen om uw verwijderings vereisten te ondersteunen.

Zie [retentie configureren in time series Insights](time-series-insights-how-to-configure-retention.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het verlenen van toegang tot gegevens aan uw Azure time series Insights-omgeving](./time-series-insights-data-access.md).

* Bekijk de [Azure time series Insights Explorer](time-series-insights-explorer.md).

* Meer informatie over het [configureren van Bewaar periode in time series Insights](time-series-insights-how-to-configure-retention.md).