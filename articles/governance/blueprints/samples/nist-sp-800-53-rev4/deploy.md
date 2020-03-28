---
title: NIST SP 800-53 R4-blauwdrukvoorbeeld implementeren
description: Stappen implementeren voor het blauwdrukvoorbeeld NIST SP 800-53 R4, inclusief details van de parameterparameters voor blauwdrukken.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: db8c2302a6c2311e096be2cdc78935bdab2ef9c7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546613"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Het blauwdrukvoorbeeld van NIST SP 800-53 R4 implementeren

Als u het blueprintvoorbeeld azure blueprints NIST SP 800-53 R4 wilt implementeren, moeten de volgende stappen worden genomen:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken uit het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauwdruk maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken met behulp van het voorbeeld als starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek het **blauwdrukvoorbeeld van NIST SP 800-53 R4** onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** Geef een naam op voor uw exemplaar van het NIST SP 800-53 R4-blauwdrukvoorbeeld.
   - **Definitielocatie:** gebruik de ellips en selecteer de beheergroep om uw exemplaar van het voorbeeld op te slaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het blauwdrukvoorbeeld. Veel artefacten hebben parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het bekijken van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden **gepubliceerd** voordat het kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar die wijziging kan het verplaatsen van uitlijning met NIST SP 800-53 besturingselementen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Geef op de nieuwe pagina aan de rechterkant een **versie** voor uw exemplaar van het blauwdrukvoorbeeld. Deze eigenschap is handig voor als u later een wijziging aanbrengt. **Geef wijzigingsnotities** op, zoals 'Eerste versie gepubliceerd uit het NIST SP 800-53 R4-blauwdrukvoorbeeld'. Selecteer vervolgens **Publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **gepubliceerd,** kan deze worden toegewezen aan een abonnement binnen de beheergroep waaraan het is opgeslagen. Deze stap is waar parameters worden verstrekt om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina met blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen:** selecteer een of meer abonnementen in de beheergroep waarop u uw exemplaar van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt voor elk van de parameters een toewijzing gemaakt met behulp van de ingevoerde parameters.
     - **Toewijzingsnaam:** de naam is vooraf ingevuld op basis van de naam van de blauwdruk.
       Verander indien nodig of laat zoals het is.
     - **Locatie:** selecteer een gebied waarin de beheerde identiteit moet worden gemaakt. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [beheerde identiteiten voor Azure-resources voor](../../../../active-directory/managed-identities-azure-resources/overview.md)meer informatie.
     - **Versie van de blauwdrukdefinitie**: Kies een **gepubliceerde** versie van uw exemplaar van het blauwdrukvoorbeeld.

   - Vergrendelingstoewijzing

     Selecteer de instelling voor blauwdrukvergrendeling voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de _standaardsysteemtoegewezen beheerde_ identiteitoptie.

   - Artefact-parameters

     De parameters die in deze sectie zijn gedefinieerd, zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters) omdat ze worden gedefinieerd tijdens de toewijzing van de blauwdruk. Zie [Artefact-parameterstabel](#artifact-parameters-table)voor een volledige lijst of artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert **u Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van artefacten begint. Implementatie duurt ongeveer een uur. Als u de status van implementatie wilt controleren, opent u de blauwdruktoewijzing.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis.** Azure-bronnen worden [geprijsd per product.](https://azure.microsoft.com/pricing/) Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van het uitvoeren van resources die door dit blauwdrukvoorbeeld worden geïmplementeerd, te schatten.

## <a name="artifact-parameters-table"></a>Tabel Artefactparameters

In de volgende tabel vindt u een lijst met de parameters van het blauwdrukartefact:

|Artefact-naam|Artefacttype|Parameternaam|Beschrijving|
|-|-|-|-|
|\[Preview:\]Controle NIST SP 800-53 R4 besturingselementen en implementeren van specifieke VM-extensies ter ondersteuning van auditvereisten|Beleidstoewijzing|Log Analytics-werkruimte-id waarvoor VM's moeten worden geconfigureerd|Dit is de ID (GUID) van de Log Analytics-werkruimte waarvoor de VM's moeten worden geconfigureerd.|
|\[Preview:\]Controle NIST SP 800-53 R4 besturingselementen en implementeren van specifieke VM-extensies ter ondersteuning van auditvereisten|Beleidstoewijzing|Lijst met resourcetypen waarmee diagnostische logboeken zijn ingeschakeld|Lijst met resourcetypen om te controleren of diagnostische logboekinstelling niet is ingeschakeld. Acceptabele waarden zijn te vinden op [azure monitor diagnostische logboekenschema's.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|\[Preview:\]Controle NIST SP 800-53 R4 besturingselementen en implementeren van specifieke VM-extensies ter ondersteuning van auditvereisten|Beleidstoewijzing|Lijst met gebruikers die moeten worden uitgesloten van de groep Windows VM Administrators|Een lijst met gescheiden punten van punten die moeten worden uitgesloten in de lokale groep Administrators. Bijvoorbeeld: beheerder; myUser1; myUser2|
|\[Preview:\]Controle NIST SP 800-53 R4 besturingselementen en implementeren van specifieke VM-extensies ter ondersteuning van auditvereisten|Beleidstoewijzing|Lijst met gebruikers die moeten worden opgenomen in de groep Windows VM-beheerders|Een lijst met gescheiden punten van puntkomma's die moeten worden opgenomen in de lokale groep Administrators. Bijvoorbeeld: beheerder; myUser1; myUser2|
|\[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Log Analytics-werkruimte voor Vm-schaalsets (Linux VMSS)|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u handmatig machtigingen voor 'Log Analytics Contributor' (of iets dergelijks) verlenen aan de hoofd-id van de beleidstoewijzing.|
|\[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Linux OS hebben ondersteund om aan bereik toe te voegen|Een lege array kan worden gebruikt om geen optionele parameters aan te geven:\[\]|
|\[Preview:\]Log Analytics Agent implementeren voor Linux VM's|Beleidstoewijzing|Log Analytics-werkruimte voor Linux VM's|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u handmatig machtigingen voor 'Log Analytics Contributor' (of iets dergelijks) verlenen aan de hoofd-id van de beleidstoewijzing.|
|\[Preview:\]Log Analytics Agent implementeren voor Linux VM's|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Linux OS hebben ondersteund om aan bereik toe te voegen|Een lege array kan worden gebruikt om geen optionele parameters aan te geven:\[\]|
|\[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Logboekanalysewerkruimte voor VM-schaalsets (Windows VMSS)|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u handmatig machtigingen voor 'Log Analytics Contributor' (of iets dergelijks) verlenen aan de hoofd-id van de beleidstoewijzing.|
|\[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Windows OS hebben ondersteund om aan bereik toe te voegen|Een lege array kan worden gebruikt om geen optionele parameters aan te geven:\[\]|
|\[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's|Beleidstoewijzing|Log Analytics-werkruimte voor Windows VM's|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u handmatig machtigingen voor 'Log Analytics Contributor' (of iets dergelijks) verlenen aan de hoofd-id van de beleidstoewijzing.|
|\[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Windows OS hebben ondersteund om aan bereik toe te voegen|Een lege array kan worden gebruikt om geen optionele parameters aan te geven:\[\]|
|Geavanceerde bedreigingsbeveiliging implementeren op opslagaccounts|Beleidstoewijzing|Effect|Informatie over beleidseffecten vindt u [op Azure Policy Effects begrijpen](../../../policy/concepts/effects.md)|
|Auditing implementeren op SQL-servers|Beleidstoewijzing|De waarde in dagen van de bewaartermijn (0 duidt op onbeperkte retentie)|Bewaardagen (optioneel, 180 dagen indien niet gespecificeerd)|
|Auditing implementeren op SQL-servers|Beleidstoewijzing|Naam van brongroep voor opslagaccount voor SQL-servercontrole|Controle schrijft databasegebeurtenissen naar een controlelogboek in uw Azure Storage-account (er wordt een opslagaccount gemaakt in elke regio waar een SQL Server wordt gemaakt die wordt gedeeld door alle servers in die regio). Belangrijk - voor een goede uitvoering van auditing worden de resourcegroep of de opslagaccounts niet verwijderd of de naam ervan naamswijziging.|
|Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren|Beleidstoewijzing|Voorvoegsel voor opslagaccount voor diagnose van netwerkbeveiligingsgroepen|Dit voorvoegsel wordt gecombineerd met de locatie van de netwerkbeveiligingsgroep om de gemaakte naam van het opslagaccount te vormen.|
|Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren|Beleidstoewijzing|Naam van resourcegroep voor opslagaccount voor diagnose van netwerkbeveiligingsgroepen (moet bestaan)|De brongroep waarin het opslagaccount wordt gemaakt. Deze resourcegroep moet al bestaan.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen hebt bekeken om het nist SP 800-53 R4-blauwontwerp te implementeren, gaat u naar de volgende artikelen voor meer informatie over de blauwdruk- en controletoewijzing:

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 blauwdruk - Overzicht](./index.md)
> [NIST SP 800-53 R4 blauwdruk - Controle mapping](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
