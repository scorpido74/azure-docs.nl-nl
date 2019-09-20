---
title: 'Account migratie van Cloud Partner-portal naar partner Center: commerciële Marketplace voor Azure'
description: Uw account migreren van CPP naar partner Center. -Commerciële Marketplace voor Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147127"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Account migratie van Cloud Partner-portal naar het partner centrum

Als u een bestaand CPP-account hebt, moeten uw account instellingen worden gemigreerd naar het partner centrum.

## <a name="account-migration-process"></a>Account migratie proces

Als u een gebruiker bent met de rol owner in CPP voor een bepaald account, wordt een gele banner weer gegeven op de Publisher-profiel pagina. U kunt een van de volgende twee gevallen horen:

- Uw account is al gemigreerd en u bent klaar om uw account instellingen in het partner centrum te beheren.
- U moet een aantal verdere acties ondernemen om uw account migratie naar het partner centrum te volt ooien.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Uw account is gemigreerd naar het partner centrum

Voor alle accounts die de migratie van het CPP naar partner Center hebben voltooid, vindt account beheer plaats in het partner centrum. Wijzigingen, zoals het toevoegen of verwijderen van gebruikers, worden teruggesynchroniseerd naar CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>U hebt uw account nog niet gemigreerd naar het partner centrum

Klik op de banner om uw account migratie proces te initiëren. U wordt verwacht de volgende items in te voeren:

1. E-mail adres van werk: <br> <br> In de meeste gevallen meldt u zich aan met het e-mail adres dat u gebruikt om u aan te melden bij CPP. In bepaalde gevallen moet een ander e-mail adres worden gebruikt:

    * Microsoft-account: Als het CPP-account een Microsoft-account is, moet u een geldig werk-e-mail adres invoeren dat is gekoppeld aan de Tenant, waarvoor de MPN-ID is geregistreerd.

    * Niet-overeenkomende tenants: Als uw zakelijke e-mail adres niet hoort bij de Tenant die is gekoppeld aan de Microsoft Partner Network-ID die is opgegeven in uw CPP-account, wordt er een fout weer gegeven. Als u deze fout wilt verplaatsen, voert u een e-mail adres in dat is gekoppeld aan de Tenant. Er wordt een fout bericht weer gegeven met de naam van de Tenant.

2. Registreren voor Microsoft Partner Network programma

    In het geval dat uw CPP-account geen Microsoft Partner Network-ID heeft of een certificaat heeft dat ongeldig is, moet u zich registreren voor het Microsoft Partner Network-programma als onderdeel van het activerings proces.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registreren voor Microsoft Partner Network programma

Bedrijven die met micro soft willen samen werken, moeten lid zijn van de Microsoft Partner Network (MPN) en een MPN-ID krijgen. Als u al lid bent van de Microsoft Partner Network en u een MPN-ID hebt, moet u deze hand matig laten staan, omdat u deze moet invoeren tijdens het account activerings proces.  

Als u nog geen lid bent van de Microsoft Partner Network, kunt u [nu deel nemen](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) om een MPN-id op te halen. Zorg ervoor dat u de MPN-ID noteert, omdat u deze moet invoeren tijdens het account activerings proces.

Zie voor meer informatie over de Microsoft Partner Network [lid worden van de Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) op de website van de partner. Zie de [ISV resource hub](https://partner.microsoft.com/isv-resource-hub)voor meer informatie over het koppelen van de Microsoft Partner Network bene fits isv's.  

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
