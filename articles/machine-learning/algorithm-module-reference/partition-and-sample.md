---
title: 'Partitie en voorbeeld: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de partitie- en voorbeeldmodule in Azure Machine Learning om steekproeven uit te voeren op een gegevensset of om partities te maken van uw gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477507"
---
# <a name="partition-and-sample-module"></a>Partitie- en voorbeeldmodule

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik de module Partitie en Voorbeeld om steekproeven uit te voeren op een gegevensset of om partities te maken van uw gegevensset.

Sampling is een belangrijk hulpmiddel in machine learning, omdat u hiermee de grootte van een gegevensset verkleinen met behoud van dezelfde verhouding van waarden. Deze module ondersteunt verschillende gerelateerde taken die belangrijk zijn in machine learning: 

- Uw gegevens verdelen in meerdere onderafdelingen van dezelfde grootte. 

  U de partities gebruiken voor crossvalidatie of om aanvragen toe te wijzen aan willekeurige groepen.

- Gegevens scheiden in groepen en vervolgens werken met gegevens uit een specifieke groep. 

  Nadat u willekeurig aanvragen aan verschillende groepen hebt toegewezen, moet u mogelijk de functies wijzigen die aan slechts één groep zijn gekoppeld.

- Bemonstering. 

  U een percentage van de gegevens extraheren, willekeurige steekproeven toepassen of een kolom kiezen die u wilt gebruiken voor het in evenwicht brengen van de gegevensset en gestratificeerde steekproeven uitvoeren op de waarden ervan.

- Een kleinere gegevensset maken voor het testen. 

  Als u veel gegevens hebt, u alleen de eerste *n-rijen* gebruiken tijdens het instellen van de pijplijn en vervolgens overschakelen naar het gebruik van de volledige gegevensset wanneer u uw model bouwt. U ook steekproeven gebruiken om een kleinere gegevensset te maken voor gebruik in ontwikkeling.

## <a name="configure-the-module"></a>De module configureren

Deze module ondersteunt de volgende methoden voor het verdelen van uw gegevens in partities of voor bemonstering. Kies eerst de methode en stel vervolgens extra opties in die de methode vereist.

- Head
- Steekproeven
- Toewijzen aan vouwen
- Kies vouw

### <a name="get-top-n-rows-from-a-dataset"></a>TOP N-rijen ophalen uit een gegevensset

Gebruik deze modus om alleen de eerste *n-rijen* te krijgen. Deze optie is handig als u een pijplijn op een klein aantal rijen wilt testen en u hoeft de gegevens op geen enkele manier in evenwicht te brengen of te bemonsteren.

1. Voeg de **partitie- en voorbeeldmodule** toe aan uw pijplijn in de interface en verbind de gegevensset.  

1. **Partitie- of voorbeeldmodus**: Stel deze optie in op **Hoofd**.

1. **Aantal rijen dat u wilt selecteren:** Voer het aantal rijen in dat moet worden retournerd.

   Het aantal rijen moet een niet-negatief geheel getal zijn. Als het aantal geselecteerde rijen groter is dan het aantal rijen in de gegevensset, wordt de volledige gegevensset geretourneerd.

1. Verzend de pijplijn.

De module wordt uitgevoerd met één gegevensset die alleen het opgegeven aantal rijen bevat. De rijen worden altijd vanaf de bovenkant van de gegevensset gelezen.

### <a name="create-a-sample-of-data"></a>Een voorbeeld van gegevens maken

Deze optie ondersteunt eenvoudige willekeurige bemonstering of gestratificeerde steekproeven. Het is handig als u een kleinere representatieve voorbeeldgegevensset wilt maken voor het testen.

1. Voeg de **partitie- en voorbeeldmodule** toe aan uw pijplijn en verbind de gegevensset.

1. **Partitie- of voorbeeldmodus**: Stel deze optie in op **Sampling**.

