---
title: Veilige, door Azure beheerde werkstations begrijpen - Azure Active Directory
description: Meer informatie over beveiligde, door Azure beheerde werkstations en begrijp waarom ze belangrijk zijn.
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
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672662"
---
# <a name="understand-secure-azure-managed-workstations"></a>Veilige, door Azure beheerde werkstations begrijpen

Beveiligde, geïsoleerde werkstations zijn van cruciaal belang voor de beveiliging van gevoelige rollen zoals beheerders, ontwikkelaars en kritieke servicebeheerders. Als de beveiliging van clientwerkstations in het gedrang komt, kunnen veel beveiligingscontroles en -garanties mislukken of niet effectief zijn.

In dit document wordt uitgelegd wat u nodig hebt voor het bouwen van een beveiligd werkstation, ook wel bekend als een geprivilegieerd toegangswerkstation (PAW). Het artikel bevat ook gedetailleerde instructies voor het instellen van eerste beveiligingscontroles. Deze richtlijnen beschrijven hoe cloudgebaseerde technologie de service kan beheren. Het is gebaseerd op beveiligingsmogelijkheden die zijn geïntroduceerd in Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory en Microsoft Intune.

> [!NOTE]
> In dit artikel wordt het concept van een veilig werkstation en het belang ervan uitgelegd. Als u al bekend bent met het concept en wilt u naar implementatie gaan, gaat u naar [Een beveiligd werkstation implementeren.](howto-azure-managed-workstation.md)

## <a name="why-secure-workstation-access-is-important"></a>Waarom veilige toegang tot workstations belangrijk is

De snelle adoptie van cloudservices en de mogelijkheid om overal te werken heeft een nieuwe exploitatiemethode gecreëerd. Door gebruik te maken van zwakke beveiligingsbesturingselementen op apparaten waar beheerders werken, kunnen aanvallers toegang krijgen tot bevoorrechte bronnen.

