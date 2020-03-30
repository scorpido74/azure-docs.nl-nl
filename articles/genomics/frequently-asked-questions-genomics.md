---
title: Veelgestelde vragen - Veelgestelde vragen
titleSuffix: Microsoft Genomics
description: Antwoorden op veelgestelde vragen met betrekking tot het gebruik van de Microsoft Genomics-service, waaronder technische informatie, SLA en facturering.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986033"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: veelgestelde vragen

In dit artikel worden de belangrijkste query's weergegeven die mogelijk betrekking hebben op Microsoft Genomics. Zie Wat is Microsoft Genomics [voor](overview-what-is-genomics.md)meer informatie over de Microsoft Genomics-service? Zie onze handleiding voor probleemoplossing voor meer informatie over [probleemoplossing.](troubleshooting-guide-genomics.md) 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Hoe kan ik GATK4-workflows uitvoeren op Microsoft Genomics?
Geef in het config.txt-bestand van de `gatk4`Microsoft Genomics-service de process_name op aan . Houd er rekening mee dat u regelmatig factureren ontvangt.

## <a name="how-do-i-enable-output-compression"></a>Hoe schakel ik uitvoercompressie in?
U de uitvoervcf of gvcf comprimeren met een optioneel argument voor uitvoercompressie. Dit is gelijk `-bgzip` aan `-tabix` het uitvoeren gevolgd door op `.gz` de vcf of `.tbi` gvcf-uitgang, om (bgzip-uitvoer) en (tabix-uitvoer)bestanden te produceren. `bgzip`comprimeert het vcf- `tabix` of gvcf-bestand en maakt een index voor het gecomprimeerde bestand. Het argument is een booleaan, die standaard is `false` ingesteld `true` op vcf-uitvoer en standaard op gcvf-uitvoer. Als u op de `-bz` opdrachtregel wilt gebruiken, geeft u op of `--bgzip-output` als `true` (uitvoeren bgzip en tabix) of `false`. Als u dit argument wilt gebruiken in `bgzip_output: true` het `bgzip_output: false` bestand config.txt, voegt u of het bestand toe.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Wat is de SLA voor Microsoft Genomics?
We garanderen dat 99,9% van de tijd dat Microsoft Genomics-service beschikbaar is om API-aanvragen voor workflows te ontvangen. Zie [SLA voor](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)meer informatie.

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hoe wordt het gebruik van Microsoft Genomics weergegeven op mijn factuur?
Microsoft Genomics-facturen op basis van het aantal gigabases dat per workflow wordt verwerkt. Zie [Prijzen voor](https://azure.microsoft.com/pricing/details/genomics/)meer informatie.


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Waar vind ik een lijst met alle mogelijke `msgen` opdrachten en argumenten voor de klant?
U een volledige lijst met beschikbare `msgen help`opdrachten en argumenten krijgen door. Als er geen verdere argumenten worden verstrekt, wordt een lijst `submit` `list`met `cancel`beschikbare `status`helpsecties weergegeven, één voor elk van , , en . Als u hulp wilt krijgen `msgen help command`voor een specifieke opdracht, typt u ; geeft bijvoorbeeld `msgen help submit` alle opties voor verzenden weer.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Wat zijn de meest gebruikte `msgen` opdrachten voor de client?
De meest gebruikte opdrachten zijn `msgen` argumenten voor de client: 

 |**Opdracht**          |  **Veldbeschrijving** |
 |:--------------------|:-------------         |
 |`list`               |Retourneert een lijst met vacatures die u hebt ingediend. Voor argumenten, `msgen help list`zie .  |
 |`submit`             |Dient een werkstroomaanvraag in bij de service. Voor argumenten, `msgen help submit`zie .|
 |`status`             |Geeft als resultaat de `--workflow-id`status van de werkstroom die is opgegeven door . Zie `msgen help status`ook . |
 |`cancel`             |Hiermee verzendt u een verzoek om `--workflow-id`de verwerking van de werkstroom te annuleren die is opgegeven door . Zie `msgen help cancel`ook . |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Waar haal ik de `--api-url-base`waarde vandaan?
Ga naar azure portal en open uw Genomics-accountpagina. Kies **Access-toetsen**onder de kop **Beheer** . Daar vindt u zowel de API-URL als uw toegangssleutels.

## <a name="where-do-i-get-the-value-for---access-key"></a>Waar haal ik de `--access-key`waarde vandaan?
Ga naar azure portal en open uw Genomics-accountpagina. Kies **Access-toetsen**onder de kop **Beheer** . Daar vindt u zowel de API-URL als uw toegangssleutels.

## <a name="why-do-i-need-two-access-keys"></a>Waarom heb ik twee toegangssleutels nodig?
U hebt twee toegangssleutels nodig voor het geval u ze wilt bijwerken (regenereren) zonder het gebruik van de service te onderbreken. Als u bijvoorbeeld de eerste toets wilt bijwerken, moet u alle nieuwe werkstromen de tweede sleutel laten gebruiken. Wacht vervolgens tot alle werkstromen met de eerste sleutel zijn voltooid voordat u de eerste sleutel bijwerkt.

## <a name="do-you-save-my-storage-account-keys"></a>Slaat u mijn opslagaccountsleutels op?
Uw opslagaccountsleutel wordt gebruikt om korte termijn toegangstokens voor de Microsoft Genomics-service te maken om uw invoerbestanden te lezen en de uitvoerbestanden te schrijven. De standaardtokenduur is 48 uur. De tokenduur kan worden `-sas/--sas-duration` gewijzigd met de optie van de opdracht Verzenden; de waarde is in uren.

## <a name="what-genome-references-can-i-use"></a>Welke genoomreferenties kan ik gebruiken?

Deze verwijzingen worden ondersteund:

 |Naslaginformatie              | Waarde van`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (geen alt analyse) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hoe kan ik mijn opdrachtregelargumenten opmaken als een config-bestand? 

msgen begrijpt configuratiebestanden in de volgende indeling:
* Alle opties worden geleverd als sleutelwaardeparen met waarden die door een dubbele punt van sleutels zijn gescheiden.
  Witruimte wordt genegeerd.
* Lijnen die `#` beginnen met worden genegeerd.
* Elk argument van de opdrachtregel in de lange notatie kan worden omgezet in een sleutel door de voorlopende streepjes te strippen en streepjes tussen woorden te vervangen door onderdoelpunten. Hier volgen enkele conversievoorbeelden:

  |Argument Opdrachtregel            | Configuratiebestandsregel |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:TOETS*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende bronnen om aan de slag te gaan met Microsoft Genomics:
- Ga aan de slag door uw eerste workflow uit te voeren via de Microsoft Genomics-service. [Een werkstroom uitvoeren via de Microsoft Genomics-service](quickstart-run-genomics-workflow-portal.md)
- Stuur uw eigen gegevens in voor verwerking door de Microsoft Genomics-service: [gekoppelde FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ of BAM](quickstart-input-multiple.md) 

