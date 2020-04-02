---
title: Onverwachte fout bij het uitvoeren van toestemming voor een aanvraag | Microsoft Documenten
description: Bespreekt fouten die kunnen optreden tijdens het proces van toestemming voor een toepassing en wat u eraan doen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea14e02920cf7ba6c5e0a7b415cb92137c915576
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519712"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Onverwachte fout bij het uitvoeren van toestemming voor een toepassing

In dit artikel worden fouten besproken die kunnen optreden tijdens het proces waarin u toestemming geeft voor een toepassing. Zie [Verificatiescenario's voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)als u problemen oplost met onverwachte toestemmingsprompts die geen foutberichten bevatten.

Voor veel toepassingen die zijn geïntegreerd met Azure Active Directory, zijn machtigingen nodig om toegang te krijgen tot andere bronnen om te kunnen functioneren. Wanneer deze bronnen ook zijn geïntegreerd met Azure Active Directory, worden machtigingen voor toegang tot deze bronnen vaak aangevraagd met behulp van het common consent framework. Er wordt een toestemmingsprompt weergegeven, die over het algemeen plaatsvindt wanneer een toepassing voor het eerst wordt gebruikt, maar ook kan optreden bij een volgend gebruik van de toepassing.

Bepaalde voorwaarden moeten gelden voor een gebruiker om in te stemmen met de machtigingen die een toepassing vereist. Als niet aan deze voorwaarden wordt voldaan, kunnen de volgende fouten optreden.

## <a name="requesting-not-authorized-permissions-error"></a>Fout niet-geautoriseerde machtigingen aanvragen
* **AADSTS90093:** &lt;clientAppDisplayName&gt; vraagt een of meer machtigingen die u niet mag verlenen. Neem contact op met een beheerder, die namens u kan instemmen met deze toepassing.
* **AADSTS90094:** &lt;clientAppDisplayName&gt; heeft toestemming nodig om toegang te krijgen tot bronnen in uw organisatie die alleen een beheerder kan verlenen. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.

Deze fout treedt op wanneer een gebruiker die geen bedrijfsbeheerder is, probeert een toepassing te gebruiken die machtigingen aanvraagt die alleen een beheerder kan verlenen. Deze fout kan worden opgelost door een beheerder die namens de organisatie toegang verleent tot de toepassing.

Deze fout kan ook optreden wanneer een gebruiker geen toestemming kan geven voor een toepassing omdat Microsoft detecteert dat het machtigingsverzoek riskant is. In dit geval wordt een auditgebeurtenis ook geregistreerd met een categorie 'ApplicationManagement', activiteitstype 'Toestemming voor toepassing' en statusreden van "Riskante toepassing gedetecteerd".

## <a name="policy-prevents-granting-permissions-error"></a>Beleid voorkomt fout machtigingen verlenen
* **AADSTS90093:** Een beheerder &lt;van&gt; tenantDisplayName heeft een beleid &lt;ingesteld&gt; dat voorkomt dat u de naam van de app de machtigingen verleent die deze aanvraagt. Neem contact &lt;op met&gt;een beheerder van tenantDisplayName , die namens u machtigingen voor deze app kan verlenen.

Deze fout treedt op wanneer een bedrijfsbeheerder de mogelijkheid voor gebruikers om toestemming te geven voor toepassingen uitschakelt, waarna een niet-beheerdersgebruiker probeert een toepassing te gebruiken waarvoor toestemming vereist is. Deze fout kan worden opgelost door een beheerder die namens de organisatie toegang verleent tot de toepassing.

## <a name="intermittent-problem-error"></a>Intermitterende fout in het probleem
* **AADSTS90090:** Het lijkt erop dat het aanmeldingsproces een onderbroken probleem heeft &lt;ondervonden bij&gt;het opnemen van de machtigingen die u hebt geprobeerd toe te kennen aan clientAppDisplayName. probeer het later opnieuw.

Deze fout geeft aan dat er een probleem met de servicezijde is opgetreden. Het kan worden opgelost door te proberen opnieuw in te stemmen met de toepassing.

## <a name="resource-not-available-error"></a>Fout resource niet beschikbaar
* **AADSTS65005:** De &lt;app clientAppDisplayName&gt; heeft machtigingen aangevraagd &lt;voor toegang&gt; tot een bronbronAppDisplayName die niet beschikbaar is. 

Neem contact op met de ontwikkelaar van de toepassing.

##  <a name="resource-not-available-in-tenant-error"></a>Resource is niet beschikbaar in tenantfout
* **AADSTS65005:** &lt;&gt; clientAppDisplayName vraagt toegang tot &lt;een bronbronAppDisplayName&gt; die &lt;niet beschikbaar&gt;is in uw organisatietenantDisplayName . 

Controleer of deze bron beschikbaar is &lt;of&gt;neem contact op met een beheerder van tenantDisplayName .

## <a name="permissions-mismatch-error"></a>Fout in fout bij fout in de fout van de fout permachtigingen
* **AADSTS65005:** De app heeft toestemming &lt;gevraagd om&gt;toegang te krijgen tot bronbronAppDisplayName . Dit verzoek is mislukt omdat deze niet overeenkomt met de manier waarop de app vooraf is geconfigureerd tijdens de registratie van de app. Neem contact op met de app-leverancier.**

Deze fouten treden allemaal op wanneer de toepassing waarmee een gebruiker probeert in te stemmen, machtigingen aanvraagt om toegang te krijgen tot een brontoepassing die niet kan worden gevonden in de map (tenant) van de organisatie. Deze situatie kan zich om verschillende redenen voordoen:

-   De ontwikkelaar van de clienttoepassing heeft de toepassing onjuist geconfigureerd, waardoor deze toegang tot een ongeldige bron kan aanvragen. In dit geval moet de toepassingsontwikkelaar de configuratie van de clienttoepassing bijwerken om dit probleem op te lossen.

-   Een serviceprincipal die de doelbrontoepassing vertegenwoordigt, bestaat niet in de organisatie of bestond in het verleden, maar is verwijderd. Om dit probleem op te lossen, moet een serviceprincipal voor de resourcetoepassing in de organisatie worden ingericht, zodat de clienttoepassing er machtigingen voor kan aanvragen. De Service Principal kan op verschillende manieren worden ingericht, afhankelijk van het type toepassing, waaronder:

    -   Een abonnement voor de brontoepassing aanschaffen (door Microsoft gepubliceerde toepassingen)

    -   Toestemming voor de resourcetoepassing

    -   De toepassingsmachtigingen verlenen via de Azure-portal

    -   De toepassing toevoegen vanuit de Azure AD-toepassingsgalerie

## <a name="next-steps"></a>Volgende stappen 

[Apps, machtigingen en toestemming in Azure Active Directory (v1-eindpunt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Scopes, machtigingen en toestemming in de Azure Active Directory (v2.0-eindpunt)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


