---
title: Wat is Windows Virtual Desktop? - Azure
description: Een overzicht van Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0a32ee682490c5930b8c48d069087020c4763dcb
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127768"
---
# <a name="what-is-windows-virtual-desktop"></a>Wat is Windows Virtual Desktop? 

Virtueel bureau blad van Windows is een desktop-en app Virtualization-service die in de Cloud wordt uitgevoerd.

U kunt het volgende doen wanneer u Windows virtueel bureau blad op Azure uitvoert:

* Een Windows 10-implementatie met meerdere sessies instellen die een volledige Windows 10 biedt met schaal baarheid
* Virtualiseren van Office 365 ProPlus en optimaliseer dit om uit te voeren in virtuele scenario's met meerdere gebruikers
* Virtuele Windows 7-Bureau bladen bieden met gratis uitgebreide beveiligings updates
* Uw bestaande Extern bureaublad-services (RDS) en Windows Server-Desk tops en-apps naar elke computer brengen
* Zowel Desk tops als apps virtualiseren
* Windows 10-, Windows Server-en Windows 7-Desk tops en-apps beheren met een uniforme beheer ervaring

## <a name="introductory-video"></a>Introductie video

Meer informatie over virtueel bureau blad van Windows, waarom het uniek is en wat er nieuw is in deze video:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Zie [onze afspeel lijst](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)voor meer Video's over virtuele Windows-Bureau bladen.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met Windows virtueel bureau blad kunt u een schaal bare en flexibele omgeving instellen:

* Maak een volledige bureau blad-virtualisatiehost in uw Azure-abonnement zonder extra gateway servers uit te voeren.
* Publiceer zoveel hostgroepen als u nodig hebt om uw diverse werk belastingen te kunnen verwerken.
* Neem uw eigen installatie kopie voor de werk belasting van de productie of test vanuit de Azure Gallery.
* Kosten verlagen met gegroepeerde, multi-sessie resources. Met de nieuwe Windows 10 Enter prise-functionaliteit voor meerdere sessies voor Windows Virtual Desktop en de functie sessiehost (Extern bureaublad Session Host) in Windows Server, kunt u het aantal virtuele machines en de overhead van het besturings systeem (OS) aanzienlijk verminderen terwijl het nog steeds u kunt dezelfde resources voor uw gebruikers opgeven.
* Geef individueel eigendom op via persoonlijke (permanente) Bureau bladen.

U kunt virtuele Bureau bladen implementeren en beheren:

* Gebruik de Windows Power shell-en REST-interfaces van het virtuele bureau blad om de hostgroepen te configureren, app-groepen te maken, gebruikers toe te wijzen en resources te publiceren.
* Volledig bureau blad of afzonderlijke externe apps publiceren vanuit één hostgroep, afzonderlijke app-groepen maken voor verschillende groepen gebruikers, of zelfs gebruikers toewijzen aan meerdere app-groepen om het aantal installatie kopieën te verminderen.
* Bij het beheren van uw omgeving gebruikt u ingebouwde gedelegeerde toegang om rollen toe te wijzen en diagnostische gegevens te verzamelen om inzicht te krijgen in verschillende configuratie-of gebruikers fouten.
* Gebruik de nieuwe service voor diagnostische gegevens om fouten op te lossen.
* Beheer alleen de installatie kopie en de virtuele machines, niet de infra structuur. U hoeft niet persoonlijk de Extern bureaublad-functies te beheren, zoals u dat met Extern bureaublad-services kunt doen, maar alleen de virtuele machines in uw Azure-abonnement.

U kunt ook gebruikers toewijzen en verbinden met uw virtuele Bureau bladen:

* Na de toewijzing kunnen gebruikers een virtueel-bureaubladclient van Windows starten om gebruikers te verbinden met hun gepubliceerde Windows-Bureau bladen en-toepassingen. U kunt vanaf elk apparaat verbinding maken via een systeem eigen toepassing op uw apparaat of op de Windows Virtual Desktop HTML5-webclient.
* Stel gebruikers veilig in via omgekeerde verbindingen met de service. u hoeft geen binnenkomende poorten te laten staan.

