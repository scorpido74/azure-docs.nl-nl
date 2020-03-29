---
title: Veelgestelde vragen over roaming voor ondernemingsstatus - Azure Active Directory
description: Veelgestelde vragen over ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad76835b0c72b691e1ef8810f2c58dedb8f597d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672380"
---
# <a name="settings-and-data-roaming-faq"></a>Veelgestelde vragen over instellingen en gegevensroaming

In dit artikel worden enkele vragen beantwoord die IT-beheerders mogelijk hebben over instellingen en het synchroniseren van app-gegevens.

## <a name="what-data-roams"></a>Welke gegevens zwerven?

**Windows-instellingen:** de pc-instellingen die zijn ingebouwd in het Windows-besturingssysteem. Over het algemeen zijn dit instellingen die uw pc personaliseren en de volgende brede categorieën:

* *Thema*, dat functies bevat, zoals bureaubladthema en taakbalkinstellingen.
* *Internet Explorer-instellingen,* inclusief onlangs geopende tabbladen en favorieten.
* *Browserinstellingen van Microsoft Edge,* zoals favorieten en leeslijst.
* *Wachtwoorden,* waaronder internetwachtwoorden, Wi-Fi-profielen en andere.
* *Taalvoorkeuren*, waaronder instellingen voor toetsenbordindelingen, systeemtaal, datum en tijd en meer.
* *Toegankelijkheidsfuncties,* zoals thema's met een hoog contrast, Narrator en Vergrootglas.
* *Andere Windows-instellingen,* zoals muisinstellingen.

> [!NOTE]
> Dit artikel is van toepassing op de Microsoft Edge Legacy HTML-gebaseerde browser die in juli 2015 met Windows 10 is gelanceerd. Het artikel is niet van toepassing op de nieuwe Microsoft Edge Chromium-gebaseerde browser uitgebracht op 15 januari 2020. Zie het artikel Microsoft Edge Sync voor meer informatie over het gedrag Synchroniseren voor de nieuwe Microsoft [Edge.](/deployedge/microsoft-edge-enterprise-sync)

**Toepassingsgegevens:** Universele Windows-apps kunnen instellingengegevens naar een zwervende map schrijven en alle gegevens die naar deze map worden geschreven, worden automatisch gesynchroniseerd. Het is aan de individuele app-ontwikkelaar om een app te ontwerpen om te profiteren van deze mogelijkheid. Zie de [appdata storage API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) en de [Windows 8 appdata roaming developer blog](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)voor meer informatie over het ontwikkelen van een Universele Windows-app die roaming gebruikt.

## <a name="what-account-is-used-for-settings-sync"></a>Welk account wordt gebruikt voor synchronisatie van instellingen?

In Windows 8.1 synchroniseren instellingen altijd gebruikte Microsoft-accounts voor consumenten. Enterprise-gebruikers hadden de mogelijkheid om een Microsoft-account te koppelen aan hun Active Directory-domeinaccount om toegang te krijgen tot synchronisatie van instellingen. In Windows 10 wordt deze verbonden Microsoft-accountfunctionaliteit vervangen door een primair/secundair accountframework.

Het primaire account wordt gedefinieerd als het account dat wordt gebruikt om u aan te melden bij Windows. Dit kan een Microsoft-account, een Azure Active Directory-account (Azure AD) zijn, een on-premises Active Directory-account of een lokaal account. Naast het primaire account kunnen Windows 10-gebruikers een of meer secundaire cloudaccounts toevoegen aan hun apparaat. Een secundair account is over het algemeen een Microsoft-account, een Azure AD-account of een ander account zoals Gmail of Facebook. Deze secundaire accounts bieden toegang tot aanvullende services, zoals eenmalige aanmelding en de Windows Store, maar ze zijn niet in staat om instellingen te synchroniseren.

