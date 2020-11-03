---
title: Problemen met Azure-reserveringsaanbevelingen oplossen
description: Dit artikel helpt u bij het begrijpen en oplossen van problemen met Azure-reserveringsaanbevelingen die in de Azure Portal worden weergeven.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492229"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Problemen met Azure-reserveringsaanbevelingen oplossen

Dit artikel helpt u bij het begrijpen en oplossen van problemen met Azure-reserveringsaanbevelingen die in de Azure Portal worden weergeven. Het is mogelijk dat u geen aanbevelingen ziet of aanbevelingen die niet bij uw verwachtingen passen. Zo hebt u bijvoorbeeld al een gereserveerde instantie voor een specifieke VM-configuratie. U verwacht misschien een aanbeveling voor een vergelijkbare VM-configuratie te zien.

## <a name="symptoms"></a>Symptomen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar **Reserveringen**.
2. Selecteer **+ Toevoegen** en selecteer vervolgens een product.
3. Het is mogelijk dat u op het tabblad **Aanbevolen** geen aanbevelingen ziet of aanbevelingen die niet voldoen aan uw verwachtingen.

## <a name="cause"></a>Oorzaak

De lijst met aanbevolen producten wordt gegenereerd op basis van de mate gebruik die u voor uw reserveringsbereik hebt (gedeeld of enkelvoudig) in vergelijking met de kosten van een reservering voor het product. Als de aanbevelingsengine besparingen detecteert, wordt een product voor aankoop aanbevolen. Als de engine echter geen besparingen identificeert, wordt er geen product aangeraden.

Wanneer u een resource met een bepaalde configuratie uitvoert, bestaat er geen garantie dat u geld bespaart door een reservering aan te schaffen voor die configuratie. Het gebruik kan bijvoorbeeld slechts sporadisch zijn. In dat geval, is het mogelijk dat u geen geld bespaart over de totale kosten van de reservering gedurende de levensduur van de reserveringsperiode.

Het is ook belangrijk dat u begrijpt hoe de selectie van het reserveringsbereik invloed heeft op aanbevelingen. Wanneer het bereik is ingesteld op **Gedeeld** , geven de aanbevelingen in de lijst gereserveerde instanties weer waarin Azure besparingen vindt voor de volledige inschrijving die is gekoppeld aan het factureringsabonnement. Wanneer het reserveringsbereik is ingesteld op **Enkelvoudig** , zijn de aanbevelingen in de lijst alleen van toepassing op resources die worden uitgevoerd in het abonnement. Het is mogelijk dat sommige VM-grootten worden aanbevolen voor één reserveringsbereik, maar niet voor een andere. Zo kunt u bijvoorbeeld een gecombineerd gebruik hebben van **Standard_B1ls** in uw inschrijving dat groot genoeg is om de kosten te rechtvaardigen van het aanschaffen van een reservering bij het inschrijvingsbereik. Een enkelvoudig abonnement in de inschrijving heeft mogelijk niet voldoende gebruik om de kosten voor het aanschaffen van een reservering in het reserveringsbereik te rechtvaardigen. Als u het reserveringsbereik van **Gedeeld** en **Enkelvoudig** wijzigt, kan dit leiden tot verschillende aanbevelingen.

Azure kan aanraden om een reservering aan te schaffen voor bepaalde voorwaarden en niet voor anderen, op basis van de kostenbesparingen die zijn geïdentificeerd. Termijnen van drie jaar hebben met name een grotere korting dan een termijn van één jaar. Het is waarschijnlijker dat Azure voor een termijn van drie jaar besparingen oplevert dan voor een termijn van één jaar.

Als u wilt weten waarom Azure een specifieke resourcegrootte en -hoeveelheid aanbeveelt, selecteert u **&lt;Hoeveelheid&gt; Zie details** om een diepgaande visualisatie te bekijken met potentiële besparingen in de loop van de tijd.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Voorbeeld van de aanbeveling voor de reservering Zie link met details" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Oplossing

U kunt een reserveringshoeveelheid kopen voor een termijn die bij u past. Een reserveringsaankoop met een hoeveelheid en een termijn die verschilt van de aanbeveling, resulteert waarschijnlijk in minder dan optimale besparingen en gebruik.

## <a name="next-steps"></a>Volgende stappen

- Zie [Aanbevelingen voor reserveringsaankopen](determine-reservation-purchase.md) voor meer informatie over aanbevelingen voor reserveringen.
