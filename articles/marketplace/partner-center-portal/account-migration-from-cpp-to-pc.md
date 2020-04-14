---
title: Accountmigratie van Cloud Partner Portal naar Partner Center - commerciële marktplaats voor Azure
description: Uw account migreren van CPP naar Partnercenter. - commerciële marktplaats voor Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e17a76d5a017400287644ad2da46caa5b6636654
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262295"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Accountmigratie van Cloud Partner-portal naar het Partnercentrum

Als u een bestaand CPP-account (Cloud Partner Portal) hebt, moeten uw accountinstellingen worden gemigreerd naar partnercentrum.

## <a name="account-migration-process"></a>Accountmigratieproces

Als u een gebruiker bent met de rol Eigenaar in CPP voor een bepaald account, wordt een gele banner weergegeven op de pagina Publisher-profiel. U deel uitmaken van een van de volgende twee gevallen:

- Je account is al gemigreerd en je bent klaar om je accountinstellingen te beheren in het Partnercentrum.
- Je account is niet gemigreerd naar Partner Center en je moet verdere actie ondernemen.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Uw account is gemigreerd naar Partnercenter

Voor alle accounts die de migratie van het CPP naar het Partnercentrum hebben voltooid, vindt accountbeheer plaats in het Partnercentrum. Wijzigingen zoals het toevoegen/verwijderen van gebruikers worden gesynchroniseerd met CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Je account hebt je nog niet gemigreerd naar Partner Center

Klik op de banner om het migratieproces van uw account te starten. Er wordt van je verwacht dat je de volgende objecten invoert:

