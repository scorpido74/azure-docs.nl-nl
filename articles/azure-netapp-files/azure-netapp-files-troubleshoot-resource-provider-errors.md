---
title: Problemen met Azure NetApp Files Resource Provider fouten oplossen | Microsoft Documenten
description: Beschrijft oorzaken, oplossingen en tijdelijke oplossingen voor veelvoorkomende azure NetApp Files Resource Provider-fouten.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597210"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Problemen met Azure NetApp Files-resourceproviders oplossen 

In dit artikel worden veelvoorkomende fouten van Azure NetApp Files Resource Provider, de oorzaken, oplossingen en tijdelijke oplossingen beschreven (indien beschikbaar).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Veelvoorkomende fouten in Azure NetApp Files Resource Provider

***BareMetalTenantId kan niet worden gewijzigd.***  

Deze fout treedt op wanneer u een `BaremetalTenantId` volume probeert bij te werken of te patchen en de eigenschap een gewijzigde waarde heeft.

* Oorzaak:   
U probeert een volume bij `BaremetalTenantId` te werken en de eigenschap heeft een andere waarde dan de waarde die is opgeslagen in Azure.
* Oplossing:   
Neem niet `BaremetalTenantId` op in de patch en update (put) verzoek. U ook `BaremetalTenantId` ervoor zorgen dat hetzelfde is in het verzoek.

***ServiceLevel kan niet worden gewijzigd.***  

Deze fout treedt op wanneer u een capaciteitsgroep met een ander serviceniveau probeert bij te werken of te patchen wanneer de capaciteitsgroep al volumes bevat.

* Oorzaak:   
U probeert een serviceniveau voor capaciteitspoolbij te werken wanneer de groep volumes bevat.
* Oplossing:   
Verwijder alle volumes uit de capaciteitsgroep en wijzig vervolgens het serviceniveau.
* Tijdelijke oplossing:   
Maak een andere capaciteitsgroep en maak de volumes opnieuw in de nieuwe capaciteitsgroep.

***PoolId kan niet worden gewijzigd***  

Deze fout treedt op wanneer u een capaciteitsgroep `PoolId` met een gewijzigde eigenschap probeert bij te werken of te patchen.

* Oorzaak:   
U probeert een capaciteitspoolte goed `PoolId` bij te werken. De `PoolId` eigenschap is een alleen-lezen eigenschap en kan niet worden gewijzigd.
* Oplossing:   
Neem niet `PoolId` op in de patch en update (put) verzoek.  U ook `PoolId` ervoor zorgen dat hetzelfde is in het verzoek.

***CreationToken kan niet worden gewijzigd.***

Deze fout treedt op wanneer u`CreationToken`het bestandspad ( ) probeert te wijzigen nadat het volume is gemaakt. Bestandspad`CreationToken`( ) moet worden ingesteld wanneer het volume wordt gemaakt en het kan later niet worden gewijzigd.

* Oorzaak:   
U probeert het bestandspad`CreationToken`te wijzigen ( ) nadat het volume is gemaakt, wat geen ondersteunde bewerking is. 
* Oplossing:   
Als het wijzigen van het bestandspad niet nodig is, u overwegen de parameter uit het verzoek te verwijderen om het foutbericht te verwijderen.
* Tijdelijke oplossing:   
Als u het bestandspad`CreationToken`( ) moet wijzigen, u een nieuw volume maken met een nieuw bestandspad en de gegevens migreren naar het nieuwe volume.

***CreationToken moet ten minste 16 tekens lang zijn.***

Deze fout treedt op`CreationToken`wanneer het bestandspad ( ) niet voldoet aan de lengtevereiste. De lengte van het bestandspad moet ten minste één teken in lengte zijn.

* Oorzaak:   
Het bestandspad is leeg.  Wanneer u een volume maakt met behulp van de API, is een creatietoken vereist. Als u de Azure-portal gebruikt, wordt het bestandspad automatisch gegenereerd.
* Oplossing:   
Voer ten minste één teken`CreationToken`in als bestandspad ( ).

***De domeinnaam kan niet worden gewijzigd.***

Deze fout treedt op wanneer u de domeinnaam in Active Directory probeert te wijzigen.

* Oorzaak:   
U probeert de eigenschap domeinnaam bij te werken.
* Oplossing:    
Geen. U de domeinnaam niet wijzigen.
* Tijdelijke oplossing:   
Verwijder alle volumes met behulp van de Active Directory-configuratie. Verwijder vervolgens de Active Directory-configuratie en maak de volumes opnieuw.

***Fout in dubbele waarde voor object ExportPolicy.Rules[RuleIndex].***

Deze fout treedt op wanneer het exportbeleid niet is gedefinieerd met een unieke index. Wanneer u exportbeleid definieert, moeten alle exportbeleidsregels een unieke index tussen 1 en 5 hebben.

