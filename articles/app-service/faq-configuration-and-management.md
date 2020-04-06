---
title: Veelgestelde vragen over configuratie
description: Krijg antwoorden op veelgestelde vragen over configuratie- en beheerproblemen voor Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 9e7070e925d12df82adbc3683da5b10e48c5d4b0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668121"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over configuratie en beheer voor Web Apps in Azure

In dit artikel vindt u antwoorden op veelgestelde vragen (veelgestelde vragen) over configuratie- en beheerproblemen voor de [Web Apps-functie van Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Zijn er beperkingen waarvan ik me bewust moet zijn als ik appservicebronnen wil verplaatsen?

Als u app-servicebronnen wilt verplaatsen naar een nieuwe brongroep of -abonnement, zijn er een paar beperkingen om rekening mee te houden. Zie [Beperkingen van app-service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)voor meer informatie .

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hoe gebruik ik een aangepaste domeinnaam voor mijn web-app?

Zie onze zeven minuten durende video [Een aangepaste domeinnaam toevoegen](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)voor antwoorden op veelgestelde vragen over het gebruik van een aangepaste domeinnaam met uw Azure-webapp. De video biedt een walkthrough van hoe u een aangepaste domeinnaam toevoegt. Het beschrijft hoe u uw eigen URL gebruikt in plaats van de URL *.azurewebsites.net met uw App Service-webapp. U ook een gedetailleerde walkthrough zien van [hoe u een aangepaste domeinnaam in kaart brengen.](app-service-web-tutorial-custom-domain.md)


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hoe koop ik een nieuw aangepast domein voor mijn web-app?

Zie Een aangepaste domeinnaam kopen [en configureren in App Service](manage-custom-dns-buy-domain.md)voor meer informatie over het kopen en instellen van een aangepast domein voor uw app-webapp.


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hoe upload en configureer ik een bestaand TLS/SSL-certificaat voor mijn web-app?

Zie [Een TLS/SSL-certificaat toevoegen aan uw App Service-app](configure-ssl-certificate.md)voor meer informatie over het uploaden en instellen van een bestaand aangepast TLS/SSL-certificaat.


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hoe koop en configureer ik een nieuw TLS/SSL-certificaat in Azure voor mijn web-app?

Zie [Een TLS/SSL-certificaat toevoegen aan uw App Service-app](configure-ssl-certificate.md)voor meer informatie over het aanschaffen en instellen van een TLS/SSL-certificaat voor uw App Service-webapp.


## <a name="how-do-i-move-application-insights-resources"></a>Hoe verplaats ik de resources van Application Insights?

Azure Application Insights biedt momenteel geen ondersteuning voor de verplaatsingsbewerking. Als uw oorspronkelijke resourcegroep een resource Application Insights bevat, u die bron niet verplaatsen. Als u de bron Application Insights opneemt wanneer u een App Service-app probeert te verplaatsen, mislukt de hele verplaatsingsbewerking. Application Insights en het App Service-abonnement hoeven echter niet in dezelfde brongroep te zitten als de app om de app correct te laten functioneren.

Zie [Beperkingen van app-service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)voor meer informatie .

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Waar vind ik een begeleidingschecklist en meer informatie over resource move-bewerkingen?

[Beperkingen van app-service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) laten zien hoe u resources verplaatst naar een nieuw abonnement of naar een nieuwe brongroep in hetzelfde abonnement. U informatie krijgen over de checklist voor het verplaatsen van resources, welke services de verplaatsingsbewerking ondersteunen en meer informatie krijgen over beperkingen van appservice en andere onderwerpen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hoe stel ik de servertijdzone in voor mijn web-app?

Ga als u de servertijdzone voor uw web-app in:

1. Ga in de Azure-portal in uw App Service-abonnement naar het menu **Toepassingsinstellingen.**
2. Voeg onder **App-instellingen**de gewenste instelling toe:
    * Sleutel = WEBSITE_TIME_ZONE
    * Waarde = *de gewenste tijdzone*
3. Selecteer **Opslaan**.

Zie de kolom **Tijdzone** in het artikel [Standaardtijdzones](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) voor geaccepteerde waarden voor de app-services die op Windows worden uitgevoerd. Voor de App-services die op Linux worden uitgevoerd, stelt u de naam van de [TZ-database](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) in als de tijdzonewaarde. Hier is een voorbeeld van tz database naam: Amerika / Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Waarom mislukken mijn doorlopende WebJobs soms?

Web-apps worden standaard leeggemaakt als ze gedurende een bepaalde periode niet actief zijn. Hierdoor kan het systeem grondstoffen besparen. In basis- en standaardabonnementen u de instelling **Altijd ingeschakeld** inschakelen om de web-app altijd geladen te houden. Als uw web-app continu WebJobs uitvoert, moet u **Altijd inschakelen**of wordt webjobs mogelijk niet betrouwbaar uitgevoerd. Zie [Een continu draaiende WebJob maken](webjobs-create.md#CreateContinuous)voor meer informatie.

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hoe krijg ik het uitgaande IP-adres voor mijn web-app?

Ga als eerste voor op de lijst met uitgaande IP-adressen voor uw web-app:

1. Ga in de Azure-portal in uw web-app-blad naar het menu **Eigenschappen.**
2. Zoek naar **uitgaande ip-adressen**.

De lijst met uitgaande IP-adressen wordt weergegeven.

Zie [Uitgaande netwerkadressen](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)voor meer informatie over het uitgaande IP-adres als uw website wordt gehost in een App Service-omgeving.

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hoe krijg ik een gereserveerd of speciaal inkomend IP-adres voor mijn web-app?

Als u een speciaal of gereserveerd IP-adres wilt instellen voor inkomende oproepen naar uw Azure-app-website, installeert en configureert u een TLS/SSL-certificaat op basis van IP.

Houd er rekening mee dat als u een speciaal of gereserveerd IP-adres wilt gebruiken voor inkomende gesprekken, uw App Service-abonnement in een basis- of hoger serviceabonnement moet zijn.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan ik mijn App Service-certificaat exporteren om buiten Azure te gebruiken, bijvoorbeeld voor een website die elders wordt gehost? 

Ja, u ze exporteren om buiten Azure te gebruiken. Zie [veelgestelde vragen voor App Service-certificaten en aangepaste domeinen voor](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)meer informatie.

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan ik mijn App Service-certificaat exporteren om te gebruiken met andere Azure-cloudservices?

De portal biedt een eersteklas ervaring voor het implementeren van een App Service-certificaat via Azure Key Vault naar App Service-apps. We hebben echter verzoeken ontvangen van klanten om deze certificaten buiten het App Service-platform te gebruiken, bijvoorbeeld met Azure Virtual Machines. Zie [Een lokale PFX-kopie van een App Service-certificaat maken](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)voor meer informatie over het maken van een lokaal PFX-exemplaar van uw App Service-certificaat, zodat u het certificaat gebruiken met andere Azure-bronnen.

Zie [veelgestelde vragen voor App Service-certificaten en aangepaste domeinen voor](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)meer informatie.


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Waarom zie ik het bericht 'Gedeeltelijk geslaagd' wanneer ik een back-up van mijn web-app probeer te maken?

Een veel voorkomende oorzaak van back-upfouten is dat sommige bestanden door de toepassing worden gebruikt. Bestanden die in gebruik zijn, worden vergrendeld terwijl u de back-up uitvoert. Dit voorkomt dat er een back-up van deze bestanden wordt gemaakt en kan resulteren in een status 'Gedeeltelijk geslaagd'. U dit mogelijk voorkomen door bestanden uit te sluiten van het back-upproces. U ervoor kiezen om alleen een back-up te maken van wat nodig is. Zie [Back-up maken van alleen de belangrijke onderdelen van uw site met Azure-webapps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)voor meer informatie.

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hoe verwijder ik een koptekst uit het HTTP-antwoord?

Als u de kopteksten uit het HTTP-antwoord wilt verwijderen, werkt u het web.config-bestand van uw site bij. Zie [Standaardserverkoppen verwijderen op uw Azure-websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)voor meer informatie.

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Voldoet App Service aan PCI Standard 3.0 en 3.1?

Momenteel voldoet de web-apps-functie van Azure App Service aan PCI Data Security Standard (DSS) versie 3.0 Level 1. PCI DSS versie 3.1 staat op onze roadmap. De planning is al aan de gang voor de manier waarop de goedkeuring van de nieuwste standaard zal verlopen.

Pci DSS versie 3.1-certificering vereist het uitschakelen van Transport Layer Security (TLS) 1.0. Momenteel is het uitschakelen van TLS 1.0 geen optie voor de meeste App Service-abonnementen. Als u echter de App Service-omgeving gebruikt of bereid bent uw werklast te migreren naar de App-serviceomgeving, u meer controle krijgen over uw omgeving. Dit houdt in dat TLS 1.0 wordt uitgeschakeld door contact op te nemen met Azure Support. In de nabije toekomst zijn we van plan om deze instellingen toegankelijk te maken voor gebruikers.

Zie [Microsoft Azure App Service webapp compliance met PCI Standard 3.0 en 3.1](https://support.microsoft.com/help/3124528)voor meer informatie.

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hoe gebruik ik de faseringsomgeving en implementatieslots?

In standaard- en Premium App-serviceplannen u bij het implementeren van uw web-app naar App Service een aparte implementatiesleuf implementeren in plaats van naar de standaardproductiesleuf. Implementatieslots zijn live web-apps met hun eigen hostnamen. Inhoud en configuratie-elementen van web-apps kunnen worden verwisseld tussen twee implementatiesleuven, waaronder de productiesleuf.

Zie [Een faseringsomgeving instellen in App-service](deploy-staging-slots.md)voor meer informatie over het gebruik van implementatiesleuven.

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hoe kan ik WebJob-logboeken openen en bekijken?

Ga als u WebJob-logboeken bekijkt:

1. Meld u aan op uw [Kudu-website.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Selecteer de WebJob.
3. Selecteer de knop **Uitvoer schakelen.**
4. Als u het uitvoerbestand wilt downloaden, selecteert u de koppeling **Downloaden.**
5. Selecteer **Individueel aanroepen**voor afzonderlijke uitvoeringen .
6. Selecteer de knop **Uitvoer schakelen.**
7. Selecteer de downloadkoppeling.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Ik probeer hybride verbindingen met SQL Server te gebruiken. Waarom zie ik het bericht "System.OverflowException: Rekenkundige bewerking resulteerde in een overloop"?

Als u hybride verbindingen gebruikt om toegang te krijgen tot SQL Server, kan een Microsoft .NET-update op 10 mei 2016 ervoor zorgen dat verbindingen mislukken. Mogelijk ziet u dit bericht:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Oplossing

De uitzondering is veroorzaakt door een probleem met de Hybrid Connection Manager dat sindsdien is opgelost. Zorg ervoor dat [u uw Hybride Verbindingsbeheer bijwerkt](https://go.microsoft.com/fwlink/?LinkID=841308) om dit probleem op te lossen.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hoe voeg ik een URL-herschrijfregel toe?

Als u een url-herschrijfregel wilt toevoegen, maakt u een web.config-bestand met de relevante config-vermeldingen in de **map wwwroot.** Zie [Azure App Services: URL-herschrijven voor](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/)meer informatie.

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hoe beheer ik binnenkomend verkeer naar App Service?

Op siteniveau hebt u twee opties voor het beheren van binnenkomend verkeer naar App-service:

* Dynamische IP-beperkingen inschakelen. Zie [IP- en domeinbeperkingen voor Azure-websites voor](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)meer informatie over het inschakelen van dynamische IP-beperkingen.
* Modulebeveiliging inschakelen. Zie Firewall voor [de webtoepassing ModSecurity op Azure-websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)voor meer informatie over het inschakelen van modulebeveiliging.

Als u App Service Environment gebruikt, u [de Barracuda-firewall](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)gebruiken.

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hoe blokkeer ik poorten in een App Service-webapp?

In de gedeelde tenantomgeving van App Service is het niet mogelijk om specifieke poorten te blokkeren vanwege de aard van de infrastructuur. TCP-poorten 4020, 4022 en 4024 kunnen mogelijk ook open staan voor externe foutopsporing op afstand van Visual Studio.

In de App-serviceomgeving hebt u volledige controle over binnenkomend en uitgaand verkeer. U netwerkbeveiligingsgroepen gebruiken om specifieke poorten te beperken of te blokkeren. Zie [App Service-omgeving introduceren](https://azure.microsoft.com/blog/introducing-app-service-environment/)voor meer informatie over de app-serviceomgeving.

## <a name="how-do-i-capture-an-f12-trace"></a>Hoe leg ik een F12-spoor vast?

Je hebt twee opties voor het vastleggen van een F12-trace:

* F12 HTTP-tracering
* F12-console-uitvoer

### <a name="f12-http-trace"></a>F12 HTTP-tracering

1. Ga in Internet Explorer naar uw website. Het is belangrijk om je aan te melden voordat je de volgende stappen zet. Anders legt de F12-tracering gevoelige aanmeldingsgegevens vast.
2. Druk op F12.
3. Controleer of het tabblad **Netwerk** is geselecteerd en selecteer vervolgens de groene **knop Afspelen.**
4. Doe de stappen die het probleem reproduceren.
5. Selecteer de rode **knop Stoppen.**
6. Selecteer de knop **Opslaan** (schijfpictogram) en sla het HAR-bestand (in Internet Explorer en Microsoft Edge) *op of* klik met de rechtermuisknop op het HAR-bestand en selecteer Opslaan als HAR **met inhoud** (in Chrome).

### <a name="f12-console-output"></a>F12-console-uitvoer

1. Selecteer het tabblad **Console.**
2. Selecteer voor elk tabblad dat meer dan nul items bevat het tabblad **(Fout**, **Waarschuwing**of **Informatie).** Als het tabblad niet is geselecteerd, is het tabbladpictogram grijs of zwart wanneer u de cursor ervan wegbeweegt.
3. Klik met de rechtermuisknop in het berichtgebied van het deelvenster en selecteer **Alle kopiëren**.
4. Plak de gekopieerde tekst in een bestand en sla het bestand op.

Als u een HAR-bestand wilt bekijken, u de [HAR-viewer](https://www.softwareishard.com/har/viewer/)gebruiken.

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Waarom krijg ik een foutmelding wanneer ik een App Service-webapp probeer te verbinden met een virtueel netwerk dat is verbonden met ExpressRoute?

Als u een Azure-webapp probeert te verbinden met een virtueel netwerk dat is verbonden met Azure ExpressRoute, mislukt dit. Het volgende bericht wordt weergegeven: "Gateway is geen VPN-gateway."

Op dit moment u geen point-to-site VPN-verbindingen hebben met een virtueel netwerk dat is verbonden met ExpressRoute. Een point-to-site VPN en ExpressRoute kunnen niet naast elkaar bestaan voor hetzelfde virtuele netwerk. Zie beperkingen en beperkingen voor meer informatie [over ExpressRoute en vpn-verbindingen van site-to-site.](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hoe verbind ik een App Service-webapp met een virtueel netwerk met een statische routering (beleidsgebaseerde) gateway?

Momenteel wordt het aansluiten van een App Service-web-app op een virtueel netwerk met een statische routering (beleidsgebaseerde) gateway niet ondersteund. Als uw doelvirtuele netwerk al bestaat, moet het vpn van point-to-site zijn ingeschakeld, met een dynamische routeringgateway, voordat het kan worden verbonden met een app. Als uw gateway is ingesteld op statische routering, u geen point-to-site VPN inschakelen. 

Zie [Een app integreren met een virtueel Azure-netwerk](web-sites-integrate-with-vnet.md)voor meer informatie.

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Waarom kan ik in mijn app-serviceomgeving slechts één App-serviceplan maken, ook al heb ik twee werknemers beschikbaar?

Om fouttolerantie te bieden, vereist app-serviceomgeving dat elke werknemersgroep ten minste één extra rekenbron nodig heeft. De extra compute resource kan geen workload toegewezen krijgen.

Zie [Een app-serviceomgeving maken](environment/app-service-web-how-to-create-an-app-service-environment.md)voor meer informatie.

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Waarom zie ik time-outs wanneer ik een app-serviceomgeving probeer te maken?

Soms mislukt het maken van een App-serviceomgeving. In dat geval ziet u de volgende fout in de activiteitslogboeken:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Als u dit wilt oplossen, moet u ervoor zorgen dat geen van de volgende voorwaarden waar is:
* Het subnet is te klein.
* Het subnet is niet leeg.
* ExpressRoute voorkomt de netwerkconnectiviteitsvereisten van een App Service-omgeving.
* Een slechte netwerkbeveiligingsgroep voorkomt de vereisten voor netwerkconnectiviteit van een App-serviceomgeving.
* Geforceerde tunneling is ingeschakeld.

Zie [Frequente problemen bij het implementeren (maken) van een nieuwe Azure App Service-omgeving](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/)voor meer informatie.

## <a name="why-cant-i-delete-my-app-service-plan"></a>Waarom kan ik mijn App Service-abonnement niet verwijderen?

U een App Service-abonnement niet verwijderen als er App Service-apps zijn gekoppeld aan het App Service-abonnement. Verwijder alle bijbehorende App Service-apps uit het App Service-abonnement voordat u een App Service-abonnement verwijdert.

## <a name="how-do-i-schedule-a-webjob"></a>Hoe plan ik een WebJob?

U een geplande WebJob maken met Cron-expressies:

1. Maak een bestand instellingen.job.
2. Neem in dit JSON-bestand een eigenschap planning op met een Cron-expressie: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Zie [Een geplande WebJob maken met een Cron-expressie](webjobs-create.md#CreateScheduledCRON)voor meer informatie over geplande WebJobs.

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hoe voer ik penetratietesten uit voor mijn App Service-app?

Als u penetratietests wilt uitvoeren, [dient u een verzoek in](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hoe configureer ik een aangepaste domeinnaam voor een App Service-webapp die Traffic Manager gebruikt?

Zie [Een aangepaste domeinnaam configureren voor een Azure-webapp configureren met Traffic Manager](configure-domain-traffic-manager.md)voor meer informatie over het gebruik van een aangepaste domeinnaam met een App Service-app die Azure Traffic Manager gebruikt voor taakverdeling.

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mijn App Service-certificaat is gemarkeerd voor fraude. Hoe los ik dit op?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tijdens de domeinverificatie van een App Service-certificaataankoop ziet u mogelijk het volgende bericht:

"Uw certificaat is gemarkeerd voor mogelijke fraude. Het verzoek wordt momenteel herzien. Als het certificaat niet binnen 24 uur bruikbaar is, neem dan contact op met Azure Support."

Zoals het bericht aangeeft, kan dit verificatieproces voor fraude tot 24 uur in beslag nemen. Gedurende deze periode blijft u het bericht zien.

Als uw App Service-certificaat dit bericht na 24 uur blijft weergeven, voert u het volgende PowerShell-script uit. Het script neemt rechtstreeks contact op met de [certificaatprovider](https://www.godaddy.com/) om het probleem op te lossen.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hoe werken verificatie en autorisatie in App Service?

Zie documenten voor verschillende aanmeldingen van leveranciers voor gedetailleerde documentatie voor verificatie en autorisatie in App-service:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-account](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hoe kan ik het standaarddomein *.azurewebsites.net doorverwijzen naar het aangepaste domein van mijn Azure-webapp?

Wanneer u een nieuwe website maakt met Web Apps in Azure, wordt een *standaardsitenaam*.azurewebsites.net domein toegewezen aan uw site. Als u een aangepaste hostnaam aan uw site toevoegt en niet wilt dat gebruikers toegang hebben tot uw standaard *.azurewebsites.net-domein, u de standaard-URL omleiden. Zie [Het standaarddomein omleiden naar uw aangepaste domein in Azure-webapps](https://zainrizvi.io/blog/block-default-azure-websites-domain/)voor meer informatie over het omleiden van al het verkeer vanuit het standaarddomein van uw website naar uw aangepaste domein.

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hoe bepaal ik welke versie van .NET-versie is geïnstalleerd in App Service?

De snelste manier om de versie van Microsoft .NET te vinden die is geïnstalleerd in App Service, is door de Kudu-console te gebruiken. U hebt toegang tot de Kudu-console via de portal of via de URL van uw App Service-app. Zie [De geïnstalleerde .NET-versie bepalen in App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/)voor gedetailleerde instructies.

## <a name="why-isnt-autoscale-working-as-expected"></a>Waarom werkt Autoscale niet zoals verwacht?

Als Azure Autoscale de web-app-instantie niet heeft ingeschaald of geschaald zoals u had verwacht, loopt u mogelijk tegen een scenario aan waarin we er bewust voor kiezen om niet te schalen om een oneindige lus te voorkomen vanwege 'fladderen'. Dit gebeurt meestal wanneer er geen voldoende marge is tussen de scale-out- en scale-indrempels. Zie Aanbevolen procedures voor Autoscale voor meer informatie over het voorkomen van "flapperen" en om te lezen over andere aanbevolen procedures voor [Autoscale.](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Waarom schaalt Autoscale soms slechts gedeeltelijk?

Autoscale wordt geactiveerd wanneer statistieken vooraf geconfigureerde grenzen overschrijden. Soms merkt u dat de capaciteit slechts gedeeltelijk is gevuld in vergelijking met wat u had verwacht. Dit kan gebeuren wanneer het gewenste aantal exemplaren niet beschikbaar is. In dat scenario vult Autoscale gedeeltelijk het beschikbare aantal exemplaren in. Autoscale voert vervolgens de logica voor herbalans uit om meer capaciteit te krijgen. Het wijst de resterende exemplaren toe. Houd er rekening mee dat dit enkele minuten kan duren.

Als u het verwachte aantal exemplaren na een paar minuten niet ziet, kan dit zijn omdat de gedeeltelijke vulling voldoende was om de statistieken binnen de grenzen te brengen. Of Autoscale is mogelijk verkleind omdat deze de lagere metrische gegevensgrens heeft bereikt.

Als geen van deze voorwaarden van toepassing is en het probleem blijft bestaan, dient u een ondersteuningsverzoek in.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hoe schakel ik HTTP-compressie in voor mijn inhoud?

Als u compressie wilt inschakelen voor statische en dynamische inhoudstypen, voegt u de volgende code toe aan het web.config-bestand op toepassingsniveau:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

U ook de specifieke dynamische en statische MIME-typen opgeven die u wilt comprimeren. Zie voor meer informatie ons antwoord op een forumvraag in [httpCompression-instellingen op een eenvoudige Azure-website.](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hoe migreer ik van een on-premises omgeving naar App Service?

Als u sites wilt migreren van Windows- en Linux-webservers naar App Service, u Azure App Service Migration Assistant gebruiken. Met het migratiehulpprogramma worden web-apps en databases in Azure zo nodig gemaakt en wordt de inhoud vervolgens gepubliceerd. Zie [Azure App Service Migration Assistant](https://appmigration.microsoft.com/)voor meer informatie.
