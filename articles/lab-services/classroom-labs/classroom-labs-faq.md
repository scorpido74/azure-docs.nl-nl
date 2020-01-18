---
title: Klassikale Labs in Azure Lab Services — Veelgestelde vragen | Microsoft Docs
description: Vind antwoorden op veelgestelde vragen over klassikale Labs in Azure Lab Services.
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 3bc58e9545f38508a9e08e9ae1aa9cf8713cc520
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264743"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Klassikale Labs in Azure Lab Services: veelgestelde vragen (FAQ)
Krijg antwoorden op enkele van de meest voorkomende vragen over klassikale Labs in Azure Lab Services. 

## <a name="quotas"></a>Quota

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Is het quotum per gebruiker of per week of per volledige duur van het lab? 
Het quotum dat u voor een Lab hebt ingesteld, is voor elke student voor een volledige duur van het lab. En de [geplande uitvoerings tijd van vm's](how-to-create-schedules.md) telt niet op het quotum dat aan een gebruiker is toegewezen. Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt.  Zie [quota's voor gebruikers instellen](how-to-configure-student-usage.md#set-quotas-for-users)voor meer informatie over quota's.

## <a name="schedules"></a>Schema's

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Worden alle Vm's in het lab automatisch gestart wanneer een planning wordt ingesteld? 
Nee. Niet alle virtuele machines. Alleen de Vm's die aan gebruikers zijn toegewezen volgens een schema. De Vm's die niet aan een gebruiker zijn toegewezen, worden niet automatisch gestart. Het ontwerp is standaard. 

## <a name="lab-accounts"></a>Labaccounts

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Waarom kan ik geen Lab maken vanwege niet-beschik baarheid van het adres bereik? 
Klassikale Labs kan virtuele lab-Vm's maken binnen een IP-adres bereik dat u opgeeft bij het maken van uw Lab-account in de Azure Portal. Wanneer een adres bereik wordt verstrekt, wordt elk lab dat is gemaakt na de 512 IP-adressen voor Lab-Vm's toegewezen. Het adres bereik voor het lab-account moet groot genoeg zijn voor alle Labs die u wilt maken onder het lab-account. 

Als u bijvoorbeeld een blok van/19-10.0.0.0/19 hebt, is dit adres bereik geschikt voor 8192 IP-adressen en 16 Labs (8192/512 = 16 Labs). In dit geval mislukt het maken van het Lab bij het maken van een 17-Lab.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welke poortbereiken moet ik openen in de firewall instelling van mijn organisatie om verbinding te maken met virtuele lab-machines via RDP/SSH?

De poorten zijn: 49152 – 65535. Klassikale Labs bevindt zich achter een load balancer, zodat alle virtuele machines in een Lab één IP-adres hebben en elke virtuele machine in het lab een unieke poort heeft. De poort nummers en het open bare IP-adres kunnen worden gewijzigd telkens wanneer het lab opnieuw wordt gepubliceerd.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welk openbaar IP-adres bereik moet ik openen in de firewall instellingen van mijn organisatie om verbinding te maken met virtuele lab-machines via RDP/SSH?
Zie [Azure IP-adresbereiken en service tags (open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519)), die het open bare IP-adres bereik voor data centers in Azure biedt. U kunt de IP-adressen openen voor de regio's waar uw Lab-accounts zich bevinden.

## <a name="users"></a>Gebruikers

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Hoeveel gebruikers kunnen er in een leslokaal Lab?
U kunt Maxi maal 400 gebruikers toevoegen aan een leslokaal Lab. 

## <a name="blog-post"></a>Blog bericht
Abonneer u op de [Azure Lab Services blog](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Meldingen bijwerken
Abonneer u op de [updates van Lab Services](https://azure.microsoft.com/updates/?product=lab-services) om op de hoogte te blijven van nieuwe functies in de test services.

## <a name="general"></a>Algemeen
### <a name="what-if-my-question-isnt-answered-here"></a>Wat gebeurt er als mijn vraag hier niet wordt beantwoord?
Als uw vraag hier niet wordt vermeld, laat het ons dan weten, zodat we u kunnen helpen om een antwoord te vinden.

- Plaats een vraag aan het einde van deze veelgestelde vragen. 
- Als u een breder publiek wilt bereiken, plaatst u een vraag op het [Azure Lab Services-stack overflow-forum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Voor functie aanvragen stuurt u uw aanvragen en ideeën naar [Azure Lab Services: gebruikers stem](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

