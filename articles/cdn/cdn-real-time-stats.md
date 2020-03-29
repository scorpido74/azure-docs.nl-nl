---
title: Realtime statistieken in Azure CDN | Microsoft Documenten
description: Realtime statistieken bieden realtime gegevens over de prestaties van Azure CDN bij het leveren van inhoud aan uw clients.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593497"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Realtime statistieken in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht
In dit document worden realtime statistieken in Microsoft Azure CDN uitgelegd.  Deze functionaliteit biedt realtime gegevens, zoals bandbreedte, cachestatussen en gelijktijdige verbindingen met uw CDN-profiel bij het leveren van inhoud aan uw clients. Dit maakt continue monitoring van de gezondheid van uw service op elk gewenst moment mogelijk, inclusief go-live evenementen.

De volgende grafieken zijn beschikbaar:

* [Bandbreedte](#bandwidth)
* [Statuscodes](#status-codes)
* [Cachestatussen](#cache-statuses)
* [Verbindingen](#connections)

## <a name="accessing-real-time-stats"></a>Toegang tot realtime statistieken
1. Blader in de [Azure Portal](https://portal.azure.com)naar uw CDN-profiel.
   
    ![CDN-profielblad](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profielblad beheer](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
3. Plaats de plaats op het tabblad **Analytics** en plaats de plaats vervolgens boven de **flyout voor realtime statistieken.**  Klik op **HTTP Large Object**.
   
    ![CDN-beheerportal](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    De real-time statistieken grafieken worden weergegeven.

In elk van de grafieken worden realtime statistieken weergegeven voor de geselecteerde tijdspanne, te beginnen wanneer de pagina wordt geladen.  De grafieken worden om de paar seconden automatisch bijgewerkt.  Met de knop **Grafiek vernieuwen** wordt, indien aanwezig, de grafiek gewist, waarna alleen de geselecteerde gegevens worden weergegeven.

## <a name="bandwidth"></a>Bandbreedte
![Bandbreedtegrafiek](./media/cdn-real-time-stats/cdn-bandwidth.png)

In de **grafiek Bandbreedte** wordt de hoeveelheid bandbreedte weergegeven die voor het huidige platform wordt gebruikt gedurende de geselecteerde tijdspanne. Het gearceerde gedeelte van de grafiek geeft het bandbreedtegebruik aan. De exacte hoeveelheid bandbreedte die momenteel wordt gebruikt, wordt direct onder de lijngrafiek weergegeven.

## <a name="status-codes"></a>Statuscodes
![Grafiek statuscode](./media/cdn-real-time-stats/cdn-status-codes.png)

De grafiek **Statuscodes** geeft aan hoe vaak bepaalde HTTP-antwoordcodes optreden gedurende de geselecteerde tijdspanne.

> [!TIP]
> Zie [Azure CDN HTTP-statuscodes voor](/previous-versions/azure/mt759238(v=azure.100))een beschrijving van elke HTTP-statuscodeoptie .
> 
> 

Een lijst met HTTP-statuscodes wordt direct boven de grafiek weergegeven. Deze lijst geeft elke statuscode aan die kan worden opgenomen in de lijngrafiek en het huidige aantal exemplaren per seconde voor die statuscode. Standaard wordt een regel weergegeven voor elk van deze statuscodes in de grafiek. U er echter voor kiezen om alleen de statuscodes te controleren die een speciale betekenis hebben voor uw CDN-configuratie. Controleer hiervoor de gewenste statuscodes en schakel alle andere opties uit en klik vervolgens op **Grafiek vernieuwen**. 

U geregistreerde gegevens tijdelijk verbergen voor een bepaalde statuscode.  Klik in de legenda direct onder de grafiek op de statuscode die u wilt verbergen. De statuscode wordt onmiddellijk verborgen in de grafiek. Als u opnieuw op die statuscode klikt, wordt die optie opnieuw weergegeven.

## <a name="cache-statuses"></a>Cachestatussen
![Grafiek Cachestatussen](./media/cdn-real-time-stats/cdn-cache-status.png)

De grafiek **Cachestatussen** geeft aan hoe vaak bepaalde typen cachestatussen optreden gedurende de geselecteerde tijdspanne. 

> [!TIP]
> Zie [Azure CDN-cachestatuscodes](/previous-versions/azure/mt759237(v=azure.100))voor een beschrijving van elke cachestatusoptie .
> 
> 

Een lijst met statuscodes in de cache wordt direct boven de grafiek weergegeven. Deze lijst geeft elke statuscode aan die kan worden opgenomen in de lijngrafiek en het huidige aantal exemplaren per seconde voor die statuscode. Standaard wordt een regel weergegeven voor elk van deze statuscodes in de grafiek. U er echter voor kiezen om alleen de statuscodes te controleren die een speciale betekenis hebben voor uw CDN-configuratie. Controleer hiervoor de gewenste statuscodes en schakel alle andere opties uit en klik vervolgens op **Grafiek vernieuwen**. 

U geregistreerde gegevens tijdelijk verbergen voor een bepaalde statuscode.  Klik in de legenda direct onder de grafiek op de statuscode die u wilt verbergen. De statuscode wordt onmiddellijk verborgen in de grafiek. Als u opnieuw op die statuscode klikt, wordt die optie opnieuw weergegeven.

## <a name="connections"></a>Verbindingen
![Grafiek Verbindingen](./media/cdn-real-time-stats/cdn-connections.png)

Deze grafiek geeft aan hoeveel verbindingen er zijn gemaakt met uw edge-servers. Elke aanvraag voor een asset die door onze CDN gaat, resulteert in een verbinding.

## <a name="next-steps"></a>Volgende stappen
* Ontvang een melding met [realtime waarschuwingen in Azure CDN](cdn-real-time-alerts.md)
* Graaf dieper met [geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
* [Gebruikspatronen analyseren](cdn-analyze-usage-patterns.md)

