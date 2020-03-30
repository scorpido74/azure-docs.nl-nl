---
title: Problemen met een geïmplementeerd StorSimple-apparaat oplossen | Microsoft Documenten
description: Beschrijft hoe u fouten diagnosticeren en oplossen die optreden op een StorSimple-apparaat dat momenteel is geïmplementeerd en operationeel is.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933298"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Problemen met een operationeel StorSimple-apparaat oplossen
> [!NOTE]
> De klassieke portal voor StorSimple is afgeschaft. De StorSimple-apparaatbeheerfuncties worden automatisch volgens schema verplaatst naar de nieuwe Azure Portal. U ontvangt hierover een e-mailbericht en een portalmelding. Dit document wordt binnenkort ook ingetrokken. Zie [FAQ: Move to Azure portal](storsimple-8000-move-azure-portal-faq.md) (Veelgestelde vragen: verplaatsing naar Azure Portal) voor vragen met betrekking tot de verplaatsing.

## <a name="overview"></a>Overzicht
Dit artikel biedt handige richtlijnen voor het oplossen van problemen met het oplossen van configuratieproblemen die u tegenkomen nadat uw StorSimple-apparaat is geïmplementeerd en operationeel is. Het beschrijft veelvoorkomende problemen, mogelijke oorzaken en aanbevolen stappen om u te helpen problemen op te lossen die u ondervinden wanneer u Microsoft Azure StorSimple uitvoert. Deze informatie is van toepassing op zowel het on-premises fysieke apparaat StorSimple als het virtuele StorSimple-apparaat.

Aan het einde van dit artikel vindt u een lijst met foutcodes die u tijdens de Microsoft Azure StorSimple-bewerking tegenkomen, evenals stappen die u nemen om de fouten op te lossen. 

## <a name="setup-wizard-process-for-operational-devices"></a>Wizardproces instellen voor operationele apparaten
U gebruikt de wizard Setup[(Invoke-HcsSetupWizard)][1]om de apparaatconfiguratie te controleren en indien nodig corrigerende maatregelen te nemen.

Wanneer u de wizard Setup uitvoert op een eerder geconfigureerd en operationeel apparaat, is de processtroom anders. U alleen de volgende vermeldingen wijzigen:

* IP-adres, subnetmasker en gateway
* Primaire DNS-server
* Primaire NTP-server
* Optionele webproxyconfiguratie

De wizard Setup voert de bewerkingen met betrekking tot het verzamelen van wachtwoorden en apparaatregistratie niet uit.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fouten die optreden tijdens volgende uitvoeringen van de wizard Setup
In de volgende tabel worden de fouten beschreven die u tegenkomen wanneer u de wizard Setup uitvoert op een operationeel apparaat, mogelijke oorzaken voor de fouten en aanbevolen acties om deze op te lossen. 

| Nee. | Foutbericht of -voorwaarde | Mogelijke oorzaken | Aanbevolen actie |
|:--- |:--- |:--- |:--- |
| 1 |Fout 350032: Dit apparaat is al gedeactiveerd. |U ziet deze fout als u de wizard Setup uitvoert op een apparaat dat is gedeactiveerd. |[Neem contact op met Microsoft Support](storsimple-contact-microsoft-support.md) voor de volgende stappen. Een gedeactiveerd apparaat kan niet in gebruik worden genomen. Een fabrieksreset kan nodig zijn voordat het apparaat opnieuw kan worden geactiveerd. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Uitzondering van HRESULT: 0x80070001) |De DNS-serverupdate mislukt. DNS-instellingen zijn globale instellingen en worden toegepast op alle ingeschakelde netwerkinterfaces. |Schakel de interface in en pas de DNS-instellingen opnieuw toe. Dit kan het netwerk verstoren voor andere ingeschakelde interfaces omdat deze instellingen globaal zijn. |
| 3 |Het apparaat lijkt online te zijn in de StorSimple Manager-serviceportal, maar wanneer u de minimale installatie probeert te voltooien en de configuratie wilt opslaan, mislukt de bewerking. |Tijdens de eerste installatie was de webproxy niet geconfigureerd, ook al was er een echte proxyserver. |Gebruik de [cmdlet Test-HcsmConnection][2] om de fout te lokaliseren. [Neem contact op met Microsoft Support](storsimple-contact-microsoft-support.md) als u het probleem niet verhelpen. |
| 4 |Invoke-HcsSetupWizard: Waarde valt niet binnen het verwachte bereik. |Een onjuist subnetmasker produceert deze fout. Mogelijke oorzaken zijn: <ul><li> Het subnetmasker ontbreekt of is leeg.</li><li>De ipv6-voorvoegselindeling is onjuist.</li><li>De interface is cloud-enabled, maar de gateway ontbreekt of onjuist is.</li></ul>Houd er rekening mee dat DATA 0 automatisch cloud-enabled is als deze is geconfigureerd via de wizard Setup. |Gebruik subnet 0.0.0.0 of 256.256.256.256.256 om het probleem te bepalen en kijk vervolgens naar de uitvoer. Voer indien nodig de juiste waarden in voor het subnetmasker, de gateway en het Ipv6-voorvoegsel. |

## <a name="error-codes"></a>Foutcodes
Fouten worden in numerieke volgorde weergegeven.

| Foutnummer | Fouttekst of -beschrijving | Aanbevolen actie van de gebruiker |
|:--- |:--- |:--- |
| 10502 |Er is een fout opgetreden tijdens het openen van uw opslagaccount. |Wacht een paar minuten en probeer het dan opnieuw. Als de fout blijft bestaan, neemt u contact op met Microsoft Support voor de volgende stappen. |
| 40017 |De back-upbewerking is mislukt omdat een volume dat is opgegeven in het back-upbeleid niet op het apparaat is gevonden. |Als de fout blijft bestaan, probeert u de back-upbewerking opnieuw en neemt u contact op met Microsoft Support. voor de volgende stappen. |
| 40018 |De back-upbewerking is mislukt omdat geen van de volumes die in het back-upbeleid zijn opgegeven, op het apparaat zijn gevonden. |Als de fout blijft bestaan, probeert u de back-upbewerking opnieuw en neemt u contact op met Microsoft Support. voor de volgende stappen. |
| 390061 |Het systeem is bezet of niet beschikbaar. |Wacht een paar minuten en probeer het dan opnieuw. Als de fout blijft bestaan, neemt u contact op met Microsoft Support voor de volgende stappen. |
| 390143 |Er is een fout opgetreden met foutcode 390143. (Onbekende fout.) |Als de fout blijft bestaan, neemt u contact op met Microsoft Support voor de volgende stappen. |

## <a name="next-steps"></a>Volgende stappen
Als u het probleem niet oplossen, neemt [u contact op met Microsoft Support](storsimple-contact-microsoft-support.md) voor hulp. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
