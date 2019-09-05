---
title: 'Account migratie van Cloud Partner-portal naar partner Center: commerciële Marketplace voor Azure'
description: Uw account migreren van CPP naar partner Center. -Commerciële Marketplace voor Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c7680896507cd3738fa3bce0d30a516d08509c4
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383293"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Account migratie van Cloud Partner-portal naar het partner centrum

Als uw aanbiedingen worden gemigreerd van Cloud Partner-portal (CPP) naar het partner centrum (PC), worden ze vergrendeld voor bewerking in CPP. Op dit moment moeten uw account instellingen worden gemigreerd naar het partner centrum. Zowel uw account instellingen als uw aanbiedingen kunnen worden beheerd in het partner centrum.

## <a name="account-migration-process"></a>Account migratie proces

Wanneer aanbiedingen worden gemigreerd vanuit CPP, wordt uw account geconfigureerd voor migratie. 
 
Als u een gebruiker bent met de rol owner in CPP voor een bepaald account, wordt een gele banner weer gegeven op de Publisher-profiel pagina. U wordt gevraagd om uw account instellingen te verplaatsen naar het partner centrum. 

Klik op de banner om uw account migratie proces te initiëren. U wordt verwacht de volgende items in te voeren:

### <a name="work-email-address"></a>E-mailadres werk

In de meeste gevallen meldt u zich aan met het e-mail adres dat u gebruikt om u aan te melden bij CPP. In bepaalde gevallen moet een ander e-mail adres worden gebruikt:

* Microsoft-account: Als het CPP-account een Microsoft-account is, moet u een geldig werk-e-mail adres invoeren dat is gekoppeld aan de Tenant, waarvoor de MPN-ID is geregistreerd.

* Niet-overeenkomende tenants: Als uw zakelijke e-mail adres niet hoort bij de Tenant die is gekoppeld aan de Microsoft Partner Network-ID die is opgegeven in uw CPP-account, wordt er een fout weer gegeven. Als u deze fout wilt verplaatsen, voert u een e-mail adres in dat is gekoppeld aan de Tenant. Er wordt een fout bericht weer gegeven met de naam van de Tenant.

### <a name="sign-up-for-microsoft-partner-network-program"></a>Registreren voor Microsoft Partner Network programma

In het geval dat uw CPP-account geen Microsoft Partner Network-ID heeft of een certificaat heeft dat ongeldig is, moet u zich registreren voor het Microsoft Partner Network-programma als onderdeel van het activerings proces.

## <a name="after-account-migration-is-complete"></a>Nadat de account migratie is voltooid

Migratie moet slechts één keer plaatsvinden voor een bepaald account. Zodra een gegeven partner de migratie voor het account heeft voltooid, zien alle eigen aren dit gedrag op hun Publisher-profiel pagina:

1. U ziet de pagina partner instellingen in Microsoft Partner Network, waar u nu account instellingen kunt beheren. 
2. Een geel span doek op de Publisher-profiel pagina in CPP wordt weer gegeven aan gebruikers die de rol eigenaar hebben, om hen te vragen hun account instellingen in het partner centrum te beheren.
3. De pagina account instellingen in CPP wordt geconverteerd naar de modus alleen-lezen.

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Op Dynamics 365 gebaseerde oplossingen verplaatsen naar het partner centrum

Als u Dynamics 365 hebt gemaakt voor klant betrokkenheid of Dynamics 365 voor financiering en operationele oplossingen in de ene commerciële partner GTM-Portal, **moeten deze oplossingen nu worden beheerd in het partner centrum**.

**Als u uw oplossingen niet op 31 augustus 2019 hebt verplaatst**, voert u de onderstaande stappen zo snel mogelijk uit. Totdat u dit doet:
- Isv's hebben geen toegang tot marketing voordelen
- De prioriteit van gezamenlijk verkopen gaat verloren
- Die vereisen dat het insluiten van Clouds na 15 oktober 2019 niet meer wordt nageleefd

> [!NOTE]
> Als uw MPN-lidmaatschaps account oorspronkelijk is gemaakt in het Partner Membership Center (PMC), meldt u zich aan bij het [partner centrum](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) om te bevestigen dat uw account is gemigreerd voordat u de onderstaande stappen hebt voltooid. Als er een profiel scherm met uw MPN-ID wordt weer gegeven, bent u klaar om door te gaan. Als dat niet het geval is, moet u uw account migratie starten door de prompts in het [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx)te volgen. Als u hulp nodig hebt, gaat u naar [ondersteuning](https://partner.microsoft.com/support?issueid=100-0077).

1. Ga naar de [overzichts pagina voor commerciële Marketplace in partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Als u ' commerciële Marketplace ' ziet in het navigatie deel venster aan de linkerkant, bent u Inge schreven en gaat u verder met de volgende stap. Als dat niet het geval is, moet u zich nu [inschrijven in de commerciële Marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
2. Bevestig dat uw aanbiedingen zich in AppSource bevinden door te [zoeken naar uw aanbiedingen](https://appsource.microsoft.com/). Als uw aanbiedingen al AppSource zijn, gaat u verder met de volgende stap. Voor elke aanbieding die niet in AppSource is, maakt u een [nieuwe dynamics 365 voor klant engagement](create-new-customer-engagement-offer.md) of een [nieuwe aanbieding voor Dynamics 365 voor bewerkingen](create-new-operations-offer.md).
3. Controleer uw inschrijving in het Business Applications ISV Connect-programma:
  
   * Controleer op de pagina [overeenkomsten](https://partner.microsoft.com/dashboard/account/agreements) in partner centrum of u de **Business Applications ISV-addendum** hebt geaccepteerd om in het programma te registreren.
   * Geef uw facturerings gegevens op de pagina [account instellingen](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) op.

4. Verzend elke nieuwe en bestaande aanbieding voor certificering, zelfs als uw aanbiedingen eerder zijn gecertificeerd. Als u in aanmerking komt, kunt u een aanvraag indienen om deel te nemen aan de Premium-laag tijdens dit proces. Als uw aanbieding eerder is gecertificeerd, **moet u de hercertificering van de app op 15 oktober 2019 volt ooien.** Voor certificering of nieuwe certificering moet uw app de nieuwste versie van het Business Applications-platform ondersteunen.
5. Ga naar het [gtm-portal van de ene commerciële partner](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) en voeg de URL van uw AppSource-vermelding toe in het gedeelte Marketplace-koppelingen. Als u hulp nodig hebt bij deze stap, kunt u cosell@microsoft.comons een e-mail sturen naar.

## <a name="next-steps"></a>Volgende stappen

- [Uw commerciële Marketplace-account beheren in het partner centrum](./manage-account.md) 
