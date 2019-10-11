---
title: Veelgestelde vragen-Veelgestelde vragen
titleSuffix: Microsoft Genomics
description: Antwoorden op veelgestelde vragen van klanten vragen over Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 80a656ee687b71fcaf09a02d70d0a5a4300186bc
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249232"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: algemene vragen

Dit artikel bevat een overzicht van de belangrijkste query's die u mogelijk hebt gerelateerd aan Microsoft Genomics. Zie [Wat is Microsoft Genomics?](overview-what-is-genomics.md)voor meer informatie over de Microsoft Genomics-service. Raadpleeg de [gids voor probleem oplossing](troubleshooting-guide-genomics.md)voor meer informatie over het oplossen van problemen. 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>GATK4-werk stromen op Microsoft Genomics Hoe kan ik uitvoeren?
Geef in het bestand config. txt van de Microsoft Genomics-service de process_name op `gatk4`. Houd er rekening mee dat u rekening moet houden met de normale facturerings tarieven.


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Wat is de SLA voor Microsoft Genomics?
Wij garanderen dat 99,9% van de tijd Microsoft Genomics service beschikbaar is voor het ontvangen van werk stroom API-aanvragen. Zie [Sla](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)voor meer informatie.

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hoe wordt het gebruik van Microsoft Genomics weer gegeven op mijn factuur?
Microsoft Genomics facturen op basis van het aantal gigabases dat per werk stroom is verwerkt. Zie [prijzen](https://azure.microsoft.com/pricing/details/genomics/)voor meer informatie.


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Waar vind ik een lijst met alle mogelijke opdrachten en argumenten voor de `msgen`-client?
U kunt een volledige lijst met beschik bare opdrachten en argumenten verkrijgen door `msgen help` uit te voeren. Als er geen verdere argumenten worden gegeven, wordt een lijst met beschik bare Help-secties weer gegeven, één voor elk van `submit`, `list`, `cancel` en `status`. Als u hulp wilt krijgen voor een specifieke opdracht, typt u `msgen help command`; `msgen help submit` bevat bijvoorbeeld alle opties voor het indienen.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Wat zijn de meestgebruikte opdrachten voor de `msgen`-client?
De meestgebruikte opdrachten zijn argumenten voor de `msgen`-client bevatten: 

 |**Opdracht**          |  **Beschrijving van veld** |
 |:--------------------|:-------------         |
 |`list`               |Retourneert een lijst met taken die u hebt verzonden. Zie `msgen help list` voor argumenten.  |
 |`submit`             |Hiermee wordt een werk stroom aanvraag verzonden naar de service. Zie `msgen help submit` voor argumenten.|
 |`status`             |Retourneert de status van de werk stroom die is opgegeven door `--workflow-id`. Zie ook `msgen help status`. |
 |`cancel`             |Hiermee wordt een aanvraag verzonden om de verwerking van de werk stroom die is opgegeven door `--workflow-id`, te annuleren. Zie ook `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Waar kan ik de waarde voor `--api-url-base` krijgen?
Ga naar Azure Portal en open uw Genomics-account pagina. Kies **toegangs sleutels**onder de kop **beheer** . Daar vindt u de API-URL en de toegangs sleutels.

## <a name="where-do-i-get-the-value-for---access-key"></a>Waar kan ik de waarde voor `--access-key` krijgen?
Ga naar Azure Portal en open uw Genomics-account pagina. Kies **toegangs sleutels**onder de kop **beheer** . Daar vindt u de API-URL en de toegangs sleutels.

## <a name="why-do-i-need-two-access-keys"></a>Waarom heb ik twee toegangs sleutels nodig?
U hebt twee toegangs sleutels nodig voor het geval u deze wilt bijwerken (opnieuw genereren) zonder het gebruik van de service te onderbreken. Als u bijvoorbeeld de eerste sleutel wilt bijwerken, moet u voor alle nieuwe werk stromen de tweede sleutel gebruiken. Wacht vervolgens op alle werk stromen met behulp van de eerste sleutel om te volt ooien voordat u de eerste sleutel bijwerkt.

## <a name="do-you-save-my-storage-account-keys"></a>Slaat u de sleutels van mijn opslag account op?
De sleutel van uw opslag account wordt gebruikt om toegangs tokens voor de korte termijn te maken voor de Microsoft Genomics-service om uw invoer bestanden te lezen en de uitvoer bestanden te schrijven. De standaard duur van het token is 48 uur. De duur van het token kan worden gewijzigd met de optie `-sas/--sas-duration` van de opdracht Submit. de waarde is in uren.

## <a name="what-genome-references-can-i-use"></a>Welke genoom verwijzingen kan ik gebruiken?

Deze verwijzingen worden ondersteund:

 |Referentie              | Waarde van `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (geen Alt-analyse) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hoe kan ik de notatie van mijn opdracht regel argumenten als een configuratie bestand? 

msgen begrijpt configuratie bestanden in de volgende indeling:
* Alle opties worden gegeven als sleutel-waardeparen met waarden gescheiden van sleutels door een dubbele punt.
  Spatie wordt genegeerd.
* Regels die beginnen met `#`, worden genegeerd.
* Elk opdracht regel argument in de lange notatie kan worden geconverteerd naar een sleutel door de voorloop streepjes te vervangen door de streepjes tussen woorden met een onderstrepings teken. Hier volgen enkele voor beelden van conversie:

  |Opdracht regel argument            | Regel van configuratie bestand |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key: sleutel*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende bronnen om aan de slag te gaan met Microsoft Genomics:
- Ga aan de slag door uw eerste werk stroom uit te voeren via de Microsoft Genomics-service. [Een werk stroom uitvoeren via de Microsoft Genomics-service](quickstart-run-genomics-workflow-portal.md)
- Uw eigen gegevens verzenden voor verwerking door de Microsoft Genomics-service: [gekoppelde FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [meerdere FASTQ of BAM](quickstart-input-multiple.md) 

