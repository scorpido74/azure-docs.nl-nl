---
title: 'Zelfstudie: uw domein en subdomein in Azure DNS hosten'
description: In deze zelfstudie wordt uitgelegd hoe u Azure DNS kunt configureren om uw DNS-zones te hosten.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 99a3ca0115611f45ed080c39767d13e087b8efb8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464210"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Zelfstudie: uw domein hosten in Azure DNS

U kunt Azure DNS gebruiken om uw DNS-domein te hosten en uw DNS-records te beheren. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services.

Stel dat u het domein contoso.net koopt van een domeinnaamregistrar en vervolgens een zone met de naam contoso.net in Azure DNS maakt. Omdat u de eigenaar van het domein bent, zal uw registrar u de optie bieden om de adressen van NS-records (naamserver) te configureren voor uw domein. De registrar slaat deze NS-records op in de bovenliggende .net-zone. Internetgebruikers over de hele wereld worden daarna naar uw domein in de Azure DNS-zone omgeleid wanneer ze proberen DNS-records om te zetten in contoso.net.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een DNS-zone maken
> * Een lijst met naamservers ophalen
> * Het domein delegeren
> * Controleren of de delegatie werkt


Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet een domein naam hebben om te kunnen testen met die u in Azure DNS kunt hosten. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Het voorbeeld domein dat voor deze zelf studie wordt gebruikt, is contoso.net, maar u kunt uw eigen domein naam gebruiken.

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

1. Ga naar de [Azure Portal](https://portal.azure.com/) om een DNS-zone te maken. Zoek en selecteer **DNS-zones**.

   ![DNS-zone](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Selecteer **DNS-zone maken**.
1. Voer op de pagina **DNS-zone maken** de volgende waarden in en selecteer **Maken**:

   | **Instelling** | **Waarde** | **Details** |
   |---|---|---|
   |**Naam**|[uw domeinnaam] |De domeinnaam die u hebt gekocht. In deze zelfstudie wordt contoso.net als voorbeeld gebruikt.|
   |**Abonnement**|[Uw abonnement]|Selecteer een abonnement waarin u de zone wilt maken.|
   |**Resourcegroep**|**Nieuwe maken:** contosoRG|Maak een resourcegroep. De naam van de resourcegroep moet uniek zijn binnen het abonnement dat u hebt geselecteerd.<br>De locatie van de resourcegroep heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.|
   |**Locatie**|US - oost||

## <a name="retrieve-name-servers"></a>Naamservers ophalen

Voordat u uw DNS-zone naar Azure DNS kunt delegeren, moet u weten wat de naamservers voor uw zone zijn. Telkens wanneer er een zone wordt gemaakt, wijst Azure DNS naamservers uit een groep toe.

1. Nu de DNS-zone is gemaakt, selecteert u **Alle resources** in het deelvenster **Favorieten** in Azure Portal. Selecteer op de pagina **Alle resources** uw DNS-zone. Als het abonnement dat u hebt geselecteerd al verschillende resources bevat, kunt u uw domeinnaam invoeren in het vak **Filteren op naam** om gemakkelijk toegang te krijgen tot de toepassingsgateway. 

1. U vindt de naamservers op de pagina DNS-zone. In dit voorbeeld zijn de naamservers *ns1-01.azure-dns.com*, *ns2 01.azure dns.net*, *ns3-01.azure-dns.org* en *ns4-01.azure-dns.info* aan de zone contoso.net toegewezen:

   ![Lijst met naamservers](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS maakt automatisch gezaghebbende NS-records in uw zone die de toegewezen naamservers bevatten.

## <a name="delegate-the-domain"></a>Het domein delegeren

Nu de DNS-zone is gemaakt en u de naamservers hebt, moet u het bovenliggende domein bijwerken met de Azure DNS-naamservers. Elke registrar heeft zijn eigen hulpprogramma's voor DNS-beheer om de naamserverrecords voor een domein te wijzigen. 

1. Ga naar de DNS-beheerpagina van de registrar, bewerk de NS-records en vervang de NS-records door de Azure DNS-naamservers.

1. Wanneer u een domein naar Azure DNS delegeert, moet u de naamservers gebruiken die worden verstrekt door Azure DNS. Gebruik altijd alle vier de naamservers, ongeacht de naam van uw domein. Bij het delegeren van een domein is het niet nodig dat een naamserver hetzelfde topleveldomein gebruikt als uw domein.

> [!NOTE]
> Wanneer u het adres van een naamserver kopieert, moet u niet vergeten om de punt aan het einde van het adres te kopiëren. Deze punt vormt namelijk het einde van een volledig gekwalificeerde domeinnaam. Sommige registrars voegen deze punt automatisch toe als die niet is opgenomen in de naam van de naamserver. Om te voldoen aan DNS-RFC moet u een punt aan het eind opnemen.

Delegeringen die gebruikmaken van de naamservers in uw eigen zone, ook wel *vanity-naamservers* genoemd, worden momenteel niet ondersteund in Azure DNS.

## <a name="verify-the-delegation"></a>Controleren of de delegatie werkt

Nadat u het delegeren hebt voltooid, kunt u controleren of het werkt door een hulpprogramma zoals *nslookup* te gebruiken om een query op het SOA-record (Start of Authority) voor uw zone uit te voeren. Het SOA-record wordt automatisch gemaakt wanneer de zone wordt gemaakt. Mogelijk moet u tien minuten of langer wachten nadat het delegeren is voltooid voordat u kunt controleren of het werkt. Het kan even duren voordat wijzigingen zijn doorgegeven via het DNS-systeem.

U hoeft de Azure DNS-naamservers niet op te geven. Als de overdracht correct is ingesteld, zal het standaard-DNS-omzettingsproces de naamservers automatisch vinden.

1. Typ in een opdrachtprompt een nslookup-opdracht die vergelijkbaar is met het volgende voorbeeld:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Controleer of het antwoord op de volgende nslookup-uitvoer lijkt:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep **contosoRG** behouden als u de volgende zelfstudie wilt doen. Verwijder anders de resourcegroep **contosoRG** om de resources die in deze zelfstudie zijn gemaakt te verwijderen.

- Selecteer de resourcegroep **contosoRG** en selecteer vervolgens **Resourcegroep verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een DNS-zone gemaakt voor uw domein en het naar Azure DNS gedelegeerd. Als u meer wilt weten over Azure DNS en web-apps gaat u verder met de zelfstudie voor web-apps.

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)