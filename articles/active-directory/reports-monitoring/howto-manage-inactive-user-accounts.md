---
title: Inactieve gebruikersaccounts beheren in Azure AD | Microsoft Documenten
description: Meer informatie over het detecteren en verwerken van gebruikersaccounts in Azure AD die verouderd zijn
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886038"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>How To: Inactieve gebruikersaccounts beheren in Azure AD

In grote omgevingen worden gebruikersaccounts niet altijd verwijderd wanneer werknemers een organisatie verlaten. Als IT-beheerder wilt u deze verouderde gebruikersaccounts detecteren en verwerken omdat ze een beveiligingsrisico vormen.

In dit artikel wordt een methode uitgelegd voor het verwerken van verouderde gebruikersaccounts in Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Wat zijn inactieve gebruikersaccounts?

Inactieve accounts zijn gebruikersaccounts die niet meer nodig zijn voor leden van uw organisatie om toegang te krijgen tot uw bronnen. Een belangrijke id voor inactieve accounts is dat ze al *een tijdje* niet zijn gebruikt om zich aan te melden bij uw omgeving. Omdat inactieve accounts zijn gekoppeld aan de aanmeldingsactiviteit, u de tijdstempel van de laatste aanmelding gebruiken die is geslaagd om ze te detecteren. 

De uitdaging van deze methode is om te definiëren wat *voor een tijdje* betekent in het geval van uw omgeving. Gebruikers kunnen zich bijvoorbeeld *een tijdje*niet aanmelden bij een omgeving, omdat ze op vakantie zijn. Wanneer u definieert wat uw delta voor inactieve gebruikersaccounts is, moet u rekening houden met alle legitieme redenen om u niet aan te melden bij uw omgeving. In veel organisaties ligt de delta voor inactieve gebruikersaccounts tussen de 90 en 180 dagen. 

De laatste succesvolle aanmelding biedt potentiële inzichten in de voortdurende behoefte van een gebruiker aan toegang tot bronnen.  Het kan helpen bij het bepalen of groepslidmaatschap of app-toegang nog steeds nodig is of kan worden verwijderd. Voor extern gebruikersbeheer u begrijpen of een externe gebruiker nog steeds actief is binnen de tenant of moet worden opgeschoond. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Inactieve gebruikersaccounts detecteren

U detecteert inactieve accounts door de **eigenschap lastSignInDateTime** te evalueren die wordt blootgesteld door het **brontype signInActivity** van de **Microsoft Graph-API.** Met deze eigenschap u een oplossing implementeren voor de volgende scenario's:

- **Gebruikers op naam:** In dit scenario zoekt u naar een specifieke gebruiker op naam, waarmee u de laatsteSignInDate evalueren:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Gebruikers op datum**: In dit scenario vraagt u een lijst met gebruikers met een lastSignInDateTime aan vóór een bepaalde datum:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Wat u dient te weten

In deze sectie vindt u een overzicht van wat u moet weten over de laatste SignInDateTime-eigenschap.

### <a name="how-can-i-access-this-property"></a>Hoe krijg ik toegang tot deze accommodatie?

De eigenschap **lastSignInDateTime** wordt weergegeven door het [brontype signInActivity](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) van de [Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Is de laatste eigenschapSignInDateTime beschikbaar via de cmdlet Get-AzureAdUser?

Nee.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Welke versie van Azure AD heb ik nodig om toegang te krijgen tot de eigenschap?

U hebt toegang tot deze eigenschap in alle edities van Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Welke toestemming heb ik nodig om de eigenschap te lezen?

Om deze eigenschap te lezen, moet u de volgende rechten verlenen: 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Wanneer werkt Azure AD de eigenschap bij?

Elke interactieve aanmelding die succesvol was, resulteert in een update van het onderliggende gegevensarchief. Doorgaans worden succesvolle aanmeldingen binnen 10 minuten weergegeven in het gerelateerde aanmeldingsrapport.
 

### <a name="what-does-a-blank-property-value-mean"></a>Wat betekent een lege eigenschapswaarde?

Als u een laatste SignInDateTime-tijdstempel wilt genereren, hebt u een succesvolle aanmelding nodig. Omdat de eigenschap lastSignInDateTime een nieuwe functie is, kan de waarde van de eigenschap lastSignInDateTime leeg zijn als:

- De laatste succesvolle aanmelding van een gebruiker vond plaats voordat deze functie werd uitgebracht (1 december 2019).
- Het getroffen gebruikersaccount is nooit gebruikt voor een succesvolle aanmelding.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten](tutorial-access-api-with-certificates.md)
* [Api-verwijzing voor controle](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-verwijzing naar voor aanmeldingsactiviteitsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
