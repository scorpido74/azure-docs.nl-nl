---
title: Veelvoorkomende problemen oplossen
description: Meer informatie over het oplossen van problemen met het maken, toewijzen en verwijderen van blauw drukken.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 14e957986df7a114b8c865ee82e2ac447683dc2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257174"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Problemen met Azure-blauw drukken oplossen

Er kunnen fouten optreden bij het maken of toewijzen van blauw drukken. In dit artikel worden verschillende fouten beschreven die zich kunnen voordoen en hoe u deze kunt oplossen.

## <a name="finding-error-details"></a>Fout details zoeken

Veel fouten worden veroorzaakt door het toewijzen van een blauw druk aan een bereik. Wanneer een toewijzing mislukt, bevat de blauw druk Details over de mislukte implementatie. Deze informatie geeft het probleem aan zodat het kan worden hersteld en de volgende implementatie slaagt.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **toegewezen blauw drukken** op de pagina aan de linkerkant en gebruik het zoekvak om de opdrachten in de blauw druk te filteren om de mislukte toewijzing te vinden. U kunt ook de tabel met toewijzingen sorteren op de kolom **inrichtings status** om alle mislukte toewijzingen weer te geven die samen zijn gegroepeerd.

1. Klik met de rechter muisknop op de blauw druk met de status _mislukt_ of klik met de linkermuisknop en selecteer **toewijzings details weer geven**.

1. Een rode banner waarschuwing dat de toewijzing is mislukt, bevindt zich bovenaan de pagina voor de toewijzing van blauw drukken. Klik ergens op de banner voor meer informatie.

Het is gebruikelijk dat de fout wordt veroorzaakt door een artefact en niet de blauw druk als geheel. Als een artefact een Key Vault maakt en Azure Policy voor komt dat Key Vault wordt gemaakt, mislukt de volledige toewijzing.

## <a name="general-errors"></a>Algemene fouten

### <a name="policy-violation"></a>Omstandigheden Beleids schending

#### <a name="issue"></a>Probleem

De sjabloon implementatie is mislukt vanwege een overtreding van het beleid.

#### <a name="cause"></a>Oorzaak

Een beleid kan om een aantal redenen conflicteren met de implementatie:

- De bron die wordt gemaakt, wordt beperkt door het beleid (vaak SKU of locatie beperkingen)
- De implementatie is het instellen van velden die worden geconfigureerd door beleid (gemeen schappelijk met Tags)

#### <a name="resolution"></a>Oplossing

Wijzig de blauw druk zodat deze niet in strijd is met het beleid in de fout Details. Als deze wijziging niet mogelijk is, is het bereik van de beleids toewijzing gewijzigd, zodat de blauw druk niet meer in strijd is met het beleid.

### <a name="escape-function-parameter"></a>Omstandigheden Blauw druk-para meter is een functie

#### <a name="issue"></a>Probleem

Blauw drukken-para meters die zijn functies worden verwerkt voordat ze worden door gegeven aan artefacten.

#### <a name="cause"></a>Oorzaak

Het door geven van een blauw druk-para meter die `[resourceGroup().tags.myTag]`gebruikmaakt van een functie, zoals, naar een artefact resulteert in het verwerkte resultaat van de functie die wordt ingesteld op het artefact in plaats van de dynamische functie.

#### <a name="resolution"></a>Oplossing

Als u een functie wilt door geven door middel van `[` een para meter, moet u de volledige teken reeks escapen, zodat de blauw druk-para meter `[[resourceGroup().tags.myTag]`eruit ziet. Het escape teken resulteert in blauw drukken om de waarde te behandelen als een teken reeks bij het verwerken van de blauw druk. Blauw drukken plaatst vervolgens de functie op het artefact, zodat deze dynamisch kan worden uitgevoerd. Zie [syntaxis en expressies in azure Resource Manager-sjablonen](../../../azure-resource-manager/template-expressions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
- Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.