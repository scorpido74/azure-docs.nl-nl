---
title: Azure Blockchain Workbench-databaseweergaven
description: Overzicht van beschikbare Azure Blockchain Workbench Preview SQL DB-databaseweergaven.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325976"
---
# <a name="azure-blockchain-workbench-database-views"></a>Azure Blockchain Workbench-databaseweergaven

Azure Blockchain Workbench Preview levert gegevens van gedistribueerde grootboeken naar een *off-chain* SQL DB-database. De off-chain database maakt het mogelijk om SQL en bestaande tools, zoals [SQL Server Management Studio,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)te gebruiken om te communiceren met blockchain-gegevens.

Azure Blockchain Workbench biedt een reeks databaseweergaven die toegang bieden tot gegevens die nuttig zijn bij het uitvoeren van uw query's. Deze weergaven worden sterk gedenormaliseerd om het eenvoudig te maken om snel aan de slag te gaan met het bouwen van rapporten, analyses en anderszins blockchain-gegevens te consumeren met bestaande tools en zonder databasepersoneel om te scholen.

Deze sectie bevat een overzicht van de databaseweergaven en de gegevens die deze bevatten.

> [!NOTE]
> Direct gebruik van databasetabellen die buiten deze weergaven in de database worden gevonden, terwijl dit mogelijk is, wordt niet ondersteund.
>

## <a name="vwapplication"></a>vwApplication

In deze weergave vindt u informatie over **toepassingen** die zijn geüpload naar Azure Blockchain Workbench.

| Name                             | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName                  | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDescription           | nvarchar(255) | Ja         | Een beschrijving van de aanvraag |
| ApplicationDisplayName           | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled               | bit           | Nee          | Identificeert of de toepassing momenteel is ingeschakeld<br /> **Let op:** Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database. |
| Geüploade DtTm                     | datum2(7)  | Nee          | De datum en het tijdstip waarop een contract is geüpload |
| Geüploaddoor userid                 | int           | Nee          | De id van de gebruiker die de toepassing heeft geüpload |
| Geüploaddoor userexternalid         | nvarchar(255) | Nee          | De externe id voor de gebruiker die de toepassing heeft geüpload. Deze id is standaard de gebruiker van de Azure Active Directory voor het consortium.                                                                                                |
| Status van geüploaddoor gebruikerprovisioning | int           | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor de gebruiker aangegeven. Mogelijke waarden zijn: <br />0 – Gebruiker is gemaakt door de API<br />1 – Er is een sleutel gekoppeld aan de gebruiker in de database<br />2 – De gebruiker is volledig ingericht                         |
| UploadedByUserFirstName          | nvarchar(50)  | Ja         | De voornaam van de gebruiker die het contract heeft geüpload |
| UploadedByUserLastName           | nvarchar(50)  | Ja         | De achternaam van de gebruiker die het contract heeft geüpload |
| UploadedByUserEmailAddress       | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die het contract heeft geüpload |

## <a name="vwapplicationrole"></a>vwApplicationRol

In deze weergave vindt u meer informatie over de rollen die zijn gedefinieerd in Azure Blockchain Workbench-toepassingen.

In een toepassing *Asset Transfer* kunnen bijvoorbeeld rollen zoals *rollen koper* en *verkoper* worden gedefinieerd.

| Name                   | Type             | Kan null zijn | Beschrijving                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nee          | Een unieke id voor de toepassing           |
| ApplicationName        | nvarchar(50)     | Nee          | De naam van de toepassing                       |
| ApplicationDescription | nvarchar(255)    | Ja         | Een beschrijving van de aanvraag                  |
| ApplicationDisplayName | nvarchar(255)    | Nee          | De naam die moet worden weergegeven in een gebruikersinterface      |
| RoleId (RolId)                 | int              | Nee          | Een unieke id voor een rol in de toepassing |
| RoleName               | nvarchar50)      | Nee          | De naam van de rol                              |
| RoleDescription        | description(255) | Ja         | Een beschrijving van de rol                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

In deze weergave vindt u informatie over de rollen die zijn gedefinieerd in Azure Blockchain Workbench-toepassingen en de gebruikers die eraan zijn gekoppeld.

In een asset *transfer-toepassing* kan *John Smith* bijvoorbeeld worden gekoppeld aan de rol *Koper.*

