---
title: Problemen met Azure CDN-eindpunten oplossen - 404-statuscode
description: Problemen oplossen met 404-antwoordcodes met Azure CDN-eindpunten.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083119"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Problemen met Azure CDN-eindpunten oplossen die een statuscode van 404 retourneren
In dit artikel u problemen met CDN-eindpunten (Azure Content Delivery Network) oplossen die 404 HTTP-antwoordstatuscodes retourneren.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

## <a name="symptom"></a>Symptoom
U hebt een CDN-profiel en een eindpunt gemaakt, maar uw inhoud lijkt niet beschikbaar te zijn op het CDN. Gebruikers die via de CDN-URL toegang tot uw inhoud proberen te krijgen, ontvangen een HTTP 404-statuscode. 

## <a name="cause"></a>Oorzaak
Er zijn verschillende mogelijke oorzaken, waaronder:

* De oorsprong van het bestand is niet zichtbaar voor het CDN.
* Het eindpunt is verkeerd geconfigureerd, waardoor het CDN op de verkeerde plaats kijkt.
* De host weigert de hostheader van het CDN.
* Het eindpunt heeft geen tijd gehad om zich door het CDN te verspreiden.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
> [!IMPORTANT]
> Na het maken van een CDN-eindpunt is het niet onmiddellijk beschikbaar voor gebruik, omdat het tijd kost voordat de registratie zich door het CDN verspreidt:
> - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen tien minuten voltooid. 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden doorgaans binnen 90 minuten doorgegeven, maar in sommige gevallen kan dit langer duren. 
> 
> Als u de stappen in dit document voltooit en u nog steeds 404 reacties krijgt, u overwegen om een paar uur te wachten om het opnieuw te controleren voordat u een ondersteuningsticket opent.
> 
> 

### <a name="check-the-origin-file"></a>Het oorsprongsbestand controleren
Controleer eerst of het bestand naar cache beschikbaar is op de oorsprongsserver en openbaar toegankelijk is op internet. De snelste manier om dat te doen is door een browser te openen in een privé- of incognitosessie en rechtstreeks naar het bestand te bladeren. Typ of plak de URL in het adresvak en controleer of deze resulteert in het bestand dat u verwacht. Stel dat u een bestand hebt in een Azure\/Storage-account dat toegankelijk is op https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Als u de inhoud van dit bestand met succes laden, slaagt het voor de test.

