---
title: Rollen en vereisten voor de preview-versie van Azure data share
description: Meer informatie over de rollen en vereisten voor toegangs beheer voor gegevens providers en gegevens gebruikers om gegevens te delen in de preview-versie van Azure data delen.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: c0841f6386440776c6ea719f9932a53cada9d9c4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166379"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Rollen en vereisten voor de preview-versie van Azure data share

In dit artikel worden de rollen beschreven die vereist zijn voor het delen van gegevens met behulp van Azure data share preview, en voor het accepteren en ontvangen van gegevens met behulp van Azure data share preview. 

## <a name="roles-and-requirements"></a>Functies en vereisten

Azure data share maakt gebruik van beheerde identiteiten voor Azure-Services (voorheen bekend als MSIs) om te verifiëren bij onderliggende opslag accounts om gegevens te kunnen lezen die worden gedeeld door een gegevens provider en om gegevens te ontvangen die worden gedeeld als gegevens gebruiker. Als gevolg hiervan is er geen uitwisseling van referenties tussen de gegevens provider en de gegevens verbruiker. 

Aan de Managed Service Identity moet toegang worden verleend tot de onderliggende opslag accounts. De Azure data share-service gebruikt de Managed Service Identity van de Azure-gegevens share resource om gegevens te lezen en te schrijven. De gebruiker van de Azure-gegevens share vereist dat er een roltoewijzing kan worden gemaakt voor de Managed Service Identity aan het opslag account waaruit de gegevens worden gedeeld van/tot. Machtigingen voor het maken van roltoewijzingen bestaan in de rol **eigenaar** , de rol gebruikers toegang beheerder of een aangepaste rol met micro soft. autorisatie/roltoewijzingen/schrijf machtiging toegewezen. 

Als u niet een eigenaar van het opslag account in kwestie bent en u geen roltoewijzing voor de beheerde identiteit van de Azure-gegevens share bron zelf kunt maken, is het aan te vragen een Azure-beheerder om namens u een roltoewijzing te maken. 

Hieronder volgt een samen vatting van de rollen die zijn toegewezen aan een gegevens share resource-beheerde identiteit:

| |  |  |
|---|---|---|
|**Opslag type**|**Bron opslag account van gegevens provider**|**Doel opslag account voor gegevens verbruiker**|
|Azure Blob Storage| Gegevens lezer van BLOB voor opslag | Inzender voor Storage BLOB-gegevens
|Azure Data Lake gen1 | Eigenaar | Niet ondersteund
|Azure Data Lake Gen2 | Gegevens lezer van BLOB voor opslag | Inzender voor Storage BLOB-gegevens
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

### <a name="data-consumers"></a>Gegevens gebruikers
Als u gegevens wilt ontvangen, moeten de gegevens die worden beheerd door een resource die gebruikers gegevens van de bron beheren, worden toegevoegd aan de rol van de BLOB-gegevens bijdrager. Deze rol is vereist om de Azure data share-service in staat te stellen te schrijven naar het opslag account. Dit wordt automatisch uitgevoerd door de Azure data share-service als de gebruiker gegevens sets toevoegt via Azure en een eigenaar van het opslag account is, of een lid is van een aangepaste rol die de machtiging micro soft. Authorization/Role Assignment/Write is toegewezen heeft. 

De gebruiker kan ook een Azure-beheerder hebben om de gegevens door middel van een door de resource beheerde identiteit hand matig toe te voegen aan de rol BLOB data contributor voor opslag. Het hand matig maken van deze roltoewijzing door de beheerder is ongeldig en moet een eigenaar van het opslag account zijn of een aangepaste roltoewijzing hebben. Houd er rekening mee dat dit van toepassing is op gegevens die worden gedeeld met Azure Storage of Azure Data Lake Gen2. Het ontvangen van gegevens naar Azure Data Lake gen1 wordt niet ondersteund. 

Volg de onderstaande stappen voor het maken van een roltoewijzing voor de beheerde identiteit van de gegevens share bron hand matig:

1. Navigeer naar het opslag account.
1. Selecteer **Access Control (IAM)** .
1. Selecteer **een roltoewijzing toevoegen**.
1. Onder *rol*selecteert u *gegevens Inzender voor opslag-blobs*. 
1. Typ onder *selecteren*de naam van uw Azure-gegevens share-account.
1. Klik op *Opslaan*.

Als u gegevens deelt met behulp van onze REST Api's, moet u deze roltoewijzingen hand matig maken door het gegevens share account toe te voegen aan de juiste rollen. 

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