| Name                       | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nee          | Een unieke id voor de toepassing                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Nee          | De naam van de toepassing                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Ja         | Een beschrijving van de aanvraag                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nee          | Een unieke id voor een rol in de toepassing                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nee          | De naam van de rol                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Ja         | Een beschrijving van de rol                                                                                                                                                                                                             |
| UserId                     | int           | Nee          | De id van de gebruiker die aan de rol is gekoppeld |
| UserExternalId             | nvarchar(255) | Nee          | De externe id voor de gebruiker die aan de rol is gekoppeld. Deze id is standaard de gebruiker van de Azure Active Directory voor het consortium.                                                                     |
| UserProvisioningStatus     | int           | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor de gebruiker aangegeven. Mogelijke waarden zijn: <br />0 – Gebruiker is gemaakt door de API<br />1 – Er is een sleutel gekoppeld aan de gebruiker in de database<br />2 – De gebruiker is volledig ingericht |
| UserFirstName              | nvarchar(50)  | Ja         | De voornaam van de gebruiker die aan de rol is gekoppeld |
| UserLastName               | nvarchar(255) | Ja         | De achternaam van de gebruiker die aan de rol is gekoppeld |
| Gebruikerse-mailadres           | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die aan de rol is gekoppeld |

## <a name="vwconnectionuser"></a>vwConnectionUser

Deze weergave bevat details over de verbindingen die zijn gedefinieerd in Azure Blockchain Workbench en de gebruikers die eraan zijn gekoppeld. Voor elke verbinding bevat deze weergave de volgende gegevens:

-   Gekoppelde grootboekgegevens
-   Gekoppelde gebruikersgegevens

| Name                     | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nee          | De unieke id voor een verbinding in Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Nee          | De url van het eindpunt voor een verbinding |
| ConnectionFundingAccount | nvarchar(255) | Ja         | De financieringsrekening die aan een verbinding is gekoppeld, indien van toepassing |
| LedgerId (LedgerId)                 | int           | Nee          | De unieke id voor een grootboek |
| LedgerName               | nvarchar(50)  | Nee          | De naam van het grootboek |
| LedgerDisplayName        | nvarchar(255) | Nee          | De naam van het grootboek dat moet worden weergegeven in de gebruikersinterface |
| UserId                   | int           | Nee          | De id van de gebruiker die aan de verbinding is gekoppeld |
| UserExternalId           | nvarchar(255) | Nee          | De externe id voor de gebruiker die aan de verbinding is gekoppeld. Deze id is standaard de gebruiker van de Azure Active Directory voor het consortium. |
| UserProvisioningStatus   | int           | Nee          |Hiermee wordt de huidige status van het inrichtingsproces voor de gebruiker aangegeven. Mogelijke waarden zijn: <br />0 – Gebruiker is gemaakt door de API<br />1 – Er is een sleutel gekoppeld aan de gebruiker in de database<br />2 – De gebruiker is volledig ingericht |
| UserFirstName            | nvarchar(50)  | Ja         | De voornaam van de gebruiker die is gekoppeld aan de verbinding |
| UserLastName             | nvarchar(255) | Ja         | De achternaam van de gebruiker die is gekoppeld aan de verbinding |
| Gebruikerse-mailadres         | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die aan de verbinding is gekoppeld |

## <a name="vwcontract"></a>vwContract

In deze weergave vindt u meer informatie over geïmplementeerde contracten. Voor elk contract bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Bijbehorende grootboekimplementatie voor de functie
-   Details voor de gebruiker die de actie heeft gestart
-   Details met betrekking tot het blockchain-blok en de transactie