1. **Bemonsteringspercentage**: Voer een waarde in tussen 0 en 1. deze waarde geeft het percentage rijen uit de brongegevensset op dat moet worden opgenomen in de uitvoergegevensset.

   Als u bijvoorbeeld slechts de helft van `0.5` de oorspronkelijke gegevensset wilt, voert u in om aan te geven dat de bemonsteringssnelheid 50 procent moet zijn.

   De rijen van de invoergegevensset worden geschud en selectief in de uitvoergegevensset geplaatst, volgens de opgegeven verhouding.

1. **Willekeurig zaad voor bemonstering**: Vul eventueel een geheel getal in dat als zaadwaarde kan worden gebruikt.

   Deze optie is belangrijk als u wilt dat de rijen elke keer op dezelfde manier worden verdeeld. De standaardwaarde is **0,** wat betekent dat een startzaad wordt gegenereerd op basis van de systeemklok. Deze waarde kan leiden tot iets andere resultaten elke keer dat u de pijplijn uitvoert.

1. **Gestratificeerde splitsing voor steekproeven**: Selecteer deze optie als het belangrijk is dat de rijen in de gegevensset gelijkmatig worden verdeeld door een sleutelkolom voordat u deze bemonstert.

   Selecteer **voor De sleutelkolom Stratificatie voor bemonstering**een enkele *stratakolom* die u wilt gebruiken bij het verdelen van de gegevensset. De rijen in de gegevensset worden vervolgens als volgt verdeeld:

   1. Alle invoerrijen worden gegroepeerd (gestratificeerd) door de waarden in de opgegeven lagenkolom.

   1. Rijen worden binnen elke groep geschud.

   1. Elke groep wordt selectief toegevoegd aan de uitvoergegevensset om aan de opgegeven verhouding te voldoen.


1. Verzend de pijplijn.

   Met deze optie wordt in de module één gegevensset uitgevoerd die een representatieve steekproef van de gegevens bevat. Het resterende, niet-gesamplede gedeelte van de gegevensset wordt niet uitgevoerd. 

## <a name="split-data-into-partitions"></a>Gegevens splitsen in partities

Gebruik deze optie wanneer u de gegevensset wilt verdelen in subsets van de gegevens. Deze optie is ook handig wanneer u een aangepast aantal plooien wilt maken voor crossvalidatie of rijen wilt splitsen in verschillende groepen.

1. Voeg de **partitie- en voorbeeldmodule** toe aan uw pijplijn en verbind de gegevensset.

1. Selecteer **Toewijzen aan vouwen voor** **partitie- of voorbeeldmodus.**

1. **Vervanging gebruiken in de partitionering**: Selecteer deze optie als u wilt dat de gesamplede rij weer in de groep rijen wordt geplaatst voor mogelijk hergebruik. Als gevolg hiervan kan dezelfde rij worden toegewezen aan meerdere plooien.

   Als u geen vervanging gebruikt (de standaardoptie), wordt de gesamplede rij niet teruggezet in de groep rijen voor mogelijk hergebruik. Als gevolg hiervan kan elke rij worden toegewezen aan slechts één vouw.

1. **Gerandomiseerde split**: Selecteer deze optie als u wilt dat rijen willekeurig worden toegewezen aan vouwen.

   Als u deze optie niet selecteert, worden rijen toegewezen om door de round-robin-methode te vouwen.

1. **Willekeurig zaad**: Vul eventueel een geheel getal in om te gebruiken als zaadwaarde. Deze optie is belangrijk als u wilt dat de rijen elke keer op dezelfde manier worden verdeeld. Anders betekent de standaardwaarde van **0** dat een willekeurig startzaad wordt gebruikt.

