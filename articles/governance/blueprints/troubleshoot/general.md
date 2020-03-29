---
title: Veelvoorkomende problemen oplossen
description: Meer informatie over het oplossen van problemen bij het maken, toewijzen en verwijderen van blauwdrukken, zoals beleidsschendingen en parameterfuncties voor blauwdrukken.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157080"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Fouten oplossen met Azure Blueprints

U fouten tegenkomen bij het maken, toewijzen of verwijderen van blauwdrukken. In dit artikel worden verschillende fouten beschreven die kunnen optreden en hoe u deze oplossen.

## <a name="finding-error-details"></a>Foutgegevens zoeken

Veel fouten zullen het gevolg zijn van het toewijzen van een blauwdruk aan een scope. Wanneer een toewijzing mislukt, bevat de blauwdruk details over de mislukte implementatie. Deze informatie geeft het probleem aan, zodat het kan worden opgelost en de volgende implementatie slaagt.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant en gebruik het zoekvak om de blauwdruktoewijzingen te filteren om de mislukte toewijzing te vinden. U de tabel met toewijzingen ook sorteren op de kolom **Status inrichten** om alle mislukte toewijzingen gegroepeerd te zien.

1. Klik met de rechtermuisknop op de blauwdruk met de _status Mislukt_ of klik met de rechtermuisknop en selecteer Details van **toewijzing weergeven**.

1. Een rode banner waarschuwing dat de toewijzing is mislukt is aan de bovenkant van de blauwdruk toewijzing pagina. Klik ergens op de banner om meer details te krijgen.

Het is gebruikelijk dat de fout wordt veroorzaakt door een artefact en niet door de blauwdruk als geheel. Als een artefact een sleutelkluis maakt en Azure Policy voorkomt dat Key Vault wordt gemaakt, mislukt de hele toewijzing.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scenario: Beleidsschending

#### <a name="issue"></a>Probleem

De implementatie van de sjabloon is mislukt vanwege beleidsschending.

#### <a name="cause"></a>Oorzaak

Een beleid kan om een aantal redenen in strijd zijn met de implementatie:

- De resource die wordt gemaakt, wordt beperkt door beleid (gewoonlijk SKU- of locatiebeperkingen)
- De implementatie stelt velden in die zijn geconfigureerd op basis van beleid (algemeen met tags)

#### <a name="resolution"></a>Oplossing

Wijzig de blauwdruk zodat deze niet in strijd is met het beleid in de foutgegevens. Als deze wijziging niet mogelijk is, is een alternatieve optie om het bereik van de beleidstoewijzing te wijzigen, zodat de blauwdruk niet langer in strijd is met het beleid.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scenario: Parameter Blueprint is een functie

#### <a name="issue"></a>Probleem

Blauwdrukparameters die functies zijn, worden verwerkt voordat ze worden doorgegeven aan artefacten.

#### <a name="cause"></a>Oorzaak

Het doorgeven van een blauwdrukparameter `[resourceGroup().tags.myTag]`die een functie gebruikt, zoals , aan een artefact resulteert in de verwerkte uitkomst van de functie die op het artefact wordt ingesteld in plaats van de dynamische functie.

#### <a name="resolution"></a>Oplossing

Als u een functie als parameter wilt `[` doorgeven, ontloopt `[[resourceGroup().tags.myTag]`u de hele tekenreeks met een zodanige dat de blauwdrukparameter eruit ziet als . Het escape-teken zorgt ervoor dat Blauwdrukken de waarde als een tekenreeks behandelen bij het verwerken van de blauwdruk. Blauwdrukken plaatst de functie vervolgens op het artefact, zodat deze dynamisch kan zijn zoals verwacht. Zie [Syntaxis en expressies in Azure Resource Manager-sjablonen](../../../azure-resource-manager/templates/template-expressions.md)voor meer informatie .

## <a name="delete-errors"></a>Fouten verwijderen

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scenario: time-out voor het verwijderen van toewijzingen

#### <a name="issue"></a>Probleem

Het verwijderen van een blauwdruktoewijzing is niet voltooid.

#### <a name="cause"></a>Oorzaak

Een blauwdruktoewijzing kan vast komen te zitten in een niet-terminale status wanneer deze wordt verwijderd. Deze status wordt veroorzaakt wanneer resources die zijn gemaakt door de blauwdruktoewijzing nog in behandeling zijn bij verwijdering of een statuscode niet terugsturen naar Azure Blueprints.

#### <a name="resolution"></a>Oplossing

Blueprint-toewijzingen in een niet-terminale status worden automatisch gemarkeerd **als Mislukt** na een time-out van _6 uur._ Zodra de time-out de status van de blauwdruktoewijzing heeft aangepast, kan de delete opnieuw worden geprobeerd.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
- Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.