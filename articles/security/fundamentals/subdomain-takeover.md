---
title: Voor komen dat subdomein-overname met Azure DNS alias records en de aangepaste domein verificatie van Azure App Service
description: Meer informatie over het voor komen van de veelvoorkomende bedreiging van het hoge urgentie niveau van subdomein overname
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: 2baf2b209cae11f734494c377aebd731f69f514d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610860"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen

In dit artikel wordt de veelvoorkomende bedreiging van de beveiliging van subdomeinen beschreven en de stappen die u kunt nemen om het probleem te verhelpen.


## <a name="what-is-subdomain-takeover"></a>Wat is een subdomein-overname?

De overname van subdomeinen is een veelvoorkomende bedreiging van hoge urgentie voor organisaties die regel matig veel resources maken en verwijderen. Een overname van een subdomein kan optreden wanneer u een DNS-record hebt die naar een niet-ingerichte Azure-resource verwijst. Dergelijke DNS-records worden ook wel ' Dangling DNS-vermeldingen genoemd. CNAME-records zijn met name gevoelig voor deze bedreiging.

Een veelvoorkomend scenario voor een overname van subdomeinen:

1. Er wordt een website gemaakt. 

    In dit voor beeld `app-contogreat-dev-001.azurewebsites.net` .

1. Een CNAME-vermelding wordt toegevoegd aan de DNS die verwijst naar de website. 

    In dit voor beeld is de volgende beschrijvende naam gemaakt: `greatapp.contoso.com` .

1. Na een paar maanden is de site niet meer nodig, zodat deze wordt verwijderd **zonder** de bijbehorende DNS-vermelding te verwijderen. 

    De CNAME DNS-vermelding is nu ' Dangling '.

1. Bijna onmiddellijk nadat de site is verwijderd, detecteert een bedreigings actor de ontbrekende site en maakt hij of zij een eigen website op `app-contogreat-dev-001.azurewebsites.net` .

    Het verkeer dat bestemd is voor `greatapp.contoso.com` gaat naar de Azure-site van de Threat actor en de bedreigings actor heeft nu de controle over de inhoud die wordt weer gegeven. 

    De Dangling DNS is misbruikt en het subdomein ' GreatApp ' van Contoso is het slacht offer van de overname van het subdomein. 