| Name                                     | Type           | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nee          | De unieke id voor een verbinding in Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Nee          | De url van het eindpunt voor een verbinding |
| ConnectionFundingAccount                 | nvarchar(255)  | Ja         | De financieringsrekening die aan een verbinding is gekoppeld, indien van toepassing |
| LedgerId (LedgerId)                                 | int            | Nee          | De unieke id voor een grootboek |
| LedgerName                               | nvarchar(50)   | Nee          | De naam van het grootboek |
| LedgerDisplayName                        | nvarchar(255)  | Nee          | De naam van het grootboek dat moet worden weergegeven in de gebruikersinterface |
| ApplicationId                            | int            | Nee          | Een unieke id voor de toepassing |
| ApplicationName                          | nvarchar (50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName                   | nvarchar (255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled                       | bit            | Nee          | Hiermee wordt aangegeven of de toepassing momenteel is ingeschakeld.<br /> **Let op:** Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database.  |
| Werkstroom-id                               | int            | Nee          | Een unieke id voor de werkstroom die is gekoppeld aan een contract |
| Werkstroomnaam                             | nvarchar(50)   | Nee          | De naam van de werkstroom die is gekoppeld aan een contract |
| WerkstroomWeergaveNaam                      | nvarchar(255)  | Nee          | De naam van de werkstroom die is gekoppeld aan het contract dat wordt weergegeven in de gebruikersinterface |
| Werkstroombeschrijving                      | nvarchar(255)  | Ja         | De beschrijving van de werkstroom die is gekoppeld aan een contract |
| ContractcodeId                           | int            | Nee          | Een unieke id voor de contractcode die aan het contract is gekoppeld |
| ContractFileName                         | int            | Nee          | De naam van het bestand met de slimme contractcode voor deze werkstroom. |
| ContractUploadedDtTm                     | int            | Nee          | De datum en tijd waarop de contractcode is geüpload |
| ContractId (ContractId)                               | int            | Nee          | De unieke id voor het contract |
| ContractProvisioningStatus               | int            | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor het contract aangegeven. Mogelijke waarden zijn: <br />0 – Het contract is gemaakt door de API in de database<br />1 – Het contract is verzonden naar het grootboek<br />2 – Het contract is met succes geïmplementeerd in het grootboek<br />3 of 4 - Het contract is niet geïmplementeerd in het grootboek<br />5 - Het contract is met succes geïmplementeerd in het grootboek <br /><br />Vanaf versie 1.5 worden waarden 0 tot en met 5 ondersteund. Voor backwards compatibility in de huidige release is **vwContractV0** beschikbaar die alleen waarden 0 tot en met 2 ondersteunt. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | Het e-mailadres van de gebruiker die het contract heeft geïmplementeerd |
| ContractGeïmplementeerdByUserId                 | int            | Nee          | Een externe id voor de gebruiker die het contract heeft geïmplementeerd. Standaard is deze id de guid die de Azure Active Directory ID voor de gebruiker vertegenwoordigt.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nee          | Een externe id voor de gebruiker die het contract heeft geïmplementeerd. Standaard is deze id de guid die de Azure Active Directory ID voor de gebruiker vertegenwoordigt.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor de gebruiker aangegeven. Mogelijke waarden zijn: <br />0 – gebruiker is gemaakt door de API<br />1 – Er is een sleutel gekoppeld aan de gebruiker in de database <br />2 – De gebruiker is volledig ingericht                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Ja         | De voornaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName           | nvarchar(255)  | Ja         | De achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Ja         | Het e-mailadres van de gebruiker die het contract heeft geïmplementeerd |

## <a name="vwcontractaction"></a>vwContractAction

Deze visie vertegenwoordigt de meerderheid van de informatie met betrekking tot acties die op contracten zijn genomen en is bedoeld om gemeenschappelijke rapportagescenario's gemakkelijk te vergemakkelijken. Voor elke actie die wordt ondernomen, bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Gekoppelde slimme contractfunctie en parameterdefinitie
-   Bijbehorende grootboekimplementatie voor de functie
-   Specifieke instantiewaarden voor parameters
-   Details voor de gebruiker die de actie heeft gestart
-   Details met betrekking tot het blockchain-blok en de transactie

| Name                                     | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName                          | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName                   | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled                       | bit           | Nee          | In dit veld wordt aangegeven of de toepassing momenteel is ingeschakeld. Opmerking - Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database.                                                  |
| Werkstroom-id                               | int           | Nee          | Een unieke id voor een werkstroom |
| Werkstroomnaam                             | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam                      | nvarchar(255) | Nee          | De naam van de werkstroom die moet worden weergegeven in een gebruikersinterface |
| Werkstroombeschrijving                      | nvarchar(255) | Ja         | De beschrijving van de werkstroom |
| ContractId (ContractId)                               | int           | Nee          | Een unieke id voor het contract |
| ContractProvisioningStatus               | int           | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor het contract aangegeven. Mogelijke waarden zijn: <br />0 – Het contract is gemaakt door de API in de database<br />1 – Het contract is verzonden naar het grootboek<br />2 – Het contract is met succes geïmplementeerd in het grootboek<br />3 of 4 - Het contract is niet geïmplementeerd in het grootboek<br />5 - Het contract is met succes geïmplementeerd in het grootboek <br /><br />Vanaf versie 1.5 worden waarden 0 tot en met 5 ondersteund. Voor backwards compatibility in de huidige release is **vwContractActionV0** beschikbaar die alleen waarden 0 tot en met 2 ondersteunt. |
| ContractcodeId                           | int           | Nee          | Een unieke id voor de codeuitvoering van het contract |
| ContractLedgerIdentifier                 | nvarchar(255) | Ja         | Een unieke id die is gekoppeld aan de geïmplementeerde versie van een slim contract voor een specifiek gedistribueerd grootboek. Bijvoorbeeld, Ethereum. |
| ContractGeïmplementeerdByUserId                 | int           | Nee          | De unieke id van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Ja         | Voornaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName           | nvarchar(255) | Ja         | Achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nee          | Externe id van de gebruiker die het contract heeft geïmplementeerd. Standaard is deze id de guid die hun identiteit vertegenwoordigt in het consortium Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die het contract heeft geïmplementeerd |
| WerkstroomFunctieId                       | int           | Nee          | Een unieke id voor een werkstroomfunctie |
| WerkstroomFunctieNaam                     | nvarchar(50)  | Nee          | De naam van de functie |
| WerkstroomfunctieWeergaveNaam              | nvarchar(255) | Nee          | De naam van een functie die in de gebruikersinterface moet worden weergegeven |
| WerkstroomfunctieBeschrijving              | nvarchar(255) | Nee          | De beschrijving van de functie |
| ContractActionId                         | int           | Nee          | De unieke id voor een contractactie |
| ContractActionProvisioningStatus         | int           | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor de contractactie aangegeven. Mogelijke waarden zijn: <br />0 – De contractactie is gemaakt door de API in de database<br />1 – De contractactie is naar het grootboek gestuurd<br />2 – De contractactie is met succes geïmplementeerd in het grootboek<br />3 of 4 - Het contract is niet geïmplementeerd in het grootboek<br />5 - Het contract is met succes geïmplementeerd in het grootboek <br /><br />Vanaf versie 1.5 worden waarden 0 tot en met 5 ondersteund. Voor backwards compatibility in de huidige release is **vwContractActionV0** beschikbaar die alleen waarden 0 tot en met 2 ondersteunt. |
| ContractActionTimestamp                  | datum(2,7) | Nee          | De tijdstempel van de contractactie |
| ContractactionuitgevoerdDoorUserId           | int           | Nee          | Unieke id van de gebruiker die de contractactie heeft uitgevoerd |
| ContractactionuitgevoerddoorUserFirstName    | int           | Ja         | Voornaam van de gebruiker die de contractactie heeft uitgevoerd |
| ContractactionuitgevoerdDoorUserLastName     | nvarchar(50)  | Ja         | Achternaam van de gebruiker die de contractactie heeft uitgevoerd |
| ContractactionuitgevoerddoorUserExternalId   | nvarchar(255) | Ja         | Externe id van de gebruiker die de contractactie heeft uitgevoerd. Standaard is deze id de guid die hun identiteit vertegenwoordigt in het consortium Azure Active Directory. |
| Contractactionuitgevoerddooruseremailadres | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die de contractactie heeft uitgevoerd |
| WerkstroomfunctieParameterId              | int           | Nee          | Een unieke id voor een parameter van de functie |
| WerkstroomFunctieParameterName            | nvarchar(50)  | Nee          | De naam van een parameter van de functie |
| WerkstroomfunctieParameterDisplayName     | nvarchar(255) | Nee          | De naam van een functieparameter die in de gebruikersinterface moet worden weergegeven |
| WorkflowfunctieParameterDataTypeId      | int           | Nee          | De unieke id voor het gegevenstype dat is gekoppeld aan een parameter van de werkstroomfunctie |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Nee          | De naam van een gegevenstype dat is gekoppeld aan een parameter van de werkstroomfunctie |
| ContractActionParameterWaarde             | nvarchar(255) | Nee          | De waarde voor de parameter die is opgeslagen in het slimme contract |
| BlockHash BlockHash                                | nvarchar(255) | Ja         | De hash van het blok |
| Bloknummer                              | int           | Ja         | Het nummer van het blok op het grootboek |
| BlockTimestamp                           | datum(2,7) | Ja         | De tijdstempel van het blok |
| TransactionId                            | int           | Nee          | Een unieke id voor de transactie |
| Transactievan                          | nvarchar(255) | Ja         | De partij die de transactie heeft ontstaan |
| TransactionTo                            | nvarchar(255) | Ja         | De partij die werd afgehandeld met |
| TransactionHash                          | nvarchar(255) | Ja         | De hash van een transactie |
| TransactionIsWorkbenchTransactie        | bit           | Ja         | Een bit dat aangeeft of de transactie een Azure Blockchain Workbench-transactie is |
| Transactieprovisioningstatus            | int           | Ja         | Hiermee wordt de huidige status van het inrichtingsproces voor de transactie aangegeven. Mogelijke waarden zijn: <br />0 – De transactie is gemaakt door de API in de database<br />1 – De transactie is verzonden naar het grootboek<br />2 – De transactie is met succes geïmplementeerd in het grootboek                 |
| Transactiewaarde                         | decimaal(32,2) | Ja         | De waarde van de transactie |

## <a name="vwcontractproperty"></a>vwContractProperty

Deze weergave vertegenwoordigt de meerderheid van de informatie met betrekking tot eigenschappen die zijn gekoppeld aan een contract en is ontworpen om veelvoorkomende rapportagescenario's gemakkelijk te vergemakkelijken. Voor elke genomen eigenschap bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Gegevens voor de gebruiker die de werkstroom heeft geïmplementeerd
-   Gekoppelde definitie van smart contract-eigenschappen
-   Specifieke instantiewaarden voor eigenschappen
-   Details voor het staatseigendom van het contract

| Name                               | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName                    | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName             | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled                 | bit           | Nee          | Hiermee wordt aangegeven of de toepassing momenteel is ingeschakeld.<br />**Let op:** Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database.                      |
| Werkstroom-id                         | int           | Nee          | De unieke id voor de werkstroom |
| Werkstroomnaam                       | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam                | nvarchar(255) | Nee          | De naam van de werkstroom die wordt weergegeven in de gebruikersinterface |
| Werkstroombeschrijving                | nvarchar(255) | Ja         | De beschrijving van de werkstroom |
| ContractId (ContractId)                         | int           | Nee          | De unieke id voor het contract |
| ContractProvisioningStatus         | int           | Nee          | Hiermee wordt de huidige status van het inrichtingsproces voor het contract aangegeven. Mogelijke waarden zijn: <br />0 – Het contract is gemaakt door de API in de database<br />1 – Het contract is verzonden naar het grootboek<br />2 – Het contract is met succes geïmplementeerd in het grootboek<br />3 of 4 - Het contract is niet geïmplementeerd in het grootboek<br />5 - Het contract is met succes geïmplementeerd in het grootboek <br /><br />Vanaf versie 1.5 worden waarden 0 tot en met 5 ondersteund. Voor backwards compatibility in de huidige release is **vwContractPropertyV0** beschikbaar die alleen waarden 0 tot en met 2 ondersteunt. |
| ContractcodeId                     | int           | Nee          | Een unieke id voor de codeuitvoering van het contract |
| ContractLedgerIdentifier           | nvarchar(255) | Ja         | Een unieke id die is gekoppeld aan de geïmplementeerde versie van een slim contract voor een specifiek gedistribueerd grootboek. Bijvoorbeeld, Ethereum. |
| ContractGeïmplementeerdByUserId           | int           | Nee          | De unieke id van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ja         | Voornaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName     | nvarchar(255) | Ja         | Achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nee          | Externe id van de gebruiker die het contract heeft geïmplementeerd. Deze id is standaard de guid die hun identiteit vertegenwoordigt in het consortium Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die het contract heeft geïmplementeerd |
| WerkstroomPropertyId                 | int           |             | Een unieke id voor een eigenschap van een werkstroom |
| WorkflowPropertyDataTypeId         | int           | Nee          | De id van het gegevenstype van de eigenschap |
| WerkstroomPropertyDataTypeName       | nvarchar(50)  | Nee          | De naam van het gegevenstype van de eigenschap |
| WerkstroomPropertyName               | nvarchar(50)  | Nee          | De naam van de eigenschap werkstroom |
| WerkstroomPropertyDisplayName        | nvarchar(255) | Nee          | De weergavenaam van de eigenschap werkstroom |
| Beschrijving van werkstroomEigenschapbeschrijving        | nvarchar(255) | Ja         | Een beschrijving van het pand |
| ContractPropertyValue              | nvarchar(255) | Nee          | De waarde voor een woning op het contract |
| StateName                          | nvarchar(50)  | Ja         | Als deze eigenschap de status van het contract bevat, is dit de weergavenaam voor de status. Als deze niet aan de status is gekoppeld, wordt de waarde nietig verklaard. |
| StateDisplayName                   | nvarchar(255) | Nee          | Als deze eigenschap de status bevat, is dit de weergavenaam voor de status. Als deze niet aan de status is gekoppeld, wordt de waarde nietig verklaard. |
| Statuswaarde                         | nvarchar(255) | Ja         | Als deze eigenschap de status bevat, is dit de statuswaarde. Als deze niet aan de status is gekoppeld, wordt de waarde nietig verklaard. |

## <a name="vwcontractstate"></a>vwContractState

Deze weergave vertegenwoordigt de meerderheid van de informatie met betrekking tot de stand van een specifiek contract en is bedoeld om gemeenschappelijke rapportagescenario's gemakkelijk te vergemakkelijken. Elke record in deze weergave bevat de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Gegevens voor de gebruiker die de werkstroom heeft geïmplementeerd
-   Gekoppelde definitie van smart contract-eigenschappen
-   Details voor het staatseigendom van het contract

| Name                               | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName                    | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName             | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled                 | bit           | Nee          | Hiermee wordt aangegeven of de toepassing momenteel is ingeschakeld.<br />**Let op:** Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database. |
| Werkstroom-id                         | int           | Nee          | Een unieke id voor de werkstroom |
| Werkstroomnaam                       | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam                | nvarchar(255) | Nee          | De naam die wordt weergegeven in de gebruikersinterface |
| Werkstroombeschrijving                | nvarchar(255) | Ja         | De beschrijving van de werkstroom |
| ContractLedgerImplementatieid     | nvarchar(255) | Ja         | Een unieke id die is gekoppeld aan de geïmplementeerde versie van een slim contract voor een specifiek gedistribueerd grootboek. Bijvoorbeeld, Ethereum. |
| ContractId (ContractId)                         | int           | Nee          | Een unieke id voor het contract |
| ContractProvisioningStatus         | int           | Nee          |Hiermee wordt de huidige status van het inrichtingsproces voor het contract aangegeven. Mogelijke waarden zijn: <br />0 – Het contract is gemaakt door de API in de database<br />1 – Het contract is verzonden naar het grootboek<br />2 – Het contract is met succes geïmplementeerd in het grootboek<br />3 of 4 - Het contract is niet geïmplementeerd in het grootboek<br />5 - Het contract is met succes geïmplementeerd in het grootboek <br /><br />Vanaf versie 1.5 worden waarden 0 tot en met 5 ondersteund. Voor backwards compatibility in de huidige release is **vwContractStateV0** beschikbaar die alleen waarden 0 tot en met 2 ondersteunt. |
| ConnectionId                       | int           | Nee          | Een unieke id voor het blockchain-exemplaar waaraan de werkstroom wordt geïmplementeerd |
| ContractcodeId                     | int           | Nee          | Een unieke id voor de codeuitvoering van het contract |
| ContractGeïmplementeerdByUserId           | int           | Nee          | Unieke id van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nee          | Externe id van de gebruiker die het contract heeft geïmplementeerd. Standaard is deze id de guid die hun identiteit vertegenwoordigt in het consortium Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ja         | Voornaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName     | nvarchar(255) | Ja         | Achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker die het contract heeft geïmplementeerd |
| WerkstroomPropertyId                 | int           | Nee          | Een unieke id voor een werkstroomeigenschap |
| WorkflowPropertyDataTypeId         | int           | Nee          | De id van het gegevenstype van de eigenschap werkstroom |
| WerkstroomPropertyDataTypeName       | nvarchar(50)  | Nee          | De naam van het gegevenstype van de eigenschap werkstroom |
| WerkstroomPropertyName               | nvarchar(50)  | Nee          | De naam van de eigenschap werkstroom |
| WerkstroomPropertyDisplayName        | nvarchar(255) | Nee          | De weergavenaam van de eigenschap die moet worden weergegeven in een gebruikersinterface |
| Beschrijving van werkstroomEigenschapbeschrijving        | nvarchar(255) | Ja         | De beschrijving van het pand |
| ContractPropertyValue              | nvarchar(255) | Nee          | De waarde voor een woning die in het contract is opgeslagen |
| StateName                          | nvarchar(50)  | Ja         | Als deze eigenschap de status bevat, wordt de weergavenaam voor de status weergegeven. Als deze niet aan de status is gekoppeld, wordt de waarde nietig verklaard. |
| StateDisplayName                   | nvarchar(255) | Nee          | Als deze eigenschap de status bevat, is dit de weergavenaam voor de status. Als deze niet aan de status is gekoppeld, wordt de waarde nietig verklaard. |
| Statuswaarde                         | nvarchar(255) | Ja         | Als deze eigenschap de status bevat, is dit de statuswaarde. Als deze niet aan de status is gekoppeld, wordt de waarde nietig verklaard. |

## <a name="vwuser"></a>vwUser

In deze weergave vindt u informatie over de consortiumleden die zijn ingericht om Azure Blockchain Workbench te gebruiken. Standaard worden gegevens ingevuld door de initiële inrichting van de gebruiker.

| Name               | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id                 | int           | Nee          | Een unieke id voor een gebruiker |
| Externe ID         | nvarchar(255) | Nee          | Een externe id voor een gebruiker. Standaard is deze id de guid die de Azure Active Directory ID voor de gebruiker vertegenwoordigt. |
| ProvisioningStatus | int           | Nee          |Hiermee wordt de huidige status van het inrichtingsproces voor de gebruiker aangegeven. Mogelijke waarden zijn: <br />0 – Gebruiker is gemaakt door de API<br />1 – Er is een sleutel gekoppeld aan de gebruiker in de database<br />2 – De gebruiker is volledig ingericht |
| FirstName          | nvarchar(50)  | Ja         | De voornaam van de gebruiker |
| LastName           | nvarchar(50)  | Ja         | De achternaam van de gebruiker |
| Emailaddress       | nvarchar(255) | Ja         | Het e-mailadres van de gebruiker |

## <a name="vwworkflow"></a>vwWorkflow

Deze weergave vertegenwoordigt de details core workflow metadata evenals de functies en parameters van de workflow. Ontworpen voor rapportage, bevat het ook metagegevens over de toepassing die is gekoppeld aan de werkstroom. Deze weergave bevat gegevens uit meerdere onderliggende tabellen om rapportage over workflows te vergemakkelijken. Voor elke werkstroom bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Statusstatus van gekoppelde werkstroomstart

| Name                              | Type          | Kan null zijn | Beschrijving                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName                   | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName            | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled                | bit           | Nee          | Identificeert of de toepassing is ingeschakeld |
| Werkstroom-id                        | int           | Ja         | Een unieke id voor een werkstroom |
| Werkstroomnaam                      | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam               | nvarchar(255) | Nee          | De naam die wordt weergegeven in de gebruikersinterface |
| Werkstroombeschrijving               | nvarchar(255) | Ja         | De beschrijving van de werkstroom. |
| WerkstroomConstructorFunctionId     | int           | Nee          | De id van de werkstroomfunctie die fungeert als constructor voor de werkstroom |
| WerkstroomStartStateId              | int           | Nee          | Een unieke id voor de status |
| WerkstroomStartStateName            | nvarchar(50)  | Nee          | De naam van de staat |
| WerkstroomStartStateDisplayName     | nvarchar(255) | Nee          | De naam die moet worden weergegeven in de gebruikersinterface voor de status |
| WerkstroomStartStateDescription     | nvarchar(255) | Ja         | Een beschrijving van de werkstroomstatus |
| WorkflowStartStateStyle           | nvarchar(50)  | Ja         | Deze waarde identificeert het percentage voltooid dat de werkstroom is wanneer in deze status |
| WorkflowStartStateValue           | int           | Nee          | De waarde van de staat |
| WerkstroomStartStatePercentcomplete | int           | Nee          | Een tekstbeschrijving die clients een hint geeft over hoe deze status in de gebruikersinterface moet worden weergegeven. Ondersteunde staten zijn *succes* en *mislukking* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunctie

Deze weergave vertegenwoordigt de details core workflow metadata evenals de functies en parameters van de workflow. Ontworpen voor rapportage, bevat het ook metagegevens over de toepassing die is gekoppeld aan de werkstroom. Deze weergave bevat gegevens uit meerdere onderliggende tabellen om rapportage over workflows te vergemakkelijken. Voor elke werkstroomfunctie bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Details van de werkstroomfunctie

| Name                                 | Type          | Kan null zijn | Beschrijving                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName                      | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName               | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled                   | bit           | Nee          | Identificeert of de toepassing is ingeschakeld |
| Werkstroom-id                           | int           | Nee          | Een unieke id voor een werkstroom |
| Werkstroomnaam                         | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam                  | nvarchar(255) | Nee          | De naam van de werkstroom die wordt weergegeven in de gebruikersinterface |
| Werkstroombeschrijving                  | nvarchar(255) | Ja         | De beschrijving van de werkstroom |
| WerkstroomFunctieId                   | int           | Nee          | Een unieke id voor een functie |
| WerkstroomFunctieNaam                 | nvarchar(50)  | Ja         | De naam van de functie |
| WerkstroomfunctieWeergaveNaam          | nvarchar(255) | Nee          | De naam van een functie die in de gebruikersinterface moet worden weergegeven |
| WerkstroomfunctieBeschrijving          | nvarchar(255) | Ja         | De beschrijving van de werkstroomfunctie |
| WerkstroomFunctieConstructor        | bit           | Nee          | Identificeert of de werkstroomfunctie de constructor voor de werkstroom is |
| WerkstroomfunctieParameterId          | int           | Nee          | Een unieke id voor een parameter van een functie |
| WerkstroomFunctieParameterName        | nvarchar(50)  | Nee          | De naam van een parameter van de functie |
| WerkstroomfunctieParameterDisplayName | nvarchar(255) | Nee          | De naam van een functieparameter die in de gebruikersinterface moet worden weergegeven |
| WorkflowfunctieParameterDataTypeId  | int           | Nee          | Een unieke id voor het gegevenstype dat is gekoppeld aan een parameter werkstroomfunctie |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Nee          | De naam van een gegevenstype dat is gekoppeld aan een parameter van de werkstroomfunctie |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Deze weergave vertegenwoordigt de eigenschappen die zijn gedefinieerd voor een werkstroom. Voor elke eigenschap bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Details van de eigenschap Workflow

| Name                         | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName              | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName       | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| ApplicationEnabled           | bit           | Nee          | Hiermee wordt aangegeven of de toepassing momenteel is ingeschakeld.<br />**Let op:** Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database. |
| Werkstroom-id                   | int           | Nee          | Een unieke id voor de werkstroom |
| Werkstroomnaam                 | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam          | nvarchar(255) | Nee          | De naam die moet worden weergegeven voor de werkstroom in een gebruikersinterface |
| Werkstroombeschrijving          | nvarchar(255) | Ja         | Een beschrijving van de werkstroom |
| WerkstroomPropertyID           | int           | Nee          | Een unieke id voor een eigenschap van een werkstroom |
| WerkstroomPropertyName         | nvarchar(50)  | Nee          | De naam van het pand |
| Beschrijving van werkstroomEigenschapbeschrijving  | nvarchar(255) | Ja         | Een beschrijving van het pand |
| WerkstroomPropertyDisplayName  | nvarchar(255) | Nee          | De naam die moet worden weergegeven in een gebruikersinterface |
| WerkstroomPropertyWorkflowId   | int           | Nee          | De id van de werkstroom waaraan deze eigenschap is gekoppeld |
| WorkflowPropertyDataTypeId   | int           | Nee          | De id van het gegevenstype dat voor de eigenschap is gedefinieerd |
| WerkstroomPropertyDataTypeName | nvarchar(50)  | Nee          | De naam van het gegevenstype dat voor de eigenschap is gedefinieerd |
| WorkflowPropertyIsState      | bit           | Nee          | In dit veld wordt aangegeven of deze werkstroomeigenschap de status van de werkstroom bevat |

## <a name="vwworkflowstate"></a>vwWorkflowState

Deze weergave vertegenwoordigt de eigenschappen die zijn gekoppeld aan een werkstroom. Voor elk contract bevat deze weergave de volgende gegevens:

-   Gekoppelde toepassingsdefinitie
-   Gekoppelde werkstroomdefinitie
-   Gegevens over werkstroomstatus

| Name                         | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nee          | Een unieke id voor de toepassing |
| ApplicationName              | nvarchar(50)  | Nee          | De naam van de toepassing |
| ApplicationDisplayName       | nvarchar(255) | Nee          | Een beschrijving van de aanvraag |
| ApplicationEnabled           | bit           | Nee          | Hiermee wordt aangegeven of de toepassing momenteel is ingeschakeld.<br />**Let op:** Hoewel een toepassing kan worden weergegeven als uitgeschakeld in de database, blijven gekoppelde contracten op de blockchain staan en blijven gegevens over die contracten in de database. |
| Werkstroom-id                   | int           | Nee          | De unieke id voor de werkstroom |
| Werkstroomnaam                 | nvarchar(50)  | Nee          | De naam van de werkstroom |
| WerkstroomWeergaveNaam          | nvarchar(255) | Nee          | De naam die wordt weergegeven in de gebruikersinterface voor de werkstroom |
| Werkstroombeschrijving          | nvarchar(255) | Ja         | De beschrijving van de werkstroom |
| WorkflowStateID              | int           | Nee          | De unieke id voor de status |
| WerkstroomStaatNaam            | nvarchar(50)  | Nee          | De naam van de staat |
| WorkflowStateDisplayName     | nvarchar(255) | Nee          | De naam die moet worden weergegeven in de gebruikersinterface voor de status |
| WerkstroomStatusBeschrijving     | nvarchar(255) | Ja         | Een beschrijving van de werkstroomstatus |
| WorkflowStatePercentComplete | int           | Nee          | Deze waarde identificeert het percentage voltooid dat de werkstroom is wanneer in deze status |
| WorkflowStateValue           | nvarchar(50)  | Nee          | Waarde van de staat |
| WorkflowStateStyle           | nvarchar(50)  | Nee          | Een tekstbeschrijving die clients een hint geeft over hoe deze status in de gebruikersinterface moet worden weergegeven. Ondersteunde staten zijn *succes* en *mislukking* |
