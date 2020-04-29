---
title: Klassikale Labs in Azure Lab Services — Veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over klassikale Labs in Azure Lab Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443496"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Klassikale Labs in Azure Lab Services: veelgestelde vragen (FAQ)
Krijg antwoorden op enkele van de meest voorkomende vragen over klassikale Labs in Azure Lab Services. 

## <a name="quotas"></a>Quota

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Is het quotum per gebruiker of per week of per volledige duur van het lab? 
Het quotum dat u voor een Lab hebt ingesteld, is voor elke student voor een volledige duur van het lab. En de [geplande uitvoerings tijd van vm's](how-to-create-schedules.md) telt niet op het quotum dat aan een gebruiker is toegewezen. Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt.  Zie [quota's voor gebruikers instellen](how-to-configure-student-usage.md#set-quotas-for-users)voor meer informatie over quota's.

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Als docent een student-VM inschakelt, heeft dit invloed op het student quotum? 
Nee. Dit is niet het geval. Als professor de student-VM inschakelt, heeft dit geen invloed op het quotum dat aan de student is toegewezen. 

## <a name="schedules"></a>Planningen

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Worden alle Vm's in het lab automatisch gestart wanneer een planning wordt ingesteld? 
Nee. Niet alle virtuele machines. Alleen de Vm's die aan gebruikers zijn toegewezen volgens een schema. De Vm's die niet aan een gebruiker zijn toegewezen, worden niet automatisch gestart. Het ontwerp is standaard. 

## <a name="lab-accounts"></a>Labaccounts

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Waarom kan ik geen Lab maken vanwege niet-beschik baarheid van het adres bereik? 
Klassikale Labs kan virtuele lab-Vm's maken binnen een IP-adres bereik dat u opgeeft bij het maken van uw Lab-account in de Azure Portal. Wanneer een adres bereik wordt verstrekt, wordt elk lab dat is gemaakt na de 512 IP-adressen voor Lab-Vm's toegewezen. Het adres bereik voor het lab-account moet groot genoeg zijn voor alle Labs die u wilt maken onder het lab-account. 

Als u bijvoorbeeld een blok van/19-10.0.0.0/19 hebt, is dit adres bereik geschikt voor 8192 IP-adressen en 16 Labs (8192/512 = 16 Labs). In dit geval mislukt het maken van het Lab bij het maken van een 17-Lab.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welke poortbereiken moet ik openen in de firewall instelling van mijn organisatie om verbinding te maken met virtuele lab-machines via RDP/SSH?

De poorten zijn: 49152 – 65535. Klassikale Labs bevindt zich achter een load balancer. Elk lab heeft één openbaar IP-adres en elke virtuele machine in het lab heeft een unieke poort. 

U kunt ook het privé-IP-adres van elke virtuele machine weer geven op het tabblad **virtuele-machine groep** van de start pagina voor Lab in de Azure Portal. Als u een Lab opnieuw publiceert, wordt het open bare IP-adres van het lab niet gewijzigd, maar worden het privé-IP en het poort nummer van elke virtuele machine in het lab gewijzigd. Meer informatie vindt u in het artikel: [firewall-instellingen voor Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welk openbaar IP-adres bereik moet ik openen in de firewall instellingen van mijn organisatie om verbinding te maken met virtuele lab-machines via RDP/SSH?
Zie [Azure IP-adresbereiken en service tags (open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519)), die het open bare IP-adres bereik voor data centers in Azure biedt. U kunt de IP-adressen openen voor de regio's waar uw Lab-accounts zich bevinden.

## <a name="virtual-machine-images"></a>Installatie kopieën van virtuele machines

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Waarom kan ik bij het maken van een nieuw Lab geen extra afbeeldings opties in de vervolg keuzelijst voor installatie kopieën van virtuele machines inschakelen?

Wanneer een beheerder u als een Lab-maker aan een Lab-account toevoegt, krijgt u de benodigde machtigingen voor het maken van Labs. Maar u beschikt niet over de machtigingen voor het bewerken van instellingen in het lab-account, met inbegrip van de lijst met ingeschakelde installatie kopieën van virtuele machines. Als u extra installatie kopieën wilt inschakelen, neemt u contact op met de beheerder van uw Lab-account om dit voor u te doen, of vraag de beheerder u als Inzender rol toe te voegen aan het lab-account. De rol Inzender geeft u de machtigingen om de lijst met installatie kopieën van virtuele machines te bewerken in het lab-account.

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

