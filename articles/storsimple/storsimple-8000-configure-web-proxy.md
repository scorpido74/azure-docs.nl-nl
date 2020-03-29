---
title: Webproxy instellen voor storSimple 8000-serie apparaat | Microsoft Documenten
description: Meer informatie over het gebruik van Windows PowerShell voor StorSimple om webproxy-instellingen voor uw StorSimple-apparaat te configureren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65204255"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Webproxy configureren voor uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt beschreven hoe u Windows PowerShell voor StorSimple gebruiken om webproxy-instellingen voor uw StorSimple-apparaat te configureren en weer te geven. De webproxy-instellingen worden gebruikt door het StorSimple-apparaat bij het communiceren met de cloud. Een webproxyserver wordt gebruikt om een andere beveiligingslaag toe te voegen, inhoud te filteren, cache te beheren om bandbreedtevereisten te verlichten of zelfs te helpen met analytics.

De richtlijnen in deze zelfstudie zijn alleen van toepassing op fysieke apparaten uit de StorSimple 8000-serie. Webproxyconfiguratie wordt niet ondersteund op het StorSimple Cloud Appliance (8010 en 8020).

Webproxy is een _optionele_ configuratie voor uw StorSimple-apparaat. U webproxy alleen configureren via Windows PowerShell voor StorSimple. De configuratie is een proces in twee stappen als volgt:

1. U configureert eerst webproxy-instellingen via de wizard Setup of Windows PowerShell voor StorSimple-cmdlets.
2. Vervolgens schakelt u de geconfigureerde webproxy-instellingen in via Windows PowerShell voor StorSimple-cmdlets.

Nadat de webproxyconfiguratie is voltooid, u de geconfigureerde webproxy-instellingen bekijken in zowel de Microsoft Azure StorSimple Device Manager-service als de Windows PowerShell voor StorSimple.

Na het lezen van deze tutorial, zult u in staat zijn om:

* Configureer webproxy met behulp van de wizard Setup en cmdlets.
* Webproxy inschakelen met behulp van cmdlets.
* Webproxy-instellingen weergeven in de Azure-portal.
* Fouten oplossen tijdens de configuratie van webproxy's.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Webproxy configureren via Windows PowerShell voor StorSimple

U gebruikt een van de volgende opties om webproxy-instellingen te configureren:

* De wizard Setup om u door de configuratiestappen te leiden.
* Cmdlets in Windows PowerShell voor StorSimple.

Elk van deze methoden wordt besproken in de volgende secties.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Webproxy configureren via de wizard Setup

Gebruik de wizard Setup om u door de stappen voor webproxyconfiguratie te leiden. Voer de volgende stappen uit om de webproxy op uw apparaat te configureren.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Webproxy configureren via de wizard Setup

1. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang** en geef het wachtwoord van de **apparaatbeheerder**op. Typ de volgende opdracht om een installatiewizardsessie te starten:
   
    `Invoke-HcsSetupWizard`
