---
title: 'Zelfstudie: aangepaste Azure DNS-records voor een web-app maken'
description: In deze zelfstudie maakt u DNS-records voor een web-app in een aangepast domein met behulp van Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 8722a52a097f7f830287d125a4e56e9bbcb9f932
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939105"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Zelfstudie: DNS-records voor een web-app in een aangepast domein maken 

U kunt Azure DNS configureren voor het hosten van een aangepast domein voor uw web-apps. U kunt bijvoorbeeld een Azure-web-app maken en uw gebruikers toegang geven met www\.contoso.com of contoso.com als een Fully Qualified Domain Name (FQDN).

> [!NOTE]
> Contoso.com wordt in de hele zelfstudie als voorbeeld gebruikt. Vervang uw eigen domeinnaam door contoso.com.

Hiervoor moet u drie records maken:

* Een hoofdrecord 'A' die verwijst naar contoso.com
* Een hoofdrecord 'TXT' voor verificatie
* Een record 'CNAME' voor de www-naam die naar de A-record verwijst

Houd er rekening mee dat als u een A-record voor een web-app in Azure maakt, het A-record handmatig moet worden bijgewerkt als het onderliggende IP-adres voor de web-app wordt gewijzigd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een A- en TXT-record voor uw aangepaste domein maken
> * Een CNAME-record voor uw aangepaste domein maken
> * De nieuwe records testen
> * Aangepaste hostnamen voor uw web-app toevoegen
> * De aangepaste hostnamen testen


Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* U moet een domein naam hebben om te kunnen testen met die u in Azure DNS kunt hosten. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.
* [Maak een App Service-app](../app-service/app-service-web-get-started-html.md), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.

* Maak een DNS-zone in Azure DNS en delegeer de zone in uw registrar naar Azure DNS.

   1. Volg de stappen in [Een DNS-zone maken](dns-getstarted-create-dnszone.md) om een DNS-zone te maken.
   2. Volg de stappen in [DNS domain delegation](dns-delegate-domain-azure-dns.md) (Delegering van DNS-domeinen) om uw zone naar Azure DNS te delegeren.

Nadat u een zone hebt gemaakt en deze naar Azure DNS hebt gedelegeerd, kunt u records voor uw aangepaste domein maken.

## <a name="create-an-a-record-and-txt-record"></a>Een A-record en TXT-record maken

Een A-record wordt gebruikt om een naam aan het bijbehorende IP-adres toe te wijzen. Wijs in het volgende voorbeeld met behulp van het IPv4-adres van uw web-app \@ toe als een A-record. \@ vertegenwoordigt doorgaans het hoofddomein.

### <a name="get-the-ipv4-address"></a>IPv4-adres ophalen

Selecteer in het linkernavigatievenster van de pagina App Services in Azure Portal **Aangepaste domeinen**. 

![Menu voor aangepaste domeinen](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Op de pagina **Aangepaste domeinen** kopieert u het IPv4-adres van de app:

![Navigatie naar Azure-app in de portal](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Een A-record maken

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Een TXT-record maken

App Services gebruikt dit record alleen tijdens de configuratie, om te controleren of u de eigenaar bent van het aangepaste domein. Nadat uw aangepaste domein is gevalideerd en geconfigureerd in App Service, kunt u dit TXT-record verwijderen.

> [!NOTE]
> Als u de domein naam wilt controleren, maar geen productie verkeer naar de Web-App wilt door sturen, hoeft u alleen de TXT-record voor de verificatie stap op te geven.  Voor verificatie is naast de TXT-record geen CNAME-record vereist.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>De CNAME-record maken

Als uw domein al wordt beheerd door Azure DNS (zie [DNS domain delegation](dns-domain-delegation.md) (Delegering van DNS-domeinen)) kunt u het volgende voorbeeld gebruiken om een CNAME-record voor contoso.azurewebsites.net te maken.

Open Azure PowerShell en maak een nieuw CNAME-record. In dit voorbeeld wordt een type recordset CNAME gemaakt met een 'time to live' van 600 seconden in een DNS-zone genaamd contoso.com met de alias voor de web-app contoso.azurewebsites.net.

### <a name="create-the-record"></a>Een record maken

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

Het volgende voorbeeld is het antwoord:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>De nieuwe records testen

U kunt controleren of de records correct zijn gemaakt door een query uit te voeren op www.contoso.com en contoso.com met behulp van nslookup, zoals hieronder wordt weergegeven:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Aangepaste hostnamen toevoegen

U kunt nu de aangepaste hostnamen aan uw web-app toevoegen:

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>De aangepaste hostnamen testen

Open een browser en browse naar `http://www.<your domainname>` en `http://<you domain name>`.

> [!NOTE]
> Zorg ervoor dat u het voor voegsel van `http://` opneemt, anders kan uw browser proberen een URL voor te voors pellen.

U zou voor beide URL's dezelfde pagina moeten zien. Bijvoorbeeld:

![Contoso-appservice](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep **myresourcegroup** wanneer u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u privézones in Azure DNS maakt.

> [!div class="nextstepaction"]
> [Aan de slag met privézones in Azure DNS met behulp van PowerShell](private-dns-getstarted-powershell.md)
