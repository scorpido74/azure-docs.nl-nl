---
title: Kerberos beperkte delegatie oplossen - App-proxy
description: Problemen met Kerberos Constrained Delegation-configuraties voor toepassingsproxy oplossen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275682"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Problemen met Kerberos beperkte delegatieconfiguraties voor toepassingsproxy oplossen

De methoden die beschikbaar zijn voor het bereiken van SSO tot gepubliceerde toepassingen kunnen van toepassing tot toepassing verschillen. Een optie die Azure Active Directory (Azure AD) Application Proxy standaard biedt, is Kerberos constrained delegation (KCD). U een connector voor uw gebruikers configureren om beperkte Kerberos-verificatie uit te voeren voor back-endtoepassingen.

De procedure voor het inschakelen van KCD is eenvoudig. Het vereist niet meer dan een algemeen begrip van de verschillende componenten en authenticatie stroom die SSO ondersteunen. Maar soms werkt KCD SSO niet zoals verwacht. U hebt goede informatiebronnen nodig om deze scenario's op te lossen.

Dit artikel biedt een enkel referentiepunt dat helpt bij het oplossen en zelf herstellen van enkele van de meest voorkomende problemen. Het omvat ook de diagnose van complexere uitvoeringsproblemen.

In dit artikel worden de volgende aannames aangenomen:

- Implementatie van Azure AD Application Proxy per [Aan de slag met Application Proxy](application-proxy-add-on-premises-application.md) en algemene toegang tot niet-KCD-toepassingen werken zoals verwacht.
- De gepubliceerde doeltoepassing is gebaseerd op Internet Information Services (IIS) en de Microsoft-implementatie van Kerberos.
- De server- en toepassingshosts bevinden zich in één Azure Active Directory-domein. Zie de whitepaper van [KCD](https://aka.ms/KCDPaper)voor gedetailleerde informatie over scenario's voor domeinen en bossen.
- De onderwerptoepassing wordt gepubliceerd in een Azure-tenant waarvoor pre-authenticatie is ingeschakeld. Van gebruikers wordt verwacht dat ze zich verifiëren bij Azure via verificatie op basis van formulieren. Uitgebreide clientverificatiescenario's worden niet gedekt door dit artikel. Ze kunnen worden toegevoegd op een bepaald punt in de toekomst.

## <a name="prerequisites"></a>Vereisten

Azure AD Application Proxy kan worden geïmplementeerd in vele soorten infrastructuren of omgevingen. De architecturen variëren van organisatie tot organisatie. De meest voorkomende oorzaken van KCD-gerelateerde problemen zijn niet de omgevingen. Eenvoudige verkeerde configuraties of algemene fouten veroorzaken de meeste problemen.

Om deze reden u er het beste voor zorgen dat u aan alle vereisten hebt voldaan bij [het gebruik van KCD SSO met de toepassingsproxy](application-proxy-configure-single-sign-on-with-kcd.md) voordat u problemen oplost. Let op de sectie over het configureren van Kerberos beperkte delegatie op 2012R2. Dit proces maakt gebruik van een andere benadering van het configureren van KCD op eerdere versies van Windows. Houd ook rekening met deze overwegingen:

- Het is niet ongewoon dat een domeinlidserver een dialoogvenster voor een beveiligd kanaal opent met een specifieke domeincontroller (DC). Dan kan de server op een bepaald moment naar een ander dialoogvenster gaan. Connectorhosts zijn dus niet beperkt tot communicatie met alleen specifieke lokale site-DC's.
- Cross-domeinscenario's zijn afhankelijk van verwijzingen die een connectorhost richten op DC's die zich mogelijk buiten de lokale netwerkperimeter bevinden. In deze gevallen is het net zo belangrijk om ook verkeer verder te sturen naar DC's die andere relevante domeinen vertegenwoordigen. Zo niet, dan mislukt de delegatie.
- Plaats waar mogelijk actieve IPS- of IDS-apparaten tussen connectorhosts en DC's. Deze apparaten zijn soms te opdringerig en interfereren met de kern RPC verkeer.

Test delegatie in eenvoudige scenario's. Hoe meer variabelen u introduceert, hoe meer u te kampen zou kunnen hebben met. Om tijd te besparen, beperkt u het testen tot één connector. Voeg extra connectors toe nadat het probleem is opgelost.

Sommige omgevingsfactoren kunnen ook bijdragen aan een probleem. Om deze factoren te voorkomen, minimaliseer t.a.v. architectuur zoveel mogelijk tijdens het testen. Verkeerd geconfigureerde interne firewall-ACL's komen bijvoorbeeld vaak voor. Stuur indien mogelijk al het verkeer van een connector rechtstreeks door naar de DC's en back-endtoepassing.

De beste plaats om connectoren te plaatsen is zo dicht mogelijk bij hun doelen. Een firewall die inline zit bij het testen voegt onnodige complexiteit toe en kan uw onderzoeken verlengen.

Wat toont een KCD probleem? Er zijn verschillende veel voorkomende aanwijzingen dat KCD SSO faalt. De eerste tekenen van een probleem verschijnen in de browser.

![Voorbeeld: onjuiste KCD-configuratiefout](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Voorbeeld: Autorisatie is mislukt vanwege ontbrekende machtigingen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Beide beelden vertonen hetzelfde symptoom: SSO-storing. De toegang van de gebruiker tot de toepassing wordt geweigerd.

## <a name="troubleshooting"></a>Problemen oplossen

Hoe u problemen oplost, hangt af van het probleem en de symptomen die u waarneemt. Voordat u verder gaat, onderzoekt u de volgende artikelen. Ze bieden nuttige informatie over probleemoplossing:

- [Problemen en foutmeldingen van toepassingsproxy oplossen](application-proxy-troubleshoot.md)
- [Kerberos fouten en symptomen](application-proxy-troubleshoot.md#kerberos-errors)
- [Werken met SSO wanneer on-premises en cloudidentiteiten niet identiek zijn](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Als je op dit punt, dan is uw belangrijkste probleem bestaat. Als u wilt beginnen, scheidt u de stroom in de volgende drie fasen die u oplossen.

### <a name="client-pre-authentication"></a>Clientpre-authenticatie

De externe gebruiker die via een browser naar Azure authenticert. De mogelijkheid om vooraf te verifiëren naar Azure is noodzakelijk om KCD SSO te laten functioneren. Test en pak deze mogelijkheid aan als er problemen zijn. De fase voor verificatie is niet gerelateerd aan KCD of de gepubliceerde toepassing. Het is eenvoudig om eventuele verschillen te corrigeren door te controleren of het onderwerpaccount bestaat in Azure. Controleer ook of het niet is uitgeschakeld of geblokkeerd. De foutreactie in de browser is beschrijvend genoeg om de oorzaak uit te leggen. Als u onzeker bent, controleert u andere artikelen voor het oplossen van problemen met Microsoft om te verifiëren.

### <a name="delegation-service"></a>Delegatiedienst

De Azure Proxy-connector die een Kerberos-serviceticket voor gebruikers van een Kerberos Key Distribution Center (KCD) krijgt.

De externe communicatie tussen de client en de Azure-front-end heeft geen invloed op KCD. Deze communicatie zorgt er alleen voor dat KCD werkt. De Azure Proxy-service krijgt een geldige gebruikersnaam die wordt gebruikt om een Kerberos-ticket te krijgen. Zonder deze ID is KCD niet mogelijk en mislukt.

Zoals eerder vermeld, de browser foutmeldingen biedt een aantal goede aanwijzingen over waarom dingen mislukken. Noteer de activiteits-id en de tijdstempel in het antwoord. Met deze informatie u het gedrag correleren met werkelijke gebeurtenissen in het gebeurtenislogboek Azure Proxy.

![Voorbeeld: onjuiste KCD-configuratiefout](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

De bijbehorende vermeldingen in het gebeurtenislogboek worden weergegeven als gebeurtenissen 13019 of 12027. Zoek de logboeken voor verbindingsgebeurtenissen in **logboeken van toepassingen en services** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![Gebeurtenis 13019 uit het gebeurtenislogboek van Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Gebeurtenis 12027 uit gebeurtenislogboek Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Gebruik een **A-record** in uw interne DNS voor het adres van de toepassing, niet een **CName.**
1. Bevestig opnieuw dat de connectorhost het recht heeft gekregen om te delegeren aan de SPN van het aangewezen doelaccount. Bevestig opnieuw dat **Gebruik een verificatieprotocol** is geselecteerd. Zie het [SSO-configuratieartikel](application-proxy-configure-single-sign-on-with-kcd.md)voor meer informatie.
1. Controleer of er slechts één exemplaar van de SPN bestaat in Azure AD. Probleem `setspn -x` vanaf een opdrachtprompt op een domeinlidhost.
1. Controleer of een domeinbeleid wordt afgedwongen dat de [maximale grootte van uitgegeven Kerberos-tokens](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)beperkt. Met dit beleid wordt voorkomen dat de connector een token krijgt als deze buitensporig wordt bevonden.

Een netwerktracering die de uitwisselingen tussen de connectorhost en een domein-KDC vastlegt, is de volgende beste stap om meer low-level details over de problemen te krijgen. Zie voor meer informatie de [deep dive Troubleshoot paper](https://aka.ms/proxytshootpaper).

Als ticketing er goed uitziet, ziet u een gebeurtenis in de logboeken waarin staat dat de verificatie is mislukt omdat de toepassing een 401 heeft geretourneerd. Deze gebeurtenis geeft aan dat de doelaanvraag uw ticket heeft afgewezen. Ga naar de volgende fase.

### <a name="target-application"></a>Doeltoepassing

De consument van het Kerberos-ticket dat door de connector wordt geleverd. Verwacht in dit stadium dat de connector een Kerberos-serviceticket naar de back-end heeft gestuurd. Dit ticket is een header in de eerste aanvraagaanvraag.

1. Door de interne URL van de toepassing te gebruiken die in de portal is gedefinieerd, moet u valideren dat de toepassing rechtstreeks vanuit de browser op de connectorhost toegankelijk is. Dan u zich met succes aanmelden. Details zijn te vinden op de pagina **Problemen met** de connector.
1. Controleer nog steeds op de connectorhost of de verificatie tussen de browser en de toepassing Kerberos gebruikt. Ga in dat geval op een van de volgende manieren te werk:
1. Voer DevTools **(F12)** uit in Internet Explorer of gebruik [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) vanaf de connectorhost. Ga naar de toepassing met behulp van de interne URL. Inspecteer de aangeboden WWW-autorisatiekoppen die in het antwoord van de toepassing zijn geretourneerd om ervoor te zorgen dat er wordt onderhandeld of dat Kerberos aanwezig is.

   - De volgende Kerberos blob die wordt geretourneerd in de reactie van de browser op de toepassing begint met **YII**. Deze brieven vertellen je dat Kerberos draait. Microsoft NT LAN Manager (NTLM) daarentegen begint altijd met **TlRMTVNTUAAB**, dat NTLM Security Support Provider (NTLMSSP) leest wanneer deze is gedecodeerd vanuit Base64. Als u **TlRMTVNTUAAB** aan het begin van de blob ziet, is Kerberos niet beschikbaar. Als u **TlRMTVNTUAAB**niet ziet, is Kerberos waarschijnlijk beschikbaar.

      > [!NOTE]
      > Als u Fiddler gebruikt, moet u met deze methode de uitgebreide beveiliging van de configuratie van de toepassing in IIS tijdelijk uitschakelen.

      ![Inspectievenster browsernetwerk](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - De blob in dit cijfer begint niet met **TIRMTVNTUAAB**. Dus in dit voorbeeld is Kerberos beschikbaar en de Kerberos-blob begint niet met **YII.**

1. NTLM tijdelijk verwijderen uit de providerslijst op de IIS-site. Rechtstreeks vanuit Internet Explorer toegang tot de app op de connectorhost. NTLM staat niet meer in de providerslijst. U hebt toegang tot de toepassing door alleen Kerberos te gebruiken. Als de toegang mislukt, kan er een probleem zijn met de configuratie van de toepassing. Kerberos-verificatie werkt niet.

   - Als Kerberos niet beschikbaar is, controleert u de verificatie-instellingen van de toepassing in IIS. Zorg ervoor dat **Onderhandelen** bovenaan staat, met NTLM ernet onder. Als u **Niet onderhandelen,** **Kerberos of Onderhandelen**of **PKU2U**ziet, gaat u alleen door als Kerberos functioneel is.

     ![Windows-verificatieproviders](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Met Kerberos en NTLM op zijn plaats, tijdelijk uitschakelen pre-authenticatie voor de toepassing in de portal. Probeer toegang te krijgen vanaf het internet met behulp van de externe URL. U wordt gevraagd om te verifiëren. Je dit doen met hetzelfde account dat in de vorige stap is gebruikt. Zo niet, dan is er een probleem met de back-end applicatie, niet KCD.
   - Pre-authenticatie opnieuw inschakelen in de portal. Verifieer via Azure door te proberen verbinding te maken met de toepassing via de externe URL. Als SSO mislukt, ziet u een verboden foutmelding in de browser en gebeurtenis 13022 in het logboek:

     *Microsoft AAD Application Proxy Connector kan de gebruiker niet verifiëren omdat de backendserver reageert op Kerberos-verificatiepogingen met een HTTP 401-fout.*

      ![Toont HTTTP 401 verboden fout](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Controleer de IIS-toepassing. Controleer of de geconfigureerde groep van toepassingen en de SPN zijn geconfigureerd om hetzelfde account in Azure AD te gebruiken. Navigeer in IIS zoals in de volgende afbeelding wordt weergegeven:

      ![Configuratievenster iIS-toepassing](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Nadat u de identiteit hebt weten, moet u ervoor zorgen dat dit account is geconfigureerd met de SPN in kwestie. Een voorbeeld is `setspn –q http/spn.wacketywack.com`. Voer de volgende tekst in een opdrachtprompt in:

      ![Toont het opdrachtvenster SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Controleer de SPN die is gedefinieerd aan de hand van de instellingen van de toepassing in de portal. Zorg ervoor dat dezelfde SPN die is geconfigureerd ten opzichte van het doelAzure AD-account wordt gebruikt door de app-groep van de toepassing.

      ![SPN-configuratie in de Azure-portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Ga naar IIS en selecteer de optie **Configuratie-editor** voor de toepassing. Navigeer naar **system.webServer/security/authentication/windowsAuthentication**. Controleer of de waarde **UseAppPoolCredentials** **waar**is.

      ![De optie Voor het bijvullen van de referentievan IIS-configuratie-apps](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Wijzig deze waarde in **True**. Verwijder alle Kerberos-tickets in de cache van de back-endserver door de volgende opdracht uit te voeren:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Zie [De kerberos-clientticketcache voor alle sessies wissen voor](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)meer informatie.

Als u de kernelmodus ingeschakeld laat, verbetert dit de prestaties van Kerberos-bewerkingen. Maar het zorgt er ook voor dat het ticket voor de gevraagde service wordt gedecodeerd met behulp van de machine-account. Deze rekening wordt ook wel het lokale systeem genoemd. Stel deze waarde in op **True** om KCD te verbreken wanneer de toepassing wordt gehost op meer dan één server in een farm.

- Als extra controle schakelt u **uitgebreide** beveiliging ook uit. In sommige scenario's brak **Uitgebreide** beveiliging KCD toen deze in specifieke configuraties werd ingeschakeld. In die gevallen is een aanvraag gepubliceerd als een submap van de standaardwebsite. Deze toepassing is alleen geconfigureerd voor anonieme verificatie. Alle dialoogvensters worden grijs weergegeven, wat suggereert dat onderliggende objecten geen actieve instellingen zouden overnemen. We raden u aan te testen, maar vergeet niet om deze waarde te herstellen **om ingeschakeld,** waar mogelijk.

  Deze extra controle zet u op schema om uw gepubliceerde toepassing te gebruiken. U extra connectors opdraaien die ook zijn geconfigureerd om te delegeren. Lees voor meer informatie de meer diepgaande technische doorloop, [het oplossen van de Azure AD Application Proxy.](https://aka.ms/proxytshootpaper)

Als u nog steeds geen vooruitgang boeken, kan Microsoft-ondersteuning u helpen. Maak direct binnen de portal een ondersteuningsticket. Een ingenieur neemt contact met je op.

## <a name="other-scenarios"></a>Andere scenario's

- Azure Application Proxy vraagt een Kerberos-ticket aan voordat het verzoek naar een toepassing wordt verzonden. Sommige toepassingen van derden houden niet van deze methode van authenticeren. Deze toepassingen verwachten dat de meer conventionele onderhandelingen zullen plaatsvinden. Het eerste verzoek is anoniem, waardoor de toepassing kan reageren met de verificatietypen die het ondersteunt via een 401.
- Multi-hop-verificatie wordt vaak gebruikt in scenario's waarin een toepassing is gelaagd, met een back-end en front-end, waarbij beide verificatie vereisen, zoals SQL Server Reporting Services. Zie het ondersteuningsartikel [Kerberos Constrained Delegation, dat protocolovergang in multihopscenario's vereist](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul)om het multihopscenario te configureren.

## <a name="next-steps"></a>Volgende stappen

[KCD configureren op een beheerd domein](../../active-directory-domain-services/deploy-kcd.md).