* Oorzaak:   
Het gedefinieerde exportbeleid voldoet niet aan de vereisten voor exportbeleidsregels. U moet maximaal één exportbeleidsregel hebben en maximaal vijf regels voor exportbeleid.
* Oplossing:   
Zorg ervoor dat de index nog niet is gebruikt en dat deze zich in het bereik van 1 tot 5 bevindt.
* Tijdelijke oplossing:   
Gebruik een andere index voor de regel die u probeert in te stellen.

***Fout {action} {resourceTypeName}***

Deze fout wordt weergegeven wanneer andere foutafhandeling de fout niet heeft verwerkt tijdens het uitvoeren van een actie op een resource.   Het bevat tekst 'Fout'. Het `{action}` kan een`getting`van `creating` `updating`( `deleting`, , , of ).  Het `{resourceTypeName}` is `resourceTypeName` de (bijvoorbeeld `capacityPool` `volume`, `netAppAccount`, , , en ga zo maar door).

* Oorzaak:   
Deze fout is een niet-behandelde uitzondering wanneer de oorzaak niet bekend is.
* Oplossing:   
Neem contact op met Azure Support Center om de gedetailleerde reden in logboeken te melden.
* Tijdelijke oplossing:   
Geen.

***De naam van het bestandspad kan alleen letters, cijfers en koppeltekens (""-"") bevatten.***