![Subdomein-overname van een website die niet wordt ingericht](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>De Risico's van overname van subdomeinen

Wanneer een DNS-record verwijst naar een resource die niet beschikbaar is, moet de record zelf uit de DNS-zone worden verwijderd. Als deze niet is verwijderd, is dit een Dangling DNS-record en maakt u de mogelijkheid voor de overname van subdomeinen.

Dangling DNS-vermeldingen maken het mogelijk om de bijbehorende DNS-naam in te stellen voor het hosten van een schadelijke website of service. Kwaadwillende pagina's en services op het subdomein van een organisatie kunnen resulteren in:

- **Verlies van de controle over de inhoud van het subdomein** -negatief druk over het onvermogen van uw organisatie om de inhoud ervan te beveiligen, evenals het merk beschadiging en het verlies van vertrouwen.

- Het **verzamelen van cookies van onvermoede bezoekers** : het is gebruikelijk voor web apps om sessie cookies beschikbaar te maken voor subdomeinen (*. contoso.com), waardoor elk subdomein er toegang toe heeft. Threat actors kunnen subdomein overname gebruiken om een authentiek ogende pagina samen te stellen, het niet vermoeden van gebruikers om deze te bezoeken en hun cookies te verzamelen (zelfs beveiligde cookies). Een veelvoorkomende, niet-algemene manier is dat met behulp van SSL-certificaten uw site en de cookies van uw gebruikers worden beschermd vanuit een overname. Een bedreigings actor kan echter gebruikmaken van het gehackte subdomein om toe te passen en een geldig SSL-certificaat te ontvangen. Vervolgens kunnen ze toegang krijgen tot beveiligde cookies en de waargenomen rechtmatigheid van de schadelijke site verder verg Roten.

- **Phishing campagnes** : authentiek-Zoek subdomeinen kunnen worden gebruikt in phishing-campagnes. Dit geldt voor schadelijke sites en ook voor MX-records waarmee de Threat actor e-mails kan ontvangen die zijn geadresseerd aan een legitiem subdomein van een bekend veilig merk.

- **Verdere Risico's** : schadelijke sites kunnen worden gebruikt voor het escaleren van andere klassieke aanvallen, zoals XSS, csrf, CORS bypass en meer.



## <a name="preventing-dangling-dns-entries"></a>Dangling DNS-vermeldingen voor komen

Zorg ervoor dat uw organisatie de processen heeft geïmplementeerd om te voor komen dat DNS-vermeldingen voor Dangling en de resulterende subdomeinen van het beveiligings programma een belang rijk onderdeel van zijn.

De preventieve maat regelen die voor u beschikbaar zijn, worden hieronder weer gegeven.


### <a name="use-azure-dns-alias-records"></a>Azure DNS alias records gebruiken

Door de levens cyclus van een DNS-record met een Azure-resource nauw keurig te koppelen, kunnen de [alias records](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) van Azure DNS de Dangling-verwijzingen verhinderen. Denk bijvoorbeeld aan een DNS-record die is gekwalificeerd als alias record om te verwijzen naar een openbaar IP-adres of een Traffic Manager profiel. Als u deze onderliggende bronnen verwijdert, wordt de DNS-alias record een lege recordset. Er wordt niet langer verwezen naar de verwijderde resource. Het is belang rijk te weten dat er beperkingen zijn voor wat u met alias records kunt beveiligen. De lijst is nu beperkt tot:

- Azure Front Door
- Traffic Manager-profielen
- Azure Content Delivery Network (CDN)-eind punten
- Openbare IP-adressen

Als u resources hebt die kunnen worden beveiligd tegen de overname van subdomeinen met alias records, kunt u dit het beste doen ondanks de beperkte service aanbiedingen vandaag.

Meer [informatie](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) over de mogelijkheden van de alias records van Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Aangepaste domein verificatie van Azure App Service gebruiken

Maak een asuid bij het maken van DNS-vermeldingen voor Azure App Service. subdomein TXT-record met de verificatie-ID van het domein. Wanneer een dergelijke TXT-record bestaat, kan geen ander Azure-abonnement het aangepaste domein valideren. 

Met deze records wordt niet voor komen dat iemand de Azure App Service maakt met dezelfde naam in de CNAME-vermelding. Zonder de mogelijkheid om eigenaar te worden van de domein naam, kunnen Threat actors geen verkeer ontvangen of de inhoud beheren.

Meer [informatie](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) over het toewijzen van een bestaande aangepaste DNS-naam aan Azure app service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Bouw en automatiseer processen om de dreiging te beperken

Het is vaak tot ontwikkel aars en operationele teams om opschoon processen uit te voeren om Dangling DNS-bedreigingen te voor komen. Met de volgende procedures kunt u ervoor zorgen dat uw organisatie niet kan lijden aan deze bedreiging. 

- **Procedures voor preventie maken:**

    - Train uw toepassings ontwikkelaars om adressen opnieuw te routeren wanneer ze resources verwijderen.

    - Plaats ' DNS-vermelding verwijderen ' in de lijst met vereiste controles bij het buiten gebruik stellen van een service.

    - Plaats [Verwijder vergrendelingen](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) op alle resources met een aangepaste DNS-vermelding. Een verwijderings vergrendeling fungeert als een indicator die de toewijzing moet verwijderen voordat de inrichting van de resource ongedaan wordt gemaakt. Maat staven zoals deze kunnen alleen worden gebruikt in combi natie met interne onderwijs Programma's.

- **Procedures voor detectie maken:**

    - Controleer uw DNS-records regel matig om ervoor te zorgen dat uw subdomeinen allemaal zijn toegewezen aan Azure-resources die:

        - **Existing** : query's uitvoeren op uw DNS-zones voor resources die verwijzen naar Azure-subdomeinen, zoals *. azurewebsites.net of *. cloudapp.Azure.com (Zie [deze lijst met verwijzingen](azure-domains.md)).
        - **U bent eigenaar** van de resources die zijn gericht op uw DNS-subdomeinen.

    - Onderhoud van een service catalogus van uw Azure Fully Qualified Domain Name (FQDN)-eind punten en de eigen aren van de toepassing. Als u uw service catalogus wilt maken, voert u de volgende Azure resource Graph-query uit met de para meters uit de onderstaande tabel:
    
        >[!IMPORTANT]
        > **Machtigingen** : Voer de query uit als een gebruiker met toegang tot al uw Azure-abonnementen. 
        >
        > **Beperkingen** : Azure-resource grafiek heeft beperkingen en limieten voor wissel geheugens die u moet overwegen als u een grote Azure-omgeving hebt. [Meer informatie](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) over het werken met grote Azure-resource gegevens sets.  

        ```
        Search-AzGraph -Query "resources | where type == '[ResourceType]' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = [FQDNproperty]"
        ``` 
        
        Met deze query worden bijvoorbeeld de resources uit Azure App Service geretourneerd:

        ```
        Search-AzGraph -Query "resources | where type == 'microsoft.web/sites' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```
        
        U kunt ook meerdere resource typen combi neren. Met dit voorbeeld query worden de resources van Azure App Service **en** Azure app service-sleuven geretourneerd:

        ```
        Search-AzGraph -Query "resources | where type in ('microsoft.web/sites', 'microsoft.web/sites/slots') | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```

        |Resourcenaam  |[ResourceType]  | [FQDNproperty]  |
        |---------|---------|---------|
        |Azure Front Door|micro soft. Network/frontdoors|Properties. cName|
        |Azure Blob Storage|micro soft. Storage/Storage accounts|Properties. primaire. blob|
        |Azure CDN|micro soft. CDN/profielen/eind punten|Properties. hostName|
        |Openbare IP-adressen|micro soft. Network/publicipaddresses|Eigenschappen. dnsSettings. FQDN|
        |Azure Traffic Manager|micro soft. Network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Azure Container Instance|micro soft. containerinstance/containergroups|Eigenschappen. ipAddress. FQDN|
        |Azure API Management|micro soft. apimanagement/service|Properties. hostnameConfigurations. hostName|
        |Azure App Service|micro soft. web/sites|Eigenschappen. defaultHostName|
        |Azure App Service-sleuven|micro soft. web/sites/sleuven|Eigenschappen. defaultHostName|


- **Procedures voor herstel maken:**
    - Wanneer Dangling DNS-vermeldingen worden gevonden, moet uw team onderzoeken of er inbreuk is opgetreden.
    - Onderzoek waarom het adres niet is omgeleid toen de resource uit bedrijf werd genomen.
    - Verwijder de DNS-record als deze niet meer in gebruik is of wijs deze naar de juiste Azure-resource (FQDN) van uw organisatie.
 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende pagina's voor meer informatie over gerelateerde services en Azure-functies die u kunt gebruiken om te beschermen tegen de overname van subdomeinen.

- [Azure DNS ondersteunt het gebruik van alias records voor aangepaste domeinen](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [De domein verificatie-ID gebruiken bij het toevoegen van aangepaste domeinen in Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

-    [Quickstart: Uw eerste Resource Graph-query uitvoeren met Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)