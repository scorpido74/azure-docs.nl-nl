---
title: Problemen met domein- en SSL-certificaten oplossen
description: Oplossingen zoeken voor de veelvoorkomende problemen die u tegenkomen wanneer u een domein of SSL-certificaat configureert in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: e299821b54692327cbb7d497af0295e3b93658cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966983"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Problemen met domein- en SSL-certificaten oplossen in Azure App Service

In dit artikel worden veelvoorkomende problemen weergegeven die u tegenkomen wanneer u een domein of SSL-certificaat configureert voor uw webapps in Azure App Service. Het beschrijft ook mogelijke oorzaken en oplossingen voor deze problemen.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de [Azure-experts op de MSDN- en Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Certificaatproblemen

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>U geen SSL-certificaat binding aan een app toevoegen 

#### <a name="symptom"></a>Symptoom

Wanneer u een SSL-binding toevoegt, ontvangt u het volgende foutbericht:

"Kan geen SSL-binding toevoegen. Kan geen certificaat instellen voor bestaande VIP omdat een andere VIP dat certificaat al gebruikt."

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u meerdere IP-gebaseerde SSL-bindingen hebt voor hetzelfde IP-adres in meerdere apps. App A heeft bijvoorbeeld een IP-gebaseerde SSL met een oud certificaat. App B heeft een IP-gebaseerde SSL met een nieuw certificaat voor hetzelfde IP-adres. Wanneer u de SSL-binding van de app bijwerkt met het nieuwe certificaat, mislukt deze met deze fout omdat hetzelfde IP-adres wordt gebruikt voor een andere app. 

#### <a name="solution"></a>Oplossing 

Als u dit probleem wilt oplossen, gebruikt u een van de volgende methoden:

- Verwijder de IP-gebaseerde SSL-binding op de app die het oude certificaat gebruikt. 
- Maak een nieuwe IP-gebaseerde SSL-binding die het nieuwe certificaat gebruikt.

### <a name="you-cant-delete-a-certificate"></a>U een certificaat niet verwijderen 

#### <a name="symptom"></a>Symptoom

Wanneer u een certificaat probeert te verwijderen, ontvangt u het volgende foutbericht:

"Kan het certificaat niet verwijderen omdat het momenteel wordt gebruikt in een SSL-binding. De SSL-binding moet worden verwijderd voordat u het certificaat verwijderen.

#### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als een andere app het certificaat gebruikt.

#### <a name="solution"></a>Oplossing

Verwijder de SSL-binding voor dat certificaat uit de apps. Probeer vervolgens het certificaat te verwijderen. Als u het certificaat nog steeds niet verwijderen, verwijdert u de cache van de internetbrowser en opent u de Azure-portal opnieuw in een nieuw browservenster. Probeer vervolgens het certificaat te verwijderen.

### <a name="you-cant-purchase-an-app-service-certificate"></a>U geen App Service-certificaat kopen 

#### <a name="symptom"></a>Symptoom
U geen [Azure App Service-certificaat](./configure-ssl-certificate.md#import-an-app-service-certificate) kopen via de Azure-portal.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing
Dit probleem kan zich om een van de volgende redenen voordoen:

- Het App-serviceplan is gratis of gedeeld. Deze prijsniveaus bieden geen ondersteuning voor SSL. 

    **Oplossing:** Upgrade het App Service-abonnement voor app naar Standaard.

- Het abonnement heeft geen geldige creditcard.

    **Oplossing:** Voeg een geldige creditcard toe aan uw abonnement. 

- De abonnementsaanbieding biedt geen ondersteuning voor de aankoop van een App Service-certificaat zoals Microsoft Student.  

    **Oplossing:** Upgrade uw abonnement. 

- Het abonnement heeft de limiet bereikt van aankopen die zijn toegestaan op een abonnement.

    **Oplossing:** App Service-certificaten hebben een limiet van 10 certificaataankopen voor de abonnementen voor betalen per gebruik en EA. Voor andere abonnementstypen is de limiet 3. Neem contact op met [Azure-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om de limiet te verhogen.
- Het App Service-certificaat is gemarkeerd als fraude. U hebt het volgende foutbericht ontvangen: "Uw certificaat is gemarkeerd voor mogelijke fraude. Het verzoek wordt momenteel herzien. Als het certificaat niet binnen 24 uur bruikbaar is, neemt u contact op met Azure Support."

    **Oplossing:** Als het certificaat is gemarkeerd als fraude en na 24 uur niet is opgelost, voert u de volgende stappen uit:

    1. Meld u aan bij [Azure Portal](https://portal.azure.com).
    2. Ga naar **App Service-certificaten**en selecteer het certificaat.
    3. Selecteer stap 2 **certificaatconfiguratie:** > **domeinverificatie****verifiëren** > . Met deze stap wordt een e-mailbericht naar de Azure-certificaatprovider gestuurd om het probleem op te lossen.

## <a name="custom-domain-problems"></a>Aangepaste domeinproblemen

### <a name="a-custom-domain-returns-a-404-error"></a>Een aangepast domein retourneert een fout van 404 

#### <a name="symptom"></a>Symptoom

Wanneer u naar de site bladert met de aangepaste domeinnaam, ontvangt u het volgende foutbericht:

"Fout 404-Web app niet gevonden."

#### <a name="cause-and-solution"></a>Oorzaak en oplossing

**Oorzaak 1** 

Het aangepaste domein dat u hebt geconfigureerd, mist een CNAME- of A-record. 

**Oplossing voor oorzaak 1**

- Als u een A-record hebt toegevoegd, moet u ervoor zorgen dat er ook een TXT-record wordt toegevoegd. Zie [De A-record maken](./app-service-web-tutorial-custom-domain.md#create-the-a-record)voor meer informatie .
- Als u het hoofddomein niet voor uw app hoeft te gebruiken, raden we u aan een CNAME-record te gebruiken in plaats van een A-record.
- Gebruik niet zowel een CNAME-record als een A-record voor hetzelfde domein. Dit probleem kan een conflict veroorzaken en voorkomen dat het domein wordt opgelost. 

**Oorzaak 2** 

De internetbrowser kan nog steeds het oude IP-adres voor uw domein in cache plaatsen. 

**Oplossing voor oorzaak 2**

De browser wissen. Voor Windows-apparaten u `ipconfig /flushdns`de opdracht uitvoeren. Gebruik [WhatsmyDNS.net](https://www.whatsmydns.net/) om te controleren of uw domein naar het IP-adres van de app wijst. 

### <a name="you-cant-add-a-subdomain"></a>U geen subdomein toevoegen 

#### <a name="symptom"></a>Symptoom

U geen nieuwe hostnaam aan een app toevoegen om een subdomein toe te wijzen.

#### <a name="solution"></a>Oplossing

- Neem contact op met de beheerder van het abonnement om te controleren of u machtigingen hebt om een hostnaam aan de app toe te voegen.
- Als u meer subdomeinen nodig hebt, raden we u aan de domeinhosting te wijzigen in Azure Domain Name Service (DNS). Met Azure DNS u 500 hostnamen aan uw app toevoegen. Zie [Een subdomein toevoegen voor](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)meer informatie.

### <a name="dns-cant-be-resolved"></a>DNS kan niet worden opgelost

#### <a name="symptom"></a>Symptoom

U hebt het volgende foutbericht ontvangen:

"De DNS-record kan niet worden gelokaliseerd."

#### <a name="cause"></a>Oorzaak
Dit probleem treedt op om een van de volgende redenen:

- De periode om te leven (TTL) is niet verstreken. Controleer de DNS-configuratie voor uw domein om de TTL-waarde te bepalen en wacht tot de periode is verlopen.
- De DNS-configuratie is onjuist.

#### <a name="solution"></a>Oplossing
- Wacht 48 uur tot dit probleem zichzelf is opgelost.
- Als u de TTL-instelling in uw DNS-configuratie wijzigen, wijzigt u de waarde in 5 minuten om te zien of dit het probleem oplost.
- Gebruik [WhatsmyDNS.net](https://www.whatsmydns.net/) om te controleren of uw domein naar het IP-adres van de app wijst. Als dit niet het zo is, configureert u de A-record naar het juiste IP-adres van de app.

### <a name="you-need-to-restore-a-deleted-domain"></a>U moet een verwijderd domein herstellen 

#### <a name="symptom"></a>Symptoom
Uw domein is niet meer zichtbaar in de Azure-portal.

#### <a name="cause"></a>Oorzaak 
De eigenaar van het abonnement heeft het domein mogelijk per ongeluk verwijderd.

#### <a name="solution"></a>Oplossing
Als uw domein minder dan zeven dagen geleden is verwijderd, is het domein nog niet begonnen met het verwijderingsproces. In dit geval u hetzelfde domein opnieuw kopen op de Azure-portal onder hetzelfde abonnement. (Typ de exacte domeinnaam in het zoekvak.) Er worden geen kosten meer in rekening gebracht voor dit domein. Als het domein meer dan zeven dagen geleden is verwijderd, neemt u contact op met [Azure-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) voor hulp bij het herstellen van het domein.

## <a name="domain-problems"></a>Domeinproblemen

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>U hebt een SSL-certificaat voor het verkeerde domein gekocht

#### <a name="symptom"></a>Symptoom

U hebt een App Service-certificaat voor het verkeerde domein gekocht. U het certificaat niet bijwerken om het juiste domein te gebruiken.

#### <a name="solution"></a>Oplossing

Verwijder dat certificaat en koop vervolgens een nieuw certificaat.

Als het huidige certificaat dat het verkeerde domein gebruikt, de status 'Uitgegeven' is, wordt er ook een factuur voor dat certificaat in rekening gebracht. App Service-certificaten worden niet terugbetaald, maar u contact opnemen met [Azure-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om te zien of er andere opties zijn. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Een App Service-certificaat is vernieuwd, maar de app toont het oude certificaat 

#### <a name="symptom"></a>Symptoom

Het App Service-certificaat is vernieuwd, maar de app die het App Service-certificaat gebruikt, gebruikt nog steeds het oude certificaat. Ook kreeg u een waarschuwing dat het HTTPS-protocol vereist is.

#### <a name="cause"></a>Oorzaak 
App Service synchroniseert uw certificaat automatisch binnen 48 uur. Wanneer u een certificaat roteert of bijwerkt, wordt het oude certificaat soms nog steeds opgehaald en niet het onlangs bijgewerkte certificaat. De reden hiervoor is dat de taak om de certificaatbron te synchroniseren nog niet is uitgevoerd. Klik op Synchroniseren. De synchronisatiebewerking werkt automatisch de hostnaambindingen voor het certificaat in App-service bij zonder dat uw apps minder tijd uitvalt.
 
#### <a name="solution"></a>Oplossing

U een synchronisatie van het certificaat forceren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **App Service-certificaten**en selecteer vervolgens het certificaat.
2. Selecteer **Opnieuw sleutelen en Synchroniseren**en selecteer Vervolgens **Synchroniseren**. De synchronisatie duurt enige tijd om te voltooien. 
3. Wanneer de synchronisatie is voltooid, ziet u de volgende melding: 'Alle bronnen met het nieuwste certificaat hebben bijgewerkt'.

### <a name="domain-verification-is-not-working"></a>Domeinverificatie werkt niet 

#### <a name="symptom"></a>Symptoom 
Het App Service-certificaat vereist domeinverificatie voordat het certificaat klaar is voor gebruik. Wanneer u **Verifiëren**selecteert, mislukt het proces.

#### <a name="solution"></a>Oplossing
Verifieer uw domein handmatig door een TXT-record toe te voegen:
 
1.  Ga naar de DNS-provider (Domain Name Service) die uw domeinnaam host.
2.  Voeg een TXT-record toe voor uw domein die de waarde gebruikt van het domeintoken dat wordt weergegeven in de Azure-portal. 

Wacht een paar minuten tot dns-propagatie is uitgevoerd en selecteer vervolgens de knop **Vernieuwen** om de verificatie te activeren. 

Als alternatief u de HTML-webpaginamethode gebruiken om uw domein handmatig te verifiëren. Met deze methode kan de certificaatautoriteit het domeineigendom van het domein bevestigen waarvoor het certificaat is uitgegeven.

1.  Maak een HTML-bestand met de naam {domeinverificatietoken}.html. De inhoud van dit bestand moet de waarde zijn van domeinverificatietoken.
3.  Upload dit bestand aan de basis van de webserver die uw domein host.
4.  Selecteer **Vernieuwen** om de certificaatstatus te controleren. Het kan enkele minuten duren voordat de verificatie is voltooid.

Als u bijvoorbeeld een standaardcertificaat voor azure.com koopt met het domeinverificatietoken 1234abcd, moet een webverzoek om 1234abcd terug te https://azure.com/1234abcd.html sturen. 

> [!IMPORTANT]
> Een certificaatorder heeft slechts 15 dagen om de domeinverificatiebewerking te voltooien. Na 15 dagen weigert de certificaatautoriteit het certificaat en worden er geen kosten in rekening gebracht voor het certificaat. Verwijder in deze situatie dit certificaat en probeer het opnieuw.
>
> 

### <a name="you-cant-purchase-a-domain"></a>U geen domein kopen

#### <a name="symptom"></a>Symptoom
U een App Service-domein niet kopen in de Azure-portal.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing

Dit probleem treedt op om een van de volgende redenen:

- Er is geen creditcard in het Azure-abonnement, of de creditcard is ongeldig.

    **Oplossing:** Voeg een geldige creditcard toe aan uw abonnement.

- U bent niet de eigenaar van het abonnement, dus u bent niet gemachtigd om een domein aan te schaffen.

    **Oplossing:** [Wijs de rol Eigenaar toe](../role-based-access-control/role-assignments-portal.md) aan uw account. Of neem contact op met de abonnementsbeheerder om toestemming te krijgen om een domein aan te schaffen.
- U hebt de limiet voor het aanschaffen van domeinen in uw abonnement bereikt. De huidige limiet is 20.

    **Oplossing:** Neem contact op met [Azure-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om een verhoging tot de limiet aan te vragen.
- Uw type Azure-abonnement biedt geen ondersteuning voor de aanschaf van een App Service-domein.

    **Oplossing:** Upgrade uw Azure-abonnement naar een ander abonnementstype, zoals een Abonnement per gebruik.

### <a name="you-cant-add-a-host-name-to-an-app"></a>U geen hostnaam aan een app toevoegen 

#### <a name="symptom"></a>Symptoom

Wanneer u een hostnaam toevoegt, wordt het domein niet gevalideerd en geverifieerd.

#### <a name="cause"></a>Oorzaak 

Dit probleem treedt op om een van de volgende redenen:

- Je hebt geen toestemming om een hostnaam toe te voegen.

    **Oplossing:** Vraag de abonnementsbeheerder om u toestemming te geven om een hostnaam toe te voegen.
- Uw domeineigendom kan niet worden geverifieerd.

    **Oplossing:** Controleer of uw CNAME- of A-record correct is geconfigureerd. Als u een aangepast domein wilt toewijzen aan een app, maakt u een CNAME-record of een A-record. Als u een hoofddomein wilt gebruiken, moet u A- en TXT-records gebruiken:

    |Recordtype|Host|Wijs aan op|
    |------|------|-----|
    |A|@|IP-adres voor een app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Veelgestelde vragen

**Moet ik mijn aangepaste domein voor mijn website configureren zodra ik het heb gekocht?**

Wanneer u een domein koopt via de Azure-portal, wordt de App Service-toepassing automatisch geconfigureerd om dat aangepaste domein te gebruiken. U hoeft geen extra stappen te ondernemen. Bekijk Azure App Service Self Help voor meer [informatie: voeg een aangepaste domeinnaam toe](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) op Channel9.

**Kan ik een domein gebruiken dat is aangeschaft in de Azure-portal om in plaats daarvan een Azure VM aan te wijzen?**

Ja, u het domein naar een vm wijzen. Zie [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Azure DNS gebruiken om aangepaste domeininstellingen te verstrekken voor een Azure-service) voor meer informatie.

**Wordt mijn domein gehost door GoDaddy of Azure DNS?**

App Service-domeinen gebruiken GoDaddy voor domeinregistratie en Azure DNS om de domeinen te hosten. 

**Ik heb automatisch verlengen ingeschakeld, maar nog steeds een verlengingsbericht ontvangen voor mijn domein via e-mail. Wat moet ik doen?**

Als u automatisch verlengen hebt ingeschakeld, hoeft u geen actie te ondernemen. De e-mail van de kennisgeving wordt verstrekt om u te informeren dat het domein bijna verloopt en om handmatig te verlengen als automatisch verlengen niet is ingeschakeld.

**Worden er kosten in rekening gebracht voor Azure DNS die mijn domein host?**

De initiële kosten van domeinaankoop zijn alleen van toepassing op domeinregistratie. Naast de registratiekosten zijn er kosten verbonden aan Azure DNS op basis van uw gebruik. Zie [Azure DNS-prijzen](https://azure.microsoft.com/pricing/details/dns/) voor meer informatie voor meer informatie.

**Ik heb mijn domein eerder gekocht via de Azure-portal en wil overstappen van GoDaddy-hosting naar Azure DNS-hosting. Hoe kan ik dit doen?**

Het is niet verplicht om te migreren naar Azure DNS-hosting. Als u wilt migreren naar Azure DNS, biedt de domeinbeheerervaring in de Azure-portal informatie over de stappen die nodig zijn om naar Azure DNS te gaan. Als het domein is gekocht via App Service, is migratie van GoDaddy-hosting naar Azure DNS een relatief naadloze procedure.

**Ik wil mijn domein kopen via App Service Domain, maar kan ik mijn domein hosten op GoDaddy in plaats van Azure DNS?**

Vanaf 24 juli 2017 worden App Service-domeinen die in de portal zijn aangeschaft, gehost op Azure DNS. Als u liever een andere hostingprovider gebruikt, moet u naar hun website gaan om een domeinhostingoplossing te verkrijgen.

**Moet ik betalen voor privacybescherming voor mijn domein?**

Wanneer u een domein koopt via de Azure-portal, u ervoor kiezen om zonder extra kosten privacy toe te voegen. Dit is een van de voordelen van de aankoop van uw domein via Azure App Service.

**Als ik besluit dat ik mijn domein niet meer wil, kan ik dan mijn geld terugkrijgen?**

Wanneer u een domein koopt, worden er gedurende een periode van vijf dagen geen kosten in rekening gebracht, gedurende welke tijd u beslissen dat u het domein niet wilt. Als u besluit dat u het domein niet binnen die periode van vijf dagen wilt, worden er geen kosten in rekening gebracht. (.uk domeinen zijn hierop een uitzondering. Als u een .uk-domein koopt, worden er onmiddellijk kosten in rekening gebracht en u niet worden terugbetaald.)

**Kan ik het domein gebruiken in een andere Azure App Service-app in mijn abonnement?**

Ja. Wanneer u het aangepaste domeinen en ssl-blad opent in de Azure-portal, ziet u de domeinen die u hebt gekocht. U uw app configureren om een van deze domeinen te gebruiken.

**Kan ik een domein van het ene abonnement naar een ander abonnement overzetten?**

U een domein verplaatsen naar een andere abonnementsgroep/resourcegroep met de [cmdlet Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell.

**Hoe kan ik mijn aangepaste domein beheren als ik momenteel geen Azure App Service-app heb?**

U uw domein beheren, zelfs als u geen Web App-web-app hebt. Domein kan worden gebruikt voor Azure-services zoals Virtual machine, Storage etc. Als u van plan bent het domein te gebruiken voor Web Apps van App Service, moet u een web-app opnemen die niet op het gratis app-serviceplan staat om het domein aan uw web-app te binden.

**Kan ik een web-app met een aangepast domein verplaatsen naar een ander abonnement of van App Service Environment v1 naar V2?**

Ja, u uw web-app verplaatsen tussen abonnementen. Volg de richtlijnen in [Hoe u resources in Azure verplaatsen.](../azure-resource-manager/management/move-resource-group-and-subscription.md) Er zijn een paar beperkingen bij het verplaatsen van de web-app. Zie [Beperkingen voor het verplaatsen van App Service-bronnen voor](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)meer informatie.

Na het verplaatsen van de web-app moeten de hostnaambindingen van de domeinen binnen de instelling aangepaste domeinen hetzelfde blijven. Er zijn geen extra stappen nodig om de hostnaambindingen te configureren.
