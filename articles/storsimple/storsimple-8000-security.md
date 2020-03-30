---
title: StorSimple 8000-serie beveiliging | Microsoft Documenten
description: Beschrijft de beveiligings- en privacyfuncties die uw StorSimple-service, apparaat en gegevens op locatie en in de cloud beschermen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 4598f71f9b611e68f8eb00676138784833c39f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891500"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple beveiliging en gegevensbescherming

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

Beveiliging is een grote zorg voor iedereen die een nieuwe technologie gebruikt, vooral wanneer de technologie wordt gebruikt met vertrouwelijke of bedrijfseigen gegevens. Wanneer u verschillende technologieën evalueert, moet u rekening houden met hogere risico's en kosten voor gegevensbescherming. Microsoft Azure StorSimple biedt zowel een beveiligings- als privacyoplossing voor gegevensbescherming, zodat u ervoor zorgen dat:

* **Vertrouwelijkheid** – Alleen geautoriseerde entiteiten kunnen uw gegevens bekijken.
* **Integriteit** : alleen geautoriseerde entiteiten kunnen uw gegevens wijzigen of verwijderen.

De Microsoft Azure StorSimple-oplossing bestaat uit vier hoofdcomponenten die met elkaar communiceren:

* **StorSimple Device Manager-service gehost in Microsoft Azure** – De beheerservice die u gebruikt voor het configureren en inrichten van het StorSimple-apparaat.
* **StorSimple-apparaat** – Een fysiek apparaat dat in uw datacenter is geïnstalleerd. Alle hosts en clients die gegevens genereren, maken verbinding met het StorSimple-apparaat en het apparaat beheert de gegevens en verplaatst deze naar de Azure-cloud.
* **Clients/hosts die zijn verbonden met het apparaat** : de clients in uw infrastructuur die verbinding maken met het StorSimple-apparaat en gegevens genereren die moeten worden beschermd.
* **Cloudopslag** – De locatie in de Azure-cloud waar gegevens worden opgeslagen.

In de volgende secties worden de beveiligingsfuncties van StorSimple beschreven die elk van deze componenten en de gegevens die erop zijn opgeslagen, helpen beschermen. Het bevat ook een lijst met vragen die u zou kunnen hebben over Microsoft Azure StorSimple beveiliging, en de bijbehorende antwoorden.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Device Manager-servicebeveiliging

De StorSimple Device Manager-service is een beheerservice die wordt gehost in Microsoft Azure en wordt gebruikt om alle StorSimple-apparaten te beheren die uw organisatie heeft aangeschaft. U hebt toegang tot de StorSimple Device Manager-service door uw organisatiereferenties te gebruiken om u via een webbrowser aan te melden bij de Azure-portal.

Toegang tot de StorSimple Device Manager-service vereist dat uw organisatie een Azure-abonnement heeft dat StorSimple bevat. Uw abonnement bepaalt tot welke functies u toegang hebt in de Azure-portal. Zie [Aanmelden voor Azure als organisatie](../active-directory/fundamentals/sign-up-organization.md)als u geen Azure-abonnement heeft en u wilt meer informatie over deze abonnementen.

Omdat de StorSimple Device Manager-service wordt gehost in Azure, wordt deze beschermd door de Azure-beveiligingsfuncties. Ga naar het [Vertrouwenscentrum van Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/) voor meer informatie over de beveiligingsfuncties die worden geleverd door Microsoft Azure.

## <a name="storsimple-device-protection"></a>StorSimple-apparaatbeveiliging

Het StorSimple-apparaat is een on-premises hybride opslagapparaat dat solid state drives (SSD's) en harde schijven (HDD's) bevat, samen met redundante controllers en automatische failover-mogelijkheden. De controllers beheren opslaglagen en plaatsen momenteel gebruikte (of hete) gegevens op lokale opslag (in het StorSimple-apparaat of on-premises servers), terwijl ze minder vaak gebruikte gegevens naar de cloud verplaatsen.

