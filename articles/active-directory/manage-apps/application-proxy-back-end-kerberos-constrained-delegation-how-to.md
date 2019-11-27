---
title: Problemen oplossen met beperkte Kerberos-overdracht-app proxy
description: Beperkte Kerberos-delegering configuraties voor Application Proxy oplossen
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275682"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Kerberos-beperkte overdracht configuraties voor Application Proxy oplossen

De methoden die beschikbaar zijn voor het bereiken van eenmalige aanmelding tot gepubliceerde toepassingen kunnen variëren van één toepassing naar een andere. Een optie die toepassingsproxy van Azure Active Directory (Azure AD) standaard biedt is Kerberos-beperkte delegatie (KCD). U kunt een connector voor uw gebruikers, voor het uitvoeren van beperkte Kerberos-verificatie naar de back-end-toepassingen configureren.

De procedure voor het inschakelen van KCD is vrij eenvoudig. Hiervoor niet meer dan een algemeen begrip van de verschillende onderdelen en verificatiestroom die ondersteuning bieden voor eenmalige aanmelding. Maar soms KCD SSO niet werkt zoals verwacht. U moet een goede bronnen met informatie voor het oplossen van deze scenario's.

In dit artikel biedt een single point of verwijzing die helpt bij het oplossen en een aantal van de meest voorkomende problemen zelf te verhelpen. Dit geldt ook voor diagnose van meer complexe problemen van de implementatie.

In dit artikel worden de volgende veronderstellingen:

- De implementatie van Azure AD-toepassingsproxy per aan de [slag met toepassings proxy](application-proxy-add-on-premises-application.md) en algemene toegang tot niet-KCD toepassingen werkt zoals verwacht.
- De gepubliceerde doeltoepassing is gebaseerd op Internet Information Services (IIS) en de Microsoft-implementatie van Kerberos.
- De server- en -hosts zich bevinden in één Azure Active Directory-domein. Voor gedetailleerde informatie over scenario's tussen domeinen en forests raadpleegt u het [technisch document KCD](https://aka.ms/KCDPaper).
- De desbetreffende toepassing wordt gepubliceerd in een Azure-tenant met vooraf-verificatie ingeschakeld. Gebruikers worden verwacht voor verificatie op Azure via verificatie op basis van formulieren. Rich client verificatiescenario's niet zijn gekoppeld aan dit artikel. Ze kunnen worden toegevoegd op een bepaald moment in de toekomst.

## <a name="prerequisites"></a>Vereisten

Azure AD Application Proxy kan worden geïmplementeerd in tal van infrastructuren of omgevingen. De architecturen variëren in organisaties. De meest voorkomende oorzaken van problemen met KCD zijn de omgevingen niet. Eenvoudige onjuiste configuraties of algemene fouten kunt u de meeste problemen veroorzaken.

Daarom is het raadzaam om ervoor te zorgen dat u voldoet aan alle vereisten in [het gebruik van KCD SSO met de toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md) voordat u begint met het oplossen van problemen. Houd er rekening mee de sectie over het configureren van beperkte Kerberos-overdracht op 2012R2. Dit proces maakt gebruik van een andere benadering voor het configureren van KCD in eerdere versies van Windows. Daarnaast moet u zich ervan bewust van deze overwegingen:

- Het is niet ongebruikelijk dat voor een server in het domein voor het openen van een beveiligd kanaal-dialoogvenster met een specifieke domeincontroller (DC). De server kan vervolgens verplaatsen naar een ander dialoogvenster op elk moment. Connector-hosts zijn dus niet beperkt tot communicatie met alleen specifieke lokale site DC's.
- Interdomein-scenario's, is afhankelijk van verwijzingen die rechtstreeks van een connector-host aan DC's die mogelijk buiten de perimeter van het lokale netwerk. In dergelijke gevallen is het belangrijk ook ten minste verkeer verzenden naar DC's waarbij andere domeinen. Als dat niet het geval is, overdracht is mislukt.
- Waar mogelijk, te voorkomen dat alle actieve apparaten voor de IP-Adressen of -id's tussen de connector-hosts en -DC's plaatsen. Deze apparaten zijn soms te verstorend en veroorzaken een conflict met het kern RPC-verkeer.

Delegering in eenvoudige scenario's te testen. Meer variabelen introduceren, hoe meer u wellicht met concurreren. Om tijd te besparen, Beperk de test met een één-connector. Extra connectors toevoegen nadat het probleem is opgelost.

