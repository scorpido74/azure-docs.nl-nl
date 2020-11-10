---
title: Problemen oplossen met de download met gebruiksgegevens voor Azure-reserveringen
description: Dit artikel helpt u bij het begrijpen en oplossen van het probleem waarbij de download met gebruiksgegevens voor een gereserveerde instantie niet beschikbaar is in Azure Portal.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147318"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Problemen oplossen met de download met gebruiksgegevens voor Azure-reserveringen

Dit artikel helpt u bij het begrijpen en oplossen van het probleem waarbij de download met gebruiksgegevens voor een gereserveerde instantie niet beschikbaar is in Azure Portal.

## <a name="symptoms"></a>Symptomen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar **Reserveringen**.
1. Selecteer een reservering.
1. Op de overzichtspagina van de reservering ziet u op de standaardweergave het gebruik van de afgelopen zeven dagen. U kunt **Downloaden als CSV** selecteren om de gebruiksgegevens van de reservering te downloaden.
1. Wanneer u het tijdsbereik wijzigt, is de optie om **Downloaden als CSV** niet meer beschikbaar.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Voorbeeld waarin de optie Downloaden als CSV niet beschikbaar is" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Oorzaak

Vanwege technische beperkingen biedt Azure geen ondersteuning voor het downloaden van gegevens voor een periode langer dan zeven dagen. Voor langdurige perioden voor klanten met grote aantallen reserveringen wordt een grote hoeveelheid gegevens geretourneerd. Het retourneren van gegevens via API's is een belasting voor Azure-resources.

## <a name="solution"></a>Oplossing

We begrijpen dat klanten gegevens voor langere perioden willen downloaden. Er is momenteel echter geen manier om de gebruiksgegevens voor lange perioden te downloaden.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md) voor meer informatie over reserveringen.