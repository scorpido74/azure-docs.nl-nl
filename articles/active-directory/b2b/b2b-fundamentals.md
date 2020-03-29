---
title: Aanbevolen procedures en aanbevelingen voor Azure Active Directory B2B
description: Lees aanbevolen procedures en aanbevelingen voor B2B-gastgebruikerstoegang (business-to-business) in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050848"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Aanbevolen procedures voor Azure Active Directory B2B
Dit artikel bevat aanbevelingen en aanbevolen procedures voor B2B-samenwerking (business-to-business) in Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021**ondersteunt Microsoft niet langer het inwisselen van uitnodigingen door onbeheerde Azure AD-accounts en tenants te maken voor B2B-samenwerkingsscenario's. Ter voorbereiding moedigen we klanten aan om te kiezen voor [eenmalige wachtwoordverificatie voor e-mail.](one-time-passcode.md) We zijn blij met uw feedback over deze openbare preview-functie en zijn verheugd om nog meer manieren te creëren om samen te werken.

## <a name="b2b-recommendations"></a>B2B-aanbevelingen
| Aanbeveling | Opmerkingen |
| --- | --- |
| Voor een optimale aanmeldingservaring, federatie met identiteitsaanbieders | Waar mogelijk rechtstreeks met identiteitsproviders te overleggen om uitgenodigde gebruikers toe te staan zich aan te melden bij uw gedeelde apps en bronnen zonder Microsoft-accounts (MSA's) of Azure AD-accounts te hoeven maken. U de [functie Google Federation](google-federation.md) gebruiken om B2B-gastgebruikers in staat te stellen zich aan te melden met hun Google-account. U ook de [functie Direct federation (preview)](direct-federation.md) gebruiken om een directe federatie in te stellen bij elke organisatie waarvan de identiteitsprovider (IdP) het SAML 2.0- of WS-Fed-protocol ondersteunt. |
| Gebruik de functie Onetime passcode (preview) e-mail voor B2B-gasten die zich niet op een andere manier kunnen verifiëren | De functie [Onetime passcode (preview) e-mail](one-time-passcode.md) verifieert B2B-gastgebruikers wanneer ze niet kunnen worden geverifieerd via andere middelen, zoals Azure AD, een Microsoft-account (MSA) of Google-federatie. Wanneer de gastgebruiker een uitnodiging inwisselt of toegang krijgt tot een gedeelde bron, kan hij of zij een tijdelijke code aanvragen die naar zijn e-mailadres wordt verzonden. Vervolgens voeren ze deze code in om verder te gaan met inloggen. |
| Huisstijl toevoegen aan uw aanmeldingspagina | U uw aanmeldingspagina aanpassen, zodat deze intuïtiever is voor uw B2B-gastgebruikers. Bekijk hoe u [bedrijfsbranding toevoegt om u aan te melden en pagina's in het deelvenster toegang te openen.](../fundamentals/customize-branding.md) |
| Voeg uw privacyverklaring toe aan de B2B-gebruikersinwisselervaring | U de URL van de privacyverklaring van uw organisatie toevoegen aan het eerste keer inwisselingsproces van uitnodigingen, zodat een uitgenodigde gebruiker moet instemmen met uw privacyvoorwaarden om door te gaan. [Zie How-to: Voeg de privacygegevens van uw organisatie toe in Azure Active Directory](https://aka.ms/adprivacystatement). |
| Gebruik de functie bulkuitnodiging (preview) om meerdere B2B-gastgebruikers tegelijk uit te nodigen | Nodig meerdere gastgebruikers tegelijk uit voor uw organisatie door de bulkuitnodigingspreviewfunctie in de Azure-portal te gebruiken. Met deze functie u een CSV-bestand uploaden om B2B-gastgebruikers te maken en in bulk uitnodigingen verzenden. Zie [Zelfstudie voor bulkuitnodigende B2B-gebruikers](tutorial-bulk-invite.md). |
| Beleid voor voorwaardelijke toegang afdwingen voor multifactorverificatie (MFA) | We raden u aan het MFA-beleid af te dwingen voor de apps die u wilt delen met B2B-gebruikers van partners. Op deze manier wordt MFA consequent afgedwongen op de apps in uw tenant, ongeacht of de partnerorganisatie MFA gebruikt. Zie [Voorwaardelijke toegang voor Gebruikers van B2B-samenwerkingsverbanden](conditional-access.md). |
| Als u beleid voor voorwaardelijke toegang op basis van apparaten afdwingt, gebruikt u uitsluitingslijsten om toegang te verlenen tot B2B-gebruikers | Als het op apparaten gebaseerde beleid voor voorwaardelijke toegang is ingeschakeld in uw organisatie, worden apparaten van B2B-gastgebruikers geblokkeerd omdat ze niet door uw organisatie worden beheerd. U uitsluitingslijsten maken met specifieke partnergebruikers om ze uit te sluiten van het op apparaten gebaseerde beleid voor voorwaardelijke toegang. Zie [Voorwaardelijke toegang voor Gebruikers van B2B-samenwerkingsverbanden](conditional-access.md). |
| Gebruik een tenantspecifieke URL bij het verstrekken van directe links naar uw B2B-gastgebruikers | Als alternatief voor de uitnodigingse-mail u een gast een directe link naar uw app of portal geven. Deze directe koppeling moet tenantspecifiek zijn, wat betekent dat deze een tenant-id of een geverifieerd domein moet bevatten, zodat de gast kan worden geverifieerd in uw tenant, waar de gedeelde app zich bevindt. Zie [Inwisselervaring voor de gastgebruiker](redemption-experience.md). |
| Gebruik UserType bij het ontwikkelen van een app om de gebruikerservaring van gasten te bepalen  | Als u een toepassing ontwikkelt en u verschillende ervaringen wilt bieden aan tenantgebruikers en gastgebruikers, gebruikt u de eigenschap UserType. De UserType-claim is momenteel niet opgenomen in het token. Toepassingen moeten de Microsoft Graph API gebruiken om de map op te vragen voor de gebruiker om hun UserType te krijgen. |
| De eigenschap UserType *alleen* wijzigen als de relatie van de gebruiker met de organisatie verandert | Hoewel het mogelijk is om PowerShell te gebruiken om de eigenschap UserType voor een gebruiker om te zetten van Lid naar Gast (en vice versa), moet u deze eigenschap alleen wijzigen als de relatie van de gebruiker met uw organisatie verandert. Zie [Eigenschappen van een B2B-gastgebruiker](user-properties.md).|

## <a name="next-steps"></a>Volgende stappen

[B2B delen beheren](delegate-invitations.md)
