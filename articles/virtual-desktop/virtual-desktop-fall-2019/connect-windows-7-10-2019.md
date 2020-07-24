---
title: Verbinding maken met Windows Virtual Desktop najaar 2019 Windows 10 of 7-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de Windows-desktop-client.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bddfe22f70c1044ca02fb41a1959884f41797bb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083545"
---
# <a name="connect-with-the-windows-desktop-client---fall-2019"></a>Verbinding maken met de Windows-desktop client-najaar 2019

> Van toepassing op: Windows 7, Windows 10 en Windows 10 IoT Enter prise

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het najaar van 2019 die geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../connect-windows-7-10.md) als u Azure Resource Manager Windows Virtual Desktop-objecten wilt beheren die zijn geïntroduceerd in de update Lente 2020.

U hebt toegang tot de virtuele Bureau bladen van Windows op apparaten met Windows 7, Windows 10 en Windows 10 IoT Enter prise met behulp van de Windows-bureaubladclient. De client biedt geen ondersteuning voor Windows 8 of Windows 8,1.

>[!NOTE]
>De Windows-client wordt automatisch standaard ingesteld op Windows Virtual Desktop 2019 release. Als de client echter detecteert dat de gebruiker ook Azure Resource Manager resources heeft, worden de resources automatisch toegevoegd of krijgt de gebruiker een melding dat ze beschikbaar zijn.

> [!IMPORTANT]
> Windows Virtual Desktop biedt geen ondersteuning voor de client voor RemoteApp- en bureaubladverbindingen of de client voor verbinding met extern bureaublad.

> [!IMPORTANT]
> Windows Virtual Desktop biedt momenteel geen ondersteuning voor de Extern bureaublad-client uit de Windows Store.

## <a name="install-the-windows-desktop-client"></a>De Windows-Desktop-Client installeren

Kies de client die overeenkomt met uw versie van Windows:

- [Windows 64-bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows-ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

U kunt de client installeren voor de huidige gebruiker, waarvoor geen beheerders rechten zijn vereist, of uw beheerder kan de client installeren en configureren zodat alle gebruikers op het apparaat toegang hebben.

Nadat de client is geïnstalleerd, kan deze vanuit het menu Start worden gestart door te zoeken naar **extern bureaublad**.

## <a name="subscribe-to-a-workspace"></a>Abonneren op een werk ruimte

Er zijn twee manieren waarop u zich kunt abonneren op een werk ruimte. De client kan proberen om de resources te ontdekken die beschikbaar zijn op uw werk-of school account, of u kunt rechtstreeks de URL opgeven waar uw resources zich bevinden voor gevallen waarin de client deze niet heeft gevonden. Zodra u bent geabonneerd op een werk ruimte, kunt u resources starten met een van de volgende methoden:

- Ga naar het verbindings centrum en dubbel klik op een resource om het te starten.
- U kunt ook naar het menu start gaan en zoeken naar een map met de naam van de werk ruimte of de resource naam invoeren in de zoek balk.

### <a name="subscribe-with-a-user-account"></a>Abonneren met een gebruikers account

1. Selecteer op de hoofd pagina van de client **Abonneren**.
2. Meld u aan met uw gebruikers account wanneer u hierom wordt gevraagd.
3. De resources worden weer gegeven in het verbindings centrum en worden gegroepeerd op werk ruimte.

### <a name="subscribe-with-a-url"></a>Abonneren met een URL

1. Selecteer op de hoofd pagina van de client **abonneren met URL**.
2. Voer de URL van de werk ruimte of uw e-mail adres in:
   - Als u de **werk ruimte-URL**gebruikt, gebruikt u de beheerder die u hebt ontvangen. Als u toegang hebt tot resources van virtueel bureau blad van Windows, kunt u een van de volgende Url's gebruiken:
     - Windows virtueel bureau blad tot 2019:`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows virtueel bureau blad lente 2020:`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Als u in plaats daarvan het **e-mail** veld gebruikt, voert u uw e-mail adres in. Dit geeft de client de opdracht om te zoeken naar een URL die is gekoppeld aan uw e-mail adres als uw beheerder [e-mail detectie](/windows-server/remote/remote-desktop-services/rds-email-discovery)heeft ingesteld.
3. Selecteer **Next**.
4. Meld u aan met uw gebruikers account wanneer u hierom wordt gevraagd.
5. De resources moeten worden weer gegeven in het verbindings centrum, gegroepeerd op werk ruimte.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de Windows-desktop-client naar [aan de slag met de Windows desktop-client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