1. **Geef de partitiemethode**op: geef aan hoe u wilt dat gegevens aan elke partitie worden verdeeld, met behulp van deze opties:

   - **Partitie gelijkmatig:** gebruik deze optie om een gelijk aantal rijen in elke partitie te plaatsen. Als u het aantal uitvoerpartities wilt opgeven, voert u een heel getal in het aantal plooien opgeven in om gelijkmatig in het vak **te splitsen.**

   - **Partitie met aangepaste verhoudingen**: Gebruik deze optie om de grootte van elke partitie op te geven als een door komma's gescheiden lijst.

     Stel dat u drie partities wilt maken. De eerste partitie bevat 50 procent van de gegevens. De overige twee partities bevatten elk 25 procent van de gegevens. Voer in de **lijst met verhoudingen, gescheiden door** komma,.de volgende getallen in: **.5, .25, .25**.

     De som van alle partitiegroottes moet precies 1 bedragen.

     Als u getallen invoert die optellen tot *minder dan 1,* wordt een extra partitie gemaakt om de resterende rijen vast te houden. Als u bijvoorbeeld de waarden **0,2** en **0,3**invoert, wordt een derde partitie gemaakt om de resterende 50 procent van alle rijen vast te houden.
     
     Als u getallen invoert die meer *dan 1*bevatten, wordt er een fout gegenereerd wanneer u de pijplijn uitvoert.

1. **Gestratificeerde splitsing**: Selecteer deze optie als u wilt dat de rijen worden gestratificeerd wanneer ze worden gesplitst, en kies vervolgens de _stratakolom_.

1. Verzend de pijplijn.

   Met deze optie worden meerdere gegevenssets uitgevoerd door de module. De gegevenssets worden verdeeld volgens de regels die u hebt opgegeven.

### <a name="use-data-from-a-predefined-partition"></a>Gegevens uit een vooraf gedefinieerde partitie gebruiken  

Gebruik deze optie wanneer u een gegevensset in meerdere partities hebt verdeeld en nu elke partitie op zijn beurt wilt laden voor verdere analyse of verwerking.

1. Voeg de **partitie- en voorbeeldmodule** toe aan de pijplijn.

1. Sluit de module aan op de uitvoer van een vorige instantie van **Partitie en Voorbeeld**. In dat exemplaar moet de optie **Toewijzen aan vouwen** zijn gebruikt om een aantal partities te genereren.

1. **Partitie- of voorbeeldmodus**: Selecteer **Fold kiezen**selecteren .

1. **Geef op welke vouw moet worden gesampled uit**: Selecteer een partitie die u wilt gebruiken door de index in te voeren. Partitie-indexen zijn 1-gebaseerd. Als u de gegevensset bijvoorbeeld in drie delen opgedeeld, hebben de partities de indexen 1, 2 en 3.

   Als u een ongeldige indexwaarde invoert, wordt een fout in de ontwerptijd verhoogd: 'Fout 0018: gegevensset bevat ongeldige gegevens'.

   Naast het groeperen van de gegevensset op vouwen, u de gegevensset in twee groepen scheiden: een doelvouw en al het andere. Voer hiervoor de index van één vouw in en selecteer vervolgens de **optie Pick-aanvulling van de geselecteerde vouw** om alles te krijgen, behalve de gegevens in de opgegeven vouw.

1. Als u met meerdere partities werkt, moet u meer exemplaren van de **partitie- en voorbeeldmodule** toevoegen om elke partitie te verwerken.

   De module **Partitie en Voorbeeld** in de tweede rij is bijvoorbeeld ingesteld op **Toewijzen aan plooien**en de module in de derde rij is ingesteld op Fold **kiezen.**   

   ![Partitie en voorbeeld](./media/module/partition-and-sample.png)

1. Verzend de pijplijn.

   Met deze optie wordt in de module één gegevensset uitgevoerd die alleen de rijen bevat die aan die vouw zijn toegewezen.

> [!NOTE]
>  U de vouwaanduidingen niet rechtstreeks bekijken. Ze zijn alleen aanwezig in de metadata.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 