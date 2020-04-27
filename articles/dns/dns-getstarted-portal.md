---
title: 'Snelstartgids: een DNS-zone en record-Azure Portal maken'
titleSuffix: Azure DNS
description: Gebruik deze stapsgewijze quickstart om te leren hoe u een Azure DNS-zone en -record maakt met behulp van de Azure-portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 3b34b45cba805d748dcc8a0c31340cd8116e1c80
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160763"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Snelstartgids: een Azure DNS zone en-record maken met behulp van de Azure Portal

U kunt Azure DNS configureren voor het omzetten van hostnamen in uw openbare domein. Als u bijvoorbeeld de domein naam *contoso. xyz* hebt aangeschaft vanuit een domein naam registratie, kunt u Azure DNS configureren om het domein *contoso. xyz* te hosten en *`www.contoso.xyz`* om te zetten in het IP-adres van uw webserver of web-app.

In deze quickstart maakt u een testdomein en maakt u vervolgens een adresrecord om *www* om te zetten in het IP-adres *10.10.10.10*.

>[!IMPORTANT]
>Alle namen en IP-adressen in deze quickstart zijn voorbeelden en geen weerspiegelingen van praktijkscenario’s.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voor alle portal-stappen meldt u zich aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

Een DNS-zone bevat de DNS-vermeldingen voor een domein. Als u uw domein wilt hosten in Azure DNS, maakt u een DNS-zone voor deze domeinnaam. 

**De DNS-zone maken:**

1. Selecteer linksboven **Een resource maken**, en vervolgens **Netwerken** en **DNS-zone**.

1. Typ of selecteer op de pagina **DNS-zone maken** de volgende waarden:

   - **Naam**: typ *contoso.xyz* voor het voorbeeld in deze quickstart. De naam van de DNS-zone mag elke waarde zijn die nog niet is geconfigureerd op de Azure DNS-servers. Een werkelijke waarde zou een domein zijn dat u hebt gekocht via een domeinnaamregistrar.
   - **Resource groep**: Selecteer **nieuwe maken**, Voer *MyResourceGroup*in en selecteer **OK**. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement. 

1. Selecteer **Maken**.

   ![DNS-zone](./media/dns-getstarted-portal/openzone650.png)

Het maken van de zone kan een paar minuten duren.

## <a name="create-a-dns-record"></a>Een DNS-record maken

DNS-vermeldingen of -records voor het domein maakt u binnen de DNS-zone. Maak een nieuwe adresrecord of A-record om een hostnaam om te zetten in een IPv4-adres.

**Een A-record maken:**

1. Open in de Azure Portal, onder **alle resources**, de DNS-zone **contoso. xyz** in de resource groep **MyResourceGroup** . U kunt *contoso.xyz* invoeren in het vak **Filteren op naam ** om deze eenvoudiger te vinden.

1. Selecteer boven aan de pagina **DNS-zone** de optie **+ Recordset**.

1. Typ of selecteer op de pagina **Recordset toevoegen** de volgende waarden:

   - **Naam**: typ *www*. De recordnaam is de hostnaam die u wilt omzetten in het opgegeven IP-adres.
   - **Type**: Selecteer **een**. A-records zijn het meest gangbaar, maar er zijn andere record typen voor e-mail servers (' MX '), IP V6-adressen (' AAAA '), enzovoort. 
   - **TTL**: typ *1*. *Time-to-live* van de DNS-aanvraag geeft aan hoelang DNS-servers en -clients een antwoord kunnen opslaan in de cache.
   - **TTL-eenheid**: selecteer **Uren**. Dit is de tijdseenheid voor de **TTL**-waarde. 
   - **IP-adres**: typ voor het voorbeeld in deze quickstart *10.10.10.10*. Deze waarde is het IP-adres waarin de recordnaam wordt omgezet. In een praktijkscenario zou u het openbare IP-adres van de webserver invoeren.

Omdat deze Snelstartgids alleen is bedoeld voor snelle test doeleinden, is het niet nodig om de Azure DNS naam servers te configureren op domein naam registratie. Met een echt productie domein wilt u dat iedereen op Internet de hostnaam kan omzetten om verbinding te maken met uw webserver of app. U gaat naar uw domeinnaamregistrar om de naamserverrecords te vervangen door de Azure DNS-naamservers. Zie [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md#delegate-the-domain) voor meer informatie.

## <a name="test-the-name-resolution"></a>De naamomzetting testen

Nu u een testzone hebt met daarin een DNS-record, kunt u de naamomzetting testen met het hulpprogramma *nslookup*. 

**DNS-naamomzetting testen:**

1. Open in de Azure Portal, onder **alle resources**, de DNS-zone **contoso. xyz** in de resource groep **MyResourceGroup** . U kunt *contoso.xyz* invoeren in het vak **Filteren op naam ** om deze eenvoudiger te vinden.

1. Kopieer een van de namen van de naamservers uit de lijst op de pagina **Overzicht**. 

   ![zone](./media/dns-getstarted-portal/viewzonens500.png)

1. Open een opdrachtprompt en voer de volgende opdracht uit:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Bijvoorbeeld:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Er verschijnt een scherm dat er ongeveer als volgt uitziet:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

De hostnaam **www\.. xyz** wordt omgezet in **10.10.10.10**, net zoals u deze hebt geconfigureerd. Met dit resultaat wordt gecontroleerd of de naamomzetting juist werkt. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u in deze Quick Start hebt gemaakt, niet meer nodig hebt, verwijdert u deze door de resource groep **MyResourceGroup** te verwijderen. Open de resource groep **MyResourceGroup** en selecteer **resource groep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)