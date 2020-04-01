---
title: Implementeer Britse officiële & BRITSE NHS blauwdruk monsters
description: Implementeer stappen voor de britse officiële en Britse NHS-blauwdrukvoorbeelden, inclusief details van de parameterparameters voor blauwdrukken.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 3d69f4477163618aa225150ac6352a16e901f826
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546402"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Implementeer de Britse officiële en Britse NHS blauwdruk monsters

Voor het implementeren van de britse officiële en Britse NHS-blauwdrukmonsters moeten de volgende stappen worden genomen:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken uit het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauwdruk maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken met behulp van het voorbeeld als starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek het **UK OFFICIAL** of UK **NHS blueprint** sample onder Andere _voorbeelden_ en selecteer Gebruik **dit voorbeeld**.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** geef een naam op voor uw kopie van het blauwdrukvoorbeeld.
   - **Definitielocatie:** gebruik de ellips en selecteer de beheergroep om uw exemplaar van het voorbeeld op te slaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het blauwdrukvoorbeeld. Veel artefacten hebben parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het bekijken van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden **gepubliceerd** voordat het kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar die wijziging kan het verplaatsen van de standaard.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Geef op de nieuwe pagina aan de rechterkant een **versie** voor uw exemplaar van het blauwdrukvoorbeeld. Deze eigenschap is handig voor als u later een wijziging aanbrengt. **Geef change notes** zoals "Eerste versie gepubliceerd uit het Verenigd Koninkrijk officiële of Britse NHS blauwdruk monster." Selecteer vervolgens **Publiceren** onder aan de pagina.

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

Artefact-naam|Artefacttype|Parameternaam|Beschrijving|
|-|-|-|-|
|Blueprint initiatief voor het Verenigd Koninkrijk officiële of Britse NHS|Beleidstoewijzing |Resourcetypen voor het controleren van diagnostische logboeken (Beleid: Blueprint-initiatief voor Britse officiële of Britse NHS) |Lijst met resourcetypen om te controleren of de instelling voor diagnostische logboeken is ingeschakeld.  Zie [Ondersteunde services, schema's en categorieën voor Azure Diagnostic Logs voor](../../../../azure-monitor/platform/diagnostic-logs-schema.md)acceptabele waarden. |
|\[Preview:\]Log Analytics Agent implementeren voor Linux VM's |Beleidstoewijzing |Optioneel: Lijst met VM-afbeeldingen die Linux OS hebben \[ondersteund\]om aan bereik toe te voegen (Beleid: Voorbeeld: Log Analytics Agent implementeren voor Linux VM's) |(Optioneel) Standaardwaarde is _geen_. Zie [Een werkruimte voor Logboekanalyse maken in de Azure-portal](../../../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie. |
|\[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's |Beleidstoewijzing |Optioneel: lijst met VM-afbeeldingen die Windows OS hebben \[ondersteund\]om aan bereik toe te voegen (Beleid: Voorbeeld: Logboekanalyseagent implementeren voor Windows VM's) |(Optioneel) Standaardwaarde is _geen_. Zie [Een werkruimte voor Logboekanalyse maken in de Azure-portal](../../../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie. |

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen hebt bekeken om de blueprintvoorbeelden van de Britse officiële en Britse NHS-blauwdruk te implementeren, bezoekt u de volgende artikelen om meer te weten te komen over het overzicht en het toewijzen van controle:

> [!div class="nextstepaction"]
> [Britse officiële en Britse NHS blauwdrukken - Overzicht](./index.md)
> [UK OFFICIËLE en BRITSE NHS blauwdrukken - Control mapping](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).