---
title: Veelgestelde vragen-Veelgestelde vragen
titleSuffix: Microsoft Genomics
description: Krijg antwoorden op veelgestelde vragen met betrekking tot het gebruik van de Microsoft Genomics-service, inclusief technische informatie, SLA en facturering.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76986033"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: algemene vragen

Dit artikel bevat een overzicht van de belangrijkste query's die u mogelijk hebt gerelateerd aan Microsoft Genomics. Zie [Wat is Microsoft Genomics?](overview-what-is-genomics.md)voor meer informatie over de Microsoft Genomics-service. Raadpleeg de [gids voor probleem oplossing](troubleshooting-guide-genomics.md)voor meer informatie over het oplossen van problemen. 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>GATK4-werk stromen op Microsoft Genomics Hoe kan ik uitvoeren?
Geef in het bestand config. txt van de Microsoft Genomics-service de process_name `gatk4`op. Houd er rekening mee dat u rekening moet houden met de normale facturerings tarieven.

## <a name="how-do-i-enable-output-compression"></a>Uitvoer compressie Hoe kan ik inschakelen?
U kunt de uitvoer-vcf of gvcf comprimeren met een optioneel argument voor uitvoer compressie. Dit komt overeen met het `-bgzip` uitvoeren van `-tabix` gevolgd door op de VCF-of gvcf- `.gz` uitvoer, om bestanden ( `.tbi` bgzip output) en (tabix output) te produceren. `bgzip`Hiermee comprimeert u het VCF-of gvcf `tabix` -bestand en maakt u een index voor het gecomprimeerde bestand. Het argument is een Booleaanse waarde, die standaard is `false` ingesteld op de VCF-uitvoer, en `true` voor gcvf-uitvoer. Als u wilt gebruiken op de opdracht regel `-bz` , `--bgzip-output` geeft `true` u of als (Voer bgzip en `false`tabix) of uit. Als u dit argument wilt gebruiken in het bestand config. txt `bgzip_output: true` , `bgzip_output: false` voegt u of toe aan het bestand.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Wat is de SLA voor Microsoft Genomics?
Wij garanderen dat 99,9% van de tijd Microsoft Genomics service beschikbaar is voor het ontvangen van werk stroom API-aanvragen. Zie [Sla](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)voor meer informatie.

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hoe wordt het gebruik van Microsoft Genomics weer gegeven op mijn factuur?
Microsoft Genomics facturen op basis van het aantal gigabases dat per werk stroom is verwerkt. Zie [prijzen](https://azure.microsoft.com/pricing/details/genomics/)voor meer informatie.


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Waar vind ik een lijst met alle mogelijke opdrachten en argumenten voor de `msgen` client?
U kunt een volledige lijst met beschik bare opdrachten en argumenten verkrijgen `msgen help`door uit te voeren. Als er geen verdere argumenten worden gegeven, wordt een lijst met beschik bare Help-secties weer gegeven `submit`, `list`één `cancel`voor elk `status`van,, en. Als u hulp wilt krijgen voor een specifieke opdracht `msgen help command`, typt u; een voor beeld `msgen help submit` : een lijst met alle opties voor indienen.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Wat zijn de meestgebruikte opdrachten voor de `msgen` client?
De meest gebruikte opdrachten zijn argumenten voor de `msgen` client: 

 |**Cmd**          |  **Veld Beschrijving** |
 |:--------------------|:-------------         |
 |`list`               |Retourneert een lijst met taken die u hebt verzonden. Zie `msgen help list`voor argumenten.  |
 |`submit`             |Hiermee wordt een werk stroom aanvraag verzonden naar de service. Zie `msgen help submit`voor argumenten.|
 |`status`             |Retourneert de status van de werk stroom die `--workflow-id`is opgegeven door. Zie ook `msgen help status`. |
 |`cancel`             |Hiermee wordt een aanvraag verzonden om de verwerking te annuleren van `--workflow-id`de werk stroom die is opgegeven door. Zie ook `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Waar vind ik de waarde voor `--api-url-base`?
Ga naar Azure Portal en open uw Genomics-account pagina. Kies **toegangs sleutels**onder de kop **beheer** . Daar vindt u de API-URL en de toegangs sleutels.

## <a name="where-do-i-get-the-value-for---access-key"></a>Waar vind ik de waarde voor `--access-key`?
Ga naar Azure Portal en open uw Genomics-account pagina. Kies **toegangs sleutels**onder de kop **beheer** . Daar vindt u de API-URL en de toegangs sleutels.

## <a name="why-do-i-need-two-access-keys"></a>Waarom heb ik twee toegangs sleutels nodig?
U hebt twee toegangs sleutels nodig voor het geval u deze wilt bijwerken (opnieuw genereren) zonder het gebruik van de service te onderbreken. Als u bijvoorbeeld de eerste sleutel wilt bijwerken, moet u voor alle nieuwe werk stromen de tweede sleutel gebruiken. Wacht vervolgens op alle werk stromen met behulp van de eerste sleutel om te volt ooien voordat u de eerste sleutel bijwerkt.

## <a name="do-you-save-my-storage-account-keys"></a>Slaat u de sleutels van mijn opslag account op?
De sleutel van uw opslag account wordt gebruikt om toegangs tokens voor de korte termijn te maken voor de Microsoft Genomics-service om uw invoer bestanden te lezen en de uitvoer bestanden te schrijven. De standaard duur van het token is 48 uur. De duur van het token kan worden gewijzigd `-sas/--sas-duration` met de optie opdracht indienen. de waarde is in uren.

## <a name="what-genome-references-can-i-use"></a>Welke genoom verwijzingen kan ik gebruiken?

Deze verwijzingen worden ondersteund:

 |Naslaginformatie              | Waarde van`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (geen Alt-analyse) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hoe kan ik de notatie van mijn opdracht regel argumenten als een configuratie bestand? 

msgen begrijpt configuratie bestanden in de volgende indeling:
* Alle opties worden gegeven als sleutel-waardeparen met waarden gescheiden van sleutels door een dubbele punt.
  Spatie wordt genegeerd.
* Regels die beginnen `#` met worden genegeerd.
* Elk opdracht regel argument in de lange notatie kan worden geconverteerd naar een sleutel door de voorloop streepjes te vervangen door de streepjes tussen woorden met een onderstrepings teken. Hier volgen enkele voor beelden van conversie:

  |Opdracht regel argument            | Regel van configuratie bestand |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key: sleutel*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende bronnen om aan de slag te gaan met Microsoft Genomics:
- Ga aan de slag door uw eerste werk stroom uit te voeren via de Microsoft Genomics-service. [Een werkstroom uitvoeren via de Microsoft Genomics-service](quickstart-run-genomics-workflow-portal.md)
- Uw eigen gegevens verzenden voor verwerking door de Microsoft Genomics-service: [gekoppelde FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [meerdere FASTQ of BAM](quickstart-input-multiple.md) 

