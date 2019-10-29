---
title: Beveiliging configureren voor toegang tot en beheer van Azure Time Series Insights preview | Microsoft Docs
description: In dit artikel wordt beschreven hoe u beveiliging en machtigingen configureert als beheer toegangs beleid en beleid voor gegevens toegang om Azure Time Series Insights preview te beveiligen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3d835916ab1a9d99149800a50690876080a8c5fd
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990153"
---
# <a name="grant-data-access-to-an-environment"></a>Gegevens toegang verlenen tot een omgeving

In dit artikel worden de twee typen Azure Time Series Insights preview-toegangs beleid beschreven.

## <a name="sign-in-to-time-series-insights"></a>Aanmelden bij Time Series Insights

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zoek uw Time Series Insights omgeving. Voer `Time Series` in het **zoekvak** in. Selecteer de **Time Series-omgeving** in de zoek resultaten.
1. Selecteer uw Time Series Insights-omgeving in de lijst.

## <a name="grant-data-access"></a>Gegevenstoegang verlenen

Volg deze stappen om toegang tot gegevens toe te kennen voor een gebruikers-principal.

1. Selecteer **beleid voor gegevens toegang**en selecteer **+ toevoegen**.

    [![Data-Access-One](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Kies **gebruiker selecteren**. Zoek naar de gebruikers naam of het e-mail adres om de gebruiker te zoeken die u wilt toevoegen. Selecteer **selecteren** om de selectie te bevestigen.

    [Data-Access![-twee](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Kies **rol selecteren**. Kies de juiste Access-rol voor de gebruiker:

    * Selecteer **Inzender** als u wilt toestaan dat de gebruiker referentie gegevens kan wijzigen en opgeslagen query's en perspectieven kan delen met andere gebruikers van de omgeving.

    * Als dat niet het geval is, selecteert u **lezer** om de gebruiker in staat te stellen gegevens in de omgeving op te vragen en persoonlijke, niet-gedeelde query's in de omgeving op te slaan.

   Selecteer **OK** om de gewenste rol te bevestigen.

    [![gegevens-toegang-drie](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Selecteer **OK** op de pagina **gebruikersrol selecteren** .

    [![gegevens-Access-vier](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Controleer of de pagina **Data Access policies** de gebruikers en de rollen voor elke gebruiker bevat.

    [![gegevens toegang-vijf](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Gast toegang bieden vanuit een andere AAD-Tenant

`Guest` is geen beheer functie. Het is een term die wordt gebruikt voor een account dat van de ene Tenant naar de andere wordt uitgenodigd. Nadat het gast account is uitgenodigd voor de directory van de Tenant, kan dit hetzelfde toegangs beheer hebben als een ander account. U kunt beheer toegang tot een Time Series Insights omgeving verlenen met behulp van de Blade Access Control (IAM). U kunt ook toegang tot de gegevens in de omgeving verlenen via de Blade Data Access-beleids regels. Lees [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)voor meer informatie over Azure Active Directory (Azure AD).

Volg deze stappen om gast toegang tot een Time Series Insights omgeving toe te kennen aan een Azure AD-gebruiker vanuit een andere Tenant.

1. Selecteer **beleid voor gegevens toegang**en selecteer **+ uitnodigen**.

    [![gegevens toegang-zes](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Voer het e-mail adres in voor de gebruiker die u wilt uitnodigen. Dit e-mail adres moet worden gekoppeld aan Azure AD. U kunt desgewenst een persoonlijk bericht toevoegen aan de uitnodiging.

    [![gegevens toegang-zeven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Zoek naar de bevestigings ballon die op het scherm wordt weer gegeven.

    [![Data-Access-acht](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Kies **gebruiker selecteren**. Zoek het e-mail adres van de gast gebruiker die u hebt uitgenodigd voor het zoeken van de gebruiker die u wilt toevoegen. **Selecteer** vervolgens de optie om de selectie te bevestigen.

    [![gegevens toegang-negen](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Kies **rol selecteren**. Kies de juiste Access-rol voor de gast gebruiker:

    * Selecteer **Inzender** als u wilt toestaan dat de gebruiker referentie gegevens kan wijzigen en opgeslagen query's en perspectieven kan delen met andere gebruikers van de omgeving.

    * Als dat niet het geval is, selecteert u **lezer** om de gebruiker in staat te stellen gegevens in de omgeving op te vragen en persoonlijke, niet-gedeelde query's in de omgeving op te slaan.

   Selecteer **OK** om de gewenste rol te bevestigen.

    [![gegevens toegang-tien](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Selecteer **OK** op de pagina **gebruikersrol selecteren** .

1. Controleer of de pagina **Data Access policies** de gast gebruiker en de rollen voor elke gast gebruiker bevat.

    [![Data-Access-elf](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Nu moet de gast gebruiker stappen volgen om toegang te krijgen tot de omgeving die zich bevindt in de Azure-Tenant waarnaar u deze hebt uitgenodigd. Eerst accepteren ze de uitnodiging die u ze hebt verzonden. Deze uitnodiging wordt via e-mail verzonden naar het e-mail adres dat u in stap 5 hebt gebruikt. Ze selecteren aan de **slag** om te accepteren.

    [![Data-Access-12](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Vervolgens accepteert de gast gebruiker de machtigingen die zijn gekoppeld aan de organisatie van de beheerder.

    [![Data-Access-13](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Nadat de gast gebruiker is aangemeld bij het e-mail adres dat u hebt gebruikt om ze te uitnodigen, en ze de uitnodiging accepteren, gaat u naar insights.azure.com. Daarna selecteren ze de avatar naast hun e-mail adres in de rechter bovenhoek van het scherm.

    [![gegevens toegang-14](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Vervolgens selecteert de gast gebruiker uw Azure-Tenant in het vervolg keuzemenu van de map. Deze Tenant is het account waarmee u ze hebt uitgenodigd.

    [![gegevens toegang-15](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Nadat de gast gebruiker uw Tenant heeft geselecteerd, zien ze de Time Series Insights omgeving waartoe u deze toegang hebt gegeven. Ze hebben nu alle mogelijkheden die zijn gekoppeld aan de rol die u hebt gegeven in **stap 5**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het toevoegen van een Azure Event hubs gebeurtenis bron](./time-series-insights-how-to-add-an-event-source-eventhub.md) aan uw time series Insights omgeving.

* [Gebeurtenissen verzenden naar de bron van de gebeurtenis](./time-series-insights-send-events.md).

* Bekijk [uw omgeving in de time series Insights preview Explorer](./time-series-insights-update-explorer.md).