![Gelukt!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Hoewel dit de snelste en eenvoudigste manier is om te controleren of uw bestand openbaar beschikbaar is, kunnen sommige netwerkconfiguraties in uw organisatie het lijkt alsof een bestand openbaar beschikbaar is wanneer het in feite alleen zichtbaar is voor gebruikers van uw netwerk (zelfs als het wordt gehost in Azure). Als u ervoor wilt zorgen dat dit niet het geval is, test u het bestand met een externe browser, zoals een mobiel apparaat dat niet is verbonden met het netwerk van uw organisatie of een virtuele machine in Azure.
> 
> 

### <a name="check-the-origin-settings"></a>De oorsprongsinstellingen controleren
Nadat u hebt geverifieerd dat het bestand openbaar beschikbaar is op internet, controleert u uw oorsprongsinstellingen. Blader in de [Azure Portal](https://portal.azure.com)naar uw CDN-profiel en selecteer het eindpunt dat u probleemoplossing biedt. Selecteer op de resulterende **eindpuntpagina** de oorsprong.  

![Eindpuntpagina met gemarkeerde oorsprong](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

De **Origin-pagina** wordt weergegeven. 

![Origin-pagina](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Oorsprongstype en hostnaam
Controleer of de waarden van het **origin-type** en de **oorspronkelijke hostnaam** correct zijn. In dit voorbeeld\/wordt https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, het hostnamegedeelte van de URL *cdndocdemo.blob.core.windows.net,* wat juist is. Omdat azure Storage, Web App en Cloud Service origins een vervolgkeuzelijstwaarde gebruiken voor het veld **Origin-hostname,** zijn onjuiste spellingen geen probleem. Als u echter een aangepaste oorsprong gebruikt, moet u ervoor zorgen dat uw hostnaam correct is gespeld.

#### <a name="http-and-https-ports"></a>HTTP- en HTTPS-poorten
Controleer uw **HTTP-** en **HTTPS-poorten**. In de meeste gevallen zijn 80 en 443 correct en hebt u geen wijzigingen nodig.  Echter, als de oorsprong server luistert op een andere poort, dat zal moeten hier worden vertegenwoordigd. Als u dit niet zeker weet, bekijkt u de URL voor uw herkomstbestand. De HTTP- en HTTPS-specificaties gebruiken poorten 80 en 443 als standaardwaarden. In het voorbeeld-URL, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt is een poort niet opgegeven, dus de standaardwaarde van 443 wordt aangenomen en de instellingen correct zijn.  

Stel echter dat de URL voor het oorsprongsbestand\/dat u eerder hebt getest http: /www.contoso.com:8080/file.txt is. Let op het *gedeelte :8080* aan het einde van het segment hostname. Dat nummer instrueert de browser om poort 8080\.te gebruiken om verbinding te maken met de webserver op www contoso.com, daarom moet u *8080* invoeren in het **HTTP-poortveld.** Het is belangrijk op te merken dat deze poortinstellingen alleen van invloed zijn op de poort die het eindpunt gebruikt om informatie van de oorsprong op te halen.

> [!NOTE]
> **Azure CDN Standard van Akamai-eindpunten** staan het volledige TCP-poortbereik voor oorsprong niet toe.  Zie [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Door Azure CDN van Akamai toegestane poorten van oorsprong) voor een lijst met poorten van oorsprong die niet zijn toegestaan.  
> 
> 

### <a name="check-the-endpoint-settings"></a>De eindpuntinstellingen controleren
Selecteer **op** de pagina Eindpunt de knop **Configureren.**

![Eindpuntpagina met knop configureren gemarkeerd](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

De pagina CDN-eindpunt **configureren** wordt weergegeven.

![Pagina configureren](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocollen
Controleer **bij protocollen**of het protocol dat door de clients wordt gebruikt, is geselecteerd. Omdat hetzelfde protocol dat door de client wordt gebruikt, wordt gebruikt om toegang te krijgen tot de oorsprong, is het belangrijk dat de oorsprongspoorten correct zijn geconfigureerd in de vorige sectie. Het CDN-eindpunt luistert alleen op de standaard HTTP- en HTTPS-poorten (80 en 443), ongeacht de oorsprongspoorten.

Laten we terugkeren naar ons hypothetische voorbeeld met http:\//www.contoso.com:8080/file.txt.  Zoals u zich zult herinneren, heeft Contoso *8080* opgegeven als hun HTTP-poort, maar laten we er ook van uitgaan dat ze *44300* als hun HTTPS-poort hebben opgegeven.  Als ze een eindpunt met de naam *contoso*hebben gemaakt, zou hun CDN-eindpunthostnaam *contoso.azureedge.net*zijn.  Een aanvraag voor\/http: /contoso.azureedge.net/file.txt is een HTTP-aanvraag, dus het eindpunt zou HTTP op poort 8080 gebruiken om het van de oorsprong op te halen.  Een beveiligde aanvraag via\/HTTPS, https: /contoso.azureedge.net/file.txt, zou ervoor zorgen dat het eindpunt HTTPS op poort 44300 gebruikt bij het ophalen van het bestand van de oorsprong.

#### <a name="origin-host-header"></a>Host-header van oorsprong
De **Origin-hostheader** is de hostheaderwaarde die bij elke aanvraag naar de oorsprong wordt verzonden.  In de meeste gevallen moet dit hetzelfde zijn als de **Origin-hostnaam die** we eerder hebben geverifieerd.  Een onjuiste waarde in dit veld zal over het algemeen niet leiden tot 404 statussen, maar waarschijnlijk andere 4xx-statussen veroorzaken, afhankelijk van wat de oorsprong verwacht.

#### <a name="origin-path"></a>Pad voor de oorsprong
Ten slotte moeten we ons **Origin-pad**verifiëren.  Standaard is dit leeg.  U moet dit veld alleen gebruiken als u het bereik wilt verkleinen van de door oorsprong gehoste resources die u beschikbaar wilt stellen op het CDN.  

In het voorbeeldeindpunt wilden we dat alle resources op het opslagaccount beschikbaar waren, zodat **origin-pad** leeg bleef.  Dit betekent dat een\/verzoek om https: /cdndocdemo.azureedge.net/publicblob/lorem.txt resulteert in een verbinding van het eindpunt naar cdndocdemo.core.windows.net die */publicblob/lorem.txt aanvraagt.*  Ook een aanvraag voor\/https: /cdndocdemo.azureedge.net/donotcache/status.png resulteert in het eindpunt waarin */donotcache/status.png* van de oorsprong wordt gevraagd.

Maar wat als je het CDN niet voor elk pad op je afkomst wilt gebruiken?  Stel dat je alleen het *publicblobpad* wilde blootleggen.  Als we */publicblob* invoeren in het **veld Origin-pad,** wordt het eindpunt invoegen */publicblob* voordat elk verzoek wordt ingediend bij de oorsprong.  Dit betekent dat het\/verzoek om https: /cdndocdemo.azureedge.net/publicblob/lorem.txt nu daadwerkelijk het aanvraaggedeelte van de URL, */publicblob/lorem.txt*en het toevoegen */publicblob* naar het begin zal brengen. Dit resulteert in een aanvraag voor */publicblob/publicblob/lorem.txt* van de oorsprong.  Als dat pad niet wordt opgelost in een echt bestand, geeft de oorsprong een status van 404 terug.  De juiste URL om lorem.txt in dit\/voorbeeld op te halen zou eigenlijk https zijn: /cdndocdemo.azureedge.net/lorem.txt.  Houd er rekening mee dat we het */publicblob-pad* helemaal niet opnemen, omdat het aanvraaggedeelte van de URL */lorem.txt* is en het eindpunt */publicblob*toevoegt, wat resulteert in */publicblob/lorem.txt* die het verzoek wordt doorgegeven aan de oorsprong.

