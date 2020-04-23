---
title: Met SCIM, de Microsoft Graph en de Azure AD-inrichtingsservice voor het inrichten van gebruikers en het verrijken van uw toepassing met de gegevens die deze nodig heeft | Microsoft Documenten
description: SCIM en de Microsoft Graph samen gebruiken om gebruikers in te richten en uw toepassing te verrijken met de gegevens die het nodig heeft.
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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087619"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM en Microsoft Graph samen gebruiken om gebruikers te voorzien en uw toepassing te verrijken met de gegevens die het nodig heeft

**Doelgroep:** Dit document is gericht op ontwikkelaars die toepassingen bouwen die zijn geïntegreerd met Azure AD. Voor anderen die een bestaande toepassing willen integreren, zoals Zoom, ServiceNow en DropBox, u dit overslaan en de toepassingsspecifieke [zelfstudies bekijken.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

**Algemene scenario's**

> [!div class="checklist"]
> * Gebruikers automatisch maken in mijn toepassing
> * Gebruikers automatisch uit mijn toepassing verwijderen wanneer ze geen toegang meer zouden moeten hebben
> * Mijn applicatie integreren met meerdere identiteitsproviders voor inrichten
> * Verrijk mijn toepassing met gegevens uit Microsoft-services zoals Sharepoint, Outlook en Office.
> * Gebruikers en groepen automatisch maken, bijwerken en verwijderen in Azure AD en Active Directory

![SCIM Graph beslissingsboom](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: Gebruikers automatisch maken in mijn app
Tegenwoordig maken IT-beheerders handmatig gebruikersaccounts in mijn toepassing elke keer dat iemand toegang nodig heeft of periodiek CSV-bestanden uploadt. Het proces is tijdrovend voor klanten en vertraagt de adoptie van mijn applicatie. Alles wat ik nodig heb is [basisgebruikersinformatie](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) zoals naam, e-mail en userPrincipalName om een gebruiker te maken. Verder gebruiken mijn klanten verschillende IdPs en heb ik niet de middelen om een sync engine en aangepaste integraties bij elke IdP te onderhouden. 

**Aanbeveling**: Ondersteuning van een SCIM-compatibel [/Gebruikerseindpunt.](https://aka.ms/scimreferencecode) Uw klanten kunnen dit eindpunt eenvoudig gebruiken om te integreren met de Azure AD-inrichtingsservice en automatisch gebruikersaccounts maken wanneer ze toegang nodig hebben. U het eindpunt één keer bouwen en het is compatibel met alle IdPs, zonder dat u een synchronisatie-engine hoeft te onderhouden. Bekijk het voorbeeldverzoek hieronder voor hoe een gebruiker wordt gemaakt.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: Gebruikers automatisch uit mijn app verwijderen
De klanten die mijn toepassing gebruiken zijn beveiligingsgericht en hebben governance-vereisten om accounts te verwijderen wanneer werknemers ze niet meer nodig hebben. Hoe kan ik deprovisioning uit mijn toepassing automatiseren?

**Aanbeveling:** Ondersteuning voor een SCIM-compatibel /Gebruikerseindpunt. De Azure AD-inrichtingsservice verzendt aanvragen om uit te schakelen en te verwijderen wanneer de gebruiker geen toegang meer zou moeten hebben. We raden u aan gebruikers te ondersteunen en te verwijderen. Zie de voorbeelden hieronder voor hoe een aanvraag voor uitschakelen en verwijderen eruit ziet. 

Gebruiker uitschakelen
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Gebruiker verwijderen
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Groepslidmaatschappen in mijn app automatiseren
Mijn toepassing is afhankelijk van groepen voor toegang tot verschillende bronnen en klanten willen de groepen die ze in Azure AD hebben, opnieuw gebruiken. Hoe kan ik groepen importeren uit Azure AD en deze up-to-date houden als de lidmaatschappen veranderen?  

**Aanbeveling:** Ondersteuning voor een [scim-compatibel /groepseindpunt](https://aka.ms/scimreferencecode). De Azure AD-inrichtingsservice zorgt voor het maken van groepen en het beheren van lidmaatschapsupdates in uw toepassing. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: Verrijk mijn app met gegevens van Microsoft-services zoals Teams, Outlook en OneDrive.
Mijn applicatie is ingebouwd in Microsoft Teams en is afhankelijk van berichtgegevens. Daarnaast slaan we bestanden voor gebruikers op in OneDrive. Hoe kan ik mijn toepassing verrijken met de gegevens van deze services en in Microsoft?

**Aanbeveling:** De [Microsoft Graph](https://docs.microsoft.com/graph/) is uw toegangspunt om toegang te krijgen tot Microsoft-gegevens. Elke workload stelt API's bloot met de gegevens die u nodig hebt. De Microsoft-grafiek kan worden gebruikt samen met [SCIM provisioning](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) voor de bovenstaande scenario's. U SCIM gebruiken om basisgebruikerskenmerken in uw toepassing in te richten terwijl u in grafiek aanroept om andere gegevens te krijgen die u nodig hebt. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: Wijzigingen in Microsoft-services bijhouden, zoals Teams, Outlook en Azure AD.
Ik moet wijzigingen in Teams en Outlook-berichten kunnen bijhouden en er in realtime op kunnen reageren. Hoe kan ik deze wijzigingen in mijn toepassing laten pushen?

**Aanbeveling:** De Microsoft Graph biedt [wijzigingsmeldingen](https://docs.microsoft.com/graph/webhooks) en wijzigingstracking voor verschillende bronnen. Let op de volgende beperkingen van wijzigingsmeldingen:
- Als een gebeurtenisontvanger een gebeurtenis erkent, maar er om welke reden dan ook niet naar handelt, kan de gebeurtenis verloren gaan
- Als een gebeurtenisontvanger een gebeurtenis erkent, maar er om welke reden dan ook niet naar handelt, kan de gebeurtenis verloren gaan
- Wijzigingsmeldingen bevatten niet altijd de [brongegevens](https://docs.microsoft.com/graph/webhooks-with-resource-data) Om de bovenstaande redenen gebruiken ontwikkelaars vaak wijzigingsmeldingen samen met wijzigingstracking voor synchronisatiescenario's. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: Gebruikers en groepen inrichten in Azure AD.
Mijn toepassing maakt informatie over een gebruiker die klanten nodig hebben in Azure AD. Dit kan een HR-toepassing zijn dan het beheren van inhuur, een communicatie-app die telefoonnummers voor gebruikers maakt of een andere app die gegevens genereert die waardevol zouden zijn in Azure AD. Hoe vul ik de gebruikersrecord in Azure AD in met die gegevens? 

**Aanbeveling** In de Microsoft-grafiek worden /Gebruikers- en /Groepen eindpunten weergegeven waarmee u vandaag integreren om gebruikers in Azure AD te voorzien. Houd er rekening mee dat Azure Active Directory geen ondersteuning biedt voor het terugschrijven van deze gebruikers in Active Directory. 

> [!NOTE]
> Microsoft heeft een inrichtingsservice die gegevens uit HR-toepassingen zoals Workday en SuccessFactors binnenhaalt. Deze integraties worden gebouwd en beheerd door Microsoft. Voor het onboarding en instappen van een nieuwe HR-applicatie op onze service, u deze aanvragen op [UserVoice.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Verwante artikelen:

- [De documentatie van Microsoft Graph voor synchronisatie controleren](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Een aangepaste SCIM-app integreren met Azure AD](use-scim-to-provision-users-and-groups.md)