Privileged misbruik en supply chain-aanvallen behoren tot de top vijf methoden die aanvallers gebruiken om organisaties te schenden. Ze zijn ook de tweede meest gedetecteerde tactiek in incidenten gemeld in 2018 volgens de [Verizon Threat rapport](https://enterprise.verizon.com/resources/reports/dbir/), en de Security Intelligence [Report](https://aka.ms/sir).

De meeste aanvallers volgen de volgende stappen:

1. Verkenning om een weg naar binnen te vinden, vaak specifiek voor een industrie.
1. Analyse om informatie te verzamelen en de beste manier te identificeren om een werkstation te infiltreren dat als lage waarde wordt ervaren.
1. Volharding om te zoeken naar een middel om [lateraal](https://en.wikipedia.org/wiki/Network_Lateral_Movement)te bewegen .
1. Exfiltratie van vertrouwelijke en gevoelige gegevens.

Tijdens de verkenning infiltreren aanvallers vaak in apparaten die een laag risico of ondergewaardeerd lijken. Ze gebruiken deze kwetsbare apparaten om een mogelijkheid te vinden voor zijdelingse beweging en om administratieve gebruikers en apparaten te vinden. Nadat ze toegang hebben gekregen tot bevoorrechte gebruikersrollen, identificeren aanvallers waardevolle gegevens en exfiltreren ze die gegevens met succes.

![Typisch compromispatroon](./media/concept-azure-managed-workstation/typical-timeline.png)

In dit document wordt een oplossing beschreven die uw computerapparaten kan beschermen tegen dergelijke laterale aanvallen. De oplossing isoleert beheer en services van minder waardevolle productiviteitsapparaten, waardoor de keten wordt gebroken voordat het apparaat dat toegang heeft tot gevoelige cloudbronnen kan worden geïnfiltreerd. De oplossing maakt gebruik van native Azure-services die deel uitmaken van de Microsoft 365 Enterprise-stack:

* Intune voor apparaatbeheer en een veilige lijst met toepassingen en URL's
* Automatische piloot voor het instellen, implementeren en vernieuwen van apparaten
* Azure AD voor gebruikersbeheer, voorwaardelijke toegang en meervoudige verificatie
* Windows 10 (huidige versie) voor de attest en gebruikerservaring van de status van het apparaat
* Defender ATP voor cloudbeheer endpointbescherming, -detectie en -respons
* Azure AD PIM voor het beheren van autorisatie en just-in-time (JIT) bevoorrechte toegang tot resources
* Log Analytics en Sentinel voor monitoring en waarschuwingen

## <a name="who-benefits-from-a-secure-workstation"></a>Wie profiteert van een veilig werkstation?

Alle gebruikers en operators profiteren bij het gebruik van een veilig werkstation. Een aanvaller die een pc of apparaat compromitteert, kan zich voordoen als alle accounts in de cache. Wanneer ze zijn aangemeld bij het apparaat, kunnen ze ook referenties en tokens gebruiken. Dit risico maakt het belangrijk om apparaten te beveiligen die worden gebruikt voor bevoorrechte rollen, waaronder beheerdersrechten. Apparaten met bevoorrechte accounts zijn het doelwit van laterale bewegings- en privilegeescalatieaanvallen. Deze accounts kunnen worden gebruikt voor verschillende activa, zoals:

* Beheerder van on-premises of cloudsystemen
* Werkstation voor ontwikkelaars voor kritieke systemen
* Beheerder van een social media-account met hoge blootstelling
* Zeer gevoelig werkstation, zoals een SWIFT-betaalterminal
* Werkstation dat bedrijfsgeheimen behandelt

Om risico's te verminderen, moet u verhoogde beveiligingscontroles implementeren voor bevoorrechte werkstations die gebruik maken van deze accounts. Zie de handleiding voor de [implementatie van Azure Active Directory-functies](../fundamentals/active-directory-deployment-checklist-p2.md), office [365-roadmap](https://aka.ms/o365secroadmap)en [roadmap voor bevoorrechte toegang beveiligen](https://aka.ms/sparoadmap)voor meer informatie.

## <a name="why-use-dedicated-workstations"></a>Waarom speciale werkstations gebruiken?

Hoewel het mogelijk is om beveiliging toe te voegen aan een bestaand apparaat, is het beter om te beginnen met een veilige basis. Om uw organisatie in de beste positie te brengen om een hoog beveiligingsniveau te handhaven, begint u met een apparaat waarvan u weet dat het veilig is en implementeert u een reeks bekende beveiligingscontroles.

Een groeiend aantal aanvalsvectoren via e-mail en surfen op het web maakt het steeds moeilijker om er zeker van te zijn dat een apparaat kan worden vertrouwd. Deze handleiding gaat ervan uit dat een speciaal werkstation is geïsoleerd van standaard productiviteit, browsen en e-mail. Het verwijderen van productiviteit, surfen op het web en e-mail van een apparaat kan een negatieve invloed hebben op de productiviteit. Deze beveiliging is echter meestal acceptabel voor scenario's waarin de taaktaken dit niet expliciet vereisen en het risico op een beveiligingsincident hoog is.

> [!NOTE]
> Surfen op het web hier verwijst naar algemene toegang tot willekeurige websites die een hoog risico activiteit kan zijn. Dergelijke browsen is duidelijk anders dan het gebruik van een webbrowser om toegang te krijgen tot een klein aantal bekende administratieve websites voor services zoals Azure, Office 365, andere cloudproviders en SaaS-toepassingen.

Containmentstrategieën verscherpen de beveiliging door het aantal en het type besturingselementen te verhogen dat een aanvaller ervan weerhoudt om toegang te krijgen tot gevoelige assets. Het model dat in dit artikel wordt beschreven, gebruikt een gelaagd bevoegdhedenontwerp en beperkt beheerdersbevoegdheden tot specifieke apparaten.

## <a name="supply-chain-management"></a>Supply chain management

Essentieel voor een beveiligd werkstation is een supply chain-oplossing waarbij u een vertrouwd werkstation gebruikt dat de 'root of trust' wordt genoemd. Technologie die moet worden overwogen bij de selectie van de basis van vertrouwenshardware, moet de volgende technologieën bevatten die in moderne laptops zijn opgenomen: 

* [Trusted Platform Module (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker-stationsversleuteling](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI Secure Boot](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Stuurprogramma's en firmware gedistribueerd via Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualisatie en HVCI ingeschakeld](/windows-hardware/design/device-experiences/oem-vbs)
* [Drivers en apps HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O-bescherming](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Systeembeveiliging](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Moderne stand-by](/windows-hardware/design/device-experiences/modern-standby)

Voor deze oplossing wordt root of trust geïmplementeerd met Behulp van [Microsoft Autopilot-technologie](/windows/deployment/windows-autopilot/windows-autopilot) met hardware die voldoet aan de moderne technische vereisten. Om een werkstation te beveiligen, u met Autopilot gebruikmaken van microsoft OEM-geoptimaliseerde Windows 10-apparaten. Deze apparaten komen in een bekende goede staat van de fabrikant. In plaats van een potentieel onveilig apparaat te reimaging, kan Autopilot een Windows-apparaat transformeren in een "business-ready" status. Het past instellingen en beleid toe, installeert apps en wijzigt zelfs de editie van Windows 10. Autopilot kan bijvoorbeeld de Windows-installatie van een apparaat wijzigen van Windows 10 Pro naar Windows 10 Enterprise, zodat het geavanceerde functies kan gebruiken.

![Veilige werkstationniveaus](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Apparaatrollen en -profielen

Deze richtlijnen verwijzen naar verschillende beveiligingsprofielen en -rollen waarmee u veiligere oplossingen maken voor gebruikers, ontwikkelaars en IT-personeel. Deze profielen brengen bruikbaarheid en risico's in evenwicht voor gewone gebruikers die kunnen profiteren van een verbeterd of veilig werkstation. De instellingen configuraties die hier zijn gebaseerd op de industrie geaccepteerde normen. Deze richtlijnen laten zien hoe u Windows 10 verharden en de risico's van het compromis van apparaten of gebruikers verminderen. Om te profiteren van de moderne hardwaretechnologie en root van het vertrouwensapparaat, gebruiken we [Device Health Attestation](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), dat is ingeschakeld vanaf het **high security-profiel.** Deze mogelijkheid is aanwezig om ervoor te zorgen dat de aanvallers niet persistent kunnen zijn tijdens het vroege opstarten van een apparaat. Dit gebeurt door beleid en technologie te gebruiken om beveiligingsfuncties en -risico's te beheren.
![Veilige werkstationniveaus](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Basisbeveiliging** - Een beheerd, standaard werkstation biedt een goed uitgangspunt voor de meeste thuis- en kleine bedrijven. Deze apparaten worden geregistreerd in Azure AD en worden beheerd met Intune. Dit profiel stelt gebruikers in staat om alle toepassingen uit te voeren en op elke website te bladeren. Een anti-malware oplossing zoals [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) moet worden ingeschakeld.

* **Verbeterde beveiliging** - Deze instapoplossing, beveiligde oplossing is goed voor thuisgebruikers, kleine zakelijke gebruikers en algemene ontwikkelaars.

   Het verbeterde werkstation is een op beleid gebaseerde manier om de beveiliging van het lage beveiligingsprofiel te verhogen. Het biedt een veilig middel om te werken met klantgegevens, terwijl ook het gebruik van productiviteit tools zoals e-mail en surfen op het web. U controlebeleid en Intune gebruiken om een verbeterd werkstation te controleren op gebruikersgedrag en profielgebruik. U implementeert het verbeterde werkstationprofiel met het Windows10-script (1809) en maakt gebruik van geavanceerde malwarebescherming met behulp van [Advanced Threat Protection (ATP).](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

* **Hoge beveiliging** – Het meest effectieve middel om het aanvalsoppervlak van een werkstation te verminderen, is het verwijderen van de mogelijkheid om het werkstation zelf te beheren. Het verwijderen van lokale administratieve rechten is een stap die de beveiliging verbetert, maar het kan de productiviteit beïnvloeden als het verkeerd wordt geïmplementeerd. Het hoge beveiligingsprofiel bouwt voort op het verbeterde beveiligingsprofiel met één belangrijke wijziging: het verwijderen van de lokale beheerder. Dit profiel is ontworpen voor high profile gebruikers: leidinggevenden, salarisadministratie- en gevoelige gegevensgebruikers, fiatteurs voor diensten en processen.

   De gebruiker met hoge beveiliging vraagt om een meer gecontroleerde omgeving, terwijl hij nog steeds activiteiten zoals e-mail en surfen op het web kan uitvoeren in een gebruiksvriendelijke ervaring. De gebruikers verwachten dat functies zoals cookies, favorieten en andere snelkoppelingen werken. Deze gebruikers hoeven hun apparaat echter niet te wijzigen of te debuggen. Ze hoeven ook geen stuurprogramma's te installeren. Het hoge beveiligingsprofiel wordt geïmplementeerd met behulp van het script High Security - Windows10 (1809).

* **Specialized** - Aanvallers richten ontwikkelaars en IT-beheerders omdat ze systemen kunnen wijzigen die van belang zijn voor de aanvallers. Het gespecialiseerde werkstation breidt uit naar het beleid van het hoogbeveiligde werkstation door lokale toepassingen te beheren en websites te beperken. Het beperkt ook productiviteitsmogelijkheden met een hoog risico, zoals ActiveX, Java, browserplug-ins en andere Windows-besturingselementen. U implementeert dit profiel met het DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline script.

* **Beveiligd** : een aanvaller die een administratief account compromitteert, kan aanzienlijke bedrijfsschade veroorzaken door gegevensdiefstal, gegevenswijziging of serviceonderbreking. In deze geharde status maakt het werkstation alle beveiligingscontroles en beleidsregels mogelijk die de directe controle over lokaal toepassingsbeheer beperken. Een beveiligd werkstation heeft geen productiviteitstools, zodat het apparaat moeilijker te compromitteren is. Het blokkeert de meest voorkomende vector voor phishing-aanvallen: e-mail en sociale media. Het beveiligde werkstation kan worden geïmplementeerd met het script Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Beveiligd werkstation](./media/concept-azure-managed-workstation/secure-workstation.png)

   Een beveiligd werkstation biedt een beheerder een gehard werkstation met duidelijke toepassingscontrole en applicatiebewaking. Het werkstation maakt gebruik van credential guard, device guard en exploit guard om de host te beschermen tegen kwaadaardig gedrag. Alle lokale schijven zijn ook versleuteld met BitLocker.

* **Geïsoleerd** – Dit aangepaste, offline scenario vertegenwoordigt het extreme einde van het spectrum. Voor deze aanvraag zijn geen installatiescripts beschikbaar. Mogelijk moet u een bedrijfskritieke functie beheren waarvoor een niet-ondersteund of ongepatcht bestaand besturingssysteem vereist is. Bijvoorbeeld een hoogwaardige productielijn of een levensondersteunend systeem. Omdat beveiliging essentieel is en cloudservices niet beschikbaar zijn, u deze computers handmatig beheren en bijwerken of met een geïsoleerde Active Directory-forestarchitectuur, zoals de Uitgebreide Beveiligingsbeheeromgeving (ESAE). Overweeg in deze omstandigheden om alle toegang te verwijderen, behalve basiscontroles voor intune en ATP.

   * [Vereiste voor netwerkcommunicatie intune](/intune/network-bandwidth-use)
   * [Vereiste voor communicatie van het ATP-netwerk](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Volgende stappen

[Een beveiligd Azure-beheerd werkstation implementeren.](howto-azure-managed-workstation.md)