Alleen geautoriseerde StorSimple-apparaten mogen lid worden van de StorSimple Device Manager-service die u in uw Azure-abonnement hebt gemaakt. Als u een apparaat wilt autoriseren, moet u het registreren bij de StorSimple Device Manager-service door de serviceregistratiesleutel op te geven. De serviceregistratiesleutel is een 128-bits willekeurige sleutel die wordt gegenereerd in de Azure-portal.

![Serviceregistratiesleutel](./media/storsimple-security/ServiceRegistrationKey.png)

Ga naar [Stap 2: Download de serviceregistratiesleutel](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)voor meer informatie over het krijgen van een serviceregistratiesleutel.

De serviceregistratiesleutel is een lange sleutel met meer dan 100 tekens. U de sleutel kopiëren en opslaan in een tekstbestand op een veilige locatie, zodat u deze gebruiken om indien nodig extra apparaten te autoriseren. Als de serviceregistratiesleutel verloren gaat nadat u uw eerste apparaat hebt geregistreerd, u een nieuwe sleutel genereren via de StorSimple Device Manager-service. Dit heeft geen invloed op de werking van bestaande apparaten.

Nadat een apparaat is geregistreerd, gebruikt het tokens om te communiceren met Microsoft Azure. De serviceregistratiesleutel wordt niet gebruikt na registratie van het apparaat.

> [!NOTE]
> We raden u aan de serviceregistratiesleutel na elk gebruik opnieuw te genereren.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Bescherm uw StorSimple-oplossing via wachtwoorden

Wachtwoorden zijn een belangrijk aspect van computerbeveiliging en worden op grote schaal gebruikt in de StorSimple-oplossing om ervoor te zorgen dat uw gegevens alleen toegankelijk zijn voor geautoriseerde gebruikers. Met StorSimple u de volgende wachtwoorden configureren:

* Het wachtwoord van StorSimple-apparaatbeheerder
* Chap-initiator en doelwachtwoorden (Challenge Handshake Authentication Protocol) uitdagen
* Wachtwoord StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell voor StorSimple en het wachtwoord van de StorSimple-apparaatbeheerder

Windows PowerShell voor StorSimple is een command-line interface die u gebruiken om het StorSimple-apparaat te beheren. Windows PowerShell voor StorSimple heeft functies waarmee u uw apparaat registreren, de netwerkinterface op uw apparaat configureren, bepaalde typen updates installeren, uw apparaat oplossen door toegang te krijgen tot de ondersteuningssessie en de apparaatstatus wijzigen. U hebt toegang tot Windows PowerShell voor StorSimple door verbinding te maken met de seriële console op het apparaat of door Windows PowerShell remoting te gebruiken.

PowerShell remoting kan worden gedaan via HTTPS of HTTP. Als extern beheer via HTTPS is ingeschakeld, moet u het externe beheercertificaat van het apparaat downloaden en installeren op de externe client. Ga voor meer informatie over PowerShell remoting [op afstand aansluiten op uw StorSimple-apparaat.](storsimple-8000-remote-connect.md)

Nadat u Windows PowerShell voor StorSimple hebt gebruikt om verbinding te maken met het apparaat, moet u het wachtwoord van de apparaatbeheerder opgeven om u op het apparaat aan te melden.

![Wachtwoord apparaatbeheerder](./media/storsimple-security/DeviceAdminPW.png)

Houd rekening met de volgende aanbevolen procedures:

* Extern beheer is standaard uitgeschakeld. U de StorSimple Device Manager-service gebruiken om deze in te schakelen. Als beste beveiligingspraktijk moet toegang op afstand alleen worden ingeschakeld gedurende de periode die daadwerkelijk nodig is.
* Als u het wachtwoord wijzigt, moet u alle gebruikers met externe toegang hiervan op de hoogte stellen, zodat ze geen onverwacht verbindingsverlies ervaren.
* De StorSimple Device Manager-service kan bestaande wachtwoorden niet ophalen: deze kunnen alleen worden gereset. We raden u aan alle wachtwoorden op een veilige plaats op te slaan, zodat u een wachtwoord niet hoeft opnieuw in te stellen als het wordt vergeten. Als u een wachtwoord opnieuw moet instellen, moet u alle gebruikers hiervan op de hoogte stellen voordat u het opnieuw instelt.

