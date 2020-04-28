---
title: Unieke sleutels gebruiken in Azure Cosmos DB
description: Meer informatie over het definiëren en gebruiken van unieke sleutels voor een Azure Cosmos-data base. In dit artikel wordt ook beschreven hoe unieke sleutels een laag van gegevens integriteit toevoegen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74869830"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Beperkingen voor unieke sleutels in Azure Cosmos DB

Unieke sleutels voegen een laag van gegevens integriteit toe aan een Azure Cosmos-container. U maakt een uniek sleutel beleid wanneer u een Azure Cosmos-container maakt. Met unieke sleutels, zorgt u ervoor dat een of meer waarden binnen een logische partitie uniek zijn. U kunt ook uniekheid per [partitie sleutel](partition-data.md)garanderen.

Nadat u een container met een beleid met unieke sleutels hebt gemaakt, wordt het maken van een nieuwe of een update van een bestaand item dat resulteert in een duplicaat in een logische partitie, voor komen, zoals opgegeven door de beperking van de unieke sleutel. De partitie sleutel in combi natie met de unieke sleutel garandeert de uniekheid van een item binnen het bereik van de container.

Denk bijvoorbeeld aan een Azure Cosmos-container met e-mail adres als de unieke- `CompanyID` sleutel beperking en als de partitie sleutel. Wanneer u het e-mail adres van de gebruiker met een unieke sleutel configureert, heeft elk item een uniek e- `CompanyID`mail adres binnen een gegeven. Twee items kunnen niet worden gemaakt met dubbele e-mail adressen en met dezelfde partitie sleutel waarde. 

Als u items met hetzelfde e-mail adres wilt maken, maar niet dezelfde voor naam, achternaam en e-mail adres, voegt u meer paden toe aan het beleid voor unieke sleutels. In plaats van een unieke sleutel te maken op basis van het e-mail adres, kunt u ook een unieke sleutel maken met een combi natie van de voor naam, achternaam en e-mail adres. Deze sleutel wordt ook wel een samengestelde unieke sleutel genoemd. In dit geval is elke unieke combi natie van de drie waarden binnen een `CompanyID` opgegeven toegestaan. 

De container kan bijvoorbeeld items met de volgende waarden bevatten, waarbij elk item voldoet aan de beperking van de unieke sleutel.

|CompanyID|Voornaam|Achternaam|E-mailadres|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Als u probeert een ander item in te voegen met de combi naties die in de vorige tabel worden vermeld, wordt een fout bericht weer gegeven. De fout geeft aan dat er niet aan de unieke-sleutel beperking is voldaan. U ontvangt ofwel `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` een retour bericht. 

## <a name="define-a-unique-key"></a>Een unieke sleutel definiëren

U kunt unieke sleutels alleen definiëren wanneer u een Azure Cosmos-container maakt. Een unieke sleutel ligt binnen het bereik van een logische partitie. Als u in het vorige voor beeld de container partitioneert op basis van de post code, worden er dubbele items in elke logische partitie opgedeeld. Houd rekening met de volgende eigenschappen wanneer u unieke sleutels maakt:

* U kunt een bestaande container niet bijwerken om een andere unieke sleutel te gebruiken. Met andere woorden, nadat een container is gemaakt met een uniek sleutel beleid, kan het beleid niet worden gewijzigd.

* Als u een unieke sleutel voor een bestaande container wilt instellen, maakt u een nieuwe container met de beperking voor de unieke sleutel. Gebruik het juiste hulp programma voor gegevens migratie om de gegevens van de bestaande container naar de nieuwe container te verplaatsen. Voor SQL-containers gebruikt u het [hulp programma voor gegevens migratie](import-data.md) om gegevens te verplaatsen. Voor MongoDB-containers gebruikt u [mongoimport. exe of mongorestore. exe](mongodb-migrate.md) om gegevens te verplaatsen.

* Een uniek sleutel beleid kan Maxi maal 16 padnamen bevatten. De waarden kunnen bijvoorbeeld, `/firstName` `/lastName`en `/address/zipCode`zijn. Elk uniek sleutel beleid kan Maxi maal 10 unieke sleutel beperkingen of combi Naties hebben. De gecombineerde paden voor elke unieke index beperking mogen niet groter zijn dan 60 bytes. In het vorige voor beeld is de voor naam, achternaam en e-mail adres samen één beperking. Deze beperking maakt gebruik van drie van de 16 mogelijke paden.

* Wanneer een container een uniek sleutel beleid heeft, zijn de kosten voor [aanvraag eenheden (ru)](request-units.md) om een item te maken, bij te werken en te verwijderen iets hoger.

* Sparse unieke sleutels worden niet ondersteund. Als sommige unieke padgegevens ontbreken, worden ze behandeld als Null-waarden, die deel uitmaken van de beperking van uniekheid. Daarom kan er slechts één item met een null-waarde zijn om aan deze beperking te voldoen.

* Unieke sleutel namen zijn hoofdletter gevoelig. Denk bijvoorbeeld aan een container waarbij de beperking van de unieke sleutel is `/address/zipcode`ingesteld op. Als uw gegevens een veld bevat met `ZipCode`de naam, Azure Cosmos db ' null ' als unieke sleutel invoegen `zipcode` , omdat dit niet `ZipCode`hetzelfde is als. Vanwege deze hoofdletter gevoeligheid kunnen alle andere records met ZipCode niet worden ingevoegd omdat de dubbele null-waarde de beperking van de unieke sleutel schendt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partition-data.md)
* Ontdekken [hoe u unieke sleutels kunt definiëren](how-to-define-unique-keys.md) bij het maken van een container