Deze fout treedt op wanneer het bestandspad niet-ondersteunde tekens bevat, bijvoorbeeld een periode ('.'), komma ('), onderschatting ("_"), of dollarteken ('$').

* Oorzaak:   
Het bestandspad bevat niet-ondersteunde tekens, bijvoorbeeld een periode (''), komma ('), onderstrepen ("_"), of dollarteken ("$").
* Oplossing:   
Verwijder tekens die geen alfabetische letters, cijfers of koppeltekens ("-") zijn uit het bestandspad dat u hebt ingevoerd.
* Tijdelijke oplossing:   
U een underscore vervangen door een koppelteken of hoofdletters gebruiken in plaats van spaties om het begin van nieuwe woorden aan te geven.  Gebruik bijvoorbeeld 'NewVolume' in plaats van 'nieuw volume'.

***FileSystemId kan niet worden gewijzigd.***

Deze fout treedt op `FileSystemId`wanneer u probeert te wijzigen .  Veranderen `FileSystemdId` is geen ondersteunde bewerking. 

* Oorzaak:   
De id van het bestandssysteem wordt ingesteld wanneer het volume wordt gemaakt. `FileSystemId`kan later niet worden gewijzigd.
* Oplossing:   
Niet opnemen `FileSystemId` in een patch en update (put) verzoek.  U ook `FileSystemId` ervoor zorgen dat dit hetzelfde is in het verzoek.

***ActiveDirectory met id: {string}' bestaat niet.***

Het `{string}` gedeelte is de waarde `ActiveDirectoryId` die u in de eigenschap hebt ingevoerd voor de Active Directory-verbinding.

* Oorzaak:   
Wanneer u een account hebt gemaakt met de Active `ActiveDirectoryId` Directory-configuratie, hebt u een waarde ingevoerd waarvoor leeg zou moeten zijn.
* Oplossing:   
Neem niet `ActiveDirectoryId` op in de aanvraag voor maken (put).

***Ongeldige api-versie.***

De API-versie is niet ingediend of bevat een ongeldige waarde.

* Oorzaak:   
De waarde in `api-version` de queryparameter bevat een ongeldige waarde.
* Oplossing:   
Gebruik de juiste API-versiewaarde.  De resourceprovider ondersteunt veel API-versies. De waarde is in het formaat van yyyy-mm-dd.

***Er is een ongeldige waarde {1}{waarde} ontvangen voor .***

Dit bericht geeft een fout `RuleIndex` `AllowedClients`aan `UnixReadOnly` `UnixReadWrite`in `Nfsv3`de `Nfsv4`velden voor , , , en .

* Oorzaak:   
De invoervalidatieaanvraag is mislukt voor ten minste `RuleIndex` `AllowedClients`één `UnixReadOnly` `UnixReadWrite`van `Nfsv`de `Nfsv4`volgende velden: , , , , 3 en .
* Oplossing:   
Zorg ervoor dat u alle vereiste en niet-conflicterende parameters instelt op de opdrachtregel. U bijvoorbeeld niet `UnixReadOnly` zowel `UnixReadWrite` de parameters als de parameters tegelijkertijd instellen.
* Tijdelijke oplossing:   
Zie de oplossing hierboven.

***{0} IP-bereik {1} voor {2} vlan is al in gebruik***

Deze fout treedt op omdat de interne records van de gebruikte IP-bereiken een conflict hebben met het nieuw toegewezen IP-adres.

* Oorzaak:   
Het IP-adres dat is toegewezen voor het maken van volume is al geregistreerd.
De reden kan een eerdere mislukte volumecreatie zijn.
* Oplossing:   
Contact Azure Support Center.

***Ontbrekende waarde voor {property}'.***

Deze fout geeft aan dat er een vereiste eigenschap ontbreekt in de aanvraag. De tekenreeks {eigenschap} bevat de naam van de ontbrekende eigenschap.

* Oorzaak:   
De invoervalidatieaanvraag is mislukt voor ten minste één van de eigenschappen.
* Oplossing:   
Zorg ervoor dat u alle vereiste en niet-conflicterende eigenschappen in de aanvraag instelt, speciaal de eigenschap van het foutbericht.

***MountTargets kunnen niet worden gewijzigd.***

Deze fout treedt op wanneer een gebruiker probeert de eigenschap Volume MountTargets bij te werken of te patchen.

* Oorzaak:   
U probeert de eigenschap `MountTargets` volume bij te werken. Het wijzigen van deze eigenschap wordt niet ondersteund.
* Oplossing:   
Niet opnemen `MountTargets` in een patch en update (put) verzoek.  U ook `MountTargets` ervoor zorgen dat dit hetzelfde is in het verzoek.

***Naam al in gebruik.***

Deze fout geeft aan dat de naam voor de resource al in gebruik is.

* Oorzaak:   
U probeert een resource te maken met een naam die wordt gebruikt voor een bestaande resource.
* Oplossing:   
Gebruik een unieke naam bij het maken van de resource.

***Bestandspad dat al in gebruik is.***

Deze fout geeft aan dat het bestandspad voor het volume al in gebruik is.

* Oorzaak:   
U probeert een volume te maken met een bestandspad dat hetzelfde is als een bestaand volume.
* Oplossing:   
Gebruik een uniek bestandspad bij het maken van het volume.

***Noem te lang.***

Deze fout geeft aan dat de resourcenaam niet voldoet aan de vereiste maximale lengte.

* Oorzaak:   
De naam van de resource is te lang.
* Oplossing:   
Gebruik een kortere naam voor de resource.

***Bestandspad te lang.***

Deze fout geeft aan dat het bestandspad voor het volume niet voldoet aan de vereiste maximale lengte.

* Oorzaak:   
Het pad met het volumebestand is te lang.
* Oplossing:   
Gebruik een korter bestandspad.

***Naam te kort.***

Deze fout geeft aan dat de resourcenaam niet voldoet aan de vereiste voor minimale lengte.

* Oorzaak:   
De naam van de resource is te kort.
* Oplossing:   
Gebruik een langere naam voor de resource.

***Bestandspad te kort.***

Deze fout geeft aan dat het volumebestandspad niet voldoet aan de vereiste voor minimale lengte.

* Oorzaak:   
Het pad met het volumebestand is te kort.
* Oplossing:   
Verhoog de lengte van het volumebestandspad.

***Azure NetApp Files API onbereikbaar.***

De Azure API is afhankelijk van de Azure NetApp Files API om volumes te beheren. Deze fout geeft een probleem met de API-verbinding aan.

* Oorzaak:   
De onderliggende API reageert niet, wat resulteert in een interne fout. Deze fout is waarschijnlijk tijdelijk.
* Oplossing:   
Het probleem is waarschijnlijk tijdelijk. Het verzoek moet na enige tijd slagen.
* Tijdelijke oplossing:   
Geen. De onderliggende API is essentieel voor het beheren van volumes.

***Geen operationeel resultaat-id gevonden voor '{0}'.***

Deze fout geeft aan dat een interne fout verhindert dat de bewerking wordt voltooid.

* Oorzaak:   
Er is een interne fout opgetreden en is voorkomen dat de bewerking is voltooid.
* Oplossing:   
Deze fout is waarschijnlijk tijdelijk. Wacht een paar minuten en probeer het opnieuw. Als het probleem zich blijft voordoen, maakt u een ticket om technische ondersteuning het probleem te laten onderzoeken.
* Tijdelijke oplossing:   
Wacht een paar minuten en controleer of het probleem blijft bestaan.

***Het is niet toegestaan protocoltypen CIFS en NFS te mengen***

Deze fout treedt op wanneer u een volume probeert te maken en er zijn zowel de CIFS-protocoltypen (SMB) als NFS-protocoltypen in de volume-eigenschappen.

* Oorzaak:   
Zowel de CIFS-protocoltypen (SMB) als de NFS-protocolworden gebruikt in de volume-eigenschappen.
* Oplossing:   
Verwijder een van de protocoltypen.
* Tijdelijke oplossing:   
Laat de eigenschap van het protocoltype leeg of nietig.

***Aantal items: {value} voor object: ExportPolicy.Rules[RuleIndex] ligt buiten min-max bereik.***

Deze fout treedt op wanneer de regels voor exportbeleid niet voldoen aan de minimum- of maximumbereikvereiste. Als u het exportbeleid definieert, moet het maximaal één exportbeleidsregel hebben en maximaal vijf exportbeleidsregels.

* Oorzaak:   
Het door u gedefinieerde exportbeleid voldoet niet aan het vereiste bereik.
* Oplossing:   
Zorg ervoor dat de index nog niet is gebruikt en dat is in het bereik van 1 tot 5.
* Tijdelijke oplossing:   
Het is niet verplicht om exportbeleid te gebruiken voor de volumes. U het exportbeleid volledig weglaten als u geen exportbeleidsregels hoeft te gebruiken.

***Slechts één actieve map toegestaan***

Deze fout treedt op wanneer u een Active Directory-configuratie probeert te maken en er al een bestaat voor het abonnement in de regio. De fout kan ook optreden wanneer u meer dan één Active Directory-configuratie probeert te maken.

* Oorzaak:   
U probeert een actieve map te maken (niet bijwerken), maar er bestaat er al een.
* Oplossing:   
Als de Active Directory-configuratie niet in gebruik is, u eerst de bestaande configuratie verwijderen en vervolgens de bewerking opnieuw proberen.
* Tijdelijke oplossing:   
Geen. Er is slechts één Active Directory toegestaan.

***Bewerking {operation}' wordt niet ondersteund.***

Deze fout geeft aan dat de bewerking niet beschikbaar is voor het actieve abonnement of de actieve bron.

* Oorzaak:   
De bewerking is niet beschikbaar voor het abonnement of de bron.
* Oplossing:   
Zorg ervoor dat de bewerking correct is ingevoerd en dat deze beschikbaar is voor de bron en het abonnement dat u gebruikt.

***OwnerId kan niet worden gewijzigd***

Deze fout treedt op wanneer u de eigenschap OwnerId van het volume probeert te wijzigen. Het wijzigen van de OwnerId is geen ondersteunde bewerking. 

* Oorzaak:   
De `OwnerId` eigenschap wordt ingesteld wanneer het volume wordt gemaakt. Het pand kan later niet worden gewijzigd.
* Oplossing:   
Niet opnemen `OwnerId` in een patch en update (put) verzoek. U ook `OwnerId` ervoor zorgen dat dit hetzelfde is in het verzoek.

***Bovenliggende groep niet gevonden***

Deze fout treedt op wanneer u een volume probeert te maken en de capaciteitsgroep waarin u het volume maakt, niet wordt gevonden.

* Oorzaak:   
De capaciteitsgroep waar het volume wordt gemaakt, wordt niet gevonden.
* Oplossing:   
Waarschijnlijk is de pool niet volledig gemaakt of al verwijderd op het moment van het maken van het volume.

***Patchbewerking wordt niet ondersteund voor dit resourcetype.***

Deze fout treedt op wanneer u het bevestigingsdoel of de momentopname probeert te wijzigen.

* Oorzaak:   
Het bevestigingsdoel wordt gedefinieerd wanneer het wordt gemaakt en kan later niet worden gewijzigd.
De momentopnamen bevatten geen eigenschappen die kunnen worden gewijzigd.
* Oplossing:   
Geen. Deze resources hebben geen eigenschappen die kunnen worden gewijzigd.

***Poolgrootte te klein voor totale volumegrootte.***

Deze fout treedt op wanneer u de grootte van de `usedBytes` capaciteitsgroep bijwerkt en de grootte kleiner is dan de totale waarde van alle volumes in die capaciteitsgroep.  Deze fout kan ook optreden wanneer u een nieuw volume maakt of het formaat van een bestaand volume wijzigt en de nieuwe volumegrootte groter is dan de vrije ruimte in de capaciteitsgroep.

* Oorzaak:   
U probeert de capaciteitsgroep bij te werken naar een kleiner formaat dan usedBytes in alle volumes in de capaciteitsgroep.  U probeert ook een volume te maken dat groter is dan de vrije ruimte in de capaciteitsgroep.  U probeert ook het formaat van een volume te wijzigen en de nieuwe grootte overschrijdt de vrije ruimte in de capaciteitsgroep.
* Oplossing:   
Stel de grootte van de capaciteitsgroep in op een grotere waarde of maak een kleiner volume voor een volume.
* Tijdelijke oplossing:   
Verwijder voldoende volumes zodat de grootte van de capaciteitsgroep kan worden bijgewerkt naar deze grootte.

***De eigenschap: locatie voor momentopname moet hetzelfde zijn als Volume***

Deze fout treedt op wanneer u een momentopname maakt met een andere locatie dan het volume dat eigenaar is van de momentopname.

* Oorzaak:   
Ongeldige waarde in de eigenschap Locatie voor de momentopname.
* Oplossing:   
Regel een geldige tekenreeks in de eigenschap Locatie.

***De naam {resourceType} moet hetzelfde zijn als de naam van de resource-id.***

Deze fout treedt op wanneer u een resource maakt en u vult de `resourceId`eigenschap naam in met een andere waarde dan de eigenschap naam van .

* Oorzaak:   
Ongeldige waarde in de eigenschap naam wanneer u een resource maakt.
* Oplossing:   
Laat de eigenschap naam leeg of laat het dezelfde waarde gebruiken als de eigenschap name (tussen de `resourceId`laatste backslash "/" en het vraagteken "?") in .

***Protocoltype {waarde} niet bekend***

Deze fout treedt op wanneer u een volume maakt met een onbekend protocoltype.  Geldige waarden zijn "NFSv3", "NFSv4" en "CIFS".

* Oorzaak:   
U probeert een ongeldige waarde `protocolType` in te stellen in de eigenschap volume.
* Oplossing:   
Een geldige tekenreeks `protocolType`instellen in .
* Tijdelijke oplossing:   
Stel `protocolType` in als null.

***Protocoltypen kunnen niet worden gewijzigd***

Deze fout treedt op wanneer `ProtocolType` u probeert bij te werken of patchen voor een volume.  ProtocolType wijzigen is geen ondersteunde bewerking.

* Oorzaak:   
De `ProtocolType` eigenschap wordt ingesteld wanneer het volume wordt gemaakt.  Het kan niet worden bijgewerkt.
* Oplossing:   
Geen.
* Tijdelijke oplossing:   
Maak een ander volume met nieuwe protocoltypen.

***Als u de resource van het type {resourceType} maakt, wordt het quotum van {quota} resources van type {resourceType} per {parentResourceType} overschreden. Het huidige aantal resources is {currentCount}, verwijder een aantal bronnen van dit type voordat u een nieuwe bron maakt.***

Deze fout treedt op wanneer u`NetAppAccount`een `CapacityPool` `Volume`resource `Snapshot`probeert te maken ( , of ), maar uw quotum de limiet heeft bereikt.

* Oorzaak:   
U probeert een resource te maken, maar de `NetAppAccounts` quotumlimiet `CapacityPools` is `NetAppAccount`bereikt (bijvoorbeeld per abonnement of per).
* Oplossing:   
Verhoog de quotumlimiet.
* Tijdelijke oplossing:   
Ongebruikte bronnen van hetzelfde type verwijderen en opnieuw maken.

***Een waarde ontvangen voor alleen-lezen eigenschap {propertyName}.Received a value for read-only property '{propertyName}'.***

Deze fout treedt op wanneer u een waarde definieert voor een eigenschap die niet kan worden gewijzigd. U bijvoorbeeld de volume-id niet wijzigen.

* Oorzaak:   
U probeert een parameter (bijvoorbeeld de volume-ID) te wijzigen die niet kan worden gewijzigd.
* Oplossing:   
Wijzig geen waarde voor de eigenschap.

***De gevraagde {resource} is niet gevonden.***

Deze fout treedt op wanneer u probeert te verwijzen naar een niet-bestaande bron, bijvoorbeeld een volume of momentopname. De bron is mogelijk verwijderd of heeft een naam van de ontbrekende bron.

* Oorzaak:   
U probeert te verwijzen naar een niet-bestaande resource (bijvoorbeeld een volume of momentopname) die al is verwijderd of een verkeerd gespelde resourcenaam heeft.
* Oplossing:   
Controleer het verzoek om spelfouten om er zeker van te zijn dat er correct naar wordt verwezen.
* Tijdelijke oplossing:   
Zie de sectie Oplossing hierboven.

***Serviceniveau {volumeServiceLevel}' is hoger dan ouder '{poolServiceLevel}'***

Deze fout treedt op wanneer u een volume maakt of bijwerkt en u hebt het serviceniveau ingesteld op een hoger niveau dan de capaciteitsgroep die het bevat.

* Oorzaak:   
U probeert een volume te maken of bij te werken met een hoger gerangschikt serviceniveau dan de bovenliggende capaciteitsgroep.
* Oplossing:   
Stel het serviceniveau in op dezelfde of een lagere rang dan de bovenliggende capaciteitsgroep.
* Tijdelijke oplossing:   
Maak het volume in een andere capaciteitsgroep met een correct serviceniveau. U ook alle volumes uit de capaciteitsgroep verwijderen en serviceniveau instellen voor de capaciteitsgroep op een hogere rang.

***De naam van de SMB-server mag niet langer zijn dan 10 tekens.***

Deze fout treedt op wanneer u een Active Directory-configuratie voor een account maakt of bijwerkt.

* Oorzaak:   
De lengte van de naam van de SMB-server overschrijdt 10 tekens.
* Oplossing:   
Gebruik een kortere servernaam. De maximale lengte is 10 tekens.
* Tijdelijke oplossing:   
Geen.  Zie de oplossing hierboven. 

***SubnetId kan niet worden gewijzigd.***

Deze fout treedt op wanneer `subnetId` u probeert de nadat het volume is gemaakt te wijzigen.  `SubnetId`moet worden ingesteld wanneer het volume wordt gemaakt en kan later niet worden gewijzigd.

* Oorzaak:   
U probeert de `subnetId` bewerking te wijzigen nadat het volume is gemaakt, wat geen ondersteunde bewerking is. 
* Oplossing:   
Als het `subnetId` wijzigen van de parameter niet nodig is, u overwegen de parameter uit het verzoek te verwijderen om het foutbericht te verwijderen.
* Tijdelijke oplossing:   
Als u de `subnetId`, u een nieuw `subnetId`volume maken met een nieuw, en vervolgens de gegevens migreren naar het nieuwe volume.

***SubnetId is ongeldig.***

Deze fout treedt op wanneer u een `subnetId` nieuw `resourceId` volume probeert te maken, maar het is geen voor een subnet.

* Oorzaak:   
Deze fout treedt op wanneer u een `subnetId` nieuw volume `resourceId` probeert te maken, maar het is geen voor een subnet. 
* Oplossing:   
Controleer de waarde `subnetId` voor de om `resourceId` ervoor te zorgen dat het een voor het gebruikte subnet bevat.
* Tijdelijke oplossing:   
Geen. Zie de oplossing hierboven. 

***Subnet moet een 'Microsoft.NetApp/volumes'-delegatie hebben.***

Deze fout treedt op wanneer u een volume maakt en `Microsoft.NetApp/volumes`het geselecteerde subnet niet wordt gedelegeerd aan .

* Oorzaak:   
U hebt geprobeerd volume te maken en u hebt `Microsoft.NetApp/volumes`een subnet geselecteerd dat niet is gedelegeerd aan .
* Oplossing:   
Selecteer een ander subnet `Microsoft.NetApp/volumes`dat is gedelegeerd aan .
* Tijdelijke oplossing:   
Voeg een juiste delegatie toe aan het subnet.

***Het opgegeven resourcetype is onbekend/niet van toepassing.***

Deze fout treedt op wanneer een naamcontrole is aangevraagd op een niet-toepasselijk resourcetype of op een onbekend resourcetype.

* Oorzaak:   
Naamcontrole is aangevraagd voor een onbekend of niet-ondersteund resourcetype.
* Oplossing:   
Controleer of de resource waarvoor u de aanvraag doet, wordt ondersteund of geen spelfouten bevat.
* Tijdelijke oplossing:   
Zie de oplossing hierboven.

***Onbekende Azure NetApp-bestanden fout.***

De Azure API is afhankelijk van de Azure NetApp Files API om volumes te beheren. De fout geeft een probleem aan in de communicatie naar de API.

* Oorzaak:   
De onderliggende API verzendt een onbekende fout. Deze fout is waarschijnlijk tijdelijk.
* Oplossing:   
Het probleem is waarschijnlijk tijdelijk, en het verzoek moet na enige tijd slagen. Als het probleem zich blijft voordoen, maakt u een ondersteuningsticket om het probleem te laten onderzoeken.
* Tijdelijke oplossing:   
Geen. De onderliggende API is essentieel voor het beheren van volumes.

***Waarde ontvangen voor een onbekende eigenschap {propertyName}.Value received for an unknown property {propertyName}.Value received for an unknown property {propertyName}.Value received for an unknown property {property***

Deze fout treedt op wanneer niet-bestaande eigenschappen worden verstrekt voor een resource, zoals het volume, de momentopname of het bevestigingsdoel.

* Oorzaak:   
De aanvraag heeft een set eigenschappen die bij elke resource kunnen worden gebruikt. U geen niet-bestaande eigenschappen in de aanvraag opnemen.
* Oplossing:   
Zorg ervoor dat alle eigenschapsnamen correct zijn gespeld en dat de eigenschappen beschikbaar zijn voor het abonnement en de bron.
* Tijdelijke oplossing:   
Verminder het aantal eigenschappen dat in de aanvraag is gedefinieerd om de eigenschap te elimineren die de fout veroorzaakt.

***De updatebewerking wordt niet ondersteund voor dit resourcetype.***

Alleen volumes kunnen worden bijgewerkt. Deze fout treedt op wanneer u een niet-ondersteunde updatebewerking probeert uit te voeren, bijvoorbeeld wanneer u een momentopname bijwerkt.

* Oorzaak:   
De bron die u probeert bij te werken, ondersteunt de updatebewerking niet. Alleen volumes kunnen hun eigenschappen wijzigen.
* Oplossing:   
Geen. De bron die u probeert bij te werken, ondersteunt de updatebewerking niet. Daarom kan het niet worden gewijzigd.
* Tijdelijke oplossing:   
Maak voor een volume een nieuwe bron met de update en migreer de gegevens.

***Volume kan niet worden gemaakt in een groep die niet in de status is geslaagd.***

Deze fout treedt op wanneer u een volume probeert te maken in een groep die niet in de geslaagde status is. Hoogstwaarschijnlijk is de bewerking voor het maken van de capaciteitsgroep om de een of andere reden mislukt.

* Oorzaak:   
De capaciteitsgroep met het nieuwe volume is mislukt.
* Oplossing:   
Controleer of de capaciteitsgroep is gemaakt en of deze niet in een mislukte status is.
* Tijdelijke oplossing:   
Maak een nieuwe capaciteitsgroep en maak het volume in de nieuwe groep.

***Er wordt volume gemaakt en kan op dit moment niet worden verwijderd.***

Deze fout treedt op wanneer u een nog steeds gemaakt volume probeert te verwijderen.

* Oorzaak:   
Er wordt nog steeds een volume gemaakt wanneer u het volume probeert te verwijderen.
* Oplossing:   
Wacht tot de volumecreatie is voltooid en probeer de verwijdering opnieuw.
* Tijdelijke oplossing:   
Zie de oplossing hierboven.

***Het volume wordt verwijderd en kan op dit moment niet worden verwijderd.***

Deze fout treedt op wanneer u een volume probeert te verwijderen wanneer het al wordt verwijderd.

* Oorzaak:   
Een volume wordt al verwijderd wanneer u het volume probeert te verwijderen.
* Oplossing:   
Wacht tot de huidige verwijderingsbewerking is voltooid.
* Tijdelijke oplossing:   
Zie de oplossing hierboven.

***Het volume wordt bijgewerkt en kan op dit moment niet worden verwijderd.***

Deze fout treedt op wanneer u een volume probeert te verwijderen dat wordt bijgewerkt.

* Oorzaak:   
Een volume wordt bijgewerkt wanneer u het volume probeert te verwijderen.
* Oplossing:   
Wacht tot de updatebewerking is voltooid en probeer de verwijdering opnieuw.
* Tijdelijke oplossing:   
Zie de oplossing hierboven.

***Het volume is niet gevonden of niet gemaakt.***

Deze fout treedt op wanneer het maken van volume is mislukt en u probeert het volume te wijzigen of een momentopname voor het volume te maken.

* Oorzaak:   
Het volume bestaat niet of de creatie is mislukt.
* Oplossing:   
Controleer of u het juiste volume wijzigt en of het maken van het volume is geslaagd. Of controleer of het volume waarvoor u een momentopname maakt, bestaat.
* Tijdelijke oplossing:   
Geen.  Zie de oplossing hierboven. 

***Opgegeven creatietoken bestaat al***

Deze fout treedt op wanneer u een volume probeert te maken en u een creatietoken (exportpad) opgeeft waarvoor al een volume bestaat.

* Oorzaak:   
Het creatietoken (exportpad) dat u hebt opgegeven tijdens het maken van het volume, is al gekoppeld aan een ander volume. 
* Oplossing:   
Kies een ander creatietoken.  U ook het andere volume verwijderen.

***Opgegeven creatietoken is gereserveerd***

Deze fout treedt op wanneer u een volume probeert te maken en u 'standaard' of 'geen' opgeeft als het bestandspad (token maken).

* Oorzaak:    
U probeert een volume te maken en u geeft 'standaard' of 'geen' op als bestandspad (token voor aanmaken).
* Oplossing:   
Kies een ander bestandspad (creatietoken).
 
***Active Directory-referenties zijn in gebruik***

Deze fout treedt op wanneer u de Active Directory-configuratie probeert te verwijderen uit een account waar nog ten minste één SMB-volume bestaat.  Het SMB-volume is gemaakt met behulp van de Active Directory-configuratie die u probeert te verwijderen.

* Oorzaak:   
U probeert de Active Directory-configuratie uit een account te verwijderen, maar er bestaat nog steeds minstens één SMB-volume dat in eerste instantie is gemaakt met behulp van de Active Directory-configuratie. 
* Oplossing:   
Verwijder eerst alle SMB-volumes die zijn gemaakt met behulp van de Active Directory-configuratie.  Probeer vervolgens opnieuw de configuratie verwijderen.

***Toewijzing van de organisatie-eenheid niet wijzigen als de referenties in gebruik zijn***

Deze fout treedt op wanneer u de organisatie-eenheid van een Active Directory-configuratie probeert te wijzigen, maar er bestaat nog steeds ten minste één SMB-volume.  Het SMB-volume is gemaakt met behulp van die Active Directory-configuratie die u probeert te verwijderen.

* Oorzaak:   
U probeert de organisatie-eenheid van een Active Directory-configuratie te wijzigen.  Maar er bestaat nog steeds minstens één SMB-volume dat in eerste instantie is gemaakt met behulp van de Active Directory-configuratie.
* Oplossing:   
 Verwijder eerst alle SMB-volumes die zijn gemaakt met behulp van de Active Directory-configuratie.  Probeer vervolgens opnieuw de configuratie verwijderen. 

***Active Directory-update al in uitvoering***

Deze fout treedt op wanneer u een Active Directory-configuratie probeert te bewerken waarvoor al een bewerkingsbewerking aan de gang is.

* Oorzaak:   
U probeert een Active Directory-configuratie te bewerken, maar er is al een bewerking gestart.
* Oplossing:   
Wacht tot de momenteel lopende bewerking is voltooid.

***Alle volumes verwijderen met de geselecteerde referenties eerst***

Deze fout treedt op wanneer u een Active Directory-configuratie probeert te verwijderen, maar er bestaat nog steeds ten minste één SMB-volume.  Het SMB-volume is gemaakt met behulp van de Active Directory-configuratie die u probeert te verwijderen.

* Oorzaak:   
U probeert een Active Directory-configuratie te verwijderen, maar er bestaat nog steeds minstens één SMB-volume dat in eerste instantie is gemaakt met behulp van de Active Directory-configuratie.
* Oplossing:   
Verwijder eerst alle SMB-volumes die zijn gemaakt met behulp van de Active Directory-configuratie.  Probeer vervolgens opnieuw de configuratie verwijderen. 

***Geen Active Directory-referenties gevonden in regio***

Deze fout treedt op wanneer u een SMB-volume probeert te maken, maar er is geen Active Directory-configuratie toegevoegd aan het account voor de regio.

* Oorzaak:   
U probeert een SMB-volume te maken, maar er is geen Active Directory-configuratie toegevoegd aan het account. 
* Oplossing:   
Voeg een Active Directory-configuratie toe aan het account voordat u een SMB-volume maakt.

***Kan geen DNS-server opvragen. Controleer of de netwerkconfiguratie correct is en of DNS-servers beschikbaar zijn.***

Deze fout treedt op wanneer u een SMB-volume probeert te maken, maar een DNS-server (opgegeven in uw Active Directory-configuratie) is onbereikbaar. 

* Oorzaak:   
U probeert een SMB-volume te maken, maar een DNS-server (opgegeven in uw Active Directory-configuratie) is onbereikbaar.
* Oplossing:   
Controleer de configuratie van uw Active Directory en controleer of de IP-adressen van de DNS-server correct en bereikbaar zijn.
Als er geen problemen zijn met de IP-adressen van de DNS-server, controleert u of er geen firewalls de toegang blokkeren.

***Te veel gelijktijdige taken***

Deze fout treedt op wanneer u een momentopname probeert te maken wanneer er al drie andere bewerkingen voor het maken van momentopnamen voor het abonnement worden uitgevoerd.

* Oorzaak:   
U probeert een momentopname te maken wanneer er al drie andere bewerkingen voor het maken van momentopnamen voor het abonnement worden uitgevoerd. 
* Oplossing:   
Het maken van momentopnamen duurt maximaal enkele seconden om te voltooien.  Wacht een paar seconden en probeer de bewerking voor het maken van momentopnamen opnieuw.

***Kan geen extra banen opleveren. Wacht tot de lopende taken zijn voltooid en probeer het opnieuw***

Deze fout kan optreden wanneer u onder specifieke omstandigheden een volume probeert te maken of verwijderen.

* Oorzaak:   
U probeert onder specifieke omstandigheden een volume te maken of te verwijderen.
* Oplossing:   
Wacht een minuut of zo en probeer de bewerking opnieuw.

***Volume is al een overgang tussen staten***

Deze fout kan optreden wanneer u een volume probeert te verwijderen dat zich momenteel in een overgangsstatus bevindt (dat wil zeggen, momenteel in de status maken, bijwerken of verwijderen).

* Oorzaak:   
U probeert een volume te verwijderen dat momenteel in een overgangsstatus is.
* Oplossing:   
Wacht totdat de momenteel lopende bewerking (statusovergang) is voltooid en probeer de bewerking opnieuw.

***Kan nieuw volume niet splitsen van momentopname van bronvolume***

 Deze fout kan optreden wanneer u een volume probeert te maken op basis van een momentopname.  

* Oorzaak:   
U probeert een volume te maken op basis van een momentopname en het volume eindigt in een foutstatus.
* Oplossing:   
Verwijder het volume en probeer de bewerking voor het maken van volume opnieuw uit de momentopname.

 
## <a name="next-steps"></a>Volgende stappen

* [Ontwikkelen voor Azure NetApp-bestanden met REST-API](azure-netapp-files-develop-with-rest-api.md)