Sommige omgevingsfactoren mogelijk ook bijdragen aan een probleem. Om te voorkomen dat deze factoren, architectuur zo veel mogelijk tijdens het testen te minimaliseren. Bijvoorbeeld, onjuist geconfigureerde firewall ACL's voor interne gemeen hebben. Indien mogelijk alle verkeer verzenden van een connector rechtstreeks naar de DC's en de back-endtoepassing.

De beste locatie voor het plaatsen van connectors is zo dicht mogelijk bij hun doelen. Een firewall die zich in line bevindt bij het testen van onnodige complexiteit toegevoegd en uw onderzoek kan duren.

Wat er wordt een probleem met KCD? Er zijn verschillende algemene aanwijzingen KCD SSO is mislukt. De eerste tekens van een probleem weergegeven in de browser.

![Voor beeld: onjuiste KCD-configuratie fout](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Voor beeld: autorisatie is mislukt vanwege ontbrekende machtigingen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Beide van deze installatiekopieën de dezelfde symptoom weergeven: fout met eenmalige aanmelding. Gebruikerstoegang tot de toepassing is geweigerd.

## <a name="troubleshooting"></a>Problemen oplossen

Hoe u problemen oplossen, is afhankelijk van het probleem en de problemen die u ziet. Voordat u een verder gaat, moet u de volgende artikelen verkennen. Ze bieden nuttige probleemoplossingsinformatie:

- [Problemen met toepassings proxy en fout berichten oplossen](application-proxy-troubleshoot.md)
- [Kerberos-fouten en-symptomen](application-proxy-troubleshoot.md#kerberos-errors)
- [Werken met SSO wanneer on-premises en Cloud-identiteiten niet identiek zijn](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Als u hebt gekregen tot op dit moment, is uw belangrijkste probleem. Als u wilt starten, scheidt u de stroom in de volgende drie fasen die u kunt oplossen.

### <a name="client-pre-authentication"></a>Vooraf-clientverificatie

De externe gebruiker die heeft geverifieerd naar Azure via een browser. De mogelijkheid om te vooraf worden geverifieerd bij Azure is vereist voor KCD SSO functie. Testen en los deze mogelijkheid als er problemen zijn. De fase van de pre-authenticatie is niet gerelateerd aan KCD of de gepubliceerde toepassing. Het is eenvoudig om op te lossen door het controleren van sanity eventuele verschillen die het onderwerp account in Azure bestaat. Ook controleren of het is niet uitgeschakeld of geblokkeerd. Het foutbericht in de browser is beschrijvende om uit te leggen van de oorzaak. Als u niet zeker bent, controleert u andere oplossen van problemen met Microsoft-artikelen om te controleren.

### <a name="delegation-service"></a>De delegatie-service

De Azure-Proxy-connector die opgehaald van een Kerberos-serviceticket voor gebruikers van een Kerberos Key Distribution Center (KCD).

De externe communicatie tussen de client en de Azure-front-end hebben geen gevolgen voor KCD. Deze communicatie alleen Zorg dat KCD werkt. De Azure-Proxy-service is een geldige gebruikers-ID die wordt gebruikt om op te halen van een Kerberos-ticket opgegeven. Zonder deze ID KCD niet mogelijk is en is mislukt.

Zoals eerder vermeld, biedt de foutberichten browser sommige aflezen waarom dingen mislukken. Zorg ervoor dat u Noteer de activiteits-ID en tijdstempel in het antwoord. Deze informatie helpt u het gedrag op werkelijke gebeurtenissen in het gebeurtenislogboek van de Azure-Proxy correleren.

![Voor beeld: onjuiste KCD-configuratie fout](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

De overeenkomstige vermeldingen weergegeven in het gebeurtenislogboek weergeven als gebeurtenissen 13019 of 12027. Zoek de gebeurtenis logboeken van de connector in de **Logboeken toepassingen en Services** &gt; **micro soft** &gt; **AadApplicationProxy** &gt; **connector** &gt;- **beheerder**.

![Gebeurtenis 13019 uit Application Proxy-gebeurtenislogboek](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Gebeurtenis 12027 uit Application Proxy-gebeurtenislogboek](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Gebruik een **A** -record in uw interne DNS voor het adres van de toepassing, niet een **CNAME**.
1. Bevestig dat de connector-host het recht om te delegeren aan de aangewezen doelaccount SPN is toegekend. Bevestig dat **een verificatie protocol gebruiken** is geselecteerd. Zie het artikel over SSO- [configuratie](application-proxy-configure-single-sign-on-with-kcd.md)voor meer informatie.
1. Controleer of er slechts één exemplaar van de SPN-naam bestaat in Azure AD. Voer `setspn -x` uit vanaf een opdracht prompt op een domeinlid die lid is van een domein.
1. Controleer of er een domein beleid wordt afgedwongen waardoor de [maximale grootte van de uitgegeven Kerberos-tokens](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)wordt beperkt. Dit beleid wordt de connector van het ophalen van een token als deze kan worden gevonden op zeer hoog worden gestopt.

Een netwerktracering waarmee de uitwisseling tussen de connector-host en een KDC-domein wordt vastgelegd is de volgende aanbevolen stap voor meer op laag niveau informatie krijgen over de problemen. Zie het [artikel grondige problemen oplossen](https://aka.ms/proxytshootpaper)voor meer informatie.

Als ticketing er goed uitziet, ziet u een gebeurtenis in de logboeken met de mededeling dat de verificatie is mislukt, omdat de toepassing een 401 geretourneerd. Deze gebeurtenis geeft aan dat de doeltoepassing uw ticket geweigerd. Ga naar de volgende fase.

### <a name="target-application"></a>Doeltoepassing

De gebruiker van het Kerberos-ticket dat is opgegeven door de connector. In deze fase wordt verwacht dat de connector naar een Kerberos-serviceticket hebt verzonden naar de back-end. Dit ticket is een kop in de aanvraag van de eerste.

1. Valideren dat de toepassing rechtstreeks vanuit de browser op de host van de connector toegankelijk is via de interne URL van de toepassing die is gedefinieerd in de portal. Vervolgens kunt u zich is. Meer informatie vindt u op de pagina connector **problemen oplossen** .
1. Blijf op de host connector bevestigen dat de verificatie tussen de browser en de toepassing maakt gebruik van Kerberos. Voer een van de volgende acties:
1. Voer DevTools (**F12**) uit in Internet Explorer of gebruik [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) van de host van de connector. Ga naar de toepassing met behulp van de interne URL. Inspecteer de aangeboden www-autorisatie-header die worden geretourneerd in het antwoord van de toepassing om ervoor te zorgen dat een onderhandelen over of Kerberos aanwezig is.

   - De volgende Kerberos-blob die in het antwoord van de browser naar de toepassing wordt geretourneerd, begint met **YII**. Deze letters laat u weten dat Kerberos wordt uitgevoerd. Micro soft NT LAN Manager (NTLM), aan de andere kant, begint altijd met **TlRMTVNTUAAB**, die een NTLM-beveiligings provider (NTLMSSP) leest bij het decoderen van base64. Als u **TlRMTVNTUAAB** ziet aan het begin van de blob, is Kerberos niet beschikbaar. Als u **TlRMTVNTUAAB**niet ziet, is Kerberos waarschijnlijk beschikbaar.

      > [!NOTE]
      > Als u Fiddler gebruikt, wordt deze methode moet u uitgebreide beveiliging van de configuratie van de toepassing in IIS tijdelijk uitschakelen.

      ![Venster van de controle van de browser](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - De BLOB in deze afbeelding begint niet met **TIRMTVNTUAAB**. In dit voor beeld is Kerberos beschikbaar en wordt de Kerberos-BLOB niet gestart met **YII**.

1. Tijdelijk NTLM uit de lijst met providers op de IIS-site verwijderen. Toegang tot de app rechtstreeks vanuit Internet Explorer op de host van de connector. NTLM is niet langer in de lijst met providers. U kunt toegang tot de toepassing met behulp van Kerberos alleen. Als de toegang is mislukt, is het mogelijk dat er een probleem met de configuratie van de toepassing. Kerberos-verificatie wordt niet werkt.

   - Als Kerberos niet beschikbaar is, controleert u de verificatie-instellingen van de toepassing in IIS. Zorg ervoor dat **Negotiate** bovenaan wordt weer gegeven, met NTLM alleen. Als u **niet onderhandelen**, **Kerberos of Negotiate**of **PKU2U**ziet, moet u alleen door gaan als Kerberos functioneel is.

     ![Windows authentication-providers](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Met Kerberos en NTLM in plaats, kunt u vooraf-verificatie voor de toepassing in de portal tijdelijk uitschakelen. Probeer te openen vanaf het internet met behulp van de externe URL. U wordt gevraagd om te verifiëren. U kunt dit doen met hetzelfde account gebruikt in de vorige stap. Als dat niet het geval is, er is een probleem met de back-endtoepassing, niet KCD.
   - Vooraf-verificatie in de portal opnieuw inschakelen. Er wordt geprobeerd verbinding maken met de toepassing via de externe URL voor het verifiëren via Azure. Als eenmalige aanmelding is mislukt, ziet u een niet-toegestane foutbericht wordt weergegeven in de browser en de gebeurtenis 13022 in het logboek:

     *Micro soft AAD Application proxy connector kan de gebruiker niet verifiëren omdat de back-endserver reageert op Kerberos-verificatie pogingen met een HTTP 401-fout.*

      ![Hiermee wordt HTTTP 401 verboden fout weer gegeven](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Controleer de IIS-toepassing. Zorg ervoor dat de geconfigureerde groep van toepassingen en de SPN-naam zijn geconfigureerd voor het gebruik van hetzelfde account in Azure AD. Navigeer in IIS, zoals wordt weergegeven in de volgende afbeelding:

      ![Venster voor configuratie van IIS-toepassing](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Nadat u de identiteit, zorg er dan voor dat dit account is geconfigureerd met de betreffende SPN-naam. Een voorbeeld is `setspn –q http/spn.wacketywack.com`. Voer de volgende tekst in een opdrachtprompt:

      ![Hiermee wordt het SetSPN-opdracht venster weer gegeven](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Controleer de SPN-naam gedefinieerd voor de instellingen van de toepassing in de portal. Zorg ervoor dat de dezelfde SPN-naam geconfigureerd op de doel-Azure AD-account wordt gebruikt door de toepassingsgroep van de app.

      ![SPN-configuratie in Azure portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Ga naar IIS en selecteer de optie **Configuratie-editor** voor de toepassing. Navigeer naar **System. webserver/Security/Authentication/windowsAuthentication**. Zorg ervoor dat de waarde **UseAppPoolCredentials** is ingesteld op **True**.

      ![Referentie-optie voor app-groepen van IIS-configuratie](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Wijzig deze waarde in **True**. Alle in de cache Kerberos-tickets van de back-end-server verwijderen door de volgende opdracht uit:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Zie [de Kerberos-client ticket cache voor alle sessies leegmaken](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)voor meer informatie.

Als u kernelmodus ingeschakeld laat, verbetert de prestaties van Kerberos-bewerkingen. Maar het ook ervoor zorgt dat het ticket voor de aangevraagde service om te worden ontsleuteld met behulp van het computeraccount. Dit account wordt ook het lokale systeem genoemd. Stel deze waarde in op **True** om KCD te verbreekt wanneer de toepassing wordt gehost op meer dan één server in een farm.

- Schakel als extra controle ook **uitgebreide** beveiliging uit. In sommige scenario's is **uitgebreide** beveiliging KCD wanneer het is ingeschakeld in specifieke configuraties. In deze gevallen is een toepassing worden gepubliceerd als een submap van de standaardwebsite. Deze toepassing is geconfigureerd voor alleen anonieme verificatie. Alle dialogen grijs weergegeven, wat erop duidt wouldn't eventuele actieve instellingen worden overgenomen door onderliggende objecten. Als dat mogelijk is, wordt u aangeraden deze waarde te testen, maar niet te verg eten om deze in te **scha kelen**.

  Deze aanvullende controle begeeft u zich op een route naar uw gepubliceerde toepassing gebruiken. U kunt aanvullende connectors die zijn geconfigureerd voor het delegeren van instellen. Lees voor meer informatie de uitgebreide technische [procedure voor het oplossen van problemen met de Azure-AD-toepassingsproxy](https://aka.ms/proxytshootpaper).

Als u nog steeds kan niet wordt uitgevoerd, moet op Microsoft ondersteuning u kan helpen. Maak een ondersteuningsticket rechtstreeks vanuit de portal. Een technicus neemt contact met u.

## <a name="other-scenarios"></a>Andere scenario's

- Azure Application Proxy een Kerberos-ticket-aanvragen voordat de aanvraag wordt verzonden naar een toepassing. Sommige toepassingen van derden vallen niet zoals deze verificatie methode. Deze toepassingen wordt verwacht dat de meer traditionele onderhandelingen moet plaatsvinden. De eerste aanvraag is anoniem, waarmee de toepassing om te reageren met de verificatietypen ondersteunt via een 401.
- Multihop-verificatie wordt doorgaans gebruikt in scenario's waarin een toepassing is gelaagd, met een back-end en een front-end, waarbij zowel verificatie, zoals SQL Server Reporting Services is vereist. Als u het scenario met meerdere hops wilt configureren, raadpleegt u het ondersteunings artikel [Kerberos-beperkte overdracht vereist mogelijk protocol overgang in scenario's met meerdere hops](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Volgende stappen

[KCD configureren voor een beheerd domein](../../active-directory-domain-services/deploy-kcd.md).