1. Werke-mailadres: <br> <br> Log in de meeste gevallen in met het e-mailadres dat u gebruikt om u aan te melden bij CPP. In bepaalde gevallen moet een ander e-mailadres worden gebruikt:

    * Microsoft-account: Als het CPP-account een Microsoft-account is, voert u een geldige werke-mail in die is gekoppeld aan de tenant waarvoor de MPN-id (Microsoft Partner Network) is geregistreerd. Zie [Aanmelden voor Microsoft Partner Network Program voor](#sign-up-for-microsoft-partner-network-program)meer informatie .

    * Tenant mismatch: Als uw werk-e-mailadres niet behoort tot de tenant die is gekoppeld aan de Microsoft Partner Network ID die aanwezig is op uw CPP-account, ziet u een fout. Als u deze fout wilt doorvoeren, voert u een e-mailadres in dat is gekoppeld aan de tenant. Een foutbericht geeft de naam van de tenant op.

2. Aanmelden voor Microsoft Partner Network-programma

    Als uw CPP-account geen Microsoft Partner Network-id heeft of een niet-geldige cpp-account heeft, moet u zich aanmelden voor het Microsoft Partner Network-programma als onderdeel van het activeringsproces.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Aanmelden voor Microsoft Partner Network-programma

Bedrijven die met Microsoft willen samenwerken, moeten lid worden van het Microsoft Partner Network (MPN) en een MPN-id krijgen. Als u al lid bent van het Microsoft Partner Network en een MPN-id hebt, houdt u de informatie bij de hand zoals u deze nodig hebt tijdens het activeringsproces van het account.  

Als u geen lid bent van het Microsoft Partner Network, u [hier deelnemen](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) om een MPN-id te krijgen. Noteer uw MPN-id als u deze moet invoeren tijdens het activeringsproces van het account.

Zie Lid worden van het [Microsoft Partner Netwerk](https://partner.microsoft.com/en-US/membership) op de partnerwebsite voor meer informatie over het Microsoft Partner Netwerk. Zie de [ISV Resource Hub](https://partner.microsoft.com/isv-resource-hub)voor meer informatie over ISV-voordelen in het Microsoft Partner Network.  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Move Dynamics 365 en PowerApps biedt aan Partner Center

Om het account- en aanbodbeheer voor Dynamics 365 Customer Engagement, PowerApps en Dynamics 365 Operations te stroomlijnen, zijn aanbiedingen verplaatst naar [Partner Center.](https://partner.microsoft.com/) De verhuizing zorgt ervoor dat dezelfde inhoud beschikbaar is voor zowel openbare als verkoperscatalogi.

Volg de onderstaande instructies voor specifieke informatie over wat er voor **15 oktober 2019** moet worden gedaan voor uw Dynamics 365 Customer Engagement, PowerApps en Dynamics 365 Operations-aanbiedingen.

> [!NOTE]
> Dit geldt niet voor dynamics 365 Business Central aanbiedingen.  

1. Als uw MPN-lidmaatschapsaccount oorspronkelijk is gemaakt in het Partner Membership Center (PMC), meldt u zich aan bij [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) om te bevestigen dat uw account is gemigreerd. Als u een profielscherm met uw MPN-id ziet, u doorgaan. Als dit niet het zo is, moet u de migratie van uw account starten door de aanwijzingen in het [PartnerMembership Center te](https://partners.microsoft.com/partnerprogram/Welcome.aspx)volgen. Als je hulp nodig hebt, bezoek [ondersteuning](https://partner.microsoft.com/support?issueid=100-0077).
2. Ga naar de [overzichtspagina Commerciële Marktplaats in partnercentrum.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Als u 'Commerciële marktplaats' in het linkernavigatiedeelvenster ziet, bent u ingeschreven en moet u doorgaan naar de volgende stap. Zo niet, schrijf je nu [in op de commerciële marktplaats.](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)
3. Controleer of uw aanbiedingen in AppSource staan door [te zoeken naar uw aanbiedingen.](https://appsource.microsoft.com/) Als uw aanbiedingen al in AppSource staan, gaat u verder met de volgende stap. Voor aanbiedingen die niet in AppSource staan, maakt u een [nieuwe Dynamics 365 Customer Engagement-aanbieding](create-new-customer-engagement-offer.md) of een [nieuwe Dynamics 365 Operations-aanbieding.](create-new-operations-offer.md)
4. Zorg er op de [pagina Overeenkomsten](https://partner.microsoft.com/dashboard/account/agreements)van partnercentrum op dat u het **ISV-addendum voor zakelijke toepassingen**hebt beoordeeld en geaccepteerd.
5. Controleer in de [accountinstellingen](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)van het partnercentrum of uw factureringsgegevens volledig zijn.
6. Dien elk nieuw en bestaand aanbod in voor certificering en publicatie, zelfs als uw aanbiedingen eerder zijn gecertificeerd.
    * Vul de informatieschermen in, inclusief het verstrekken van uw app voor certificering, evenals marketinginformatie. Selecteer **Verzenden** (rechterbovenhoek van het scherm) vtegen **15 oktober 2019**. Deze stappen moeten worden uitgevoerd om te voorkomen dat de beschikbaarheid van de aanbieding wordt beïnvloed.
    * Als u hiervoor in aanmerking komt, u tijdens dit proces vragen om deel te nemen aan de premielaag.
    * Certificering of hercertificering vereist dat uw app de nieuwste versie van ons Business Applications Platform ondersteunt.
    * Zodra uw app is goedgekeurd, ontvangt u een e-mail om terug te keren naar de aanbieding en selecteert u 'live gaan' om de aanbieding live te laten gaan op Microsoft AppSource.

## <a name="additional-resources"></a>Aanvullende resources

Doe mee met de wekelijkse [Dynamics ISV-community-oproep](https://aka.ms/DynamicsISV-CommunityCall) voor ondersteuning en updates.

Als u hulp nodig hebt bij het publiceren, certificeren of beheren van uw marktplaatsaanbiedingen, [dient u een ondersteuningsticket in.](https://aka.ms/MarketplacePublisherSupport)

## <a name="next-steps"></a>Volgende stappen

- [Uw commerciële marktplaatsaccount beheren in partnercentrum](./manage-account.md)