In Windows 10 kan alleen het primaire account voor het apparaat worden gebruikt voor synchronisatie van instellingen (zie [Hoe kan ik upgraden van Microsoft-accountinstellingen synchroniseren in Windows 8 naar Azure AD-instellingen synchroniseren in Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Gegevens worden nooit gemengd tussen de verschillende gebruikersaccounts op het apparaat. Er zijn twee regels voor synchronisatie van instellingen:

* Windows-instellingen zwerven altijd met het primaire account.
* App-gegevens worden getagd met het account dat wordt gebruikt om de app te verkrijgen. Alleen apps die zijn getagd met het primaire account worden gesynchroniseerd. Het taggen van apps wordt bepaald wanneer een app aan de zijkant wordt geladen via de Windows Store of het beheer van mobiele apparaten (MDM).

Als de eigenaar van een app niet kan worden geïdentificeerd, wordt deze met het primaire account weergegeven. Als een apparaat wordt geüpgraded van Windows 8 of Windows 8.1 naar Windows 10, worden alle apps getagd zoals het Microsoft-account heeft verkregen. Dit komt omdat de meeste gebruikers apps aanschaffen via de Windows Store en er geen Windows Store-ondersteuning was voor Azure AD-accounts voorafgaand aan Windows 10. Als een app via een offlinelicentie is geïnstalleerd, wordt de app getagd met het primaire account op het apparaat.

> [!NOTE]
> Windows 10-apparaten die eigendom zijn van een onderneming en die zijn verbonden met Azure AD, kunnen hun Microsoft-accounts niet langer verbinden met een domeinaccount. De mogelijkheid om een Microsoft-account te koppelen aan een domeinaccount en alle gegevens van de gebruiker te laten synchroniseren met het Microsoft-account (dat wil zeggen het Microsoft-account dat roaming maakt via het verbonden Microsoft-account en Active Directory-functionaliteit) wordt verwijderd uit Windows 10 apparaten die zijn verbonden met een verbonden Active Directory- of Azure AD-omgeving.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hoe kan ik upgraden van Microsoft-accountinstellingen synchroniseren in Windows 8 naar Azure AD-instellingen synchroniseren in Windows 10?

Als u bent verbonden met het Active Directory-domein met Windows 8.1 met een verbonden Microsoft-account, worden de instellingen gesynchroniseerd via uw Microsoft-account. Na een upgrade naar Windows 10 blijft u gebruikersinstellingen synchroniseren via het Microsoft-account, zolang u een met een domein verbonden gebruiker bent en het Active Directory-domein geen verbinding maakt met Azure AD.

Als het on-premises Active Directory-domein wel verbinding maakt met Azure AD, probeert uw apparaat instellingen te synchroniseren met het verbonden Azure AD-account. Als de Azure AD-beheerder Enterprise State Roaming niet inschakelt, worden de synchronisatie-instellingen van uw verbonden Azure AD-account gestopt. Als u een Windows 10-gebruiker bent en u zich aanmeldt met een Azure AD-identiteit, begint u met het synchroniseren van windows-instellingen zodra uw beheerder instellingen inschakelt die worden gesynchroniseerd via Azure AD.

Als u persoonlijke gegevens op uw bedrijfsapparaat hebt opgeslagen, moet u zich ervan bewust zijn dat windows-besturingssysteem- en toepassingsgegevens beginnen te synchroniseren met Azure AD. Dit heeft de volgende implicaties:

* Uw persoonlijke Microsoft-accountinstellingen worden afgetrokken van de instellingen voor uw AD-accounts op uw werk of school. Dit komt omdat de Microsoft-account en Azure AD-instellingen nu afzonderlijke accounts gebruiken.
* Persoonlijke gegevens zoals Wi-Fi-wachtwoorden, webreferenties en Internet Explorer-favorieten die eerder via een verbonden Microsoft-account zijn gesynchroniseerd, worden gesynchroniseerd via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hoe werken de interoperabiliteit van Microsoft-account en Azure AD Enterprise State Roaming?

In de releases van Windows 10 in november 2015 of later wordt Enterprise State Roaming slechts voor één account tegelijk ondersteund. Als u zich aanmeldt bij Windows met een Azure AD-account voor werk of school, worden alle gegevens gesynchroniseerd via Azure AD. Als u zich aanmeldt bij Windows met een persoonlijk Microsoft-account, worden alle gegevens gesynchroniseerd via het Microsoft-account. Universele appdata zwerft alleen met het primaire aanmeldingsaccount op het apparaat en het zwerft alleen als de licentie van de app eigendom is van het primaire account. Universele appdata voor de apps die eigendom zijn van secundaire accounts worden niet gesynchroniseerd.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Synchroniseren instellingen voor Azure AD-accounts van meerdere tenants?

Wanneer meerdere Azure AD-accounts van verschillende Azure AD-tenants zich op hetzelfde apparaat bevinden, moet u het register van het apparaat bijwerken om te communiceren met de Azure Rights Management-service voor elke Azure AD-tenant.  

1. Zoek de GUID voor elke Azure AD-tenant. Open de Azure-portal en selecteer een Azure AD-tenant. De GUID voor de tenant bevindt zichhttps://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)op de pagina Eigenschappen voor de geselecteerde tenant ( , gelabeld **Directory-id**. 
2. Nadat u de GUID hebt, moet u de registersleutel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**.
   Maak op de GUID-sleutel van **tenant-ID** een nieuwe Multi-String-waarde (REG-MULTI-SZ) met de naam **AllowedRMSServerUrls**. Geef voor de gegevens de URL's van het licentiedistributiepunt op van de andere Azure-tenants die het apparaat toegang heeft.
3. U de URL's van het licentiedistributiepunt vinden door de **cmdlet Get-AadrmConfiguration** uit de AADRM-module uit te voeren. Als de waarden voor de **LicensingIntranetDistributionPointUrl** en **LicensingExtranetDistributionPointUrl** verschillend zijn, geeft u beide waarden op. Als de waarden hetzelfde zijn, geeft u de waarde slechts één keer op.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Wat zijn de roaming-instellingen voor bestaande Windows-bureaubladtoepassingen?

Roaming werkt alleen voor Universele Windows-apps. Er zijn twee opties beschikbaar voor het inschakelen van roaming op een bestaande Windows-bureaubladtoepassing:

* Met [de Desktop Bridge](https://aka.ms/desktopbridge) u uw bestaande Windows-bureaubladapps naar het Universele Windows-platform brengen. Vanaf hier zijn minimale codewijzigingen vereist om te profiteren van azure AD-app-gegevensroaming. De Desktop Bridge biedt uw apps een app-identiteit, die nodig is om app-dataroaming voor bestaande bureaublad-apps mogelijk te maken.
* [Met User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) u een aangepaste instellingensjabloon maken voor bestaande Windows-bureaublad-apps en roaming voor Win32-apps inschakelen. Voor deze optie hoeft de app-ontwikkelaar de code van de app niet te wijzigen. UE-V is beperkt tot on-premises Active Directory-roaming voor klanten die het Microsoft Desktop Optimization Pack hebben gekocht.

Beheerders kunnen UE-V configureren om door te zwerven op Windows-bureaublad-app-gegevens door roaming van Windows-besturingssysteeminstellingen en Universele app-gegevens te wijzigen via [ue-v-groepsbeleid](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), waaronder:

* Groepsbeleid voor Windows-instellingen roamen
* Groepsbeleid van Windows Apps niet synchroniseren
* Internet Explorer-roaming in de sectie toepassingen

In de toekomst kan Microsoft onderzoeken hoe UE-V diep geïntegreerd kan worden in Windows en UE-V kan uitbreiden naar roam-instellingen via de Azure AD-cloud.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan ik gesynchroniseerde instellingen en on-premises gesynchroniseerde instellingen en gegevens opslaan?

Enterprise State Roaming slaat alle gesynchroniseerde gegevens op in de Microsoft-cloud. UE-V biedt een on-premises roamingoplossing.

## <a name="who-owns-the-data-thats-being-roamed"></a>Van wie zijn de gegevens die worden doorgezworven?

De ondernemingen zijn eigenaar van de gegevens die via Enterprise State Roaming zijn geroamd. Gegevens worden opgeslagen in een Azure-datacenter. Alle gebruikersgegevens worden zowel onderweg als in rust in de cloud versleuteld met behulp van de Azure Rights Management-service van Azure Information Protection. Dit is een verbetering ten opzichte van microsoft-accountgebaseerde instellingen synchronisatie, die alleen bepaalde gevoelige gegevens, zoals gebruikersreferenties versleutelt voordat het apparaat verlaat.

Microsoft zet zich in voor het beveiligen van klantgegevens. De instellingengegevens van een zakelijke gebruiker worden automatisch versleuteld door de Azure Rights Management-service voordat deze een Windows 10-apparaat verlaat, zodat geen enkele andere gebruiker deze gegevens kan lezen. Als uw organisatie een betaald abonnement heeft voor de Azure Rights Management-service, u andere beveiligingsfuncties gebruiken, zoals documenten bijhouden en intrekken, e-mails die gevoelige informatie bevatten automatisch beveiligen en uw eigen sleutels beheren (de uw eigen key"-oplossing, ook wel BYOK genoemd). Zie [Wat is Azure Rights Management](/azure/information-protection/what-is-information-protection)voor meer informatie over deze functies en hoe deze beveiligingsservice werkt.

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan ik synchronisatie beheren voor een specifieke app of instelling?

In Windows 10 is er geen MDM- of groepsbeleidingesteld om roaming voor een afzonderlijke toepassing uit te schakelen. Tenantbeheerders kunnen appdata-synchronisatie uitschakelen voor alle apps op een beheerd apparaat, maar er is geen fijner besturingselement op een niveau per app of binnen-app.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hoe kan ik roaming inschakelen of uitschakelen?

Ga in de app **Instellingen** naar **Accounts** > **Uw instellingen synchroniseren.** Op deze pagina u zien welk account wordt gebruikt om door instellingen te zwerven en u afzonderlijke groepen instellingen in- of uitschakelen die moeten worden uitgevoerd.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Wat is de aanbeveling van Microsoft om roaming in Windows 10 in te schakelen?

Microsoft heeft een aantal verschillende instellingen roaming oplossingen beschikbaar, waaronder Roaming Gebruikersprofielen, UE-V, en Enterprise State Roaming.  Microsoft is toegewijd aan het doen van een investering in Enterprise State Roaming in toekomstige versies van Windows. Als uw organisatie niet klaar of comfortabel is met het verplaatsen van gegevens naar de cloud, raden we u aan UE-V te gebruiken als uw primaire roamingtechnologie. Als uw organisatie roamingondersteuning nodig heeft voor bestaande Windows-bureaubladtoepassingen, maar graag naar de cloud wil overstappen, raden we u aan zowel Enterprise State Roaming als UE-V te gebruiken. Hoewel UE-V en Enterprise State Roaming zeer vergelijkbare technologieën zijn, sluiten ze elkaar niet uit. Ze vullen elkaar aan om ervoor te zorgen dat uw organisatie de roamingservices biedt die uw gebruikers nodig hebben.  

Bij het gebruik van zowel Enterprise State Roaming als UE-V gelden de volgende regels:

* Enterprise State Roaming is de primaire roamingagent op het apparaat. UE-V wordt gebruikt om de "Win32 kloof" aan te vullen.
* UE-V-roaming voor Windows-instellingen en moderne UWP-app-gegevens moeten worden uitgeschakeld bij het gebruik van het ue-v-groepsbeleid. Deze vallen al onder Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hoe ondersteunt Enterprise State Roaming virtual desktop infrastructure (VDI)?

Enterprise State Roaming wordt ondersteund op SKU's van Windows 10-client, maar niet op sKU's van de server. Als een client-VM wordt gehost op een hypervisormachine en u zich op afstand aanmeldt bij de virtuele machine, zwerven uw gegevens rond. Als meerdere gebruikers hetzelfde besturingssysteem delen en gebruikers zich op afstand aanmelden bij een server voor een volledige desktopervaring, werkt roaming mogelijk niet. Dit laatste sessiescenario wordt niet officieel ondersteund.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Wat gebeurt er wanneer mijn organisatie een abonnement koopt dat Azure Rights Management bevat na gebruik van roaming?

Als uw organisatie roaming al gebruikt in Windows 10 met het gratis abonnement voor tijdelijk gebruik van Azure Rights Management, heeft de aankoop van een [betaald abonnement](https://azure.microsoft.com/pricing/details/information-protection/) dat de beveiligingsservice Azure Rights Management bevat geen invloed op de functionaliteit van de roamingfunctie en zijn er geen configuratiewijzigingen vereist door uw IT-beheerder.

## <a name="known-issues"></a>Bekende problemen

Zie de documentatie in de sectie [probleemoplossing](enterprise-state-roaming-troubleshooting.md) voor een lijst met bekende problemen. 

## <a name="next-steps"></a>Volgende stappen 

Zie overzicht [van roaming voor bedrijfsstatus](enterprise-state-roaming-overview.md)