## <a name="requirements"></a>Vereisten

Er zijn enkele dingen die u nodig hebt om virtuele Windows-Bureau bladen in te stellen en uw gebruikers te verbinden met hun Windows-Bureau bladen en-toepassingen.

We willen ondersteuning voor de volgende besturings systemen toevoegen, dus zorg ervoor dat u over de [juiste licenties](https://azure.microsoft.com/pricing/details/virtual-desktop/) voor uw gebruikers beschikt op basis van het bureau blad en de apps die u wilt implementeren:

|OS|Vereiste licentie|
|---|---|
|Windows 10 Enter prise-meerdere sessies of Windows 10 Enter prise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS Client Access License (CAL) met Software Assurance|

Uw infra structuur heeft de volgende zaken nodig om virtueel bureau blad van Windows te ondersteunen:

* Een [Azure Active Directory](/azure/active-directory/)
* Een Windows Server-Active Directory gesynchroniseerd met Azure Active Directory. U kunt dit configureren met een van de volgende opties:
  * Azure AD Connect (voor hybride organisaties)
  * Azure AD Domain Services (voor hybride of Cloud organisaties)
* Een Azure-abonnement dat een virtueel netwerk bevat dat of die is verbonden met de Windows Server-Active Directory
  
De virtuele machines van Azure die u maakt voor het virtuele bureau blad van Windows, moeten zijn:

* [Standaard lid](../active-directory-domain-services/active-directory-ds-comparison.md) van een domein of een [hybride AD-join](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuele machines kunnen geen deel uitmaken van Azure AD.
* Een van de volgende [ondersteunde installatie kopieën van het besturings systeem](#supported-virtual-machine-os-images)uitvoeren.

>[!NOTE]
>Als u een Azure-abonnement nodig hebt, kunt u [zich aanmelden voor een gratis proef versie van één maand](https://azure.microsoft.com/free/). Als u de gratis proef versie van Azure gebruikt, moet u Azure AD Domain Services gebruiken om uw Windows Server-Active Directory gesynchroniseerd te laten met Azure Active Directory.

De virtuele machines van Azure die u voor virtuele Windows-Bureau bladen maakt, moeten toegang hebben tot de volgende Url's:

|Adres|Uitgaande poort|Doel|
|---|---|---|
|*. wvd.microsoft.com|TCP-poort 443|Service verkeer|
|*.blob.core.windows.net|TCP-poort 443|Agent, gelijktijdige stack-updates en agent verkeer|
|*.core.windows.net|TCP-poort 443|Agent verkeer|
|*.servicebus.windows.net|TCP-poort 443|Agent verkeer|
|prod.warmpath.msftcloudes.com|TCP-poort 443|Agent verkeer|
|catalogartifact.azureedge.net|TCP-poort 443|Azure Marketplace|
|kms.core.windows.net|TCP-poort 1688|Windows 10-activering|

>[!IMPORTANT]
>Het openen van deze Url's is essentieel voor een betrouw bare implementatie van een virtueel bureau blad in Windows. Het blok keren van de toegang tot deze Url's wordt niet ondersteund en heeft invloed op de service functionaliteit. Deze Url's komen alleen overeen met virtuele bureau blad-sites en-bronnen van Windows en bevatten geen Url's voor andere services, zoals Azure Active Directory.

>[!NOTE]
>Het virtuele bureau blad van Windows heeft momenteel geen lijst met IP-adresbereiken die u kunt white list om netwerk verkeer toe te staan. We ondersteunen op dit moment alleen white list-specifieke Url's.
>
>U moet het Joker teken (*) gebruiken voor Url's waarbij service verkeer betrokken is. Als u liever geen * gebruikt voor verkeer dat betrekking heeft op agents, kunt u de Url's vinden zonder joker tekens:
>
>1. Registreer uw virtuele machines in de Windows-hostgroep voor virtueel bureau blad.
>2. Open **Logboeken** en navigeer naar **Windows logs** > **toepassing** > **WVD-Agent** en zoek naar gebeurtenis-id 3702.
>3. White List de Url's die u vindt onder gebeurtenis-ID 3702. De Url's onder gebeurtenis-ID 3702 zijn specifiek voor een regio. U moet het white list-proces herhalen met de relevante Url's voor elke regio waarin u uw virtuele machines wilt implementeren.

Virtueel bureau blad van Windows bestaat uit de Windows-Desk tops en-apps die u levert aan gebruikers en de beheer oplossing, die als een service op Azure wordt gehost door micro soft. Desk tops en apps kunnen worden geïmplementeerd op virtuele machines (Vm's) in elke Azure-regio en de beheer oplossing en gegevens voor deze Vm's bevinden zich in de Verenigde Staten. Dit kan ertoe leiden dat gegevens worden overgedragen naar de Verenigde Staten.

Zorg ervoor dat uw netwerk voldoet aan de volgende vereisten voor optimale prestaties:

* Round-trip latentie (RTT) van het netwerk van de client naar de Azure-regio waar de hostgroepen zijn geïmplementeerd, moeten kleiner zijn dan 150 MS.
* Netwerk verkeer kan buiten de grenzen van het land/de regio stromen wanneer Vm's die Desk tops en apps hosten, verbinding maken met de beheer service.
* Om de netwerk prestaties te optimaliseren, wordt aangeraden de Vm's van de co in dezelfde Azure-regio als de beheer service te vinden.

## <a name="supported-remote-desktop-clients"></a>Ondersteunde Extern bureaublad-clients

De volgende Extern bureaublad-clients ondersteunen Windows virtueel bureau blad:

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (preview-versie)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Ondersteunde installatie kopieën van besturings systemen voor virtuele machines

Het virtuele bureau blad van Windows ondersteunt de volgende installatie kopieën van x64-besturings systemen:

* Windows 10 Enter prise-meerdere sessies, versie 1809 of hoger
* Windows 10 Enter prise, versie 1809 of hoger
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop biedt geen ondersteuning voor x86-installatie kopieën (32-bits), Windows 10 Enter prise N of Windows 10 Enter prise. Windows 7 ondersteunt ook geen op VHD of VHDX gebaseerde profiel oplossingen die worden gehost op beheerde Azure Storage als gevolg van een limiet voor de sector grootte.

De beschik bare opties voor automatisering en implementatie zijn afhankelijk van het besturings systeem en de versie die u kiest, zoals wordt weer gegeven in de volgende tabel: 

|Besturingssysteem|Galerie met installatie kopieën van Azure|Hand matige VM-implementatie|Integratie van Azure Resource Manager-sjabloon|Hostgroep inrichten op Azure Marketplace|Windows Virtual Desktop agent-updates|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10-meerdere sessies, versie 1903|Ja|Ja|Ja|Ja|Automatisch|
|Windows 10-meerdere sessies, versie 1809|Ja|Ja|Nee|Nee|Automatisch|
|Windows 10 Enter prise, versie 1903|Ja|Ja|Ja|Ja|Automatisch|
|Windows 10 Enter prise, versie 1809|Ja|Ja|Nee|Nee|Automatisch|
|Windows 7 Enterprise|Ja|Ja|Nee|Nee|Handmatig|
|Windows Server 2019|Ja|Ja|Nee|Nee|Automatisch|
|Windows Server 2016|Ja|Ja|Ja|Ja|Automatisch|
|Windows Server 2012 R2|Ja|Ja|Nee|Nee|Automatisch|

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, moet u een Tenant maken. Voor meer informatie over het maken van een Tenant gaat u verder met de zelf studie voor het maken van een Tenant.

> [!div class="nextstepaction"]
> [Een tenant maken in Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
