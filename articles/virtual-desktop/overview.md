---
title: Wat is Windows Virtual Desktop? - Azure
description: Een overzicht van Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 289cc463732ee6b612b67f6c408d9d7260016137
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125801"
---
# <a name="what-is-windows-virtual-desktop"></a>Wat is Windows Virtual Desktop? 

Windows Virtual Desktop is een virtualisatieservice voor desktops en apps die in de cloud wordt uitgevoerd.

Dit kunt u doen met Windows Virtual Desktop in Azure:

* Een Windows 10-implementatie met meerdere sessies instellen voor een volledige en schaalbare Windows 10
* Microsoft 365-apps voor ondernemingen virtualiseren en optimaliseren voor gebruik in virtuele scenario's met meerdere gebruikers
* Gratis uitgebreide beveiligingsupdates bieden voor virtuele Windows 7-desktops
* Uw bestaande Extern bureaublad-services en Windows Server-desktops en -apps meenemen op elke computer
* Zowel desktops als apps virtualiseren
* Windows 10-, Windows Server- en Windows 7-desktops en -apps beheren vanuit een geïntegreerde beheerervaring

## <a name="introductory-video"></a>Inleidende video

In deze video krijgt u meer informatie over Windows Virtual Desktop, wat het zo uniek maakt en wat er nieuw is:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Bekijk [onze afspeellijst](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev) voor meer video's over Windows Virtual Desktop.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met Windows Virtual Desktop kunt u een schaalbare en flexibele omgeving instellen:

* Maak een volledige virtualisatieomgeving voor desktops in uw Azure-abonnement zonder extra gatewayservers uit te voeren.
* Publiceer zoveel hostgroepen als u nodig hebt voor uw verschillende werkbelastingen.
* Gebruik uw eigen installatiekopie voor productieworkloads of voer tests uit vanuit de Azure Gallery.
* Beperk kosten met gegroepeerde resources voor meerdere sessies. Met de nieuwe mogelijkheid voor meerdere sessies van Windows 10 Enterprise, exclusief voor Windows Virtual Desktop en de RDSH-rol (Remote Desktop Session Host) in Windows Server kunt u het aantal virtuele machines en de overhead voor het besturingssysteem beperken zonder dat uw gebruikers minder resources ter beschikking hebben.
* Bied individuele eigendom aan via persoonlijke (permanente) desktops.

De virtuele desktops implementeren en beheren:

* Gebruik de interfaces van Windows Virtual Desktop PowerShell en REST om de hostgroepen te configureren, app-groepen te maken, gebruikers toe te wijzen en resources te publiceren.
* Publiceer volledige desktop- of individuele externe apps vanuit één hostgroep, maak individuele app-groepen voor verschillende sets van gebruikers of wijs gebruikers toe aan meerdere app-groepen om het aantal installatiekopieën te beperken.
* Beheer uw omgeving en gebruik ingebouwde gedelegeerde toegang om rollen toe te wijzen en verzamel diagnostische gegevens om de verschillende configuratie- en gebruikersfouten te begrijpen.
* Gebruik de nieuwe Diagnoseservice om problemen op te lossen.
* Beheer enkel de installatiekopieën en virtuele machines, niet de infrastructuur. U moet de Extern bureaublad-rollen niet persoonlijk beheren zoals bij Extern bureaublad-services, maar enkel de virtuele machines in uw Azure-abonnement.

U kunt ook gebruikers toewijzen en verbinden met uw virtuele desktops:

* Zodra ze zijn toegewezen kunnen gebruikers elke Windows Virtual Desktop-client opstarten om gebruikers te verbinden met hun toegewezen Windows-desktops en toepassingen. Maak verbinding vanaf elk apparaat via een native toepassing op uw apparaat of de Windows Virtual Desktop HTML5-webclient.
* Maak veilig gebruikers aan via omgekeerde verbindingen met de service, zodat u nooit een binnenkomende poort moet laten openstaan.

## <a name="requirements"></a>Vereisten

Er zijn een aantal dingen die u nodig heeft om Windows Virtual Desktop in te stellen en uw gebruikers te verbinden met hun Windows-desktops en -toepassingen.

