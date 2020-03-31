---
title: Bekende problemen met de naleving van scim 2.0-protocol - Azure AD
description: Algemene problemen met protocolcompatibiliteit oplossen bij het toevoegen van een niet-galerietoepassing die SCIM 2.0 aan Azure AD ondersteunt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522931"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Bekende problemen en oplossingen met de naleving van het SCIM 2.0-protocol van de Azure AD-service voor het inrichten van gebruikers

Azure Active Directory (Azure AD) kan gebruikers en groepen automatisch inrichten voor elke toepassing of systeem die wordt voorgeschreven door een webservice met de interface die is gedefinieerd in de [SCIM-protocolspecificatie (System for Cross-Domain Identity Management).](https://tools.ietf.org/html/draft-ietf-scim-api-19) 

De ondersteuning van Azure AD voor het SCIM 2.0-protocol wordt beschreven in [System for Cross-Domain Identity Management (SCIM) gebruiken om gebruikers en groepen van Azure Active Directory automatisch in te richten op toepassingen](use-scim-to-provision-users-and-groups.md), waarin de specifieke onderdelen van het protocol worden vermeld dat het implementeert om gebruikers en groepen van Azure AD automatisch te voorzien in toepassingen die SCIM.0 ondersteunen.

In dit artikel worden huidige en eerdere problemen beschreven met de naleving van het SCIM 2.0-protocol door de Azure AD-gebruikersinrichtingsservice en hoe u deze problemen oplossen.

> [!IMPORTANT]
> De laatste update voor de Azure AD-gebruikersinrichtingsservice SCIM-client is gemaakt op 18 december 2018. Deze update heeft de bekende compatibiliteitsproblemen in de onderstaande tabel verholpen. Zie de veelgestelde vragen hieronder voor meer informatie over deze update.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 compliance problemen en status

| **SCIM 2.0 compliance issue** |  **Vaste?** | **Datum vastzetten**  |  
|---|---|---|
| Azure AD vereist dat "/scim" in de hoofdmap van de SCIM-eindpunt-URL van de toepassing staat  | Ja  |  18 december 2018 | 
| Extensiekenmerken gebruiken puntnota "." notatie vóór kenmerknamen in plaats van dubbele punt ":" notatie |  Ja  | 18 december 2018  | 
|  Patchaanvragen voor kenmerken met meerdere waarden bevatten de syntaxis van ongeldige padfilter | Ja  |  18 december 2018  | 
|  Aanvragen voor het maken van groepen bevatten een ongeldig schema URI | Ja  |  18 december 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Zijn de beschreven servicesfixes automatisch toegepast op mijn reeds bestaande SCIM-app?

Nee. Aangezien het een baanbrekende wijziging zou zijn geweest in SCIM-apps die waren gecodeerd om met het oudere gedrag te werken, werden de wijzigingen niet automatisch toegepast op bestaande apps.

De wijzigingen worden toegepast op alle nieuwe [niet-galerij SCIM-apps](../manage-apps/configure-single-sign-on-non-gallery-applications.md) die zijn geconfigureerd in de Azure-portal, na de datum van de oplossing.

Zie de volgende sectie voor informatie over het migreren van een reeds bestaande taak voor het inrichten van gebruikers om de nieuwste oplossingen op te nemen.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Kan ik een bestaande SCIM-gebaseerde gebruikersinrichtingstaak migreren om de nieuwste servicefixes op te nemen?

Ja. Als u deze toepassingsinstantie al gebruikt voor eenmalige aanmelding en de bestaande inrichtingstaak moet migreren om de nieuwste oplossingen op te nemen, volgt u de onderstaande procedure. In deze procedure wordt beschreven hoe u de Microsoft Graph API en de Microsoft Graph API explorer gebruiken om uw oude inrichtingstaak uit uw bestaande SCIM-app te verwijderen en een nieuwe te maken die het nieuwe gedrag vertoont.

> [!NOTE]
> Als uw toepassing nog in ontwikkeling is en nog niet is geïmplementeerd voor eenmalige aanmelding of gebruikersinrichting, is de eenvoudigste oplossing het verwijderen van de toepassingsvermelding in de sectie **Azure Active Directory > Enterprise-toepassingen** van de Azure-portal en voegt u eenvoudig een nieuwe vermelding voor de toepassing toe met de optie Toepassing maken > **niet-galerij.** Dit is een alternatief voor het uitvoeren van de onderstaande procedure.
 
1. Meld u aan https://portal.azure.combij de Azure-portal op .
2. Zoek en selecteer in de sectie **Azure Active Directory > Enterprise Applications** van de Azure-portal uw bestaande SCIM-toepassing.
3. Kopieer de **object-id**in de sectie **Eigenschappen** van uw bestaande SCIM-app.
4. Ga in een nieuw browservenster naar https://developer.microsoft.com/graph/graph-explorer en meld u aan als beheerder voor de Azure AD-tenant waar uw app is toegevoegd.
5. Voer in de Grafiekverkenner de onderstaande opdracht uit om de id van uw inrichtingstaak te zoeken. Vervang "[object-id]" door de serviceprincipal-id (object-ID) die is gekopieerd uit de derde stap.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Vacatures zoeken](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Vacatures zoeken") 


6. Kopieer in de resultaten de volledige tekenreeks "ID" die begint met "customappsso" of "scim".
7. Voer de onderstaande opdracht uit om de configuratie van de toewijzing van kenmerken op te halen, zodat u een back-up maken. Gebruik dezelfde [object-id] als voorheen en vervang [job-id] door de inrichtende taak-ID die is gekopieerd van de laatste stap.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Schema ophalen](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Schema ophalen") 

8. Kopieer de JSON-uitvoer uit de laatste stap en sla deze op in een tekstbestand. Dit bevat alle aangepaste attribuuttoewijzingen die u aan uw oude app hebt toegevoegd en die ongeveer een paar duizend regels JSON moeten zijn.
9. Voer de opdracht hieronder uit om de inrichtingstaak te verwijderen:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Voer de onderstaande opdracht uit om een nieuwe inrichtingstaak te maken met de nieuwste servicefixes.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Kopieer in de resultaten van de laatste stap de volledige tekenreeks "ID" die begint met "scim". Past eventueel uw oude kenmerktoewijzingen opnieuw toe door de onderstaande opdracht uit te voeren, [nieuwe taak-id] te vervangen door de nieuwe taak-id die u zojuist hebt gekopieerd en de JSON-uitvoer in te voeren vanuit stap #7 als aanvraaginstantie.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Ga terug naar het eerste browservenster en selecteer het tabblad **Inrichten** voor uw toepassing.
13. Controleer uw configuratie en start de inrichtingstaak. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Kan ik een nieuwe niet-galerij-app toevoegen met het oude gebruikersinrichtingsgedrag?

Ja. Als u een toepassing had gecodeerd voor het oude gedrag dat vóór de oplossingen bestond en een nieuw exemplaar ervan moet implementeren, volgt u de onderstaande procedure. In deze procedure wordt beschreven hoe u de Microsoft Graph API en de Microsoft Graph API explorer gebruiken om een SCIM-inrichtingstaak te maken die het oude gedrag vertoont.
 
1. Meld u aan https://portal.azure.combij de Azure-portal op .
2. Maak in de sectie **Azure Active Directory > Enterprise Applications > Toepassing maken** van de Azure-portal een nieuwe **niet-galerietoepassing.**
3. Kopieer de **object-id**in de sectie **Eigenschappen** van uw nieuwe aangepaste app.
4. Ga in een nieuw browservenster naar https://developer.microsoft.com/graph/graph-explorer en meld u aan als beheerder voor de Azure AD-tenant waar uw app is toegevoegd.
5. Voer in de Grafiekverkenner de onderstaande opdracht uit om de inrichtingsconfiguratie voor uw app te initialiseren.
   Vervang "[object-id]" door de serviceprincipal-id (object-ID) die is gekopieerd uit de derde stap.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Ga terug naar het eerste browservenster en selecteer het tabblad **Inrichten** voor uw toepassing.
7. Voltooi de configuratie van de gebruikersinrichting zoals u dat normaal zou doen.


## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het inrichten en de-provisioning voor SaaS-toepassingen](user-provisioning.md)

