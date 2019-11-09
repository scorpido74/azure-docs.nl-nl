---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 3f358dedea37eb33c2d2bb26a823d3633560d3a0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850330"
---
Hier vindt u de beperkingen voor gebruik en andere servicelimieten voor de Azure Active Directory-service (Azure AD).

| Category | Beperkingen |
| --- | --- |
| Mappen | Eén gebruiker kan als lid of gast deel uitmaken van maximaal 500 Azure AD-directory's.<br/>Eén gebruiker kan maximaal 20 directory's maken. |
| Domeinen | U kunt niet meer dan 900 beheerde domeinnamen toevoegen. Als u al uw domeinen wilt instellen voor federatie met on-premises Active Directory, kunt u niet meer dan 450 domeinnamen toevoegen in elke directory. |
|Bronnen |<ul><li>Er kunnen Maxi maal 50.000 Azure AD-resources worden gemaakt in één map door gebruikers van de gratis versie van Azure Active Directory. Als u ten minste één geverifieerd domein hebt, wordt het standaard quotum voor Directory Services in azure AD uitgebreid naar 300.000 Azure AD-resources. </li><li>Een gebruiker die geen beheerder is, kan Maxi maal 250 Azure AD-resources maken. Zowel actieve resources als verwijderde resources die beschikbaar zijn om het aantal te herstellen, worden omgerekend naar dit quotum. Alleen verwijderde Azure AD-resources die minder dan 30 dagen geleden zijn verwijderd, kunnen worden hersteld. Er zijn Azure AD-resources verwijderd die niet meer beschikbaar zijn voor het aantal restores voor dit quotum met een waarde van één kwar taal gedurende 30 dagen. Als u ontwikkel aars hebt die dit quotum waarschijnlijk herhaaldelijk overschrijden in de loop van hun normale taken, kunt u [een aangepaste rol maken en toewijzen](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) met machtigingen voor het maken van een onbeperkt aantal app-registraties.</li></ul> |
| Schema-uitbreidingen |<ul><li>Uitbreidingen van het type 'tekenreeks' mogen uit maximaal 256 tekens bestaan. </li><li>Uitbreidingen van het type 'binair' mogen uit maximaal 256 bytes bestaan.</li><li>Alleen de 100-extensie waarden, in *alle* typen en *alle* toepassingen, kunnen worden geschreven naar één Azure AD-resource.</li><li>Alleen de entiteiten User, Group, TenantDetail, Device, Application en ServicePrincipal mogen worden uitgebreid met kenmerken met één waarde van het type 'tekenreeks' of 'binair'.</li><li>Schema-uitbreidingen zijn alleen beschikbaar in de preview van Graph API-versie 1.21. Er moet schrijftoegang worden verleend aan de toepassing om een uitbreiding te kunnen registreren.</li></ul> |
| Toepassingen |Maximaal 100 gebruikers mogen eigenaar zijn van één toepassing. |
|Toepassings manifest |In het toepassings manifest kunnen Maxi maal 1200 vermeldingen worden toegevoegd. |
| Groepen |<ul><li>Maximaal 100 gebruikers mogen eigenaar zijn van één groep.</li><li>Een wille keurig aantal Azure AD-resources kan lid zijn van één groep.</li><li>Een gebruiker kan lid zijn van een willekeurig aantal groepen.</li><li>Het aantal leden in een groep dat u met behulp van Azure AD Connect vanaf uw on-premises Active Directory naar Azure Active Directory kunt synchroniseren, is beperkt tot 50.000.</li></ul> |
| Toepassingsproxy | <ul><li>Maxi maal 500 trans acties per seconde per app-proxy toepassing</li><li>Maxi maal 750 trans acties per seconde voor de Tenant</li></ul><br/>Een trans actie wordt gedefinieerd als één HTTP-aanvraag en een antwoord voor een unieke resource. Wanneer dit is beperkt, ontvangen clients een respons van 429 (te veel aanvragen). |
| Toegangsvenster |<ul><li>Er is geen limiet voor het aantal toepassingen dat per gebruiker kan worden weergegeven in het toegangsvenster. Dit geldt voor gebruikers met licenties voor Azure AD Premium of de Enterprise Mobility Suite.</li><li>Er worden maximaal 10 app-tegels weergegeven in het toegangsvenster voor iedere gebruiker. Deze limiet is van toepassing op gebruikers aan wie licenties zijn toegewezen voor Azure AD Free licentie plan. Voorbeelden van app-tegels zijn Box, Salesforce en Dropbox. Deze limiet geldt niet voor beheerdersaccounts.</li></ul> |
| Rapporten | Er kunnen maximaal 1000 rijen worden bekeken of gedownload in elk rapport. Aanvullende gegevens worden afgekapt. |
| Beheereenheden | Een Azure AD-resource kan lid zijn van niet meer dan 30 administratieve eenheden. |
| Beheerdersrollen en -machtigingen | <ul><li>Een groep kan niet worden toegevoegd als [eigenaar](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Een groep kan niet worden toegewezen aan een [rol](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>Gebruikers kunnen de Directory gegevens van andere gebruikers niet lezen buiten de switch voor de hele Tenant om alle gebruikers van niet-beheerders toegang tot alle Directory gegevens uit te scha kelen (niet aanbevolen). Meer informatie over standaard machtigingen [vindt u hier](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Het kan Maxi maal 15 minuten duren of u afmelden/aanmelden voordat het lidmaatschap van de beheerdersrol en intrekkingen van kracht worden.</li></ul> |