We zijn van plan ondersteuning voor de volgende besturingssystemen toe te voegen, zorg er dus voor dat u over de [juiste licenties](https://azure.microsoft.com/pricing/details/virtual-desktop/) beschikt voor de gebruikers op basis van de desktop en apps die u wilt implementeren:

|OS|Vereiste licentie|
|---|---|
|Windows 10 Enterprise met meerdere sessies of Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS Licentie voor clienttoegang (CAL) met Softwaregarantie|

Uw infrastructuur heeft de volgende zaken nodig om Windows Virtual Desktop te ondersteunen:

* Een [Azure Active Directory](/azure/active-directory/)
* Een Windows Server Active Directory die gesynchroniseerd is met Azure Active Directory. U kunt deze configureren met een van de volgende:
  * Azure AD Connect (voor hybride organisaties)
  * Azure AD Domain Services (voor hybride of cloudorganisaties)
* Een Azure-abonnement met een virtueel netwerk dat de Windows Server Active Directory bevat of ermee is verbonden
  
De virtuele Azure-machines die u maakt voor Windows Virtual Desktop moeten:

* [toegevoegd zijn aan een Standard-domein](../active-directory-domain-services/active-directory-ds-comparison.md) of [toegevoegd zijn aan Hybrid AD](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuele machines kunnen mogen niet toegevoegd zijn aan Azure AD.
* Een van de volgende [ondersteunde installatiekopieën van een besturingssysteem](#supported-virtual-machine-os-images) uitvoeren.

>[!NOTE]
>Als u een Azure-abonnement nodig heeft, dan kunt u [zich registreren voor een gratis proefversie van een maand](https://azure.microsoft.com/free/). Als u de gratis proefversie van Azure gebruikt, moet u Azure AD Domain Services gebruiken om uw Windows Server Active Directory gesynchroniseerd te houden met Azure Active Directory.

De virtuele Azure-machine die u maakt voor Windows Virtual Desktop moet toegang hebben tot de volgende URL's:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Updates van Agent- en SXS-stack|AzureCloud|
|*.core.windows.net|443|Agent-verkeer|AzureCloud|
|*.servicebus.windows.net|443|Agent-verkeer|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Agent-verkeer|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-activering|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Ondersteuning Azure-portal|AzureCloud|

>[!IMPORTANT]
>Windows Virtual Desktop biedt nu ondersteuning voor de FQDN-tag. Zie [Azure Firewall gebruiken om Windows Virtual Desktop-implementaties te beveiligen](../firewall/protect-windows-virtual-desktop.md) voor meer informatie.
>
>We raden u aan om FQDN-tags of servicetags te gebruiken in de plaats van URL's om serviceproblemen te vermijden. De vermelde URL's en tags komen enkel overeen met Windows Virtual Desktop-sites en -resources. Ze bevatten geen URL's voor andere services zoals Azure Active Directory.

In de volgende tabel vindt u optionele URL's waar uw virtuele Azure-machines toegang tot kunnen hebben.

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.microsoftonline.com|443|Verificatie voor Microsoft Online Services|Geen|
|*.events.data.microsoft.com|443|Telemetrieservice|Geen|
|www.msftconnecttest.com|443|Detecteert of het besturingssysteem een verbinding heeft met het internet|Geen|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Geen|
|login.windows.net|443|Aanmelden bij Microsoft Online Services, Microsoft 365|Geen|
|*.sfx.ms|443|Updates voor OneDrive-clientsoftware|Geen|
|*.digicert.com|443|Controle van certificaatintrekking|Geen|


>[!NOTE]
>Windows Virtual Desktop heeft momenteel geen lijst van toegestane IP-adresbereiken voor netwerkverkeer. Momenteel kunnen enkel specifieke URL's worden opgenomen in de whitelist.
>
>Zie [URL's en IP-adresbereiken voor Office 365](/office365/enterprise/urls-and-ip-address-ranges)voor een lijst met Office-gerelateerde URL's, inclusief vereiste Azure Active Directory-gerelateerde URL's.
>
>U moet het jokerteken (*) gebruiken voor URL's met serviceverkeer. Als u liever geen * gebruikt voor agent-gerelateerd verkeer, dan kunt u als volgt URL's zonder jokers vinden:
>
>1. Registreer uw virtuele machines bij de Windows Virtual Desktop-hostgroep.
>2. Open **Logboeken** en ga naar **Windows-logboeken** > **Toepassing** > **WVD-Agent** en zoek naar gebeurtenis-id 3702.
>3. Neem de URL's onder gebeurtenis-id 3702 op in uw whitelist. De URL's onder gebeurtenis-id 3702 zijn regiospecifiek. U moet voor elke regio waarin u uw virtuele machines wilt implementeren de relevante URL's opnemen in de whitelist.

Windows Virtual Desktop bestaat uit de Windows-desktops en -apps die u levert aan gebruikers en de beheersoplossing, die door Microsoft als een service wordt gehost in Azure. Desktops en apps kunnen worden geïmplementeerd op virtuele machines in elke Azure-regio. De beheersoplossing en gegevens voor deze VM's bevinden zich dan in de Verenigde Staten. Dit kan als gevolg hebben dat er gegevens worden overgedragen naar de Verenigde Staten.

Zorg ervoor dat uw netwerk aan de volgende vereisten voldoet voor optimale prestaties:

* De retourlatentie tussen het netwerk van de client en de Azure-regio waar de hostgroepen zijn geïmplementeerd, moeten minder dan 150 ms bedragen.
* Netwerkverkeer kan buiten de grenzen van het land/de regio stromen wanneer virtuele machines die desktops en apps hosten, verbinding maken met de beheerservice.
* Voor optimale netwerkprestaties raden we aan dat de virtuele machines van de host van de sessie zich in dezelfde Azure-regio bevinden als de beheerservice.

## <a name="supported-remote-desktop-clients"></a>Ondersteunde Extern bureaublad-clients

De volgende Extern bureaublad-clients bieden ondersteuning voor Windows Virtual Desktop:

* [Windows Desktop](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [MacOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (preview)](connect-android.md)

> [!IMPORTANT]
> Windows Virtual Desktop biedt geen ondersteuning voor de client voor RemoteApp- en bureaubladverbindingen of de client voor verbinding met extern bureaublad.

> [!IMPORTANT]
> Windows Virtual Desktop biedt momenteel geen ondersteuning voor de Extern bureaublad-client uit de Windows Store. Ondersteuning voor deze client wordt in een toekomstige versie toegevoegd.

De Extern bureaublad-clients moeten toegang hebben tot de volgende URL's:

|Adres|Uitgaande TCP-poort|Doel|Client(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|Alle|
|*.servicebus.windows.net|443|Probleemoplossingsgegevens|Alle|
|go.microsoft.com|443|Microsoft FWLinks|Alle|
|aka.ms|443|Microsoft URL-verkorter|Alle|
|docs.microsoft.com|443|Documentatie|Alle|
|privacy.microsoft.com|443|Privacyverklaring|Alle|
|query.prod.cms.rt.microsoft.com|443|Clientupdates|Windows-pc|

>[!IMPORTANT]
>Het openen van deze URL's is essentieel voor een betrouwbare clientervaring. Toegang blokkeren tot deze URL's wordt niet ondersteund en heeft gevolgen voor de functionaliteit van de service. Deze URL's komen enkel overeen met de clientsites en -resources, en bevatten geen URL's voor andere services zoals Azure Active Directory.

## <a name="supported-virtual-machine-os-images"></a>Ondersteunde installatiekopieën van besturingssystemen voor virtuele machines

Windows Virtual Desktop biedt ondersteuning voor de volgende installatiekopieën van x64-besturingssystemen:

* Windows 10 Enterprise voor meerdere sessies, versie 1809 of hoger
* Windows 10 Enterprise, versie 1809 of hoger
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop biedt geen ondersteuning voor installatiekopieën van x86- (32-bits), Windows 10 Enterprise N- of Windows 10 Enterprise-besturingssystemen. Windows 7 biedt ook geen ondersteuning voor VHD- of VHDX-gebaseerde profieloplossingen die gehost worden op beheerde Azure Storage omwille van een beperking van de sectorgrootte.

De beschikbare opties voor automatisering en implementatie zijn afhankelijk van het besturingssysteem en de versie die u kiest, zoals te zien is in de volgende tabel: 

|Besturingssysteem|Azure Image Gallery|Handmatige VM-implementatie|Integratie van Azure Resource Manager-sjabloon|Inrichting hostgroepen in Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 met meerdere sessies, versie 1903|Ja|Ja|Ja|Ja|
|Windows 10 met meerdere sessies, versie 1809|Ja|Ja|Nee|Nee|
|Windows 10 Enterprise, versie 1903|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise, versie 1809|Ja|Ja|Nee|Nee|
|Windows 7 Enterprise|Ja|Ja|Nee|Nee|
|Windows Server 2019|Ja|Ja|Nee|Nee|
|Windows Server 2016|Ja|Ja|Ja|Ja|
|Windows Server 2012 R2|Ja|Ja|Nee|Nee|

## <a name="next-steps"></a>Volgende stappen

Als u de Windows Virtual Desktop-versie van het najaar 2019 gebruikt, ga dan aan de slag met onze zelfstudie op [Een tenant maken in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Als u de Windows Virtual Desktop-versie van het voorjaar 2020 gebruikt, dan moet u in de plaats daarvan een hostgroep maken. Ga naar de volgende zelfstudie om te beginnen.

> [!div class="nextstepaction"]
> [Een hostpool maken met de Azure-portal](create-host-pools-azure-marketplace.md)
