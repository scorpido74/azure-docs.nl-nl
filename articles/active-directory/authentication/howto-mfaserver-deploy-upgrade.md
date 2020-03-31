---
title: Azure MFA Server upgraden - Azure Active Directory
description: Stappen en richtlijnen om de Azure Multi-Factor Authentication Server te upgraden naar een nieuwere versie.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756c45541907c52448805376e1b054180c31fdf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848099"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgraden naar de nieuwste versie van Azure Multi-Factor Authentication Server

In dit artikel wordt u door het proces van het upgraden van Azure Multi-Factor Authentication (MFA) Server v6.0 of hoger geüpgraded. Als u een oude versie van de PhoneFactor-agent moet upgraden, raadpleegt u [De PhoneFactor-agent upgraden naar Azure Multi-Factor Authentication Server.](howto-mfaserver-deploy-upgrade-pf.md)

Als u een upgrade uitvoert van v6.x of ouder naar v7.x of nieuwer, worden alle onderdelen gewijzigd van .NET 2.0 naar .NET 4.5. Alle componenten vereisen ook Microsoft Visual C++ 2015 Redistributable Update 1 of hoger. Het installatieprogramma van MFA Server installeert zowel de x86- als x64-versies van deze componenten als ze nog niet zijn geïnstalleerd. Als de gebruikersportal en de mobiele app-webservice op afzonderlijke servers worden uitgevoerd, moet u deze pakketten installeren voordat deze onderdelen worden geüpgraded. U zoeken naar de nieuwste Microsoft Visual C++ 2015 Redistributable-update in het [Microsoft Download Center.](https://www.microsoft.com/download/) 

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.

Upgradestappen in één oogopslag:

* Azure MFA-servers bijwerken (ondergeschikten vervolgens Master)
* De exemplaren van de gebruikersportal bijwerken
* De exemplaren van de AD FS-adapter bijwerken

## <a name="upgrade-azure-mfa-server"></a>Azure MFA-server upgraden

1. Gebruik de instructies in [De Azure Multi-Factor Authentication Server downloaden](howto-mfaserver-deploy.md#download-the-mfa-server) om de nieuwste versie van het Azure MFA Server-installatieprogramma te downloaden.
2. Maak een back-up van het MFA Server-gegevensbestand op C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (uitgaande van de standaardinstallatielocatie) op uw hoofdMFA-server.
3. Als u meerdere servers uitvoert voor hoge beschikbaarheid, wijzigt u de clientsystemen die zijn geverifieerd naar de MFA-server, zodat ze geen verkeer meer verzenden naar de servers die upgraden. Als u een load balancer gebruikt, verwijdert u een ondergeschikte MFA-server uit de load balancer, uitvoert u de upgrade en voegt u de server weer toe aan de farm.
4. Voer het nieuwe installatieprogramma uit op elke MFA-server. Upgrade eerst ondergeschikte servers omdat ze het oude gegevensbestand kunnen lezen dat door de master wordt gerepliceerd.

   > [!NOTE]
   > Bij het upgraden van een server moet deze worden verwijderd uit elke loadbalancing of het delen van verkeer met andere MFA-servers.
   >
   > U hoeft uw huidige MFA-server niet te verwijderen voordat u het installatieprogramma uitvoert. De installateur voert een in-place upgrade uit. Het installatiepad wordt opgehaald uit het register van de vorige installatie, zodat het op dezelfde locatie wordt geïnstalleerd (bijvoorbeeld C:\Program Files\Multi-Factor Authentication Server).
  
5. Als u wordt gevraagd een Microsoft Visual C++ 2015 Redistributable-updatepakket te installeren, accepteert u de prompt. Zowel de x86- als x64-versies van het pakket zijn geïnstalleerd.
6. Als u de Web Service SDK gebruikt, wordt u gevraagd de nieuwe Web Service SDK te installeren. Controleer bij het installeren van de nieuwe Web Service SDK of de naam van de virtuele map overeenkomt met de eerder geïnstalleerde virtuele map (bijvoorbeeld MultiFactorAuthWebServiceSdk).
7. Herhaal de stappen op alle ondergeschikte servers. Promoot een van de ondergeschikten om de nieuwe master te zijn en upgrade vervolgens de oude hoofdserver.

## <a name="upgrade-the-user-portal"></a>De gebruikersportal bijwerken

Voltooi de upgrade van uw MFA-servers voordat u naar deze sectie gaat.

1. Maak een back-up van het web.config-bestand dat zich in de virtuele map van de installatielocatie van de gebruikersportal bevindt (bijvoorbeeld C:\inetpub\wwwroot\MultiFactorAuth). Als er wijzigingen zijn aangebracht in het standaardthema, maakt u ook een back-up van de map App_Themes\Standaard. Het is beter om een kopie van de standaardmap te maken en een nieuw thema te maken dan het standaardthema te wijzigen.
2. Als de gebruikersportal op dezelfde server wordt uitgevoerd als de andere MFA Server-componenten, vraagt de installatie van mfaserver u om de gebruikersportal bij te werken. Accepteer de prompt en installeer de update van de gebruikersportal. Controleer of de naam van de virtuele map overeenkomt met de eerder geïnstalleerde virtuele map (bijvoorbeeld MultiFactorAuth).
3. Als de gebruikersportal zich op de eigen server bevindt, kopieert u het MultiFactorAuthenticationUserPortalSetup64.msi-bestand vanaf de installatielocatie van een van de MFA-servers en plaatst u het op de webserver van de gebruikersportal. Voer het installatieprogramma uit.

   Als er een fout optreedt met de vermelding 'Microsoft Visual C++ 2015 Redistributable Update 1 of hoger is vereist', download en installeer t u het nieuwste updatepakket van het [Microsoft Download Center.](https://www.microsoft.com/download/) Installeer zowel de x86- als de x64-versies.

4. Nadat de bijgewerkte User Portal-software is geïnstalleerd, vergelijkt u de web.config-back-up die u in stap 1 hebt gemaakt met het nieuwe web.config-bestand. Als er geen nieuwe kenmerken bestaan in het nieuwe web.config, kopieert u uw back-up web.config naar de virtuele map om de nieuwe te overschrijven. Een andere optie is het kopiëren/plakken van de appInstellingen waarden en de Web Service SDK URL van het back-upbestand naar het nieuwe web.config.

Als u de gebruikersportal op meerdere servers hebt, herhaalt u de installatie op alle servers.

## <a name="upgrade-the-mobile-app-web-service"></a>De webservice voor mobiele apps upgraden

> [!NOTE]
> Wanneer u een upgrade uitvoert van een versie van Azure MFA Server ouder dan 8,0 naar 8,0+, kan de webservice voor mobiele apps na de upgrade worden verwijderd

## <a name="upgrade-the-ad-fs-adapters"></a>De AD FS-adapters upgraden

Voltooi de upgrade van uw MFA-servers en gebruikersportal voordat u naar deze sectie gaat.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Als MFA op andere servers dan AD FS wordt uitgevoerd

Deze instructies zijn alleen van toepassing als u multifactorverificatieserver afzonderlijk van uw AD FS-servers uitvoert. Als beide services op dezelfde servers worden uitgevoerd, slaat u deze sectie over en gaat u naar de installatiestappen. 

1. Sla een kopie op van het bestand MultiFactorAuthenticationAdfsAdapter.config dat is geregistreerd in AD `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`FS of exporteer de configuratie met de volgende powershell-opdracht: . De naam van de adapter is "WindowsAzureMultiFactorAuthentication" of "AzureMfaServerAuthentication", afhankelijk van de eerder geïnstalleerde versie.
2. Kopieer de volgende bestanden vanaf de installatielocatie van de MFA-server naar de AD FS-servers:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Bewerk het script Register-MultiFactorAuthenticationAdfsAdapter.ps1 door aan `Register-AdfsAuthenticationProvider` het einde van de opdracht toe te voegen. `-ConfigurationFilePath [path]` Vervang *[pad]* door het volledige pad naar het multifactorauthenticationAdfsAdapter.config-bestand of het configuratiebestand dat in de vorige stap is geëxporteerd.

   Controleer de kenmerken in de nieuwe MultiFactorAuthenticationAdfsAdapter.config om te zien of ze overeenkomen met het oude config-bestand. Als er kenmerken zijn toegevoegd of verwijderd in de nieuwe versie, kopieert u de kenmerkwaarden uit het oude configuratiebestand naar het nieuwe bestand of wijzigt u het oude configuratiebestand dat overeenkomt.

### <a name="install-new-ad-fs-adapters"></a>Nieuwe AD FS-adapters installeren

> [!IMPORTANT]
> Uw gebruikers hoeven tijdens stap 3-8 van deze sectie geen verificatie in twee stappen uit te voeren. Als ad FS is geconfigureerd in meerdere clusters, u elk cluster in de farm onafhankelijk van de andere clusters verwijderen, upgraden en herstellen om downtime te voorkomen.

1. Verwijder sommige AD FS-servers uit de farm. Werk deze servers bij terwijl de andere servers nog actief zijn.
2. Installeer de nieuwe AD FS-adapter op elke server die uit de AD FS-farm is verwijderd. Als de MFA-server op elke AD FS-server is geïnstalleerd, u deze bijwerken via de UX voor MFA Server-beheerders. Anders wordt u bijgewerkt door MultiFactorAuthenticationAdfsAdapterSetup64.msi uit te voeren.

   Als er een fout optreedt met de vermelding 'Microsoft Visual C++ 2015 Redistributable Update 1 of hoger is vereist', download en installeer t u het nieuwste updatepakket van het [Microsoft Download Center.](https://www.microsoft.com/download/) Installeer zowel de x86- als de x64-versies.

3. Ga naar **AD FS-verificatiebeleid** > **Authentication Policies** > **Bewerk het globale multifactorverificatiebeleid**. Schakel de controle **uit over WindowsAzureMultiFactorAuthentication** of **AzureMFAServerAuthentication** (afhankelijk van de huidige geïnstalleerde versie).

   Zodra deze stap is voltooid, is verificatie in twee stappen via MFA Server niet beschikbaar in dit AD FS-cluster totdat u stap 8 hebt voltooid.

4. De oudere versie van de AD FS-adapter uitschrijven door het PowerShell-script Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 uit te voeren. Controleer of de parameter *-Naam* (WindowsAzureMultiFactorAuthentication" of "AzureMFAServerAuthentication") overeenkomt met de naam die in stap 3 is weergegeven. Dit geldt voor alle servers in hetzelfde AD FS-cluster, omdat er een centrale configuratie is.
5. Registreer de nieuwe AD FS-adapter door het PowerShell-script register-MultiFactorAuthenticationAdfsAdapter.ps1 uit te voeren. Dit geldt voor alle servers in hetzelfde AD FS-cluster, omdat er een centrale configuratie is.
6. Start de AD FS-service opnieuw op elke server die is verwijderd uit de AD FS-farm.
7. Voeg de bijgewerkte servers terug naar de AD FS-farm en verwijder de andere servers uit de farm.
8. Ga naar **AD FS-verificatiebeleid** > **Authentication Policies** > **Bewerk het globale multifactorverificatiebeleid**. **AzureMfaServerAuthentication controleren**.
9. Herhaal stap 2 om de servers bij te werken die nu zijn verwijderd uit de AD FS-farm en start de AD FS-service opnieuw op die servers.
10. Voeg deze servers weer toe aan de AD FS-farm.

## <a name="next-steps"></a>Volgende stappen

* Voorbeelden van [geavanceerde scenario's met Azure Multi-Factor Authentication en VPN's van derden](howto-mfaserver-nps-vpn.md)

* [MFA-server synchroniseren met Active Directory van Windows Server](howto-mfaserver-dir-ad.md)

* [Windows-verificatie configureren](howto-mfaserver-windows.md) voor uw toepassingen
