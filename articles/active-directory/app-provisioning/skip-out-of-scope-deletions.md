---
title: Verwijdering van gebruikers buiten het bereik overslaan | Microsoft Documenten
description: Meer informatie over het overschrijven van het standaardgedrag van het de-provisioning out of scope users.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522446"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Verwijdering van gebruikersaccounts die buiten het bereik vallen overslaan

Standaard verwijdert of schakelt de Azure AD-inrichtingsengine gebruikers die buiten het bereik vallen, uit. Voor bepaalde scenario's, zoals Workday to AD-gebruiker Inbound Provisioning, wordt dit gedrag echter mogelijk niet verwacht en wilt u dit standaardgedrag mogelijk overschrijven.  

In deze handleiding wordt beschreven hoe u de Microsoft Graph API en de Microsoft Graph API explorer gebruiken om de vlag ***SkipOutOfScopeDeletions*** in te stellen die de verwerking regelt van accounts die buiten het bereik vallen. 
* Als ***SkipOutOfScopeDeletions*** is ingesteld op 0 (false), worden accounts die buiten het bereik vallen uitgeschakeld in het doel
* Als ***SkipOutOfScopeDeletions*** is ingesteld op 1 (true), worden accounts die buiten het bereik vallen niet uitgeschakeld in het doel Deze vlag is ingesteld op het niveau *Provisioning App* en kan worden geconfigureerd met behulp van de Graph API. 

Aangezien deze configuratie op grote schaal wordt gebruikt met de app *Workday naar Active Directory voor het inrichten van gebruikers,* bevatten de onderstaande stappen schermafbeeldingen van de Workday-toepassing. Dit kan echter ook worden gebruikt met **alle andere apps** zoals (ServiceNow, Salesforce, Dropbox, enz.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Stap 1: Uw hoofd-id voor het inrichten van app-service ophalen (object-id)

1. Start de [Azure-portal](https://portal.azure.com)en navigeer naar de sectie Eigenschappen van uw inrichtingstoepassing. Als u bijvoorbeeld uw werkdag wilt exporteren naar *het toewijzen van toepassingtoewijzing voor AD-gebruikerstoewijzing,* navigeert u naar de sectie Eigenschappen van die app. 
1. Kopieer in de sectie Eigenschappen van uw inrichtingsapp de GUID-waarde die is gekoppeld aan het veld *Object-id.* Deze waarde wordt ook wel de **ServicePrincipalId** van uw app genoemd en wordt gebruikt in Graph Explorer-bewerkingen.

   ![Hoofd-id van de Workday App-service](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Stap 2: Aanmelden bij Microsoft Graph Explorer

1. [Microsoft Graph Explorer starten](https://developer.microsoft.com/graph/graph-explorer)
1. Klik op de knop Aanmelden met Microsoft en meld u aan met azure AD Global Admin- of App Admin-referenties.

    ![Aanmelding in grafiek](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Bij een succesvolle aanmelding ziet u de gegevens van het gebruikersaccount in het linkerdeelvenster.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Stap 3: Bestaande app-referenties en verbindingsdetails opvragen

Voer in de Microsoft Graph Explorer de volgende GET-query uit die [servicePrincipalId] vervangt door de **ServicePrincipalId** die uit stap [1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)is geëxtraheerd.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Functiequery GET](./media/skip-out-of-scope-deletions/skip-03.png)

Kopieer het antwoord naar een tekstbestand. Het ziet eruit als de JSON-tekst hieronder, met waarden die in het geel zijn gemarkeerd, specifiek voor uw implementatie. Voeg de in het groen gemarkeerde regels toe aan het einde en werk het wachtwoord van de werkdagverbinding bij dat in het blauw is gemarkeerd. 

   ![GET-taakrespons](./media/skip-out-of-scope-deletions/skip-04.png)

Hier is het JSON-blok dat u aan de toewijzing moet toevoegen. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Stap 4: Update het eindpunt van de geheimen bij met de vlag SkipOutOfScopeDeletions

Voer in de Grafiekverkenner de onderstaande opdracht uit om het eindpunt van de geheimen bij te werken met de vlag ***SkipOutOfScopeDeletions.*** 

Vervang in de onderstaande URL [servicePrincipalId] door de **ServicePrincipalId** uit stap [1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopieer de bijgewerkte tekst uit stap 3 naar de 'Aanvraaginstantie' en stel de koptekst 'Inhoudstype' in op 'toepassing/json' in 'Kopteksten aanvragen'. 

   ![PUT-verzoek](./media/skip-out-of-scope-deletions/skip-05.png)

Klik op 'Query uitvoeren'. 

Je moet de output als "Succes - Status Code 204". 

   ![PUT-reactie](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Stap 5: Controleer of gebruikers buiten het bereik niet worden uitgeschakeld

U deze vlag testen in verwacht gedrag door uw scopingregels bij te werken om een specifieke gebruiker over te slaan. In het onderstaande voorbeeld sluiten we de werknemer met ID 21173 (die eerder in het bereik was) uit door een nieuwe scopingregel toe te voegen: 

   ![Voorbeeld van Scoping](./media/skip-out-of-scope-deletions/skip-07.png)

In de volgende inrichtingscyclus wordt in de Azure AD-inrichtingsservice aangegeven dat de gebruiker 21173 buiten het bereik is gevallen en als de eigenschap SkipOutOfScopeDeletions is ingeschakeld, wordt een bericht weergegeven zoals hieronder wordt weergegeven: 

   ![Voorbeeld van Scoping](./media/skip-out-of-scope-deletions/skip-08.png)


