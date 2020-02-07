---
title: Uw inrichtings configuratie exporteren of importeren met behulp van Graph API | Microsoft Docs
description: Meer informatie over het exporteren en importeren van inrichtings configuratie met behulp van Graph API.
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
ms.openlocfilehash: af699fa2201bce5627426dcdefc1b98c1d885ae5
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066614"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Uw inrichtings configuratie exporteren of importeren met behulp van Graph API

U kunt Microsoft Graph-API en Graph Explorer gebruiken om de kenmerk toewijzingen en het schema voor het inrichten van gebruikers te exporteren naar een JSON-bestand en dit weer te importeren in azure AD. U kunt ook de stappen die hier zijn vastgelegd, gebruiken om een back-up van uw inrichtings configuratie te maken. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Stap 1: de principal-ID van uw inrichtings App Service ophalen (object-ID)

1. Start de [Azure Portal](https://portal.azure.com)en navigeer naar de sectie eigenschappen van uw inrichtings toepassing. Bijvoorbeeld, als u uw werkdag wilt exporteren *naar AD User Provisioning toepassings* toewijzing, navigeert u naar de sectie eigenschappen van de app. 
1. In de sectie eigenschappen van uw inrichtings app kopieert u de GUID-waarde die is gekoppeld aan het veld *object-id* . Deze waarde wordt ook wel de **ServicePrincipalId** van uw app genoemd en wordt gebruikt in Graph Explorer-bewerkingen.

   ![App Service Principal-ID van workday](./media/export-import-provisioning-configuration/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Stap 2: aanmelden bij Microsoft Graph Explorer

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) starten
1. Klik op de knop Aanmelden met micro soft en meld u aan met Azure AD Global admin of de referenties van de app-beheerder.

    ![Graph-aanmelding](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Wanneer de aanmelding is geslaagd, worden de gegevens van het gebruikers account in het linkerdeel venster weer gegeven.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Stap 3: de inrichtings taak-ID van de inrichtings-app ophalen

Voer in de Microsoft Graph Explorer de volgende GET-query Vervang [servicePrincipalId] uit met de **servicePrincipalId** geëxtraheerd uit [stap 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

U ontvangt een antwoord zoals hieronder wordt weer gegeven. Kopieer het kenmerk id dat aanwezig is in het antwoord. Deze waarde is de **ProvisioningJobId** en wordt gebruikt voor het ophalen van de onderliggende meta gegevens van het schema.

   [inrichtings taak-ID ![](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Stap 4: het inrichtings schema downloaden

Voer in de Microsoft Graph Explorer de volgende GET-query uit en vervang [servicePrincipalId] en [ProvisioningJobId] door de ServicePrincipalId en de ProvisioningJobId die in de vorige stappen zijn opgehaald.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieer het JSON-object van het antwoord en sla het op in een bestand om een back-up van het schema te maken.

## <a name="step-5-import-the-provisioning-schema"></a>Stap 5: het inrichtings schema importeren

> [!CAUTION]
> Voer deze stap alleen uit als u het schema voor configuratie wilt wijzigen dat niet kan worden gewijzigd met behulp van de Azure Portal of als u de configuratie wilt herstellen vanuit een eerder back-up van een bestand met een geldig en werk schema.

Configureer in de Microsoft Graph Explorer de volgende PUT-query en vervang [servicePrincipalId] en [ProvisioningJobId] door de ServicePrincipalId en de ProvisioningJobId die in de vorige stappen zijn opgehaald.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieer de inhoud van het JSON-schema bestand op het tabblad aanvraag tekst.

   [![aanvraag tekst](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Voeg op het tabblad aanvraag headers het kenmerk content-type header toe met de waarde ' application/json '

   [![aanvraag headers](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Klik op de knop Query uitvoeren om het nieuwe schema te importeren.
