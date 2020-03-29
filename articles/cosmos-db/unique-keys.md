---
title: Unieke sleutels gebruiken in Azure Cosmos DB
description: Meer informatie over het definiëren en gebruiken van unieke sleutels voor een Azure Cosmos-database. In dit artikel wordt ook beschreven hoe unieke sleutels een laag gegevensintegriteit toevoegen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869830"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Unieke belangrijke beperkingen in Azure Cosmos DB

Unieke sleutels voegen een laag gegevensintegriteit toe aan een Azure Cosmos-container. U maakt een uniek sleutelbeleid wanneer u een Azure Cosmos-container maakt. Met unieke toetsen zorgt u ervoor dat een of meer waarden binnen een logische partitie uniek zijn. U ook uniciteit garanderen per [partitiesleutel.](partition-data.md)

Nadat u een container met een uniek sleutelbeleid hebt gemaakt, wordt het maken van een nieuw of een update van een bestaand item, wat resulteert in een duplicaat binnen een logische partitie, voorkomen, zoals gespecificeerd door de unieke sleutelbeperking. De partitiesleutel in combinatie met de unieke sleutel garandeert de uniciteit van een artikel binnen het bereik van de container.

Beschouw bijvoorbeeld een Azure Cosmos-container met e-mailadres `CompanyID` als de unieke sleutelbeperking en als de partitiesleutel. Wanneer u het e-mailadres van de gebruiker configureert met een `CompanyID`unieke sleutel, heeft elk item een uniek e-mailadres binnen een bepaald. Twee items kunnen niet worden gemaakt met dubbele e-mailadressen en met dezelfde partitiesleutelwaarde. 

Als u items met hetzelfde e-mailadres wilt maken, maar niet dezelfde voornaam, achternaam en e-mailadres, voegt u meer paden toe aan het unieke sleutelbeleid. In plaats van alleen een unieke sleutel te maken op basis van het e-mailadres, u ook een unieke sleutel maken met een combinatie van de voornaam, achternaam en e-mailadres. Deze sleutel staat bekend als een samengestelde unieke sleutel. In dit geval is elke unieke combinatie `CompanyID` van de drie waarden binnen een gegeven toegestaan. 

De container kan bijvoorbeeld items met de volgende waarden bevatten, waarbij elk item de unieke sleutelbeperking nakomt.

|Bedrijfs-ID|Voornaam|Achternaam|E-mailadres|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam Fabrkam|   |   |gaby@fabraikam.com|

Als u een ander item probeert in te voegen met de combinaties die in de vorige tabel worden vermeld, ontvangt u een fout. De fout geeft aan dat de unieke sleutelbeperking niet is voldaan. U ontvangt `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` een van beide of als een retourbericht. 

## <a name="define-a-unique-key"></a>Een unieke sleutel definiëren

U alleen unieke sleutels definiëren wanneer u een Azure Cosmos-container maakt. Een unieke sleutel is scoped naar een logische partitie. In het vorige voorbeeld, als u de container partitie op basis van de postcode, je eindigt met gedupliceerde items in elke logische partitie. Houd rekening met de volgende eigenschappen wanneer u unieke toetsen maakt:

* U een bestaande container niet bijwerken om een andere unieke sleutel te gebruiken. Met andere woorden, nadat een container is gemaakt met een uniek sleutelbeleid, kan het beleid niet worden gewijzigd.

* Als u een unieke sleutel voor een bestaande container wilt instellen, maakt u een nieuwe container met de unieke sleutelbeperking. Gebruik het juiste hulpprogramma voor gegevensmigratie om de gegevens van de bestaande container naar de nieuwe container te verplaatsen. Voor SQL-containers gebruikt u het [hulpprogramma Gegevensmigratie](import-data.md) om gegevens te verplaatsen. Voor MongoDB-containers gebruikt u [mongoimport.exe of mongorestore.exe](mongodb-migrate.md) om gegevens te verplaatsen.

* Een uniek sleutelbeleid kan maximaal 16 padwaarden hebben. De waarden kunnen bijvoorbeeld `/firstName` `/lastName`, `/address/zipCode`, en . Elk uniek sleutelbeleid kan maximaal 10 unieke sleutelbeperkingen of -combinaties hebben. De gecombineerde paden voor elke unieke indexbeperking mogen niet groter zijn dan 60 bytes. In het vorige voorbeeld zijn voornaam, achternaam en e-mailadres samen één beperking. Deze beperking gebruikt 3 van de 16 mogelijke paden.

* Wanneer een container een uniek sleutelbeleid heeft, worden de kosten [van de Aanvraageenheid (RU)](request-units.md) om een item te maken, bij te werken en te verwijderen iets hoger.

* Schaarse unieke toetsen worden niet ondersteund. Als er unieke padwaarden ontbreken, worden ze behandeld als null-waarden, die deelnemen aan de uniciteitsbeperking. Om deze reden kan er slechts één artikel met een null-waarde zijn om aan deze beperking te voldoen.

* Unieke sleutelnamen zijn hoofdlettergevoelig. Overweeg bijvoorbeeld een container met de unieke `/address/zipcode`sleutelbeperking die is ingesteld op . Als uw gegevens een `ZipCode`veld hebben met de naam , voegt `zipcode` Azure Cosmos DB `ZipCode`'null' in als de unieke sleutel omdat deze niet hetzelfde is als . Vanwege deze gevoeligheid voor dit geval kunnen alle andere records met postcode niet worden ingevoegd omdat de dubbele 'null' in strijd is met de unieke sleutelbeperking.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partition-data.md)
* Ontdek [hoe u unieke sleutels definieert](how-to-define-unique-keys.md) bij het maken van een container
