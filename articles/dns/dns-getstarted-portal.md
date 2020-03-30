---
title: 'Snelstart: een DNS-zone maken en opnemen - Azure-portal'
titleSuffix: Azure DNS
description: Gebruik deze stapsgewijze quickstart om te leren hoe u een Azure DNS-zone en -record maakt met behulp van de Azure-portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 26e5386f1c9730f1600e59a002ea7845b82ffe06
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76937132"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Snelstart: een Azure DNS-zone maken en opnemen met de Azure-portal

U kunt Azure DNS configureren voor het omzetten van hostnamen in uw openbare domein. Als u bijvoorbeeld de *contoso.xyz-domeinnaam* hebt gekocht bij een domeinnaamregistrar, u Azure DNS configureren om het *contoso.xyz-domein* te hosten en *www.contoso.xyz* op te lossen naar het IP-adres van uw webserver of web-app.

In deze quickstart maakt u een testdomein en maakt u vervolgens een adresrecord om *www* om te zetten in het IP-adres *10.10.10.10*.

>[!IMPORTANT]
>Alle namen en IP-adressen in deze quickstart zijn voorbeelden en geen weerspiegelingen van praktijkscenario’s.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Voor alle portal-stappen meldt u zich aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

Een DNS-zone bevat de DNS-vermeldingen voor een domein. Als u uw domein wilt hosten in Azure DNS, maakt u een DNS-zone voor deze domeinnaam. 

**De DNS-zone maken:**

1. Selecteer linksboven **Een resource maken**, en vervolgens **Netwerken** en **DNS-zone**.

1. Typ of selecteer op de pagina **DNS-zone maken** de volgende waarden:

   - **Naam**: typ *contoso.xyz* voor het voorbeeld in deze quickstart. De naam van de DNS-zone mag elke waarde zijn die nog niet is geconfigureerd op de Azure DNS-servers. Een werkelijke waarde zou een domein zijn dat u hebt gekocht via een domeinnaamregistrar.
   - **Resourcegroep**: Selecteer **Nieuw maken,** voer *MyResourceGroup*in en selecteer **OK**. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement. 

1. Selecteer **Maken**.

   ![DNS-zone](./media/dns-getstarted-portal/openzone650.png)

Het maken van de zone kan een paar minuten duren.

## <a name="create-a-dns-record"></a>Een DNS-record maken

DNS-vermeldingen of -records voor het domein maakt u binnen de DNS-zone. Maak een nieuwe adresrecord of A-record om een hostnaam om te zetten in een IPv4-adres.

**Een A-record maken:**

1. Open in de Azure-portal onder **Alle resources**de **contoso.xyz-zone** in de resourcegroep **MyResourceGroup.** U kunt *contoso.xyz* invoeren in het vak **Filteren op naam ** om deze eenvoudiger te vinden.

1. Selecteer boven aan de pagina **DNS-zone** de optie **+ Recordset**.

1. Typ of selecteer op de pagina **Recordset toevoegen** de volgende waarden:

   - **Naam**: typ *www*. De recordnaam is de hostnaam die u wilt omzetten in het opgegeven IP-adres.
   - **Type**: Selecteer **A**. 'A'-records zijn de meest voorkomende, maar er zijn andere recordtypen voor e-mailservers ('MX'), IP v6-adressen ('AAAA'), enzovoort. 
   - **TTL**: typ *1*. *Time-to-live* van de DNS-aanvraag geeft aan hoelang DNS-servers en -clients een antwoord kunnen opslaan in de cache.
   - **TTL-eenheid**: selecteer **Uren**. Dit is de tijdseenheid voor de **TTL**-waarde. 
   - **IP-adres**: typ voor het voorbeeld in deze quickstart *10.10.10.10*. Deze waarde is het IP-adres waarin de recordnaam wordt omgezet. In een praktijkscenario zou u het openbare IP-adres van de webserver invoeren.

Aangezien deze quickstart alleen voor snelle testdoeleinden is, is het niet nodig om de Azure DNS-naamservers bij een domeinnaamregistrar te configureren. Met een echt productiedomein wilt u dat iedereen op internet de hostnaam oplost om verbinding te maken met uw webserver of app. U gaat naar uw domeinnaamregistrar om de naamserverrecords te vervangen door de Azure DNS-naamservers. Zie [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md#delegate-the-domain) voor meer informatie.

## <a name="test-the-name-resolution"></a>De naamomzetting testen

Nu u een testzone hebt met daarin een DNS-record, kunt u de naamomzetting testen met het hulpprogramma *nslookup*. 

**DNS-naamomzetting testen:**

1. Open in de Azure-portal onder **Alle resources**de **contoso.xyz-zone** in de resourcegroep **MyResourceGroup.** U kunt *contoso.xyz* invoeren in het vak **Filteren op naam ** om deze eenvoudiger te vinden.

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

De hostnaam **www\.contoso.xyz** wordt opgelost tot **10.10.10.10,** net zoals u het hebt geconfigureerd. Met dit resultaat wordt gecontroleerd of de naamomzetting juist werkt. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u in deze quickstart hebt gemaakt, niet meer nodig hebt, verwijdert u deze door de resourcegroep **MyResourceGroup** te verwijderen. Open de resourcegroep **MyResourceGroup** en selecteer **Brongroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)