---
title: Klaslokalen in Azure Lab Services — Veelgestelde vragen | Microsoft Documenten
description: In dit artikel vindt u antwoorden op veelgestelde vragen (FAQ) over klaslabs in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443496"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Classroom labs in Azure Lab Services — Veelgestelde vragen (FAQ)
Krijg antwoorden op enkele van de meest voorkomende vragen over klaslabs in Azure Lab Services. 

## <a name="quotas"></a>Quota

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Is het quotum per gebruiker of per week of per volledige duur van het lab? 
Het quotum dat u instelt voor een lab is voor elke student voor de gehele duur van het lab. En de [geplande looptijd van VM's](how-to-create-schedules.md) telt niet mee voor het quotum dat aan een gebruiker wordt toegewezen. Het quotum is voor de tijd buiten de planningsuren die een student aan VM's besteedt.  Zie [Quota voor gebruikers instellen voor](how-to-configure-student-usage.md#set-quotas-for-users)meer informatie over quota.

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Als professor een student-VM inschakelt, heeft dat dan invloed op het studentenquotum? 
Nee. Dat doet het niet. Wanneer de professor de student-VM inschakelt, heeft dit geen invloed op het quotum dat aan de student wordt toegewezen. 

## <a name="schedules"></a>Planningen

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Starten alle VM's in het lab automatisch wanneer een schema is ingesteld? 
Nee. Niet alle VM's. Alleen de VM's die volgens een planning aan gebruikers zijn toegewezen. De VM's die niet aan een gebruiker zijn toegewezen, worden niet automatisch gestart. Het is door het ontwerp. 

## <a name="lab-accounts"></a>Labaccounts

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Waarom kan ik geen lab maken vanwege de onbeschikbaarheid van het adresbereik? 
Classroom labs kunnen lab VM's maken binnen een IP-adresbereik dat u opgeeft bij het maken van uw labaccount in de Azure-portal. Wanneer een adresbereik is opgegeven, wordt elk lab dat is gemaakt nadat het 512 IP-adressen voor laboratorium-VM's heeft toegewezen. Het adresbereik voor het labaccount moet groot genoeg zijn om alle labs die u wilt maken onder het labaccount te kunnen huisvesten. 

Als u bijvoorbeeld een blok van /19 - 10.0.0.0/19 hebt, biedt dit adresbereik plaats aan 8192 IP-adressen en 16 labs (8192/512 = 16 labs). In dit geval faalt labcreatie op de 17e laboratoriumcreatie.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welke poortbereiken moet ik openen op de firewall-instelling van mijn organisatie om verbinding te maken met virtuele labmachines via RDP/SSH?

De havens zijn: 49152–65535. De laboratoria van het klaslokaal zitten achter een lastbalancer. Elk lab heeft één openbaar IP-adres en elke virtuele machine in het lab heeft een unieke poort. 

U ook het privé-IP-adres van elke virtuele machine bekijken op het tabblad **Virtuele machinegroep** van de startpagina voor het lab in de Azure-portal. Als u een lab opnieuw publiceert, verandert het openbare IP-adres van het lab niet, maar kunnen het privé-IP- en poortnummer van elke virtuele machine in het lab veranderen. Meer informatie vindt u in het artikel: [Firewall-instellingen voor Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welk openbaar IP-adresbereik moet ik openen op de firewall-instellingen van mijn organisatie om verbinding te maken met virtuele labmachines via RDP/SSH?
Zie [Azure IP Ranges and Service Tags — Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die het openbare IP-adresbereik biedt voor datacenters in Azure. U de IP-adressen openen voor de regio's waar uw labaccounts zich bevinden.

## <a name="virtual-machine-images"></a>Afbeeldingen van virtuele machines

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Waarom kan ik als maker van een lab geen extra beeldopties inschakelen in de vervolgkeuzelijst voor virtuele machineafbeeldingen bij het maken van een nieuw lab?

Wanneer een beheerder u als labmaker toevoegt aan een labaccount, krijgt u de machtigingen om labs te maken. U hebt echter niet de machtigingen om instellingen in het labaccount te bewerken, inclusief de lijst met ingeschakelde virtuele machineafbeeldingen. Als u extra afbeeldingen wilt inschakelen, neemt u contact op met de beheerder van het lab om dit voor u te doen of vraagt u de beheerder om u toe te voegen als bijdrager aan het labaccount. De rol Inzender geeft u de machtigingen om de lijst met virtuele afbeeldingen in het labaccount te bewerken.

## <a name="users"></a>Gebruikers

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Hoeveel gebruikers kunnen zich in een klaslokaallab bevinden?
U maximaal 400 gebruikers toevoegen aan een klaslokaallab. 

## <a name="blog-post"></a>Blogbericht
Abonneer u op het [Azure Lab Services-blog](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Meldingen bijwerken
Abonneer u op [Lab Services-updates](https://azure.microsoft.com/updates/?product=lab-services) om op de hoogte te blijven van nieuwe functies in Lab Services.

## <a name="general"></a>Algemeen
### <a name="what-if-my-question-isnt-answered-here"></a>Wat als mijn vraag hier niet wordt beantwoord?
Als uw vraag hier niet wordt vermeld, laat het ons weten, zodat we u kunnen helpen een antwoord te vinden.

- Plaats een vraag aan het einde van deze FAQ. 
- Als u een breder publiek wilt bereiken, plaatst u een vraag op het [Azure Lab Services - Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Voor functieaanvragen dient u uw aanvragen en ideeën in bij [Azure Lab Services — User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

