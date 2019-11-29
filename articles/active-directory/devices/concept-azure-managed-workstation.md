---
title: Meer informatie over beveiligde, door Azure beheerde werk stations-Azure Active Directory
description: Meer informatie over veilige, door Azure beheerde werk stations en inzicht in waarom ze belang rijk zijn.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c26197a14e78b1cf1a1e078ba0145eca207206bf
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561949"
---
# <a name="understand-secure-azure-managed-workstations"></a>Meer informatie over veilige, door Azure beheerde werk stations

Beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Als de beveiliging van het client-werk station is aangetast, kunnen veel beveiligings controles en-garanties mislukken of niet worden doorgevoerd.

In dit document wordt uitgelegd wat u nodig hebt voor het bouwen van een beveiligd werk station, vaak bekend als een privileged Access Workstation (PAW). Het artikel bevat ook gedetailleerde instructies voor het instellen van eerste beveiligings controles. In deze richt lijnen wordt beschreven hoe u met Cloud technologie de service kunt beheren. Het is afhankelijk van de beveiligings mogelijkheden die zijn geïntroduceerd in Windows 10RS5, micro soft Defender Advanced Threat Protection (ATP), Azure Active Directory en Microsoft Intune.

> [!NOTE]
> In dit artikel wordt het concept van een beveiligd werk station en het belang ervan uitgelegd. Als u al bekend bent met het concept en de implementatie wilt overs Laan, gaat u naar [een beveiligd werk station implementeren](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Waarom beveiligde toegang tot werk stations belang rijk is

De snelle acceptatie van Cloud Services en de mogelijkheid om vanaf elke locatie te werken, heeft een nieuwe licentie methode gemaakt. Door zwakke beveiligings controles te gebruiken op apparaten waar beheerders werken, kunnen aanvallers toegang krijgen tot geprivilegieerde bronnen.

Misbruik van geprivilegieerde en toeleverings ketens is een van de vijf belangrijkste methoden die kwaadwillende personen gebruiken om organisaties te schenden. Ze zijn ook de tweede meest gedetecteerde tactiek in incidenten die zijn gerapporteerd in 2018 volgens het [Verizon Threats-rapport](https://enterprise.verizon.com/resources/reports/dbir/)en het [rapport over beveiligings informatie](https://aka.ms/sir).

De meeste aanvallers volgen deze stappen:

1. Reconnaissance om een manier te vinden in, vaak specifiek voor een branche.
1. Analyse voor het verzamelen van informatie en het bepalen van de beste manier om een werk station te Infiltrate dat wordt waargenomen als lage waarde.
1. Persistentie om te zoeken naar een manier om deze [later](https://en.wikipedia.org/wiki/Network_Lateral_Movement)te verplaatsen.
1. Exfiltration van vertrouwelijke en gevoelige gegevens.

Tijdens Reconnaissance kunnen aanvallers vaak apparaten Infiltrate die weinig risico of een onderwaardeel gezicht hebben. Ze gebruiken deze kwets bare apparaten om een mogelijkheid te vinden voor laterale verplaatsing en om gebruikers en apparaten met beheerders rechten te vinden. Nadat ze toegang hebben gekregen tot bevoegde gebruikers rollen, identificeren aanvallers gegevens met hoge waarden en exfiltreren die gegevens.

![Typisch risico patroon](./media/concept-azure-managed-workstation/typical-timeline.png)

In dit document wordt een oplossing beschreven waarmee u uw computer apparaten kunt beveiligen tegen dergelijke zijdelingse aanvallen. De oplossing isoleert beheer en services van minder waardevol productiviteits apparaten, waardoor de keten wordt versneld voordat het apparaat dat toegang heeft tot gevoelige Cloud bronnen kan worden infiltrated. De oplossing maakt gebruik van systeem eigen Azure-Services die deel uitmaken van de Microsoft 365 Enterprise stack:

* InTune voor Apparaatbeheer en een veilige lijst van toepassingen en Url's
* Automatische test voor het instellen van apparaten, implementatie en vernieuwing
* Azure AD voor gebruikers beheer, voorwaardelijke toegang en multi-factor Authentication
* Windows 10 (huidige versie) voor apparaatstatusverklaring en gebruikers ervaring
* Defender ATP voor Cloud-beheerde Endpoint Protection, detectie en respons
* Azure AD PIM voor het beheren van autorisatie en just-in-time-toegang tot bronnen
* Log Analytics en Sentinel voor bewaking en waarschuwingen

## <a name="who-benefits-from-a-secure-workstation"></a>Wie profiteren van een veilig werk station?

Alle gebruikers en Opera tors profiteren van het gebruik van een beveiligd werk station. Een aanvaller die een PC of apparaat verkrijgt, kan alle accounts in de cache imiteren. Wanneer u bent aangemeld bij het apparaat, kunnen ze ook referenties en tokens gebruiken. Dit risico maakt het belang rijk om apparaten te beveiligen die worden gebruikt voor geprivilegieerde rollen, met inbegrip van beheerders rechten. Apparaten met geprivilegieerde accounts zijn doelen voor zijdelingse verplaatsingen en escalatie van bevoegdheden. Deze accounts kunnen worden gebruikt voor diverse activa, zoals:

* Beheerder van on-premises of in de cloud gebaseerde systemen
* Ontwikkel werkstation voor kritieke systemen
* Beheerder van sociale media-account met hoge bloot stelling
* Zeer gevoelig werk station, zoals een snelle betalings Terminal
* Handels geheimen voor werk stations verwerken

Om het risico te beperken, moet u verhoogde beveiligings controles implementeren voor bevoegde werk stations die gebruikmaken van deze accounts. Voor meer informatie raadpleegt u de [implementatie handleiding](../fundamentals/active-directory-deployment-checklist-p2.md)voor de Azure Active Directory-functie, [Office 365-wegwijzer](https://aka.ms/o365secroadmap)en de beveiliging van de [uitgebreide toegang](https://aka.ms/sparoadmap).

## <a name="why-use-dedicated-workstations"></a>Waarom speciale werk stations gebruiken?

Hoewel het mogelijk is om beveiliging toe te voegen aan een bestaand apparaat, is het beter om te beginnen met een beveiligde basis. Als u een hoog beveiligings niveau voor uw organisatie wilt instellen, kunt u het beste beginnen met een apparaat dat u weet en een aantal bekende beveiligings controles implementeert.

Een groeiend aantal aanvals vectoren via e-mail en surfen maakt het steeds lastig om ervoor te zorgen dat een apparaat kan worden vertrouwd. In deze hand leiding wordt ervan uitgegaan dat een toegewezen werk station is geïsoleerd van de standaard productiviteit, het surfen en e-mail. Het verwijderen van productiviteit, surfen op internet en e-mail van een apparaat kan een negatieve invloed hebben op de productiviteit. Deze beveiliging is echter meestal acceptabel voor scenario's waarbij de taak taken niet expliciet vereist zijn en het risico van een beveiligings incident is hoog.

> [!NOTE]
> Websurfen hier verwijst naar algemene toegang tot wille keurige websites die een activiteit met een hoog risico kunnen zijn. Deze Browse wijkt af van het gebruik van een webbrowser om toegang te krijgen tot een klein aantal bekende administratieve websites voor services zoals Azure, Office 365, andere cloud providers en SaaS-toepassingen.

Containment-strategieën verg Roten de beveiliging door het aantal en type besturings elementen te verhogen waarmee een aanvaller geen toegang kan krijgen tot gevoelige assets. Het model dat in dit artikel wordt beschreven, maakt gebruik van een gelaagd bevoegdheids ontwerp en beperkt beheerders bevoegdheden voor specifieke apparaten.

## <a name="supply-chain-management"></a>Supply Chain Management

Essentieel voor een beveiligd werk station is een supply chain-oplossing waarbij u een vertrouwd werk station met de naam ' root of Trust ' gebruikt. Technologie die in aanmerking moet worden genomen in de selectie van de basis van Trust-hardware moet de volgende technologieën bevatten die zijn opgenomen in moderne laptops: 

* [Trusted Platform Module (TPM) 2,0](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker-stationsversleuteling](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI Secure boot](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)
* [Stuur Programma's en firmware die via Windows Update worden gedistribueerd](https://docs.microsoft.com/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualisatie en HVCI ingeschakeld](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs)
* [Stuur Programma's en apps HVCI-Ready](https://docs.microsoft.com/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](https://docs.microsoft.com/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O-beveiliging](https://docs.microsoft.com/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Systeem beveiliging](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Moderne stand-by](https://docs.microsoft.com/windows-hardware/design/device-experiences/modern-standby)

Voor deze oplossing wordt de basis van de vertrouwens relatie geïmplementeerd met behulp van [micro soft auto pilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) -technologie met hardware die voldoet aan de moderne technische vereisten. Voor het beveiligen van een werk station kunt u met auto pilot gebruikmaken van door micro soft OEM geoptimaliseerde Windows 10-apparaten. Deze apparaten komen in een bekende goede staat van de fabrikant. In plaats van een mogelijk onveilig apparaat te, kan auto pilot een Windows-apparaat omzetten in een status die klaar is voor het bedrijf. Hiermee worden instellingen en beleid toegepast, worden apps geïnstalleerd en wordt zelfs de editie van Windows 10 gewijzigd. Auto Pilot kan bijvoorbeeld de Windows-installatie van een apparaat wijzigen van Windows 10 Pro in Windows 10 Enter prise, zodat deze geavanceerde functies kan gebruiken.

![Niveaus van beveiligde werk stations](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Apparaatfuncties en-profielen

Deze richt lijnen verwijzen naar verschillende beveiligings profielen en rollen die u kunnen helpen bij het maken van veiligere oplossingen voor gebruikers, ontwikkel aars en IT-personeel. Deze profielen sluiten bruikbaarheid en risico's voor algemene gebruikers die kunnen profiteren van een uitgebreid of veilig werk station. De instellingen die hier worden beschreven, zijn gebaseerd op de door de industrie aanvaarde standaarden. In deze richt lijnen wordt beschreven hoe u Windows 10 kunt beveiligen en de Risico's kunt verminderen die aan het apparaat of de gebruiker zijn gekoppeld. Om te profiteren van de moderne hardware-technologie en de basis van het vertrouwens apparaat, zullen we gebruikmaken van [Apparaatstatusverklaring](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), dat is ingeschakeld vanaf het **High-Security** -profiel. Deze mogelijkheid is aanwezig om ervoor te zorgen dat de aanvallers niet permanent kunnen zijn tijdens het vroegtijdig opstarten van een apparaat. Dit doet u door beleid en technologie te gebruiken om beveiligings functies en-risico's te beheren.
![beveiligde werk station](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Basis beveiliging** : een beheerd, standaard werk station biedt een goed uitgangs punt voor het gebruik van de meeste thuis en kleine bedrijven. Deze apparaten worden geregistreerd in azure AD en beheerd met intune. Met dit profiel kunnen gebruikers toepassingen uitvoeren en bladeren door alle websites. Een anti-malware-oplossing zoals [micro soft Defender](https://www.microsoft.com/windows/comprehensive-security) moet zijn ingeschakeld.

* **Verbeterde beveiliging** : deze op instap niveau beveiligde oplossing is geschikt voor thuis gebruikers, gebruikers van kleine bedrijven en algemene ontwikkel aars.

   Het verbeterde werk station is een op beleid gebaseerde manier om de beveiliging van het lage beveiligings profiel te verhogen. Het biedt een veilige manier om met klant gegevens te werken en ook met productiviteits Programma's zoals e-mail en surfen op internet. U kunt controle beleid en intune gebruiken om een uitgebreid werk station te bewaken voor gebruikers gedrag en profiel gebruik. U implementeert het profiel uitgebreid werk station met het script Windows10 (1809) en maakt gebruik van geavanceerde beveiliging tegen schadelijke software met behulp van [Advanced Threat Protection (ATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Hoge beveiliging** : de meest efficiënte manier om de kwets baarheid van een werk station te beperken, is het verwijderen van de mogelijkheid om het werk station zelf te beheren. Het verwijderen van lokale beheerders rechten is een stap die de beveiliging verbetert, maar kan de productiviteit beïnvloeden als deze onjuist is geïmplementeerd. Het High-Security-profiel is gebaseerd op het verbeterde beveiligings profiel met één belang rijke wijziging: het verwijderen van de lokale beheerder. Dit profiel is bedoeld voor gebruikers met een hoog profiel: leidinggevenden, Sala rissen en gevoelige gegevens gebruikers, goed keurders voor services en processen.

   De gebruiker met hoge beveiliging heeft een meer gecontroleerde omgeving nodig, terwijl er nog steeds activiteiten zoals e-mail en surfen op internet kunnen worden uitgevoerd in een gebruiks vriendelijke ervaring. De gebruikers verwachten dat functies, zoals cookies, favorieten en andere snelkoppelingen, worden gebruikt. Deze gebruikers vereisen echter mogelijk niet de mogelijkheid om hun apparaat te wijzigen of fouten op te sporen. Ze hoeven ook geen stuur Programma's te installeren. Het High-Security-profiel wordt geïmplementeerd met het High Security-Windows10 (1809-script).

* **Gespecialiseerd** – kwaadwillenden richten zich op ontwikkel AARS en IT-beheerders omdat ze systemen van belang kunnen aanpassen aan de aanvallers. Het gespecialiseerde werk station wordt uitgebreid op het beleid van het High Security-werk station door lokale toepassingen te beheren en websites te beperken. Het biedt ook een beperking voor productiviteits mogelijkheden met een hoog risico, zoals ActiveX, Java, browser-invoeg toepassingen en andere Windows-besturings elementen. U implementeert dit profiel met het DeviceConfiguration_NCSC-Windows10 (1803) Security Baseline Baseline-script.

* **Beveiligd** : een aanvaller die een beheerders account verkrijgt, kan aanzienlijke schade aan het bedrijf veroorzaken door gegevens diefstal, gegevens wijziging of onderbrekingen in de service. In deze beveiligde status maakt het werk station alle beveiligings controles en-beleids regels die het directe beheer van lokale toepassings beheer beperken. Een beveiligd werk station heeft geen productiviteits tools, waardoor het apparaat moeilijker te manipuleren is. Het blokkeert de meest voorkomende vector voor phishing-aanvallen: e-mail en sociale media. Het beveiligde werk station kan worden geïmplementeerd met het Security Baseline Baseline-script Secure Workstation-Windows10 (1809).

   ![Beveiligd werk station](./media/concept-azure-managed-workstation/secure-workstation.png)

   Een beveiligd werk station biedt een beheerder met een beperkt werk station met duidelijke toepassings controle en toepassings beveiliging. Het werk station gebruikt referentie beveiliging, Device Guard en exploit Guard om de host te beschermen tegen kwaad aardig gedrag. Alle lokale schijven worden ook versleuteld met BitLocker.

* **Geïsoleerd** : dit aangepaste, offline scenario vertegenwoordigt het extreem einde van het spectrum. Er zijn geen installatie scripts voor deze aanvraag. Mogelijk moet u een bedrijfskritische functie beheren waarvoor een niet-ondersteund of verouderd besturings systeem is vereist. Bijvoorbeeld een productie lijn met hoge waarde of een systeem voor levens ondersteuning. Omdat de beveiliging kritiek is en Cloud Services niet beschikbaar zijn, kunt u deze computers hand matig of met een geïsoleerde Active Directory-forest-architectuur, zoals de verbeterde beveiligings beheer omgeving (ESAE), beheren en bijwerken. In deze omstandigheden kunt u alle toegang verwijderen, behalve basis intune en ATP-status controles.

   * [Vereisten voor de intune-netwerk communicatie](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Vereiste voor ATP-netwerk communicatie](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Volgende stappen

[Implementeer een beveiligd, door Azure beheerd werk station](howto-azure-managed-workstation.md).
