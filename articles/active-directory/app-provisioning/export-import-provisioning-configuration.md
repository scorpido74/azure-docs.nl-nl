---
title: Exporteer uw inrichtingsconfiguratie en draai terug naar een bekende goede staat voor herstel na noodgevallen.| Microsoft Documenten
description: Meer informatie over het exporteren van uw inrichtingsconfiguratie en terugdraaien naar een bekende goede staat voor herstel na noodgevallen.
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
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051318"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Uw inrichtingsconfiguratie exporteren en terugsturen naar een bekende goede staat

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Uw inrichtingsconfiguratie exporteren en importeren vanuit de Azure-portal

### <a name="how-can-i-export-my-provisioning-configuration"></a>Hoe kan ik mijn inrichtingsconfiguratie exporteren?
Ga als uw gewenste configuratie als gevolg van het exporteren van uw configuratie:
1. Selecteer **Azure Active Directory**in de [Azure-portal](https://portal.azure.com/)in het linkernavigatiedeelvenster .
2. Selecteer in het deelvenster **Azure Active Directory** de optie **Enterprise-toepassingen** en kies uw toepassing.
3. Selecteer in het linkernavigatiedeelvenster de optie **Inrichten**. Klik vanaf de configuratiepagina op **kenmerktoewijzingen,** **toon**vervolgens geavanceerde opties en bekijk uiteindelijk **uw schema**. Dit brengt u naar de schema-editor. 
5. Klik op downloaden in de opdrachtbalk boven aan de pagina om uw schema te downloaden.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Disaster recovery - terug te keren naar een bekende goede staat
Als u uw configuratie exporteert en opslaat, u terugnaar een vorige versie van uw configuratie. We raden u aan uw inrichtingsconfiguratie te exporteren en op te slaan voor later gebruik wanneer u een wijziging aanbrengt in uw kenmerktoewijzingen of scopingfilters. Het enige wat u hoeft te doen is het JSON-bestand dat u in de bovenstaande stappen hebt gedownload, de volledige inhoud van het JSON-bestand te kopiëren, de volledige inhoud van de JSON-payload in de schema-editor te vervangen en vervolgens op te slaan. Als er een actieve inrichtingscyclus is, wordt deze voltooid en wordt de volgende cyclus gebruikt het bijgewerkte schema. De volgende cyclus zal ook een eerste cyclus zijn, die elke gebruiker en groep opnieuw evalueert op basis van de nieuwe configuratie. Houd rekening met het volgende wanneer u terugrolt naar een vorige configuratie:
* Gebruikers zullen opnieuw worden geëvalueerd om te bepalen of ze binnen het bereik moeten zijn. Als de scopingfilters zijn gewijzigd, is een gebruiker niet meer in het bereik en worden ze uitgeschakeld. Hoewel dit in de meeste gevallen het gewenste gedrag is, zijn er momenten waarop u dit wilt voorkomen en de functionaliteit [voor het verwijderen van scopecookies](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) gebruiken. 
* Als u de inrichtingsconfiguratie wijzigt, wordt de service opnieuw gestart en wordt een [eerste cyclus geactiveerd.](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Uw inrichtingsconfiguratie exporteren en importeren met behulp van de Microsoft Graph API
U de Microsoft Graph API en de Microsoft Graph Explorer gebruiken om uw kenmerktoewijzingstoewijzingen en -schema voor gebruikersvoorziening te exporteren naar een JSON-bestand en deze terug te importeren in Azure AD. U ook de stappen die hier zijn vastgelegd gebruiken om een back-up van uw inrichtingsconfiguratie te maken. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Stap 1: Uw hoofd-id voor het inrichten van app-service ophalen (object-id)

1. Start de [Azure-portal](https://portal.azure.com)en navigeer naar de sectie Eigenschappen van uw inrichtingstoepassing. Als u bijvoorbeeld uw werkdag wilt exporteren *naar het toewijzen van toepassingtoewijzing voor ad-gebruikerstoewijzing,* navigeert u naar de sectie Eigenschappen van die app. 
1. Kopieer in de sectie Eigenschappen van uw inrichtingsapp de GUID-waarde die is gekoppeld aan het veld *Object-id.* Deze waarde wordt ook wel de **ServicePrincipalId** van uw app genoemd en wordt gebruikt in Microsoft Graph Explorer-bewerkingen.

   ![Hoofd-id van de Workday App-service](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Stap 2: Aanmelden bij Microsoft Graph Explorer

1. [Microsoft Graph Explorer starten](https://developer.microsoft.com/graph/graph-explorer)
1. Klik op de knop Aanmelden met Microsoft en meld u aan met azure AD Global Admin- of App Admin-referenties.

    ![Microsoft Graph-aanmelding](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Bij een succesvolle aanmelding ziet u de gegevens van het gebruikersaccount in het linkerdeelvenster.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Stap 3: De inrichtingstaak-id van de inrichtingsapp ophalen

Voer in de Microsoft Graph Explorer de volgende GET-query uit die [servicePrincipalId] vervangt door de **ServicePrincipalId** die uit stap [1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)is geëxtraheerd.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

U krijgt een antwoord zoals hieronder weergegeven. Kopieer het kenmerk 'id' dat in het antwoord aanwezig is. Deze waarde is de **ProvisioningJobId** en wordt gebruikt om de onderliggende schemametagegevens op te halen.

   [![Job-id inrichten](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Stap 4: Download het inrichtingsschema

Voer in de Microsoft Graph Explorer de volgende GET-query uit, waarbij [servicePrincipalId] en [ProvisioningJobId] worden vervangen door de ServicePrincipalId en de ProvisioningJobId die in de vorige stappen zijn opgehaald.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieer het JSON-object uit het antwoord en sla het op in een bestand om een back-up van het schema te maken.

### <a name="step-5-import-the-provisioning-schema"></a>Stap 5: Het inrichtingsschema importeren

> [!CAUTION]
> Voer deze stap alleen uit als u het schema moet wijzigen voor configuratie die niet kan worden gewijzigd met de Azure-portal of als u de configuratie moet herstellen van een eerder geback-upbestand met een geldig en werkend schema.

Configureer in de Microsoft Graph Explorer de volgende PUT-query, waarbij [servicePrincipalId] en [ProvisioningJobId] worden vervangen door de ServicePrincipalId en de provisioningJobId die in de vorige stappen zijn opgehaald.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieer op het tabblad 'Aanvraaghoofd' de inhoud van het JSON-schemabestand.

   [![Aanvraagtekst](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Voeg op het tabblad Kopteksten aanvragen het kenmerk Content-Type toe met de waarde 'toepassing/json'

   [![Kopteksten aanvragen](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Klik op de knop Query uitvoeren om het nieuwe schema te importeren.
