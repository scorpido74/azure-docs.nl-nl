---
title: Bekende problemen met SCIM 2,0-protocol compatibiliteit-Azure AD
description: Veelvoorkomende problemen met protocol compatibiliteit oplossen bij het toevoegen van een niet-galerie toepassing die ondersteuning biedt voor SCIM 2,0 in azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 12/03/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ae08589785d8a482801c71ce3641ba0d66d11133
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782259"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Bekende problemen en oplossingen met SCIM 2,0-protocol compatibiliteit van de Azure AD User Provisioning Service

Azure Active Directory (Azure AD) kan gebruikers en groepen automatisch inrichten voor elke toepassing of elk systeem dat wordt gefronteerd door een webservice met de interface die is gedefinieerd in het [systeem voor Identity Management (scim) 2,0-protocol specificatie](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

De ondersteuning van Azure AD voor het SCIM 2,0-protocol wordt beschreven in [met behulp van systeem voor Cross-Domain Identity Management (scim) voor het automatisch inrichten van gebruikers en groepen van Azure Active Directory naar toepassingen](use-scim-to-provision-users-and-groups.md), waarin de specifieke onderdelen van het door IT geïmplementeerde protocol worden vermeld om automatisch gebruikers en groepen van Azure AD in te richten op toepassingen die scim 2,0 ondersteunen.

In dit artikel worden huidige en eerdere problemen beschreven met betrekking tot het SCIM 2,0-protocol van Azure AD User Provisioning Service, en hoe u deze problemen omzeilt.

> [!IMPORTANT]
> De meest recente update voor de Azure AD User Provisioning Service SCIM-client is gemaakt op 18 december 2018. Deze update heeft betrekking op de bekende compatibiliteits problemen die in de volgende tabel worden vermeld. Raadpleeg de veelgestelde vragen hieronder voor meer informatie over deze update.

## <a name="scim-20-compliance-issues-and-status"></a>Nalevings problemen en status van SCIM 2,0

| **Compatibiliteits probleem met SCIM 2,0** |  **Vaste?** | **Datum herstellen**  |  
|---|---|---|
| Voor Azure AD moet '/scim ' zich in de hoofdmap van de SCIM-eind punt-URL van de toepassing bevindt  | Yes  |  18 december 2018 | 
| Extensie kenmerken gebruiken de notatie dot '. ' vóór kenmerk namen in plaats van dubbele punt notatie ': ' |  Yes  | 18 december 2018  | 
|  Patch aanvragen voor kenmerken met meerdere waarden bevatten ongeldige syntaxis van het filter | Yes  |  18 december 2018  | 
|  Aanvragen voor het maken van groepen bevatten een ongeldige schema-URI | Yes  |  18 december 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Zijn de Services opgelost die worden beschreven die automatisch worden toegepast op mijn al bestaande SCIM-app?

Nee. Zoals een belang rijke wijziging in de SCIM-apps die zijn gecodeerd om met het oudere gedrag te werken, zijn de wijzigingen niet automatisch toegepast op bestaande apps.

De wijzigingen worden toegepast op alle nieuwe [niet-galerij scim-apps](../manage-apps/configure-single-sign-on-non-gallery-applications.md) die zijn geconfigureerd in de Azure Portal na de datum van de oplossing.

Zie de volgende sectie voor informatie over het migreren van een vooraf bestaande gebruikers inrichtings taak voor het toevoegen van de meest recente oplossingen.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Kan ik een bestaande SCIM voor het inrichten van gebruikers migreren om de meest recente service oplossingen op te stellen?

Ja. Als u dit exemplaar van de toepassing al gebruikt voor eenmalige aanmelding en de bestaande inrichtings taak moet migreren om de meest recente oplossingen te bevatten, volgt u de onderstaande procedure. In deze procedure wordt beschreven hoe u de Microsoft Graph-API en de Microsoft Graph API Explorer gebruikt om uw oude inrichtings taak uit uw bestaande SCIM-app te verwijderen en een nieuwe te maken die het nieuwe gedrag vertoont.

> [!NOTE]
> Als uw toepassing nog steeds in ontwikkeling is en nog niet is geïmplementeerd voor eenmalige aanmelding of gebruikers inrichting, is het eenvoudig om de vermelding van de toepassing te verwijderen in het gedeelte **Azure Active Directory > Enter prise** van de Azure Portal en een nieuwe vermelding voor de toepassing toe te voegen met de optie **toepassing maken > niet-galerie** . Dit is een alternatief voor het uitvoeren van de onderstaande procedure.
 
1. Meld u aan bij de Azure Portal op https://portal.azure.com .
2. Zoek en selecteer uw bestaande SCIM-toepassing in de sectie **Azure Active Directory > Enter prise Applications** van de Azure Portal.
3. Kopieer de **object-id**in het gedeelte **Eigenschappen** van uw bestaande scim-app.
4. In een nieuw browser venster gaat u naar https://developer.microsoft.com/graph/graph-explorer en meldt u zich aan als beheerder voor de Azure AD-Tenant waar uw app is toegevoegd.
5. Voer in de Graph Explorer de onderstaande opdracht uit om de ID van uw inrichtings taak te vinden. Vervang ' [object-id] ' door de Service-Principal-ID (object-ID) die uit de derde stap is gekopieerd.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Taken ophalen](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Taken ophalen") 


6. In de resultaten kopieert u de volledige ID-teken reeks die begint met ' customappsso ' of ' scim '.
7. Voer de onderstaande opdracht uit om de configuratie voor kenmerk toewijzing op te halen, zodat u een back-up kunt maken. Gebruik dezelfde [object-id] als voorheen en vervang [job-id] door de ID van de inrichtings taak die u uit de laatste stap hebt gekopieerd.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Schema ophalen](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Schema ophalen") 

8. Kopieer de JSON-uitvoer van de laatste stap en sla deze op in een tekst bestand. Dit bevat alle aangepaste kenmerk toewijzingen die u aan uw oude app hebt toegevoegd, en moet ongeveer een paar duizend regels JSON zijn.
9. Voer de onderstaande opdracht uit om de inrichtings taak te verwijderen:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Voer de onderstaande opdracht uit om een nieuwe inrichtings taak te maken met de meest recente service oplossingen.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. In de resultaten van de laatste stap kopieert u de volledige ID-teken reeks die begint met ' scim '. U kunt eventueel uw oude kenmerk toewijzingen opnieuw Toep assen door de onderstaande opdracht uit te voeren, waarbij u [New-Job-ID] vervangt door de nieuwe taak-ID die u zojuist hebt gekopieerd, en de JSON-uitvoer van stap #7 als de aanvraag tekst in te voeren.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Ga terug naar het eerste webbrowser venster en selecteer het tabblad **inrichten** voor uw toepassing.
13. Controleer uw configuratie en start vervolgens de inrichtings taak. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Kan ik een nieuwe niet-galerij-app toevoegen die het oude gedrag voor het inrichten van gebruikers heeft?

Ja. Als u een toepassing hebt gecodeerd voor het oude gedrag dat vóór de oplossingen bestond en een nieuw exemplaar hiervan moet implementeren, volgt u de onderstaande procedure. In deze procedure wordt beschreven hoe u de Microsoft Graph-API en de Microsoft Graph API Explorer kunt gebruiken om een SCIM-inrichtings taak te maken die het oude gedrag vertoont.
 
1. Meld u aan bij de Azure Portal op https://portal.azure.com .
2. Maak een nieuwe **niet-galerie** toepassing in de **Azure Active Directory > bedrijfs toepassingen > toepassings sectie maken** van de Azure Portal.
3. Kopieer de **object-id**in het gedeelte **Eigenschappen** van de nieuwe aangepaste app.
4. In een nieuw browser venster gaat u naar https://developer.microsoft.com/graph/graph-explorer en meldt u zich aan als beheerder voor de Azure AD-Tenant waar uw app is toegevoegd.
5. Voer in de Graph Explorer de onderstaande opdracht uit om de inrichtings configuratie voor uw app te initialiseren.
   Vervang ' [object-id] ' door de Service-Principal-ID (object-ID) die uit de derde stap is gekopieerd.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Ga terug naar het eerste webbrowser venster en selecteer het tabblad **inrichten** voor uw toepassing.
7. Voltooi de configuratie van de gebruikers inrichting zoals u dat gewend bent.


## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het inrichten en ongedaan maken van de inrichting van SaaS-toepassingen](user-provisioning.md)