U hebt toegang tot de Windows PowerShell-interface via een seriële verbinding met het apparaat. U het ook op afstand openen met HTTP of HTTPS, die extra beveiliging bieden. HTTPS biedt een hoger beveiligingsniveau dan een seriële of HTTP-verbinding. Als u HTTPS wilt gebruiken, moet u echter eerst een certificaat op de clientcomputer installeren dat toegang krijgt tot het apparaat. U het rastoegangscertificaat downloaden van de apparaatconfiguratiepagina in de StorSimple Device Manager-service. Als het certificaat voor externe toegang verloren gaat, moet u een nieuw certificaat downloaden en doorgeven aan alle clients die gemachtigd zijn om extern beheer te gebruiken.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Chap-initiator en doelwachtwoorden (Challenge Handshake Authentication Protocol) uitdagen

CHAP is een verificatieschema dat wordt gebruikt door het StorSimple-apparaat om de identiteit van externe clients te valideren. De verificatie is gebaseerd op een gedeeld wachtwoord. CHAP kan eenrichtingsverkeer (unidirectioneel) of wederzijds (bidirectioneel) zijn. Met eenrichtingsCHAP verifieert het doel (het StorSimple-apparaat) een initiator (host). Wederzijdse of omgekeerde CHAP vereist dat het doel de initiator verifieert en vervolgens de initiator het doel verifieert. Uw StorSimple kan worden geconfigureerd om beide methoden te gebruiken.

Houd rekening met het volgende wanneer u CHAP configureert:

* De CHAP-gebruikersnaam moet minder dan 233 tekens bevatten.
* Het CHAP-wachtwoord moet tussen de 12 en 16 tekens liggen. Als u een langere gebruikersnaam of wachtwoord probeert te gebruiken, wordt de Windows-host niet gesanmeerd.
* U niet hetzelfde wachtwoord gebruiken voor zowel de CHAP-initiator als het CHAP-doel.
* Nadat u het wachtwoord hebt ingesteld, kan het worden gewijzigd, maar kan het niet worden opgehaald. Als het wachtwoord wordt gewijzigd, moet u alle gebruikers van externe toegang hiervan op de hoogte stellen, zodat ze met succes verbinding kunnen maken met het StorSimple-apparaat.

Ga voor meer informatie over CHAP en hoe u deze configureert voor uw StorSimple-oplossing naar [CHAP configureren voor uw StorSimple-apparaat.](storsimple-8000-configure-chap.md)

### <a name="storsimple-snapshot-manager-password"></a>Wachtwoord StorSimple Snapshot Manager

StorSimple Snapshot Manager is een MMC-module (Microsoft Management Console) die volumegroepen en de Windows Volume Shadow Copy Service gebruikt om toepassingsconsistente back-ups te genereren. Daarnaast u StorSimple Snapshot Manager gebruiken om back-upschema's te maken en volumes te klonen of te herstellen.

Wanneer u een apparaat configureert om StorSimple Snapshot Manager te gebruiken, moet u het wachtwoord van StorSimple Snapshot Manager opgeven. Dit wachtwoord wordt voor het eerst ingesteld in Windows PowerShell voor StorSimple tijdens de registratie. Het wachtwoord kan ook worden ingesteld en gewijzigd vanuit de StorSimple Device Manager-service. Met dit wachtwoord wordt het apparaat geverifieerd met StorSimple Snapshot Manager.

