---
title: Een aangepaste domeinnaam configureren in Cloud Services | Microsoft Documenten
description: Meer informatie over het blootstellen van uw Azure-toepassing of -gegevens aan internet op een aangepast domein door DNS-instellingen te configureren.  In deze voorbeelden wordt de Azure-portal gebruikt.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: b1f75a5f7a97907bf5b8bb460ff2df420d053f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386796"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Een aangepaste domeinnaam configureren voor een Azure-cloudservice
Wanneer u een Cloud Service maakt, wijst Azure deze toe aan een subdomein van **cloudapp.net.** Als uw Cloud Service bijvoorbeeld 'contoso' wordt genoemd, hebben uw gebruikers `http://contoso.cloudapp.net`toegang tot uw toepassing op een URL zoals . Azure wijst ook een virtueel IP-adres toe.

U uw toepassing echter ook blootstellen aan uw eigen domeinnaam, zoals **contoso.com.** In dit artikel wordt uitgelegd hoe u een aangepaste domeinnaam voor webrollen van Cloud Service reserveren of configureren.

Weet u al wat CNAME- en A-records zijn? [Spring voorbij de uitleg.](#add-a-cname-record-for-your-custom-domain)

> [!NOTE]
> De procedures in deze taak zijn van toepassing op Azure Cloud Services. Zie [Een bestaande aangepaste DNS-naam toewijzen aan Azure Web Apps voor](../app-service/app-service-web-tutorial-custom-domain.md)App Services. Zie [Een aangepaste domeinnaam configureren voor uw Azure Blob-opslageindpunt voor](../storage/blobs/storage-custom-domain-name.md)opslagaccounts voor opslagaccounts.
> 
> 

<p/>

> [!TIP]
> Ga sneller aan de slag met de nieuwe Azure [begeleide walkthrough!](https://support.microsoft.com/kb/2990804)  Het maakt het koppelen van een aangepaste domeinnaam en het beveiligen van communicatie (SSL) met Azure Cloud Services of Azure Websites in een handomdraai.
> 
> 

## <a name="understand-cname-and-a-records"></a>CNAME- en A-records begrijpen
Met CNAME (of aliasrecords) en A-records u een domeinnaam koppelen aan een specifieke server (of service in dit geval), maar ze werken anders. Er zijn ook enkele specifieke overwegingen bij het gebruik van A-records met Azure Cloud-services die u moet overwegen voordat u beslist welke te gebruiken.

### <a name="cname-or-alias-record"></a>CNAME- of Alias-record
Een CNAME-record brengt een *specifiek* domein, zoals **contoso.com** of **\.www contoso.com,** in kaart met een canonieke domeinnaam. In dit geval is de canonieke domeinnaam de **[myapp].cloudapp.net-domeinnaam** van uw Azure-gehoste toepassing. Eenmaal gemaakt, maakt de CNAME een alias voor de **[myapp].cloudapp.net**. De CNAME-vermelding wordt automatisch op het IP-adres van uw **[myapp].cloudapp.net-service** opgelost, dus als het IP-adres van de cloudservice verandert, hoeft u geen actie te ondernemen.

> [!NOTE]
> Bij sommige domeinregistrars u alleen subdomeinen toewijzen wanneer\.u een CNAME-record gebruikt, zoals contoso.com, en geen hoofdnamen, zoals contoso.com. Zie voor meer informatie over CNAME-records de documentatie van uw registrar, [het Wikipedia-item op CNAME-record](https://en.wikipedia.org/wiki/CNAME_record)of het [IETF-domeinnamen - Implementatie- en specificatiedocument.](https://tools.ietf.org/html/rfc1035)

### <a name="a-record"></a>Een record
Een *A-record* brengt een domein, zoals **contoso.com** of **\.www contoso.com,** *of een wildcarddomein* zoals ** \*.contoso.com**, naar een IP-adres. In het geval van een Azure Cloud Service, het virtuele IP van de service. Het belangrijkste voordeel van een A-record boven een CNAME-record is dus \*dat u één vermelding hebben die een wildcard gebruikt, zoals **.contoso.com**, die aanvragen voor meerdere subdomeinen zoals **mail.contoso.com,** **login.contoso.com**of **\.www contso.com**zou verwerken.

> [!NOTE]
> Aangezien een A-record is toegewezen aan een statisch IP-adres, kan deze niet automatisch wijzigingen in het IP-adres van uw Cloud Service oplossen. Het IP-adres dat door uw Cloud Service wordt gebruikt, wordt toegewezen wanneer u voor het eerst implementeert aan een lege sleuf (productie of fasering.) Als u de implementatie voor de sleuf verwijdert, wordt het IP-adres vrijgegeven door Azure en kunnen eventuele toekomstige implementaties naar de sleuf een nieuw IP-adres krijgen.
> 
> Handig is dat het IP-adres van een bepaalde implementatiesleuf (productie of fasering) blijft bestaan wanneer het verwisselen tussen faserings- en productie-implementaties of het uitvoeren van een in-place upgrade van een bestaande implementatie. Zie [Hoe cloudservices beheren](cloud-services-how-to-manage-portal.md)voor meer informatie over het uitvoeren van deze acties.
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Een CNAME-record toevoegen voor uw aangepaste domein
Als u een CNAME-record wilt maken, moet u een nieuw item toevoegen aan de DNS-tabel voor uw aangepaste domein met behulp van de hulpprogramma's die door uw registrar worden geleverd. Elke registrar heeft een vergelijkbare, maar iets andere methode voor het opgeven van een CNAME-record, maar de concepten zijn hetzelfde.

1. Gebruik een van deze methoden om de **.cloudapp.net** domeinnaam te vinden die is toegewezen aan uw cloudservice.

   * Meld u aan bij de [Azure-portal,]selecteer uw cloudservice, bekijk de sectie **Overzicht** en zoek vervolgens het **URL-item site.**

       ![snel blik sectie met de site-URL][csurl]

       **Of**
   * Installeer en configureer [Azure Powershell](/powershell/azure/overview)en gebruik vervolgens de volgende opdracht:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Sla de domeinnaam op die wordt gebruikt in de URL die op beide manieren wordt geretourneerd, omdat u deze nodig hebt bij het maken van een CNAME-record.
2. Meld u aan bij de website van uw DNS-registrar en ga naar de pagina voor het beheren van DNS. Zoek naar koppelingen of gebieden van de site met het label **Domain Name,** **DNS**of Name **Server Management**.
3. Zoek nu uit waar je CNAME's selecteren of invoeren. Mogelijk moet u het recordtype selecteren in een vervolgkeuzelijst of naar een pagina met geavanceerde instellingen gaan. U moet zoeken naar de woorden **CNAME,** **Alias**of **Subdomeinen**.
4. U moet ook de domein- of subdomeinalias voor de CNAME opgeven, zoals **www** als u een alias wilt maken voor **\.www customdomain.com.** Als u een alias voor het hoofddomein wilt maken,**\@** wordt deze mogelijk vermeld als het '' symbool in de DNS-hulpprogramma's van uw registrar.
5. Vervolgens moet u een canonieke hostnaam opgeven, die in dit geval **het cloudapp.net** domein van uw toepassing is.

Met de volgende CNAME-record wordt bijvoorbeeld al het verkeer doorgeschoven van **www\.contoso.com** naar **contoso.cloudapp.net,** de aangepaste domeinnaam van uw geïmplementeerde toepassing:

| Alias/Hostnaam/Subdomein | Canoniek domein |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Een bezoeker van **www\.contoso.com** zal nooit de ware host (contoso.cloudapp.net) zien, dus het doorstuurproces is onzichtbaar voor de eindgebruiker.
> 
> Het bovenstaande voorbeeld is alleen van toepassing op verkeer op het **subdomein www.** Aangezien u geen jokertekens met CNAME-records gebruiken, moet u één CNAME maken voor elk domein/subdomein. Als u verkeer van subdomeinen, zoals *.contoso.com, naar uw cloudapp.net adres wilt leiden, u een **URL Redirect** of **URL Forward-vermelding** configureren in uw DNS-instellingen of een A-record maken.

## <a name="add-an-a-record-for-your-custom-domain"></a>Een A-record toevoegen voor uw aangepaste domein
Als u een A-record wilt maken, moet u eerst het virtuele IP-adres van uw cloudservice vinden. Voeg vervolgens een nieuwe vermelding toe in de DNS-tabel voor uw aangepaste domein met behulp van de hulpprogramma's die door uw registrar worden geleverd. Elke registrar heeft een vergelijkbare, maar iets andere methode voor het opgeven van een A-record, maar de concepten zijn hetzelfde.

1. Gebruik een van de volgende methoden om het IP-adres van uw cloudservice te krijgen.

   * Meld u aan bij de [Azure-portal,]selecteer uw cloudservice, bekijk de sectie **Overzicht** en zoek vervolgens de vermelding **Openbare IP-adressen.**

       ![quick glance sectie met de VIP][vip]

       **Of**
   * Installeer en configureer [Azure Powershell](/powershell/azure/overview)en gebruik vervolgens de volgende opdracht:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Sla het IP-adres op, zoals u het nodig hebt bij het maken van een A-record.
2. Meld u aan bij de website van uw DNS-registrar en ga naar de pagina voor het beheren van DNS. Zoek naar koppelingen of gebieden van de site met het label **Domain Name,** **DNS**of Name **Server Management**.
3. Zoek nu uit waar u een record selecteren of invoeren. Mogelijk moet u het recordtype selecteren in een vervolgkeuzelijst of naar een pagina met geavanceerde instellingen gaan.
4. Selecteer of voer het domein of subdomein in dat deze A-record gebruikt. Selecteer bijvoorbeeld **www** als u een alias wilt maken voor **www\.customdomain.com**. Als u een wildcard-vermelding voor alle subdomeinen wilt maken, voert u '****** in. Dit geldt voor alle subdomeinen zoals **mail.customdomain.com,** **login.customdomain.com**en **www\.customdomain.com**.

    Als u een A-record voor het hoofddomein wilt maken,**\@** wordt deze mogelijk vermeld als het '' symbool in de DNS-hulpprogramma's van uw registrar.
5. Voer het IP-adres van uw cloudservice in het opgegeven veld in. Hiermee wordt de domeininvoer die in de A-record wordt gebruikt, gekoppeld aan het IP-adres van uw implementatie van uw cloudservice.

De volgende A-record stuurt bijvoorbeeld al het verkeer door van **contoso.com** naar **137.135.70.239,** het IP-adres van uw geïmplementeerde toepassing:

| Hostnaam/subdomein | IP-adres |
| --- | --- |
| \@ |137.135.70.239 |

In dit voorbeeld wordt het maken van een A-record voor het hoofddomein getoond. Als u een wildcard-vermelding wilt maken voor alle subdomeinen, voert u '****** * in als subdomein.

> [!WARNING]
> IP-adressen in Azure zijn standaard dynamisch. U zult waarschijnlijk een [gereserveerd IP-adres](../virtual-network/virtual-networks-reserved-public-ip.md) willen gebruiken om ervoor te zorgen dat uw IP-adres niet verandert.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Cloud Services beheren](cloud-services-how-to-manage-portal.md)
* [CDN-inhoud toewijzen aan een aangepast domein](../cdn/cdn-map-content-to-custom-domain.md)
* [Algemene configuratie van uw cloudservice.](cloud-services-how-to-configure-portal.md)
* Meer informatie over het [implementeren van een cloudservice](cloud-services-how-to-create-deploy-portal.md).
* [Ssl-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren .

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure-portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



