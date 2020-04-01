---
title: Caf Foundation blueprint sample implementeren
description: Zet stappen in voor het blauwdrukvoorbeeld van de CAF Foundation, inclusief details van de parameterparameters voor blauwdrukken.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: df17f8c3f539e25635ea4718be9d51d5e5e3f708
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545521"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Het blueprintsample voor Microsoft Cloud Adoption Framework voor Azure Foundation implementeren

Als u het blueprintsample voor microsoft cloudadoptie voor Azure (CAF) Foundation wilt implementeren, moeten de volgende stappen worden genomen:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken uit het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauwdruk maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken met behulp van het voorbeeld als starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek het **blauwdrukvoorbeeld** van de CAF Foundation onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** Geef een naam op voor uw exemplaar van het blauwdrukvoorbeeld van de CAF Foundation.
   - **Definitielocatie:** gebruik de ellips en selecteer de beheergroep om uw exemplaar van het voorbeeld op te slaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het blauwdrukvoorbeeld. Veel artefacten hebben parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het bekijken van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden **gepubliceerd** voordat het kan worden toegewezen en geïmplementeerd. De kopie van de blauwdruk monster kan worden aangepast aan uw omgeving en behoeften, maar die wijziging kan verplaatsen uit de buurt van de CAF Foundation blauwdruk.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Geef op de nieuwe pagina aan de rechterkant een **versie** voor uw exemplaar van het blauwdrukvoorbeeld. Deze eigenschap is handig voor als u later een wijziging aanbrengt. **Geef wijzigingennotities** op, zoals 'Eerste versie gepubliceerd uit de CAF Foundation blauwdruk steekproef'. Selecteer vervolgens **Publiceren** onder aan de pagina.

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
     - **Locatie:** selecteer een gebied waarin de beheerde identiteit moet worden gemaakt.
     - Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren.
       Zie [beheerde identiteiten voor Azure-resources voor](../../../../active-directory/managed-identities-azure-resources/overview.md)meer informatie.
     - **Versie van de blauwdrukdefinitie**: Kies een **gepubliceerde** versie van uw exemplaar van het blauwdrukvoorbeeld.

   - Vergrendelingstoewijzing

     Selecteer de instelling voor blauwdrukvergrendeling voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Kies de _standaardsysteemtoegewezen beheerde_ identiteitsoptie of de _door de gebruiker toegewezen_ identiteitsoptie.

   - Blauwdrukparameters

     De parameters die in deze sectie worden gedefinieerd, worden door veel van de artefacten in de blauwdrukdefinitie gebruikt om consistentie te bieden.

     - **Organisatie:** Voer uw organisatienaam in, zoals Contoso, moet uniek zijn.
     - **Azure-regio:** selecteer het Azure-gebied voor implementatie.
     - **Toegestane locaties**: Welke Azure-regio's laat u resources inbouwen?
     
   - Artefact-parameters

     De parameters die in deze sectie zijn gedefinieerd, zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters) omdat ze worden gedefinieerd tijdens de toewijzing van de blauwdruk. Zie [Artefact-parameterstabel](#artifact-parameters-table)voor een volledige lijst of artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert **u Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van artefacten begint. Implementatie duurt ongeveer een uur. Als u de status van implementatie wilt controleren, opent u de blauwdruktoewijzing.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis.** Azure-bronnen worden [geprijsd per product.](https://azure.microsoft.com/pricing/) Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van het uitvoeren van resources die door dit blauwdrukvoorbeeld worden geïmplementeerd, te schatten.

## <a name="artifact-parameters-table"></a>Tabel Artefactparameters

In de volgende tabel vindt u een lijst met de parameters van het blauwdrukartefact:

|Artefact-naam|Artefacttype|Parameternaam|Beschrijving|
|-|-|-|-|
|Toegestane opslagaccount-SKU's|Beleidstoewijzing|Policy_Allowed-StorageAccount-SKU's|SKU gebruikt in opslagaccounts voor diagnostisch logboek|
|Toegestane virtuele machine SKU's|Beleidstoewijzing|Policy_Allowed-VM-SKU's|Toegestane virtuele machine SKU's|
|CostCenter-TAG toevoegen aan resourcegroepen|Beleidstoewijzing|Policy_CostCenter_Tag|CostCenter-TAG en de waarde ervan toevoegen aan de resourcegroep|
|Resourcetypen die u niet in uw omgeving wilt toestaan|Beleidstoewijzing|Beleids_Allowed-Resourcetypen|Welke Azure-bronnen u wilt toestaan in uw omgeving|
|Sleutelkluis implementeren|Resource Manager-sjabloon|KV-Toegangsbeleid|**Vergrendeld** - Azure AD-groep of -gebruiker <Object ID> om machtigingen toe te kennen in Key Vault|
|Logboekanalyse implementeren|Resource Manager-sjabloon|LogAnalytics_DataRetention|**Vergrendeld** - Aantal dagen dat gegevens worden bewaard in Log Analytics|
|Logboekanalyse implementeren|Resource Manager-sjabloon|LogAnalytics_Location|**Vergrendeld** - Regio die wordt gebruikt bij het instellen van de werkruimte|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen hebt bekeken om het blauwdrukvoorbeeld van de CAF Foundation te implementeren, gaat u naar het volgende artikel om meer te weten te komen over de architectuur:

> [!div class="nextstepaction"]
> [CAF Foundation blauwdruk - Overzicht](./index.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).