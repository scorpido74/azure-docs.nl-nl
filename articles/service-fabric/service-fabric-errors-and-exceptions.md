---
title: Common FabricClient uitzonderingen gegooid
description: Beschrijft de veelvoorkomende uitzonderingen en fouten die door de FabricClient-API's kunnen worden gegenereerd tijdens het uitvoeren van toepassings- en clusterbeheerbewerkingen.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457934"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Algemene uitzonderingen en fouten bij het werken met de FabricClient-API's
Met de [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API's kunnen cluster- en toepassingsbeheerders beheertaken uitvoeren op een Service Fabric-toepassing, -service of -cluster. Bijvoorbeeld het implementeren, upgraden en verwijderen van toepassingen, het controleren van de status van een cluster of het testen van een service. Toepassingsontwikkelaars en clusterbeheerders kunnen de FabricClient API's gebruiken om tools te ontwikkelen voor het beheer van het cluster en de toepassingen van Service Fabric.

Er zijn veel verschillende soorten bewerkingen die kunnen worden uitgevoerd met FabricClient.  Elke methode kan uitzonderingen maken voor fouten als gevolg van onjuiste invoer, runtime-fouten of tijdelijke infrastructuurproblemen.  Zie de API-referentiedocumentatie om te zoeken welke uitzonderingen door een specifieke methode worden gegenereerd. Er zijn echter enkele uitzonderingen die door veel verschillende [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API's kunnen worden gegooid. In de volgende tabel worden de uitzonderingen weergegeven die vaak voorkomen in de FabricClient-API's.

| Uitzondering | Gegooid wanneer |
| --- |:--- |
| [Uitzondering op System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |Het [object FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) bevindt zich in een gesloten staat. Gooi het [FabricClient-object](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) dat u gebruikt weg en instantiate een nieuw [FabricClient-object.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Er is een time-out ophet moment van de operatie. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) wordt geretourneerd wanneer de bewerking meer duurt dan MaxOperationTimeout heeft voltooid. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |De toegangscontrole voor de bewerking is mislukt. E_ACCESSDENIED is terug. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Er is een runtime-fout opgetreden tijdens het uitvoeren van de bewerking. Een van de FabricClient-methoden kan mogelijk [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)gooien, de eigenschap [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) geeft de exacte oorzaak van de uitzondering aan. Foutcodes worden gedefinieerd in de opsomgegevens van [FabricErrorCode.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |De bewerking is mislukt als gevolg van een tijdelijke fout voorwaarde van een soort. Een bewerking kan bijvoorbeeld mislukken omdat een quorum van replica's tijdelijk niet bereikbaar is. Tijdelijke uitzonderingen komen overeen met mislukte bewerkingen die opnieuw kunnen worden geprobeerd. |

Enkele veelvoorkomende [FabricErrorCode-fouten](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) die kunnen worden geretourneerd in een [FabricException:](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)

| Fout | Voorwaarde |
| --- |:--- |
| CommunicationError CommunicationError CommunicationError CommunicationError |Door een communicatiefout is de bewerking mislukt en is de bewerking opnieuw geprobeerd. |
| OngeldigCredentialType |Het referentietype is ongeldig. |
| OngeldigX509FindType |De X509FindType is ongeldig. |
| OngeldigeX509StoreLocatie |De x509-winkellocatie is ongeldig. |
| OngeldigX509StoreName |De naam van de X509-winkel is ongeldig. |
| OngeldigX509Thumbprint |De duimafdruktekenreeks van het X509-certificaat is ongeldig. |
| Ongeldig protectionlevel |Het beveiligingsniveau is ongeldig. |
| OngeldigX509Store |Het X509-certificaatarchief kan niet worden geopend. |
| Ongeldige subjectname |De naam van het onderwerp is ongeldig. |
| Ongeldig toegestaneCommonNameList |De indeling van de lijst met algemene naamlijst is ongeldig. Het moet een door komma's gescheiden lijst zijn. |

