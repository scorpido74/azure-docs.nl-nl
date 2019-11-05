---
title: Rollen en vereisten voor Azure Data Share
description: Meer informatie over de rollen en vereisten voor toegangs beheer voor gegevens providers en gegevens gebruikers om gegevens te delen in de Azure-gegevens share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490584"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Rollen en vereisten voor Azure Data Share 

In dit artikel worden de rollen beschreven die nodig zijn voor het delen van gegevens met behulp van Azure data share, en voor het accepteren en ontvangen van gegevens met behulp van Azure data share. 

## <a name="roles-and-requirements"></a>Functies en vereisten

Azure data share maakt gebruik van beheerde identiteiten voor Azure-Services (voorheen bekend als MSIs) om te verifiëren bij onderliggende opslag accounts om gegevens te kunnen lezen die worden gedeeld door een gegevens provider en om gegevens te ontvangen die worden gedeeld als gegevens gebruiker. Als gevolg hiervan is er geen uitwisseling van referenties tussen de gegevens provider en de gegevens verbruiker. 

Het Managed Service Identity moet toegang krijgen tot het onderliggende opslag account of SQL database. De Azure data share-service gebruikt de Managed Service Identity van de Azure-gegevens share resource om gegevens te lezen en te schrijven. De gebruiker van Azure data share vereist de mogelijkheid om een roltoewijzing te maken voor de Managed Service Identity aan het opslag account of SQL database dat ze gegevens delen van/naar. 

In het geval van opslag is machtiging voor het maken van roltoewijzingen in de rol **eigenaar** , de rol gebruikers toegang beheerder of een aangepaste rol met micro soft. autorisatie/roltoewijzingen/schrijf machtiging toegewezen. 

Als u niet een eigenaar van het opslag account in kwestie bent en u geen roltoewijzing voor de beheerde identiteit van de Azure-gegevens share bron zelf kunt maken, is het aan te vragen een Azure-beheerder om namens u een roltoewijzing te maken. 

Hieronder volgt een samen vatting van de rollen die zijn toegewezen aan een gegevens share resource-beheerde identiteit:

| |  |  |
|---|---|---|
|**Opslag type**|**Gegevens provider archief**|**Doel archief van de gegevens verbruiker**|
|Azure Blob Storage| Gegevens lezer van BLOB voor opslag | Inzender voor Storage BLOB-gegevens
|Azure Data Lake gen1 | Eigenaar | Niet ondersteund
|Azure Data Lake Gen2 | Gegevens lezer van BLOB voor opslag | Inzender voor Storage BLOB-gegevens
|Azure SQL | dbo | dbo 
|

### <a name="data-providers"></a>Gegevens providers 
Als u een gegevensset wilt toevoegen aan een Azure-gegevens share, moeten de gegevens providers voor gegevens bronnen beheerde identiteit worden toegevoegd aan de rol van BLOB-gegevens lezer voor opslag. Dit wordt automatisch uitgevoerd door de Azure data share-service als de gebruiker gegevens sets toevoegt via Azure en een eigenaar van het opslag account is, of lid is van een aangepaste rol die de machtiging micro soft. Authorization/roltoewijzingen/schrijven heeft toegewezen. 

De gebruiker kan ook een Azure-beheerder hebben om de door de resource beheerde identiteit van de gegevens share bron hand matig toe te voegen aan de rol van de BLOB-gegevens lezer voor opslag. Het hand matig maken van deze roltoewijzing door de beheerder is ongeldig en moet een eigenaar van het opslag account zijn of een aangepaste roltoewijzing hebben. Dit geldt voor gegevens die worden gedeeld via Azure Storage of Azure Data Lake Gen2. 

Bij het delen van gegevens uit Azure Data Lake gen1 moet de roltoewijzing worden gemaakt aan de rol van eigenaar. 

Volg de onderstaande stappen voor het maken van een roltoewijzing voor de beheerde identiteit van de gegevens share Bron:

1. Navigeer naar het opslag account.
1. Selecteer **Access Control (IAM)** .
1. Selecteer **een roltoewijzing toevoegen**.
1. Selecteer onder *rol* *Storage BLOB data Reader*.
1. Typ onder *selecteren*de naam van uw Azure-gegevens share-account.
1. Klik op *Opslaan*.

Voor SQL-bronnen moet een gebruiker worden gemaakt op basis van een externe provider in het SQL database waarvan de gegevens worden gedeeld met dezelfde naam als de Azure data share-account. In de zelf studie [uw gegevens delen vindt u](share-your-data.md) een voorbeeld script samen met andere vereisten voor het delen van SQL. 

### <a name="data-consumers"></a>Gegevens gebruikers
Als u gegevens wilt ontvangen, moeten de gegevens die door de bron beheerde gegevens worden beheerd, worden toegevoegd aan de gegevensbeheerderrol en/of dbo-rol van een SQL database als er gegevens worden ontvangen in een SQL database. 

In het geval van opslag wordt dit automatisch gedaan door de Azure data share-service als de gebruiker gegevens sets toevoegt via Azure en een eigenaar van het opslag account is, of lid is van een aangepaste rol die de machtigingen voor micro soft. Authorization/roltoewijzingen/schrijven heeft Assign. 

De gebruiker kan ook een Azure-beheerder hebben om de gegevens door middel van een door de resource beheerde identiteit hand matig toe te voegen aan de rol BLOB data contributor voor opslag. Het hand matig maken van deze roltoewijzing door de beheerder is ongeldig en moet een eigenaar van het opslag account zijn of een aangepaste roltoewijzing hebben. Houd er rekening mee dat dit van toepassing is op gegevens die worden gedeeld met Azure Storage of Azure Data Lake Gen2. Het ontvangen van gegevens naar Azure Data Lake gen1 wordt niet ondersteund. 

Volg de onderstaande stappen voor het maken van een roltoewijzing voor de beheerde identiteit van de gegevens share bron hand matig:

1. Navigeer naar het opslag account.
1. Selecteer **Access Control (IAM)** .
1. Selecteer **een roltoewijzing toevoegen**.
1. Onder *rol*selecteert u *gegevens Inzender voor opslag-blobs*. 
1. Typ onder *selecteren*de naam van uw Azure-gegevens share-account.
1. Klik op *Opslaan*.

Als u gegevens deelt met behulp van onze REST Api's, moet u deze roltoewijzingen hand matig maken door het gegevens share account toe te voegen aan de juiste rollen. 

Als u gegevens ontvangt in een bron op basis van SQL, moet u ervoor zorgen dat er een nieuwe gebruiker wordt gemaakt van een externe provider met dezelfde naam als uw Azure-gegevens share-account. Zie vereisten in de zelf studie voor [accepteren en gegevens ontvangen](subscribe-to-data-share.md) . 

Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) voor meer informatie over het toevoegen van een roltoewijzing. hierin wordt beschreven hoe u een roltoewijzing kunt toevoegen aan een Azure-resource. 

## <a name="resource-provider-registration"></a>Registratie van resource provider 

Wanneer u een uitnodiging voor een Azure-gegevens share accepteert, moet u de resource provider micro soft. DataShare hand matig registreren bij uw abonnement. Volg deze stappen om de resource provider micro soft. DataShare te registreren bij uw Azure-abonnement. 

1. Ga in het Azure Portal naar **abonnementen**.
1. Selecteer het abonnement dat u voor Azure-gegevens share gebruikt.
1. Klik op **resource providers**.
1. Zoek naar micro soft. DataShare.
1. Klik op **registreren**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over rollen in azure- [definities van functie-begrippen](../role-based-access-control/role-definitions.md)