![Wachtwoord StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Het wachtwoord van De Momentopname van StorSimple moet 14 tot 15 tekens bevatten en moet 3 of meer van een combinatie van hoofdletters, kleine letters, numerieke en speciale tekens bevatten. Nadat u het wachtwoord van De Momentopname Van StorSimple hebt ingesteld, kan het worden gewijzigd, maar kan het niet worden opgehaald. Als u het wachtwoord wijzigt, moet u alle externe gebruikers hiervan op de hoogte stellen.

Ga voor meer informatie over StorSimple Snapshot Manager naar [Wat is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Aanbevolen procedures voor wachtwoorden

We raden u aan de volgende richtlijnen te gebruiken om ervoor te zorgen dat StorSimple-wachtwoorden sterk en goed beschermd zijn:

* Wijzig uw wachtwoorden elke drie maanden. Het wijzigen van de wachtwoorden wordt jaarlijks afgedwongen.
* Gebruik sterke wachtwoorden. Ga voor meer informatie naar [Sterkere wachtwoorden maken en deze beveiligen.](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/)
* Gebruik altijd verschillende wachtwoorden voor verschillende toegangsmechanismen; elk van de wachtwoorden die u opgeeft, moet uniek zijn.
* Deel geen wachtwoorden met iedereen die geen toegang heeft tot het StorSimple-apparaat.
* Spreek niet over een wachtwoord in het bijzijn van anderen of hint naar het formaat van een wachtwoord.
* Als u vermoedt dat een account of wachtwoord is gecompromitteerd, meldt u het incident bij uw afdeling informatiebeveiliging.
* Behandel alle wachtwoorden als gevoelige, vertrouwelijke informatie. 

## <a name="storsimple-data-protection"></a>StorSimple-gegevensbescherming

In deze sectie worden de beveiligingsfuncties van StorSimple beschreven die gegevens tijdens het transport en opgeslagen gegevens beschermen.

Zoals beschreven in andere secties, wachtwoorden worden gebruikt om te autoriseren en te verifiëren gebruikers voordat ze toegang kunnen krijgen tot uw StorSimple oplossing. Een andere beveiligingsoverweging is het beschermen van gegevens tegen onbevoegde gebruikers terwijl deze worden overgedragen tussen opslagsystemen en terwijl deze worden opgeslagen. In de volgende secties worden de gegevensbeschermingsfuncties beschreven die bij StorSimple zijn geleverd.

> [!NOTE]
> Ontdubbeling biedt extra bescherming voor gegevens die zijn opgeslagen op het StorSimple-apparaat en in Microsoft Azure-opslag. Wanneer gegevens worden gededupliceerd, worden de gegevensobjecten apart opgeslagen van de metagegevens die worden gebruikt om ze in kaart te brengen en te openen: er is geen beschikbare context op opslagniveau om de gegevens te reconstrueren op basis van volumestructuur, bestandssysteem of bestandsnaam.


## <a name="protect-data-flowing-through-the-service"></a>Gegevens die door de service stromen beschermen

Het primaire doel van de StorSimple Device Manager-service is het beheren en configureren van het StorSimple-apparaat. De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure. U gebruikt de Azure-portal om apparaatconfiguratiegegevens in te voeren en vervolgens gebruikt Microsoft Azure de StorSimple Device Manager-service om de gegevens naar het apparaat te verzenden. StorSimple maakt gebruik van een systeem van asymmetrische sleutelparen om ervoor te zorgen dat een compromis van de Azure-service niet zal resulteren in een compromis van opgeslagen informatie.

![Gegevensversleuteling tijdens de vlucht](./media/storsimple-security/DataEncryption.png)

Het asymmetrische sleutelsysteem helpt de gegevens die door de service stromen als volgt te beschermen:

1. Een versleutelingscertificaat voor gegevens dat een asymmetrisch openbaar en privésleutelpaar gebruikt, wordt gegenereerd op het apparaat en wordt gebruikt om de gegevens te beschermen. De sleutels worden gegenereerd wanneer het eerste apparaat wordt geregistreerd.
2. De gegevens encryptie certificaat sleutels worden geëxporteerd naar een Personal Information Exchange (.pfx) bestand dat wordt beschermd door de service data encryptie sleutel, dat is een sterke 128-bits sleutel die willekeurig wordt gegenereerd door het eerste apparaat tijdens de registratie.
3. De openbare sleutel van het certificaat wordt veilig beschikbaar gesteld aan de StorSimple Device Manager-service en de privésleutel blijft bij het apparaat.
4. Gegevens die de service invoeren, worden versleuteld met behulp van de openbare sleutel en gedecodeerd met behulp van de privésleutel die op het apparaat is opgeslagen, zodat de Azure-service de gegevens die naar het apparaat worden verzonden, niet kan decoderen.

De versleutelingssleutel voor servicegegevens wordt alleen gegenereerd op het eerste apparaat dat bij de service is geregistreerd. Alle volgende apparaten die bij de service zijn geregistreerd, moeten dezelfde versleutelingssleutel voor servicegegevens gebruiken.

> [!IMPORTANT]
> Het is zeer belangrijk om een kopie van de encryptiesleutel van servicegegevens te maken en deze op een veilige locatie op te slaan. Een kopie van de versleutelingssleutel voor servicegegevens moet zodanig worden opgeslagen dat deze door een geautoriseerde persoon kan worden geopend en eenvoudig aan de apparaatbeheerder kan worden doorgegeven.
> 
> Als de versleutelingssleutel voor servicegegevens verloren gaat, kan een Microsoft-ondersteuningspersoon u helpen deze op te halen, mits u ten minste één apparaat online hebt. We raden u aan de versleutelingssleutel voor servicegegevens te wijzigen nadat deze is opgehaald.

Als u de versleutelingssleutel voor servicegegevens en het bijbehorende versleutelingscertificaat voor gegevens wilt wijzigen, voert u de stappen uit in [De versleutelingssleutel voor servicegegevens wijzigen voor uw StorSimple Device Manager-service.](storsimple-8000-manage-service.md#change-the-service-data-encryption-key) Als u de versleutelingssleutels wijzigt, moeten alle apparaten worden bijgewerkt met de nieuwe sleutel. Daarom raden we u aan de sleutel te wijzigen wanneer alle apparaten online zijn. Als apparaten offline zijn, kunnen hun sleutels op een ander tijdstip worden gewijzigd. De apparaten met verouderde sleutels kunnen nog steeds back-ups uitvoeren, maar ze kunnen gegevens pas herstellen als de sleutel is bijgewerkt.

De versleutelingssleutel voor servicegegevens en het versleutelingscertificaat voor gegevens verlopen niet. We raden u echter aan de versleutelingssleutel voor servicegegevens jaarlijks te wijzigen om belangrijke compromissen te voorkomen.

## <a name="protect-data-at-rest"></a>Data-at-rest beschermen

Het StorSimple-apparaat beheert gegevens door deze lokaal en in de cloud op te slaan in lagen, afhankelijk van de gebruiksfrequentie. Alle hostmachines die met het apparaat zijn verbonden, verzenden gegevens naar het apparaat, waardoor gegevens naar de cloud worden verplaatst, naar gelang van het geval. Gegevens worden veilig via internet van het apparaat naar de cloud overgebracht. Elk apparaat heeft één iSCSI-doel dat alle gedeelde volumes op dat apparaat weergeeft. Alle gegevens worden versleuteld voordat ze naar cloudopslag worden verzonden. 

![Versleutelingssleutel voor cloudopslag](./media/storsimple-security/CloudStorageEncryption.png)

Om de beveiliging en integriteit van gegevens die naar de cloud worden verplaatst te waarborgen, u met StorSimple versleutelingssleutels voor cloudopslag als volgt definiëren:

* U geeft de versleutelingssleutel voor cloudopslag op wanneer u een volumecontainer maakt. De sleutel kan niet later worden gewijzigd of toegevoegd.
* Alle volumes in een volumecontainer hebben dezelfde versleutelingssleutel. Als u een andere versleutelingsvorm voor een bepaald volume wilt, raden we u aan een nieuwe volumecontainer te maken om dat volume te hosten.
* Wanneer u de versleutelingssleutel voor cloudopslag invoert in de StorSimple Device Manager-service, wordt de sleutel versleuteld met behulp van het openbare gedeelte van de versleutelingssleutel voor servicegegevens en vervolgens naar het apparaat verzonden.
* De versleutelingssleutel voor cloudopslag wordt nergens in de service opgeslagen en is alleen bekend bij het apparaat.
* Het opgeven van een versleutelingssleutel voor cloudopslag is optioneel. U gegevens die bij de host zijn versleuteld naar het apparaat verzenden.

### <a name="additional-security-best-practices"></a>Aanvullende aanbevolen procedures voor beveiliging

* Gesplitst verkeer: isoleer uw iSCSI SAN van gebruikersverkeer op een bedrijfs-LAN door een volledig gescheiden netwerk te implementeren en VLAN's te gebruiken waar fysieke isolatie geen optie is. Een speciaal netwerk voor iSCSI-opslag garandeert de veiligheid en prestaties van uw bedrijfskritieke gegevens. Het mengen van opslag- en gebruikersverkeer via een bedrijfs-LAN wordt niet aanbevolen en kan de latentie verhogen en netwerkfouten veroorzaken.
* Gebruik netwerkinterfaces die TCP/IP Offload Engine (TOE) ondersteunen voor netwerkbeveiliging aan de hostzijde. TOE vermindert de CPU-belasting door TCP op de netwerkadapter te verwerken.

## <a name="protect-data-via-storage-accounts"></a>Gegevens beveiligen via opslagaccounts

Elk Microsoft Azure-abonnement kan een of meer opslagaccounts maken. (Een opslagaccount biedt een unieke naamruimte voor het werken met gegevens die zijn opgeslagen in de Azure-cloud.) De toegang tot een opslagaccount wordt beheerd door het abonnement en de toegangssleutels die aan dat opslagaccount zijn gekoppeld.

Wanneer u een opslagaccount maakt, genereert Microsoft Azure twee 512-bits opslagtoegangssleutels, waarvan er één wordt gebruikt voor verificatie wanneer het StorSimple-apparaat toegang krijgt tot het opslagaccount. Houd er rekening mee dat slechts één van deze sleutels in gebruik is. De andere sleutel wordt in reserve gehouden, zodat u de toetsen periodiek draaien. Als u toetsen wilt roteren, maakt u de secundaire sleutel actief en verwijdert u de primaire sleutel. U vervolgens een nieuwe sleutel maken voor gebruik tijdens de volgende rotatie. (Om veiligheidsredenen vereisen veel datacenters sleutelrotatie.)

We raden u aan deze aanbevolen procedures voor sleutelrotatie te volgen:

* U moet regelmatig opslagaccountsleutels roteren om ervoor te zorgen dat uw opslagaccount niet toegankelijk is voor onbevoegde gebruikers.
* Uw Azure-beheerder moet periodiek de primaire of secundaire sleutel wijzigen of regenereren met behulp van de sectie Opslag van de Azure-portal om rechtstreeks toegang te krijgen tot het opslagaccount.

## <a name="protect-data-via-encryption"></a>Gegevens beveiligen via versleuteling

StorSimple gebruikt de volgende versleutelingsalgoritmen om gegevens te beschermen die zijn opgeslagen in of reizen tussen de componenten van uw StorSimple-oplossing.

| Algoritme | Sleutellengte | Protocollen/toepassingen/opmerkingen |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v1.5 wordt door de Azure-portal gebruikt om configuratiegegevens te versleutelen die naar het apparaat worden verzonden: bijvoorbeeld opslagaccountreferenties, StorSimple-apparaatconfiguratie en cloudopslagversleutelingssleutels. |
| AES |256 |AES met CBC wordt gebruikt om het openbare gedeelte van de versleutelingssleutel voor servicegegevens te versleutelen voordat deze vanaf het StorSimple-apparaat naar de Azure-portal wordt verzonden. Het wordt ook gebruikt door het StorSimple-apparaat om gegevens te versleutelen voordat de gegevens naar het cloudopslagaccount worden verzonden. |

## <a name="storsimple-cloud-appliance-security"></a>Beveiliging van StorSimple Cloud Appliance

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Persoonlijke informatie beheren

De StorSimple Device Manager voor zowel fysieke als virtuele series verzamelt persoonlijke informatie in de volgende belangrijke instanties:

- Gebruikersinstellingen waarschuwen wanneer het e-mailadres van gebruikers is geconfigureerd. Deze informatie kan door de beheerder worden bekeken en gewist. Dit geldt voor zowel de StorSimple 8000-serie apparaten en StorSimple Virtual Arrays.
  * Als u de instellingen voor storSimple 8000-reeks wilt weergeven en wissen, voert u de stappen in [StorSimple-waarschuwingen weer geven en beheert u storSimple-waarschuwingen](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Als u de instellingen voor StorSimple Virtual Array wilt weergeven en wissen, voert u de stappen in [StorSimple-waarschuwingen weer geven en beheert u storSimple-waarschuwingen](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Gebruikers die toegang hebben tot de gegevens die zich op de aandelen bevinden. Een lijst met gebruikers die toegang hebben tot de share-gegevens wordt weergegeven en kan worden bekeken. Deze lijst wordt ook verwijderd wanneer de aandelen worden verwijderd. Dit geldt alleen voor StorSimple Virtual Arrays.
  * Als u de lijst wilt bekijken van de gebruiker die een aandeel kan openen of verwijderen, volgt u de stappen in [Aandelen beheren op de Virtuele Array van StorSimple](storsimple-virtual-array-manage-shares.md)

Bekijk het Microsoft-privacybeleid in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter) voor meer informatie.

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Hieronder volgen enkele vragen en antwoorden over beveiliging en Microsoft Azure StorSimple.

**V:** Mijn service is gecompromitteerd. Wat moeten mijn volgende stappen zijn?

**A:** U moet onmiddellijk de versleutelingssleutel voor servicegegevens en de opslagaccountsleutels voor het opslagaccount wijzigen dat wordt gebruikt voor het tieren van gegevens. Ga voor instructies naar:

* [De versleutelingssleutel voor servicegegevens wijzigen](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Sleutelrotatie van opslagrekeningen](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**V:** Ik heb een nieuwe StorSimple apparaat dat vraagt om de service registratie sleutel. Hoe haal ik het op?

**A:** Deze sleutel is gemaakt toen u voor het eerst de StorSimple Device Manager-service maakte. Wanneer u de StorSimple Device Manager-service gebruikt om verbinding te maken met het apparaat, u de snelstartpagina van de service gebruiken om de serviceregistratiesleutel weer te geven of opnieuw te genereren. Het genereren van een nieuwe serviceregistratiesleutel heeft geen invloed op de bestaande geregistreerde apparaten. Ga voor instructies naar:

* [De serviceregistratiesleutel weergeven of regenereren](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**V:** Ik ben de versleutelingssleutel voor servicegegevens kwijt. Wat moet ik doen?

**A:** Contact Microsoft Support. Ze kunnen zich aanmelden bij een ondersteuningssessie op uw apparaat en u helpen de sleutel op te halen (mits ten minste één apparaat online is). Onmiddellijk nadat u de versleutelingssleutel voor servicegegevens hebt verkregen, moet u deze wijzigen om ervoor te zorgen dat de nieuwe sleutel alleen bij u bekend is. Ga voor instructies naar:

* [De versleutelingssleutel voor servicegegevens wijzigen](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**V:**  Ik heb een apparaat geautoriseerd voor een versleutelingssleutelwijziging voor servicegegevens, maar heb het sleutelwijzigingsproces niet gestart. Wat moet ik doen?

**A:** Als de time-outperiode is verstreken, moet u het apparaat opnieuw autoriseren voor de wijziging van de versleutelingssleutel van servicegegevens en het proces opnieuw starten.

**V:**  Ik veranderde de service data encryptie sleutel, maar ik was niet in staat om de andere apparaten te werken binnen 4 uur. Moet ik opnieuw beginnen?

**A:** De periode van 4 uur is alleen voor het initiëren van de wijziging. Nadat u het updateproces op het geautoriseerde StorSimple-apparaat hebt gestart, is de autorisatie geldig totdat alle apparaten zijn bijgewerkt.

**V:** Onze StorSimple beheerder heeft het bedrijf verlaten. Wat moet ik doen?

**A:** Wijzig en reset de wachtwoorden die toegang geven tot het StorSimple-apparaat en wijzig de versleutelingssleutel voor servicegegevens om ervoor te zorgen dat de nieuwe informatie niet bekend is bij onbevoegd personeel. Ga voor instructies naar:

* [Gebruik de StorSimple Device Manager-service om uw storsimple-wachtwoorden te wijzigen](storsimple-8000-change-passwords.md)
* [De versleutelingssleutel voor servicegegevens wijzigen](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [CHAP configureren voor uw StorSimple-apparaat](storsimple-8000-configure-chap.md)

**V:** Ik wil het storSimple Snapshot Manager-wachtwoord opgeven aan een host die verbinding maakt met het StorSimple-apparaat, maar het wachtwoord is niet beschikbaar. Wat kan ik doen?

**A:** Als u het wachtwoord bent vergeten, moet u een nieuw wachtwoord maken. Zorg er vervolgens voor dat u alle bestaande gebruikers informeert dat het wachtwoord is gewijzigd en dat ze hun clients moeten bijwerken om het nieuwe wachtwoord te gebruiken. Ga voor instructies naar:

* [Het wachtwoord van De Momentopname van StorSimple wijzigen](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Een apparaat verifiëren](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**V:** Het certificaat voor externe toegang tot de Windows PowerShell voor StorSimple is gewijzigd op het apparaat. Hoe werk ik mijn externe toegangsclients bij?

**A:** U het nieuwe certificaat downloaden van de StorSimple Device Manager-service en het vervolgens installeren in de certificaatopslag van uw externe toegangsclients. Ga voor instructies naar:

* [Cmdlet voor importcertificaat](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**V:** Zijn mijn gegevens beschermd als de StorSimple Device Manager-service is gecompromitteerd?

**A:** Serviceconfiguratiegegevens worden altijd versleuteld met uw openbare sleutel wanneer u deze in een webbrowser bekijkt. Omdat de service geen toegang heeft tot de privésleutel, kan de service geen gegevens zien. Als de StorSimple Device Manager-service is gecompromitteerd, is er geen impact, omdat er geen sleutels zijn opgeslagen in de StorSimple Device Manager-service.

**V:** Als iemand toegang krijgt tot het certificaat voor gegevensversleuteling, worden mijn gegevens dan gecompromitteerd?

**A:** Microsoft Azure slaat de gegevensversleutelingssleutel van de klant (.pfx-bestand) op in een versleutelde indeling. Omdat het .pfx-bestand versleuteld is en de StorSimple-service niet de encryptiesleutel voor servicegegevens heeft om het .pfx-bestand te decoderen, zal het simpelweg toegang krijgen tot het .pfx-bestand geen geheimen onthullen.

**V:** Wat gebeurt er als een overheidsinstantie Microsoft om mijn gegevens vraagt?

**A:** Omdat alle gegevens worden versleuteld op de service en de privésleutel bij het apparaat wordt bewaard, moet de overheidsinstantie de klant om de gegevens vragen.



## <a name="next-steps"></a>Volgende stappen

[Implementeer uw StorSimple-apparaat.](storsimple-8000-deployment-walkthrough-u2.md)

