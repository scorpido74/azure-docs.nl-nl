---
title: Rollen en vereisten voor Azure Data Share
description: Meer informatie over de machtigingen die nodig zijn om gegevens te delen en te ontvangen met Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265504"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Rollen en vereisten voor Azure Data Share 

In dit artikel worden rollen en machtigingen beschreven die nodig zijn om gegevens te delen en te ontvangen met azure data share-service. 

## <a name="roles-and-requirements"></a>Functies en vereisten

Met de Azure Data Share-service u gegevens delen zonder referenties uit te wisselen tussen gegevensprovider en consument. Azure Data Share-service gebruikt Beheerde identiteiten (voorheen bekend als MSI's) om te verifiëren bij Azure-gegevensarchief. 

De beheerde identiteit van de Azure Data Share-bron moet toegang krijgen tot het Azure-gegevensarchief. Azure Data Share-service gebruikt deze beheerde identiteit vervolgens om gegevens te lezen en te schrijven voor het delen op basis van momentopnamen en om een symbolische koppeling te maken voor het delen op de plaats. 

Om gegevens uit een Azure-gegevensarchief te delen of te ontvangen, heeft de gebruiker ten minste de volgende machtigingen nodig. Er zijn aanvullende machtigingen vereist voor sql-gebaseerd delen.
* Toestemming om naar het Azure-gegevensarchief te schrijven. Deze machtiging bestaat meestal in de rol **Inzender.**
* Toestemming om roltoewijzing te maken in het Azure-gegevensarchief. Doorgaans bestaat er toestemming om roltoewijzingen te maken in de rol **Eigenaar,** de rol Administrator van gebruikerstoegang of een aangepaste rol met Microsoft.Autorisatie/roltoewijzingen/schrijfmachtigingen toegewezen. Deze toestemming is niet vereist als de beheerde identiteit van de gegevenssharebron al toegang heeft tot het Azure-gegevensarchief. Zie onderstaande tabel voor de vereiste rol.

Hieronder vindt u een overzicht van de rollen die zijn toegewezen aan de beheerde identiteit van de Data Share-bron:

| |  |  |
|---|---|---|
|**Type gegevensarchief**|**Gegevensarchief gegevensbron gegevens van gegevensprovider**|**Data Consumer Target Data Store**|
|Azure Blob Storage| Blob-gegevenslezer opslag | Opslagblob-gegevensbijdrager
|Azure Data Lake Gen1 | Eigenaar | Niet ondersteund
|Azure Data Lake Gen2 | Blob-gegevenslezer opslag | Opslagblob-gegevensbijdrager
|Azure SQL Server | SQL DB-bijdrager | SQL DB-bijdrager
|Azure Data Explorer-cluster | Inzender | Inzender
|

Voor SQL-gebaseerd delen moet een SQL-gebruiker worden gemaakt vanuit een externe provider in de SQL-database met dezelfde naam als de Azure Data Share-bron. Hieronder vindt u een overzicht van de toestemming die vereist is door de SQL-gebruiker.

| |  |  |
|---|---|---|
|**SQL-databasetype**|**Gebruikersmachtigingen voor SQL-gebruikers van gegevensprovider**|**Gebruikersmachtigingen voor SQL-gebruikers van gegevensconsumer**|
|Azure SQL Database | db_datareader | db_datareader db_datawriter db_ddladmin
|Azure Synapse Analytics (voorheen SQL DW) | db_datareader | db_datareader db_datawriter db_ddladmin
|


### <a name="data-provider"></a>Gegevensprovider 
Als u een gegevensset wilt toevoegen in Azure Data Share, moet de beheerde identiteit van de beheerde identiteit van de providergegevensdelenbron toegang krijgen tot het azure-gegevensarchief van de bron. In het geval van een opslagaccount krijgt de beheerde identiteit van de gegevenssharebron bijvoorbeeld de rol Storage Blob Data Reader. 

Dit wordt automatisch gedaan door de Azure Data Share-service wanneer de gebruiker gegevensset toevoegt via Azure-portal en de gebruiker de juiste toestemming heeft. De gebruiker is bijvoorbeeld eigenaar van het Azure-gegevensarchief of is lid van een aangepaste rol waarvoor de microsoft.authorization/roltoewijzingen/schrijfmachtigingen zijn toegewezen. 

Als alternatief kan de gebruiker de eigenaar van het Azure-gegevensarchief handmatig de beheerde identiteit van de gegevenssharebron laten toevoegen aan het Azure-gegevensarchief. Deze actie hoeft slechts één keer per bron voor gegevensshare te worden uitgevoerd.

Als u een roltoewijzing wilt maken voor de beheerde identiteit van de gegevenssharebron, voert u de volgende stappen uit:

1. Navigeer naar het Azure-gegevensarchief.
1. Selecteer **Toegangsbeheer (IAM)**.
1. Selecteer **Een roltoewijzing toevoegen**.
1. Selecteer *onder Rol*de rol in de bovenstaande taaktabel (selecteer bijvoorbeeld voor opslagaccount de optie *Opslagblob-gegevenslezer).*
1. Typ *onder Selecteren*de naam van uw Azure Data Share-bron.
1. Klik op *Opslaan*.

Voor SQL-gebaseerde bronnen moet naast de bovenstaande stappen een SQL-gebruiker worden gemaakt vanuit een externe provider in de SQL-database met dezelfde naam als de Azure Data Share-bron. Deze gebruiker moet db_datareader *toestemming* worden verleend. Een voorbeeldscript samen met andere vereisten voor SQL-gebaseerd delen is te vinden in de [zelfstudie van uw gegevens delen.](share-your-data.md) 

### <a name="data-consumer"></a>Gegevensconsument
Om gegevens te ontvangen, moet de beheerde identiteit van de gebruikersgegevensshare-bron toegang krijgen tot het doelazure-gegevensarchief. In het geval van een opslagaccount krijgt de beheerde identiteit van de gegevenssharebron bijvoorbeeld de rol Opslagblob-gegevensbijdrager. 

Dit wordt automatisch gedaan door de Azure Data Share-service als de gebruiker een doelgegevensarchief opgeeft via Azure-portal en de gebruiker de juiste toestemming heeft. De gebruiker is bijvoorbeeld eigenaar van het Azure-gegevensarchief of is lid van een aangepaste rol waarvoor de microsoft.authorization/roltoewijzingen/schrijfmachtigingen zijn toegewezen. 

Als alternatief kan de gebruiker de eigenaar van het Azure-gegevensarchief handmatig de beheerde identiteit van de gegevenssharebron laten toevoegen aan het Azure-gegevensarchief. Deze actie hoeft slechts één keer per bron voor gegevensshare te worden uitgevoerd.

Als u handmatig een roltoewijzing voor de beheerde identiteit van de gegevenssharebron wilt maken, voert u de volgende stappen uit:

1. Navigeer naar het Azure-gegevensarchief.
1. Selecteer **Toegangsbeheer (IAM)**.
1. Selecteer **Een roltoewijzing toevoegen**.
1. Selecteer *onder Rol*de rol in de bovenstaande taaktabel (selecteer bijvoorbeeld voor opslagaccount de optie *Opslagblob-gegevenslezer).*
1. Typ *onder Selecteren*de naam van uw Azure Data Share-bron.
1. Klik op *Opslaan*.

Voor SQL-doel moet naast de bovenstaande stappen een SQL-gebruiker worden gemaakt vanuit een externe provider in de SQL-database met dezelfde naam als de Azure Data Share-bron. Deze gebruiker moet *db_datareader, db_datawriter, db_ddladmin* toestemming worden verleend. Een voorbeeldscript samen met andere vereisten voor SQL-gebaseerd delen is te vinden in de zelfstudie [voor het accepteren en ontvangen van gegevens.](subscribe-to-data-share.md) 

Als u gegevens deelt met REST API's, moet u deze roltoewijzingen handmatig maken. 

Raadpleeg [deze documentatie](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)voor meer informatie over het toevoegen van een roltoewijzing. 

## <a name="resource-provider-registration"></a>Registratie van resourceprovideren 

Als u azure-uitnodiging voor gegevensshare voor de eerste keer wilt bekijken in uw Azure-tenant, moet u de Microsoft.DataShare-bronprovider mogelijk handmatig registreren in uw Azure-abonnement. Voer deze stappen uit om de Microsoft.DataShare-bronprovider te registreren in uw Azure-abonnement. U hebt *inzendertoegang* tot het Azure-abonnement nodig om de resourceprovider te registreren.

1. Navigeer in de Azure-portal naar **Abonnementen**.
1. Selecteer het abonnement dat u gebruikt voor Azure Data Share.
1. Klik op **Resource providers**.
1. Zoeken naar Microsoft.DataShare.
1. Klik **op Registreren**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over rollen in Azure - [Functiedefinities begrijpen](../role-based-access-control/role-definitions.md)

