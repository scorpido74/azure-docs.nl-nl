---
title: Veelgestelde vragen over Azure VM-grootten zonder lokale tijdelijke schijf
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Microsoft Azure VM-grootten die geen lokale tijdelijke schijf hebben.
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 502883a0c4eb36be9e89ba8693f4d32ba7f16ebd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088866"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Azure VM-grootten zonder lokale tijdelijke schijf 
In dit artikel vindt u antwoorden op veelgestelde vragen over Azure VM-grootten die geen lokale tijdelijke schijf hebben (d.w.z. geen lokale tijdelijke schijf). Zie voor meer informatie over deze VM-grootten [specificaties voor Dv4-en Dsv4-Series (algemeen workloads)](dv4-dsv4-series.md) of [specificaties voor Ev4-en Esv4-serie (voor geheugen geoptimaliseerde workloads)](ev4-esv4-series.md).

> [!IMPORTANT]
> De Dv4-, Dsv4-, Ev4-en Esv4-serie-VM-grootten zijn nu beschikbaar als open bare preview. Vul dit [formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)in om u aan te melden voor open bare preview. 

## <a name="what-does-no-local-temp-disk-mean"></a>Wat betekent er geen lokale tijdelijke schijf? 
Normaal gesp roken hebben we VM-grootten (bijvoorbeeld Standard_D2s_v3, Standard_E48_v3) die een kleine lokale schijf (bijvoorbeeld een D: station) bevatten. Nu met deze nieuwe VM-grootten is de kleine lokale schijf niet meer aanwezig; u kunt echter nog steeds Standard-HDD, Premium-SSD of Ultra-SSD bijvoegen.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>Wat moet ik doen als ik nog steeds een lokale tijdelijke schijf wil?
Als voor uw werk belasting een lokale tijdelijke schijf vereist is, zijn er ook nieuwe [Ddv4-en Ddsv4](ddv4-ddsv4-series.md) -of [Edv4-en Edsv4](edv4-edsv4-series.md) -VM-grootten beschikbaar. Deze grootten bieden 50% grotere tijdelijke schijf vergeleken met de vorige v3-grootten.

> [!NOTE]
> Lokale tijdelijke schijf is niet permanent; gebruik Standard-HDD, Premium-SSD of Ultra-SSD opties om ervoor te zorgen dat uw gegevens permanent zijn. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Wat zijn de verschillen tussen deze nieuwe VM-grootten en de Algemeen Dv3/Dsv3 of het geoptimaliseerde Ev3/Esv3-VM-formaat dat ik heb gebruikt? 
| V3 VM-families met een lokale tijdelijke schijf   | Nieuwe v4-families met een lokale tijdelijke schijf | Nieuwe v4-families zonder lokale tijdelijke schijf |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Kan ik het formaat van een VM-grootte met een lokale tijdelijke schijf wijzigen in een VM-grootte zonder lokale tijdelijke schijf?  
Nee. De enige combi naties die zijn toegestaan voor het wijzigen van de grootte zijn: 

1. VM (met lokale tijdelijke schijf)-> VM (met lokale tijdelijke schijf); maar 
2. VM (zonder lokale tijdelijke schijf)-> VM (zonder lokale tijdelijke schijf). 

> [!NOTE]
> Als een afbeelding afhankelijk is van de bron schijf, of als een wissel bestand of swapfile bestaat op de lokale tijdelijke schijf, werken de schijfloze installatie kopieën niet. gebruik in plaats daarvan het alternatief ' met schijf '. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Ondersteunen deze VM-grootten zowel Linux-als Windows-besturings systemen (OS)?
Ja.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Zullen mijn aangepaste scripts, aangepaste installatie kopieën of installatie kopieën van het besturings systeem met kras bestanden of pagina bestanden op een lokale tijdelijke schijf worden verbroken?
Als de aangepaste installatie kopie van het besturings systeem naar de lokale tijdelijke schijf verwijst, werkt de installatie kopie mogelijk niet correct met deze schijfloze grootte.

## <a name="have-questions-or-feedback"></a>Vragen of feedback?
Vul het [feedback formulier]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u)in. 

## <a name="next-steps"></a>Volgende stappen 
In dit document hebt u meer geleerd over de meest voorkomende vragen met betrekking tot virtuele machines van Azure met een lokale tijdelijke schijf. Raadpleeg de volgende artikelen voor meer informatie over deze VM-grootten:

- [Specificaties voor de Dv4-en Dsv4-serie (Algemeen workload)](dv4-dsv4-series.md)
- [Specificaties voor Ev4 en Esv4-Series (werk belasting geoptimaliseerd voor geheugen)](ev4-esv4-series.md)
