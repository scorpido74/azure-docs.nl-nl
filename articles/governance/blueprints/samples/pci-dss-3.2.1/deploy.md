---
title: PCI-DSS v3.2.1-blauwdrukvoorbeeld implementeren
description: Stappen implementeren voor het blueprintvoorbeeld van de betaalkaartindustriegegevensbeveiliging v3.2.1, inclusief details van de parameters van de parameters voor blauwdrukkenartefact.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 9df9392430fd0496ffb5e635a6fc3e31ba708208
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905552"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Het blauwdrukvoorbeeld van PCI-DSS v3.2.1 implementeren

Als u het blueprintsample Azure Blueprints PCI-DSS v3.2.1 wilt implementeren, moeten de volgende stappen worden genomen:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken uit het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauwdruk maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken met behulp van het voorbeeld als starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek het **blauwdrukvoorbeeld van PCI-DSS v3.2.1** onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** geef een naam op voor uw exemplaar van het pci-DSS v3.2.1-blauwdrukvoorbeeld.
   - **Definitielocatie:** gebruik de ellips en selecteer de beheergroep om uw exemplaar van het voorbeeld op te slaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het blauwdrukvoorbeeld. Veel artefacten hebben parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het bekijken van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden **gepubliceerd** voordat het kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar die wijziging kan het verplaatsen van de PCI-DSS v3.2.1-standaard.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Geef op de nieuwe pagina aan de rechterkant een **versie** voor uw exemplaar van het blauwdrukvoorbeeld. Deze eigenschap is handig voor als u later een wijziging aanbrengt. **Geef wijzigingsnotities** op, zoals 'Eerste versie gepubliceerd uit het pci-DSS v3.2.1-blauwdrukvoorbeeld'. Selecteer vervolgens **Publiceren** onder aan de pagina.

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
|\[Bekijk\] de besturingselementen voor audit-PCI v3.2.1:2018 en implementeer specifieke VM-extensies om controlevereisten te ondersteunen|Beleidstoewijzing|Lijst met resourcetypen | Diagnostische instelling controleren voor geselecteerde resourcetypen. Standaardwaarde is dat alle resources zijn geselecteerd| 
|Toegestane locaties|Beleidstoewijzing|Lijst met toegestane locaties|Lijst met datacenterlocaties waartoe elke resource kan worden geïmplementeerd. Deze lijst is aanpasbaar aan de gewenste Azure-locaties wereldwijd. Selecteer locaties die u wilt toestaan.| 
|Toegestane locaties voor resourcegroepen|Beleidstoewijzing |Toegestane locatie |Met dit beleid u de locaties beperken waarin uw organisatie resourcegroepen kan maken. Dit beleid wordt gebruikt om uw geografische nalevingsvereisten af te dwingen.| 
|Auditing implementeren op SQL-servers|Beleidstoewijzing|Bewaardagen|Gegevensbewaring in aantal dagen. Standaardwaarde is 180, maar PCI vereist 365.| 
|Auditing implementeren op SQL-servers|Beleidstoewijzing|Naam van resourcegroep voor opslagaccount|Controle schrijft databasegebeurtenissen naar een controlelogboek in uw Azure Storage-account (er wordt een opslagaccount gemaakt in elke regio waar een SQL Server wordt gemaakt die wordt gedeeld door alle servers in die regio).| 

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen hebt bekeken om het blauwdrukvoorbeeld van PCI-DSS v3.2.1 te implementeren, gaat u naar de volgende artikelen voor meer informatie over het overzicht en de beheertoewijzing:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1-blauwdruk - Overzicht](./index.md)
> [PCI-DSS v3.2.1-blauwdruk - Besturingstoewijzing](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
