---
title: Verwijdering van gebruikers buiten het bereik overs Laan | Microsoft Docs
description: Meer informatie over het negeren van het standaard gedrag van het verwijderen van gebruikers buiten het bereik.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a753d8cce3f3b610abab2f78d54d76a05d8bc5cb
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815977"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Verwijdering van gebruikers accounts die buiten het bereik vallen, overs Laan

De Azure AD-inrichtings engine verwijdert standaard gebruikers die buiten het bereik vallen. Voor bepaalde scenario's zoals workday naar inkomend gebruik van AD-gebruikers is dit gedrag echter mogelijk niet de verwachte en wilt u dit standaard gedrag overschrijven.  

In deze hand leiding wordt beschreven hoe u de Microsoft Graph-API en de Microsoft Graph API Explorer kunt gebruiken om de vlag ***SkipOutOfScopeDeletions*** in te stellen waarmee de verwerking van accounts die buiten het bereik vallen worden beheerd. 
* Als ***SkipOutOfScopeDeletions*** is ingesteld op 0 (false), worden de accounts die zich buiten het bereik bevinden, uitgeschakeld in het doel
* Als ***SkipOutOfScopeDeletions*** is ingesteld op 1 (waar), worden accounts die buiten het bereik vallen niet uitgeschakeld in het doel. deze vlag wordt ingesteld op het niveau van de *inrichtings app* en kan worden geconfigureerd met behulp van de Graph API. 

Omdat deze configuratie veel wordt gebruikt met de *werkdag om de app voor het inrichten van gebruikers te Active Directory* , bevatten de onderstaande stappen scherm opnamen van de werkdag-toepassing. Dit kan echter ook worden gebruikt met andere inrichtings toepassingen.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Stap 1: Uw inrichtings App Service Principal-ID ophalen (object-ID)

1. Start de [Azure Portal](https://portal.azure.com)en navigeer naar de sectie eigenschappen van uw inrichtings toepassing. Bijvoorbeeld, als u uw werkdag wilt exporteren *naar AD User Provisioning toepassings* toewijzing, navigeert u naar de sectie eigenschappen van de app. 
1. In de sectie eigenschappen van uw inrichtings app kopieert u de GUID-waarde die is gekoppeld aan het veld *object-id* . Deze waarde wordt ook wel de **ServicePrincipalId** van uw app genoemd en wordt gebruikt in Graph Explorer-bewerkingen.

   ![App Service Principal-ID van workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Stap 2: Aanmelden bij Microsoft Graph Explorer

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) starten
1. Klik op de knop Aanmelden met micro soft en meld u aan met Azure AD Global admin of de referenties van de app-beheerder.

    ![Graph-aanmelding](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Wanneer de aanmelding is geslaagd, worden de gegevens van het gebruikers account in het linkerdeel venster weer gegeven.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Stap 3: Bestaande app-referenties en connectiviteits gegevens ophalen

Voer in de Microsoft Graph Explorer de volgende GET-query Vervang [servicePrincipalId] uit met de **servicePrincipalId** geëxtraheerd uit [stap 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

   ![Taak query ophalen](./media/skip-out-of-scope-deletions/skip-03.png)

Kopieer het antwoord naar een tekst bestand. Deze ziet eruit als de JSON-tekst die hieronder wordt weer gegeven, met waarden die geel zijn gemarkeerd voor uw implementatie. Voeg de regels die zijn gemarkeerd in groen toe aan het einde en werk het wacht woord voor workday-verbindingen in op blauw gemarkeerd. 

   ![Taak respons ophalen](./media/skip-out-of-scope-deletions/skip-04.png)

Dit is het JSON-blok dat aan de toewijzing moet worden toegevoegd. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Stap 4: Het geheimen-eind punt bijwerken met de vlag SkipOutOfScopeDeletions

Voer in de Graph Explorer de onderstaande opdracht uit om het geheimen-eind punt bij te werken met de vlag ***SkipOutOfScopeDeletions*** . 

Vervang in de onderstaande URL [servicePrincipalId] door de **servicePrincipalId** geëxtraheerd uit [stap 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopieer de bijgewerkte tekst uit stap 3 naar de ' hoofd tekst van de aanvraag ' en stel de header ' content-type ' in ' application/json ' in ' aanvraag headers ' in. 

   ![Aanvraag plaatsen](./media/skip-out-of-scope-deletions/skip-05.png)

Klik op query uitvoeren. 

U moet de uitvoer als ' geslaagd – status code 204 ' ophalen. 

   ![Antwoord plaatsen](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Stap 5: Controleren of gebruikers buiten het bereik niet worden uitgeschakeld

U kunt deze vlag testen op het verwachte gedrag door de scope regels bij te werken om een specifieke gebruiker over te slaan. In het onderstaande voor beeld wordt de werk nemer met ID 21173 (die eerder in bereik was) uitgesloten door een nieuwe scope regel toe te voegen: 

   ![Voor beeld van scoping](./media/skip-out-of-scope-deletions/skip-07.png)

In de volgende inrichtings cyclus identificeert de Azure AD-inrichtings service dat de gebruiker 21173 zich buiten het bereik bevindt. als de eigenschap SkipOutOfScopeDeletions is ingeschakeld, wordt in de synchronisatie regel voor die gebruiker een bericht weer gegeven zoals hieronder wordt weer gegeven: 

   ![Voor beeld van scoping](./media/skip-out-of-scope-deletions/skip-08.png)


