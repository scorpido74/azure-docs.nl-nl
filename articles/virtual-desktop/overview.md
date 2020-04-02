---
title: Wat is Windows Virtual Desktop? - Azure
description: Een overzicht van Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 01767e88714bfb4e134957298505edd218d462d3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546928"
---
# <a name="what-is-windows-virtual-desktop"></a>Wat is Windows Virtual Desktop? 

Windows Virtual Desktop is een virtualisatieservice voor desktops en apps die in de cloud wordt uitgevoerd.

Dit is wat u doen wanneer u Windows Virtual Desktop op Azure uitvoert:

* Een Windows 10-implementatie met meerdere sessies instellen die een volledige Windows 10 met schaalbaarheid biedt
* Virtualiseer Office 365 ProPlus en optimaliseer het om te worden uitgevoerd in virtuele scenario's voor meerdere gebruikers
* Windows 7 virtuele desktops voorzien van gratis uitgebreide beveiligingsupdates
* Uw bestaande Extern bureaublad-services (RDS) en Windows Server-bureaubladen en -apps naar elke computer brengen
* Zowel desktops als apps virtualiseren
* Windows 10-, Windows Server- en Windows 7-bureaubladen en -apps beheren met een uniforme beheerervaring

## <a name="introductory-video"></a>Inleidende video

Meer informatie over Windows Virtual Desktop, waarom het uniek is en wat er nieuw is in deze video:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Zie [onze afspeellijst](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)voor meer video's over Windows Virtual Desktop.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met Windows Virtual Desktop u een schaalbare en flexibele omgeving instellen:

* Maak een volledige desktopvirtualisatieomgeving in uw Azure-abonnement zonder dat u extra gatewayservers hoeft uit te voeren.
* Publiceer zoveel hostpools als u nodig hebt om uw diverse workloads aan te passen.
* Breng uw eigen afbeelding mee voor productieworkloads of test vanuit de Azure Gallery.
* Verlaag de kosten met gepoolde bronnen voor meerdere sessies. Met de nieuwe Windows 10 Enterprise multi-session mogelijkheid exclusief voor Windows Virtual Desktop en Remote Desktop Session Host (RDSH) rol op Windows Server, u het aantal virtuele machines en besturingssysteem (OS) overhead sterk verminderen, terwijl u nog steeds dezelfde bronnen aan uw gebruikers.
* Zorg voor individueel eigendom via persoonlijke (permanente) desktops.

U virtuele bureaubladen implementeren en beheren:

* Gebruik de PowerShell- en REST-interfaces van Windows Virtual Desktop om de hostgroepen te configureren, appgroepen te maken, gebruikers toe te wijzen en bronnen te publiceren.
* Publiceer volledige bureaublad- of afzonderlijke externe apps uit één hostgroep, maak afzonderlijke app-groepen voor verschillende groepen gebruikers of wijs gebruikers zelfs toe aan meerdere app-groepen om het aantal afbeeldingen te verminderen.
* Terwijl u uw omgeving beheert, gebruikt u ingebouwde gedelegeerde toegang om rollen toe te wijzen en diagnoses te verzamelen om verschillende configuratie- of gebruikersfouten te begrijpen.
* Gebruik de nieuwe Diagnostics-service om fouten op te lossen.
* Beheer alleen de afbeelding en virtuele machines, niet de infrastructuur. U hoeft de rollen extern bureaublad niet persoonlijk te beheren, zoals u doet met Extern bureaublad-services, alleen de virtuele machines in uw Azure-abonnement.

U gebruikers ook toewijzen en verbinden met uw virtuele bureaubladen:

* Eenmaal toegewezen, kunnen gebruikers elke Windows Virtual Desktop-client starten om gebruikers te verbinden met hun gepubliceerde Windows-desktops en -toepassingen. Maak verbinding vanaf elk apparaat via een native toepassing op uw apparaat of de Windows Virtual Desktop HTML5-webclient.
* Zorg ervoor dat gebruikers veilig worden hersteld via omgekeerde verbindingen met de service, zodat u nooit inkomende poorten open hoeft te laten.

## <a name="requirements"></a>Vereisten

Er zijn een paar dingen die u nodig hebt om Windows Virtual Desktop in te stellen en uw gebruikers met succes te verbinden met hun Windows-desktops en -toepassingen.

We zijn van plan ondersteuning toe te voegen voor de volgende beste's, dus zorg ervoor dat u over de [juiste licenties](https://azure.microsoft.com/pricing/details/virtual-desktop/) voor uw gebruikers beschikt op basis van het bureaublad en de apps die u wilt implementeren:

|OS|Vereiste licentie|
|---|---|
|Windows 10 Enterprise-sessie of Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS Client Access License (CAL) met Software Assurance|

Uw infrastructuur heeft de volgende dingen nodig om Windows Virtual Desktop te ondersteunen:

* Een [Azure Active Directory](/azure/active-directory/)
* Een Active Directory van Windows Server die wordt gesynchroniseerd met Azure Active Directory. U dit configureren met een van de volgende opties:
  * Azure AD Connect (voor hybride organisaties)
  * Azure AD Domain Services (voor hybride of cloudorganisaties)
* Een Azure-abonnement dat een virtueel netwerk bevat dat de Active Directory van Windows Server bevat of is verbonden met de Active Directory van Windows Server
  
De virtuele Azure-machines die u maakt voor Windows Virtual Desktop, moeten:

* [Standaard-domein-verbonden](../active-directory-domain-services/active-directory-ds-comparison.md) of [Hybride AD-joined](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuele machines kunnen niet worden aangesloten op Azure AD.
* Het uitvoeren van een van de volgende [ondersteunde OS-afbeeldingen](#supported-virtual-machine-os-images).

>[!NOTE]
>Als u een [Azure-abonnement](https://azure.microsoft.com/free/)nodig hebt, u zich aanmelden voor een gratis proefperiode van een maand. Als u de gratis proefversie van Azure gebruikt, moet u Azure AD Domain Services gebruiken om uw Windows Server Active Directory gesynchroniseerd te houden met Azure Active Directory.

De virtuele Azure-machines die u voor Windows Virtual Desktop maakt, moeten toegang hebben tot de volgende URL's:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Updates voor de stack van agent en SXS|AzureCloud|
|*.core.windows.net|443|Het verkeer van de agent|AzureCloud|
|*.servicebus.windows.net|443|Het verkeer van de agent|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Het verkeer van de agent|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-activering|Internet|



>[!IMPORTANT]
>Het openen van deze URL's is essentieel voor een betrouwbare Windows Virtual Desktop-implementatie. Het blokkeren van de toegang tot deze URL's wordt niet ondersteund en heeft invloed op de servicefunctionaliteit. Deze URL's komen alleen overeen met Windows Virtual Desktop-sites en -bronnen en bevatten geen URL's voor andere services zoals Azure Active Directory.

In de volgende tabel worden optionele URL's weergegeven waartoe uw virtuele Azure-machines toegang kunnen hebben:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.microsoftonline.com|443|Verificatie naar MS Online Services|Geen|
|*.events.data.microsoft.com|443|Telemetrieservice|Geen|
|www.msftconnecttest.com|443|Detecteert of het besturingssysteem is verbonden met het internet|Geen|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Geen|
|login.windows.net|443|Inloggen bij MS Online Services, Office 365|Geen|
|*.sfx.ms|443|Updates voor OneDrive-clientsoftware|Geen|
|*.digicert.com|443|Certificaatintrekkingscontrole|Geen|


>[!NOTE]
>Windows Virtual Desktop heeft momenteel geen lijst met IP-adresbereiken die u whitelisten om netwerkverkeer toe te staan. We ondersteunen op dit moment alleen het whitelisten van specifieke URL's.
>
>Zie [Office 365-URL's en IP-adresbereiken](/office365/enterprise/urls-and-ip-address-ranges)voor een lijst met Office-gerelateerde URL's, waaronder vereiste Azure Active Directory-gerelateerde URL's.
>
>U moet het jokerteken (*) gebruiken voor URL's waarbij serviceverkeer betrokken is. Als u * liever niet gebruikt voor agentgerelateerd verkeer, vindt u als u de URL's zonder wildcards:
>
>1. Registreer uw virtuele machines in de windows Virtual Desktop-hostgroep.
>2. Open **logboeken** en navigeer naar **Windows-logboeken** > **Application** > **WVD-Agent** en zoek naar gebeurtenis-id 3702.
>3. Whitelist de URL's die u vindt onder gebeurtenis-id 3702. De URL's onder gebeurtenis-id 3702 zijn regiospecifiek. U moet het whitelistingproces herhalen met de relevante URL's voor elke regio waarin u uw virtuele machines wilt implementeren.

Windows Virtual Desktop bestaat uit de Windows-bureaubladen en -apps die u aan gebruikers levert en de beheeroplossing, die wordt gehost als een service op Azure door Microsoft. Desktops en apps kunnen worden geïmplementeerd op virtuele machines (VM's) in een Azure-regio en de beheeroplossing en -gegevens voor deze VM's bevinden zich in de Verenigde Staten. Dit kan leiden tot gegevensoverdracht naar de Verenigde Staten.

Zorg ervoor dat uw netwerk voldoet aan de volgende vereisten voor optimale prestaties:

* De latentie van de retourvlucht (RTT) van het netwerk van de client naar de Azure-regio waar hostpools zijn geïmplementeerd, moet minder dan 150 ms zijn.
* Netwerkverkeer kan buiten lands-/regiogrenzen stromen wanneer VM's die desktops en apps hosten, verbinding maken met de beheerservice.
* Om te optimaliseren voor netwerkprestaties raden we aan dat de VM's van de sessiehost zijn ondergebracht in dezelfde Azure-regio als de beheerservice.

## <a name="supported-remote-desktop-clients"></a>Ondersteunde Extern bureaublad-clients

De volgende Extern bureaublad-clients ondersteunen Windows Virtual Desktop:

* [Windows-bureaublad](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Macos](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (Preview)](connect-android.md)

> [!IMPORTANT]
> Windows Virtual Desktop biedt geen ondersteuning voor de RADC-client (RemoteApp- en Desktop Connections) of de MSTSC-client (Remote Desktop Connection).

> [!IMPORTANT]
> Windows Virtual Desktop ondersteunt momenteel de Extern bureaublad-client niet vanuit de Windows Store. Ondersteuning voor deze client zal worden toegevoegd in een toekomstige release.

De Extern bureaublad-clients moeten toegang hebben tot de volgende URL's:

|Adres|Uitgaande TCP-poort|Doel|Client(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|Alle|
|*.servicebus.windows.net|443|Problemen met gegevens oplossen|Alle|
|go.microsoft.com|443|Microsoft FWLinks|Alle|
|aka.ms|443|Microsoft URL shortener|Alle|
|docs.microsoft.com|443|Documentatie|Alle|
|privacy.microsoft.com|443|Privacyverklaring|Alle|
|query.prod.cms.rt.microsoft.com|443|Clientupdates|Windows-pc|

>[!IMPORTANT]
>Het openen van deze URL's is essentieel voor een betrouwbare klantervaring. Het blokkeren van de toegang tot deze URL's wordt niet ondersteund en heeft invloed op de servicefunctionaliteit. Deze URL's komen alleen overeen met de clientsites en -bronnen en bevatten geen URL's voor andere services zoals Azure Active Directory.

## <a name="supported-virtual-machine-os-images"></a>Ondersteunde besturingssysteemafbeeldingen voor virtuele machines

Windows Virtual Desktop ondersteunt de volgende x64-besturingssysteemafbeeldingen:

* Windows 10 Enterprise-multisessie, versie 1809 of hoger
* Windows 10 Enterprise, versie 1809 of hoger
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop biedt geen ondersteuning voor x86 (32-bits), Windows 10 Enterprise N of Windows 10 Enterprise KN-besturingssysteemafbeeldingen. Windows 7 biedt ook geen ondersteuning voor VHD- of VHDX-gebaseerde profieloplossingen die worden gehost op beheerde Azure Storage vanwege een beperking van de sectorgrootte.

De beschikbare automatiserings- en implementatieopties zijn afhankelijk van welk besturingssysteem en welke versie u kiest, zoals in de volgende tabel wordt weergegeven: 

|Besturingssysteem|Azure-afbeeldingsgalerie|Handmatige VM-implementatie|Azure Resource Manager-sjabloonintegratie|Hostpools inrichten op Azure Marketplace|Windows Virtual Desktop Agent-updates|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 multisessie, versie 1903|Ja|Ja|Ja|Ja|Automatisch|
|Windows 10 multisessie, versie 1809|Ja|Ja|Nee|Nee|Automatisch|
|Windows 10 Enterprise, versie 1903|Ja|Ja|Ja|Ja|Automatisch|
|Windows 10 Enterprise, versie 1809|Ja|Ja|Nee|Nee|Automatisch|
|Windows 7 Enterprise|Ja|Ja|Nee|Nee|Handmatig|
|Windows Server 2019|Ja|Ja|Nee|Nee|Automatisch|
|Windows Server 2016|Ja|Ja|Ja|Ja|Automatisch|
|Windows Server 2012 R2|Ja|Ja|Nee|Nee|Automatisch|

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan, moet u een tenant maken. Ga verder naar de zelfstudie voor het maken van tenant voor meer informatie over het maken van een tenant.

> [!div class="nextstepaction"]
> [Een tenant maken in Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
