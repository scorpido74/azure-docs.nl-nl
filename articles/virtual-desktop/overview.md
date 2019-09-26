---
title: Wat is Windows virtueel bureau blad preview?  - Azure
description: Een overzicht van de Windows-preview-versie van virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 440ebfffec9378e0dad1fd04e0880c90571bb0f1
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301002"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Wat is Windows virtueel bureau blad preview? 

Windows virtueel bureau blad preview is nu beschikbaar in de open bare preview. Dit is een desktop-en app Virtualization-service die in de Cloud wordt uitgevoerd.

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

* An [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Een Windows Server-Active Directory gesynchroniseerd met Azure Active Directory. Dit kan worden ingeschakeld via:
  * Azure AD Connect
  * Azure AD Domain Services
  >[!NOTE]
  >Windows virtueel bureau blad ondersteunt alleen Azure AD Domain Services gebruikers die afkomstig zijn van Azure Active Directory. Gebruikers die afkomstig zijn van Windows Server AD, worden op dit moment niet ondersteund.
* Een Azure-abonnement dat een virtueel netwerk bevat dat of die is verbonden met de Windows Server-Active Directory
  
De virtuele machines van Azure die u maakt voor het virtuele bureau blad van Windows, moeten zijn:

* [Standaard lid](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) van een domein of een [hybride AD-join](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuele machines kunnen geen deel uitmaken van Azure AD.
* Een van de volgende [ondersteunde installatie kopieën van het besturings systeem](#supported-virtual-machine-os-images)uitvoeren.

>[!NOTE]
>Als u een Azure-abonnement nodig hebt, kunt u [zich aanmelden voor een gratis proef versie van één maand](https://azure.microsoft.com/free/). Als u de gratis proef versie van Azure gebruikt, moet u Azure AD Domain Services gebruiken om uw Windows Server-Active Directory gesynchroniseerd te laten met Azure Active Directory.

Virtueel bureau blad van Windows bestaat uit de Windows-Desk tops en-apps die u levert aan gebruikers en de beheer oplossing, die als een service op Azure wordt gehost door micro soft. Tijdens de open bare preview-versie kunnen Desk tops en apps worden geïmplementeerd op virtuele machines (Vm's) in elke Azure-regio. de beheer oplossing en de gegevens voor deze Vm's bevinden zich in de Verenigde Staten (VS Oost 2-regio). Dit kan ertoe leiden dat gegevens worden overgedragen naar de Verenigde Staten terwijl u de service test tijdens de open bare preview-versie. We beginnen met het uitschalen van de beheer oplossing en de gegevens lokalisatie naar alle Azure-regio's, beginnend bij de algemene Beschik baarheid.

Zorg ervoor dat uw netwerk voldoet aan de volgende vereisten voor optimale prestaties:

* Round-trip latentie (RTT) van het netwerk van de client naar de Azure-regio waar de hostgroepen zijn geïmplementeerd, moeten kleiner zijn dan 150 MS.
* Netwerk verkeer kan buiten de grenzen van het land/de regio stromen wanneer Vm's die Desk tops en apps hosten, verbinding maken met de beheer service.
* Om de netwerk prestaties te optimaliseren, wordt aangeraden de Vm's van de co in dezelfde Azure-regio als de beheer service te vinden.

## <a name="supported-remote-desktop-clients"></a>Ondersteunde Extern bureaublad-clients

De volgende Extern bureaublad-clients ondersteunen Windows virtueel bureau blad:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-images"></a>Ondersteunde installatie kopieën van besturings systemen voor virtuele machines

Het virtuele bureau blad van Windows ondersteunt de volgende installatie kopieën van besturings systemen:

* Windows 10 Enter prise-meerdere sessies
* Windows Server 2016

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, moet u een Tenant maken. Voor meer informatie over het maken van een Tenant gaat u verder met de zelf studie voor het maken van een Tenant.

> [!div class="nextstepaction"]
> [Een Tenant maken in Windows virtueel bureau blad preview](tenant-setup-azure-active-directory.md)
