---
title: Veelgestelde vragen voor ClearDB MySql-databases met Azure App Service
description: Antwoorden op veelgestelde vragen over het gebruik van ClearDB MySQL-databases met Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ddbf1eb960a24cfaa8d09ab45b6febca07e6d504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979912"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Veelgestelde vragen voor ClearDB MySql-databases met Azure App Service
Deze veelgestelde vragen beantwoorden veelgestelde vragen over het gebruik en de aankoop van ClearDB MySQL-databases voor Azure Web Apps.

> [!IMPORTANT]
> Vanaf 13 juni 2018 heeft ClearDB azure-gebaseerde klanten die momenteel door Microsoft worden gefactureerd, overgezet naar een direct factureringsmodel met ClearDB. De informatie in dit artikel is nu verouderd. U niet langer een ClearDB-database maken of upgraden die in Azure is gemaakt.
>
> Zie [Wijzigingen in ClearDB-serviceplannen](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)voor meer informatie en volgende stappen.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Welke opties heb ik voor MySQL op Azure?
Zie [ClearDB](https://w2.cleardb.net/) voor de laatste informatie over die service. ClearDB is een MySQL-hostingservice en beheert de MySQL-infrastructuur voor u. 

U hebt verschillende andere opties voor het hosten van MySQL in Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [MySQL-cluster dat wordt uitgevoerd op een Azure VM](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Eén exemplaar van MySQL dat wordt uitgevoerd op een Azure VM](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Heb ik een creditcard nodig voor de Web-app + MySQL-sjabloon in de Azure Marketplace?
Dit is afhankelijk van het type abonnement dat u gebruikt. Hier volgen enkele veelgebruikte abonnementstypen:

* [Betalen als je gaat:](https://azure.microsoft.com/offers/ms-azr-0003p/)Vereist een creditcard, en bij aankoop van een betaalde MySQL-database wordt uw creditcard in rekening gebracht.
* [Gratis proefversie:](https://azure.microsoft.com/pricing/free-trial/)bevat credits voor gebruik met Microsoft Azure-services, maar staat de aankoop van bronnen van derden niet toe. Als u services van derden of een betaalde MySQL-database wilt kopen, moet u een abonnement met een creditcard gebruiken. Voor Web Apps u een GRATIS ClearDB MySQL-database maken.
* [MSDN-abonnement](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) en **MSDN Dev Test Pay als u gaat**: Net als bij Gratis proefperiode vereist een MSDN-abonnement u een creditcard om een betaalde MySQL-oplossing van ClearDB te kopen.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA-klanten worden elk kwartaal gefactureerd op hun EA voor al hun Azure Marketplace-aankopen (derden) op een afzonderlijke, geconsolideerde factuur. U wordt gefactureerd buiten de monetaire toezegging voor elke marktplaats aankopen. Houd er rekening mee dat azure store op dit moment niet beschikbaar is voor klanten die zijn ingeschreven in Azerbeidzjan, Kroatië, Noorwegen en Puerto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Waarom heb ik $ 3,50 in rekening gebracht voor een web-app + MySQL van de Azure Marketplace?
De standaard database optie is Titan, dat is $ 3,50. We tonen de kosten niet tijdens het maken van gegevens en u per ongeluk een database aanschaffen die u niet van plan was. We proberen een manier te vinden om de ervaring te verbeteren, maar tot die tijd moet u al uw geselecteerde prijsniveaus voor web-app en -database controleren voordat u op **Maken** klikt en de implementatie van de resources start.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Ik gebruik MySQL op mijn eigen Azure virtuele machine. Kan ik mijn Azure-webapp verbinden met mijn database?
Ja. U uw web-app verbinden met uw database zolang uw Azure VM op afstand toegang heeft gegeven tot uw web-app. Zie [MySQL installeren op een virtuele machine](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor meer informatie.

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>In welke landen/regio's worden ClearDB Premium MySQL-clusters ondersteund?
ClearDB Premium MySQL-clusters zijn beschikbaar in alle Azure-regio's wereldwijd, met uitzondering van India, Australië, Brazilië Zuid en China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan ik een nieuw cluster maken voordat ik een database maak met ClearDB premium clusteroplossing?
Nee, het maken van lege ClearDB-clusters wordt niet ondersteund. Met de Azure-portal u databases maken in een cluster, waarmee tegelijkertijd een nieuw cluster kan worden gemaakt.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Word ik gewaarschuwd als ik probeer een ClearDB MySQL-database te verwijderen die door een van mijn toepassingen wordt gebruikt?
Nee, Azure waarschuwt u niet als u een marktplaatsaankoop verwijdert waarvan uw toepassing afhankelijk is.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>In welke regio's kan ik ClearDB-databases maken?
Azure Marketplace is niet beschikbaar voor klanten die zijn ingeschreven in Azerbeidzjan, Kroatië, Noorwegen of Puerto Rico. ClearDB is niet beschikbaar in deze regio's.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Welke prijscategorie moet ik kiezen voor een productieweb-app en -database?
Gebruik Basic of een hogere prijscategorie voor Web Apps. Voor ClearDB raden we een Saturnus of Jupiter plan aan. Bekijk de functies & beperkingen van elke prijscategorie voor zowel [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) als [ClearDB MySQL-databases](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) om de inhoud te kiezen die aan uw behoeften voldoet.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hoe kan ik mijn ClearDB-database upgraden van het ene plan naar het andere?
In de [Azure-portal](https://portal.azure.com)u een cleardb-gedeelde hostingdatabase opschalen. Lees dit [artikel](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) voor meer informatie. We ondersteunen momenteel geen upgrade voor ClearDB Premium-clusters in de Azure-portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Kan ik mijn ClearDB-database niet zien in azure-portal?
Als u een ClearDB-database in klassiek hebt gemaakt, u uw database niet zien in de [Azure-portal.](https://portal.azure.com) Er is geen work-around voor dit scenario.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Met wie kan ik contact opnemen voor ondersteuning als mijn database is uitgeschakeld?
Neem contact op met [ClearDB-ondersteuning](https://www.cleardb.com/developers/help/support) voor databasegerelateerde problemen. Wees bereid om hen te voorzien van uw Azure-abonnementsgegevens.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan ik extra gebruikers maken voor mijn ClearDB MySQL-databaseclusteroplossing?
Nee. U geen extra gebruikers maken, maar u wel extra databases maken op uw ClearDB-databasecluster.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Kunnen Basic / Pro-serie databases worden opgewaardeerd in-plaats vergelijkbaar met Planetaire plannen vandaag op ClearDB portal?
Ja, basisseriedatabases kunnen op hun plaats worden geüpgraded (Basic 60 tot en met Basic 500). Pro-serie kan worden opgewaardeerd in-place (Pro 125 tot pro 1000), met uitzondering van Pro 60. We ondersteunen momenteel geen upgrade Pro 60-database. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Wanneer ik mijn resources migreer van het ene abonnement naar het andere, wordt mijn ClearDB MySQL-database ook gemigreerd?
Wanneer u bronmigratie uitvoert tussen abonnementen, gelden er enkele [beperkingen.](azure-resource-manager/management/move-support-resources.md) Een ClearDB MySQL-database is een service van derden en wordt dus niet gemigreerd tijdens azure-abonnementsmigratie. Als u de migratie van uw MySQL-database niet beheert voordat u Azure-bronnen migreert, kunnen uw ClearDB MySQL-databases worden uitgeschakeld. Migreer eerst handmatig uw databases en voer vervolgens Azure-abonnementsmigratie uit voor uw web-app. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Ik heb de bestedingslimiet voor mijn abonnement bereikt. Ik heb de limiet verwijderd en mijn app-service is online, maar de database is niet toegankelijk. Hoe schakel ik de ClearDB-database opnieuw in?
Neem contact op met [cleardb-ondersteuning](https://www.cleardb.com/developers/help/support) om de database opnieuw in te schakelen. Geef ze uw Azure-abonnementsgegevens en databasenaam.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan ik een ClearDB-database overzetten van een creditcardabonnement naar een EA-abonnement?
Bestaande ClearDB-databases gebruiken de creditcard die is gekoppeld aan de bestaande abonnementen. Als u een EA-abonnement wilt gebruiken, moet u uw gegevens migreren naar een nieuwe database:

* Koop een nieuwe ClearDB-database met je EA-abonnement.
* Migreer uw gegevens naar uw nieuwe database.
* Werk uw toepassing bij om de nieuwe database te gebruiken.
* Verwijder uw oude ClearDB-database.

Wanneer u een nieuwe web-app maakt met MySQL (ClearDB) of een MySQL-database (ClearDB) maakt, bepaalt het abonnement dat u kiest hoe u voor de service betaalt. Met een EA-abonnement blokkeren we de inkoop van services van derden, zoals ClearDB in de Azure-portal. EA-abonnementen worden gefactureerd buiten de monetaire toezegging en worden elk kwartaal en achterstallig gefactureerd. De EA-klant zou een betalingsmethode moeten opzetten, zoals een creditcard, om te betalen voor marktplaatsdiensten van derden.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Waar kan ik de kosten voor ClearDB-bronnen zien in een EA-abonnement?
Voor Direct EA-klanten zijn azure marketplace-kosten zichtbaar op de Enterprise Portal. Houd er rekening mee dat alle marktplaatsaankopen en -consumptie buiten de monetaire toezeggingworden gefactureerd en driemaandelijks en achterstallig worden gefactureerd. EA-klanten moeten rechtstreeks betalen aan de externe dienstverleners en kunnen dit doen door een betaalmethode zoals een creditcard met hun EA-account in te schakelen.

Indirecte EA-klanten kunnen hun Azure Marketplace-abonnementen vinden op de pagina **Abonnementen beheren** van de Enterprise Portal, maar de prijzen blijven verborgen. Klanten kunnen contact opnemen met hun LSP voor informatie over de kosten voor Marketplace.

Toegang tot Azure Marketplace voor services van derden kan worden beheerd door uw EA Azure-inschrijvingsbeheerders. Ze kunnen de toegang tot aankopen van derden in de Store uitschakelen of opnieuw inschakelen in Accounts en Abonnementen beheren onder de sectie Accounts in de Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Met wie kan ik contact opnemen voor vragen over mijn factuur voor ClearDB-services in mijn EA-abonnement?
Neem contact op met [enterprise customer support](https://aka.ms/AzureEntSupport) met betrekking tot facturering onder hun EA-inschrijving. Het Ondersteuningsteam van EA Portal beantwoordt uw vraag of helpt uw probleem op te lossen.

## <a name="more-information"></a>Meer informatie
[Veelgestelde vragen over Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