2. Als dit de eerste keer is dat u de wizard Setup hebt gebruikt voor apparaatregistratie, moet u alle vereiste netwerkinstellingen configureren totdat u de webproxyconfiguratie hebt bereikt. Als uw apparaat al is geregistreerd, accepteert u alle geconfigureerde netwerkinstellingen totdat u de webproxyconfiguratie hebt bereikt. Typ **Ja**. in de wizard Setup wanneer u wordt gevraagd de instellingen van webproxy's te configureren.
3. Geef voor de URL van de **webproxy**het IP-adres of de volledig gekwalificeerde domeinnaam (FQDN) van uw webproxyserver en het TCP-poortnummer op dat u wilt dat uw apparaat gebruikt wanneer het communiceert met de cloud. Gebruik de volgende indeling:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Standaard is TCP-poortnummer 8080 opgegeven.
4. Kies het verificatietype als **NTLM,** **Basic**of **Geen**. Basic is de minst veilige verificatie voor de proxyserverconfiguratie. NT LAN Manager (NTLM) is een zeer veilig en complex verificatieprotocol dat een drierichtingsberichtensysteem gebruikt (soms vier als extra integriteit vereist is) om een gebruiker te verifiëren. De standaardverificatie is NTLM. Zie [Basis-](https://hc.apache.org/httpclient-3.x/authentication.html) en [NTLM-verificatie](https://hc.apache.org/httpclient-3.x/authentication.html)voor meer informatie. 
   
   > [!IMPORTANT]
   > **In de StorSimple Device Manager-service werken de apparaatcontrolediagrammen niet wanneer basis- of NTLM-verificatie is ingeschakeld in de proxyserverconfiguratie voor het apparaat. Als de controlegrafieken werken, moet u ervoor zorgen dat de verificatie is ingesteld op GEEN.**
  
5. Als u de verificatie hebt ingeschakeld, geeft u een **gebruikersnaam voor webproxy** en een **webproxywachtwoord**. U moet ook het wachtwoord bevestigen.
   
    ![Webproxy configureren op StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Als u uw apparaat voor de eerste keer registreert, gaat u verder met de registratie. Als uw apparaat al is geregistreerd, wordt de wizard afgesloten. De geconfigureerde instellingen worden opgeslagen.

Webproxy is nu ingeschakeld. U de stap [Webproxy inschakelen](#enable-web-proxy) overslaan en rechtstreeks naar [Webproxy-instellingen weergeven in de Azure-portal.](#view-web-proxy-settings-in-the-azure-portal)

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Webproxy configureren via Windows PowerShell voor StorSimple-cmdlets

Een alternatieve manier om webproxy-instellingen te configureren is via de Windows PowerShell voor StorSimple-cmdlets. Voer de volgende stappen uit om de webproxy te configureren.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Webproxy configureren via cmdlets
1. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**. Geef het wachtwoord van de **apparaatbeheerder**op wanneer u daarom wordt gevraagd. Het standaardwachtwoord `Password1`is .
2. Typ het volgende na de opdrachtprompt:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Geef het wachtwoord op en bevestig het wanneer daarom wordt gevraagd.
   
    ![Webproxy configureren op StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

De webproxy is nu geconfigureerd en moet worden ingeschakeld.

## <a name="enable-web-proxy"></a>Webproxy inschakelen

Webproxy is standaard uitgeschakeld. Nadat u de webproxy-instellingen op uw StorSimple-apparaat hebt geconfigureerd, gebruikt u de Windows PowerShell voor StorSimple om de webproxy-instellingen in te schakelen.

> [!NOTE]
> **Deze stap is niet vereist als u de wizard Setup hebt gebruikt om de webproxy te configureren. Webproxy wordt standaard automatisch ingeschakeld na een installatiewizardsessie.**


Voer de volgende stappen uit in Windows PowerShell voor StorSimple om webproxy op uw apparaat in te schakelen:

#### <a name="to-enable-web-proxy"></a>Webproxy inschakelen
1. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**. Geef het wachtwoord van de **apparaatbeheerder**op wanneer u daarom wordt gevraagd. Het standaardwachtwoord `Password1`is .
2. Typ het volgende na de opdrachtprompt:
   
    `Enable-HcsWebProxy`
   
    U hebt nu de webproxyconfiguratie ingeschakeld op uw StorSimple-apparaat.
   
    ![Webproxy configureren op StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Webproxy-instellingen weergeven in de Azure-portal

De webproxy-instellingen zijn geconfigureerd via de Windows PowerShell-interface en kunnen niet worden gewijzigd vanuit de portal. U deze geconfigureerde instellingen echter wel bekijken in de portal. Voer de volgende stappen uit om de webproxy weer te geven.

#### <a name="to-view-web-proxy-settings"></a>Webproxy-instellingen weergeven
1. Navigeer naar **de StorSimple Device Manager-service > Apparaten.** Selecteer en klik op een apparaat en ga vervolgens naar **Apparaatinstellingen > Netwerk**.

    ![Klik op Netwerk](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Klik in het blad **Netwerkinstellingen** op de **tegel Webproxy.**

    ![Klik op webproxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Controleer in het **webproxyblad** de geconfigureerde webproxy-instellingen op uw StorSimple-apparaat.
   
    ![Webproxy-instellingen weergeven](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fouten tijdens de configuratie van webproxy's

Als de webproxy-instellingen onjuist zijn geconfigureerd, worden foutmeldingen weergegeven aan de gebruiker in Windows PowerShell voor StorSimple. In de volgende tabel worden enkele van deze foutmeldingen, de waarschijnlijke oorzaken en de aanbevolen acties uitgelegd.

| Seriële nr. | HRESULT-foutcode | Mogelijke oorzaak | Aanbevolen actie |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Commando wordt uitgevoerd vanaf de passieve controller en het is niet in staat om te communiceren met de actieve controller. |Voer de opdracht uit op de actieve controller. Als u de opdracht vanaf de passieve controller wilt uitvoeren, moet u de verbinding van passieve naar actieve controller herstellen. U moet Microsoft Support inschakelen als deze verbinding is verbroken. |
| 2. |0x800710dd - De bewerkings-id is niet geldig |Proxy-instellingen worden niet ondersteund op StorSimple Cloud Appliance. |Proxy-instellingen worden niet ondersteund op StorSimple Cloud Appliance. Deze kunnen alleen worden geconfigureerd op een StorSimple fysiek apparaat. |
| 3. |0x80070057 - Ongeldige parameter |Een van de parameters voor de proxy-instellingen is niet geldig. |De URI is niet in de juiste indeling. Gebruik de volgende indeling:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC-server niet beschikbaar |De hoofdoorzaak is een van de volgende:</br></br>Cluster is niet up. </br></br>De Datapath-service wordt niet uitgevoerd.</br></br>De opdracht wordt uitgevoerd vanaf de passieve controller en kan niet communiceren met de actieve controller. |Schakel Microsoft Support in om ervoor te zorgen dat het cluster is uitgevoerd en dat de datapath-service wordt uitgevoerd.</br></br>Voer de opdracht uit vanaf de actieve controller. Als u de opdracht vanaf de passieve controller wilt uitvoeren, moet u ervoor zorgen dat de passieve controller met de actieve controller kan communiceren. U moet Microsoft Support inschakelen als deze verbinding is verbroken. |
| 5. |0x800706be - RPC-gesprek mislukt |Cluster is uitgevallen. |Schakel Microsoft Support in om ervoor te zorgen dat het cluster is up. |
| 6. |0x8007138f - Clusterbron niet gevonden |Clusterbron platformservice wordt niet gevonden. Dit kan gebeuren wanneer de installatie niet correct was. |Mogelijk moet u een fabrieksreset uitvoeren op uw apparaat. Mogelijk moet u een platformbron maken. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| 7. |0x8007138c - Clusterbron niet online |Clusterbronnen van platforms of gegevensgroepen zijn niet online. |Neem contact op met Microsoft Support om ervoor te zorgen dat de gegevenspad- en platformservicebron online is. |

> [!NOTE]
> * De bovenstaande lijst met foutmeldingen is niet volledig.
> * Fouten met betrekking tot webproxy-instellingen worden niet weergegeven in de Azure-portal in uw StorSimple Device Manager-service. Als er een probleem is met de webproxy nadat de configuratie is voltooid, wordt de apparaatstatus gewijzigd in **Offline** in de klassieke portal.|

## <a name="next-steps"></a>Volgende stappen
* Als u problemen ondervindt bij het implementeren van uw apparaat of het configureren van webproxy-instellingen, raadpleegt u [Problemen met de implementatie van uw StorSimple-apparaat.](storsimple-troubleshoot-deployment.md)
* Als u wilt weten hoe u de StorSimple Device Manager-service gebruiken, gaat u [naar De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

