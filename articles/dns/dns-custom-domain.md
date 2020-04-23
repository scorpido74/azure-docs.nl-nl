---
title: Azure DNS integreren met uw Azure-bronnen - Azure DNS
description: In dit artikel leest u hoe u Azure DNS gebruiken om DNS op te nemen voor uw Azure-bronnen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 56a7680de3127da06341ac03252a9ab0cff9da7c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024945"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS gebruiken om aangepaste domeininstellingen voor een Azure-service te bieden

Azure DNS biedt DNS voor een aangepast domein voor een van uw Azure-bronnen die aangepaste domeinen ondersteunen of die een volledig gekwalificeerde domeinnaam (FQDN) hebben. Een voorbeeld hiervan is dat u een Azure-web-app hebt en\.u wilt dat uw gebruikers deze openen door contoso.com te gebruiken of www contoso.com als FQDN. In dit artikel u uw Azure-service configureren met Azure DNS voor het gebruik van aangepaste domeinen.

## <a name="prerequisites"></a>Vereisten

Als u Azure DNS wilt gebruiken voor uw aangepaste domein, moet u uw domein eerst delegeren aan Azure DNS. Ga [naar Een domein delegeren aan Azure DNS](./dns-delegate-domain-azure-dns.md) voor instructies over het configureren van uw naamservers voor overdracht. Zodra uw domein is gedelegeerd aan uw Azure DNS-zone, u de benodigde DNS-records configureren.

U een ijdelheids- of aangepast domein configureren voor [Azure Function Apps,](#azure-function-app) [Openbare IP-adressen,](#public-ip-address) [App Service (Web Apps),](#app-service-web-apps) [Blob-opslag](#blob-storage)en [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure-functie-app

Als u een aangepast domein voor Azure-functie-apps wilt configureren, wordt een CNAME-record gemaakt en wordt een configuratie op de functie-app zelf gemaakt.
 
Navigeer naar **Functie-app** en selecteer de functie-app. Klik op **Platformfuncties** en klik onder **Netwerken** op **Aangepaste domeinen**.

![functie app-blad](./media/dns-custom-domain/functionapp.png)

Let op de huidige url op het **aangepaste domeinenblad,** dit adres wordt gebruikt als alias voor de DNS-record die is gemaakt.

![aangepast domeinblad](./media/dns-custom-domain/functionshostname.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende informatie in op het **recordsetblad toevoegen** en klik op **OK** om het te maken.

|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Naam     | myfunctionapp        | Deze waarde samen met het domeinnaamlabel is de FQDN voor de aangepaste domeinnaam.        |
|Type     | CNAME        | Een CNAME-record gebruiken is het gebruik van een alias.        |
|TTL     | 1        | 1 wordt 1 uur lang gebruikt        |
|TTL-eenheid     | Uren        | Uren worden gebruikt als tijdmeting         |
|Alias     | adatumfunction.azurewebsites.net        | De DNS-naam waarvoor u de alias maakt, is in dit voorbeeld de adatumfunction.azurewebsites.net DNS-naam die standaard aan de functie-app wordt verstrekt.        |

Navigeer terug naar uw functie-app, klik op **Platformfuncties**en klik onder Aangepaste **domeinen** **netwerken** en klik vervolgens onder **Aangepaste hostnamen** klik **op + Hostname toevoegen**.

Voer op het blad **Hostname toevoegen** de CNAME-record in het tekstveld **hostname** in en klik op **Valideren**. Als de record wordt gevonden, wordt de knop **Hostname toevoegen** weergegeven. Klik **op Hostname toevoegen** om de alias toe te voegen.

![functie-apps voegen het blad hostnaam toe](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Openbaar IP-adres

Als u een aangepast domein wilt configureren voor services die een openbare IP-adresbron gebruiken, zoals Application Gateway, Load Balancer, Cloud Service, Resource Manager VM's en Klassieke VM's, wordt een A-record gebruikt.

Navigeer naar**het openbare IP-adres** **voor netwerken,** > selecteer de openbare IP-bron en klik op **Configuratie**. Notate het GETOONDe IP-adres.

![openbaar ip-blad](./media/dns-custom-domain/publicip.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende informatie in op het **recordsetblad toevoegen** en klik op **OK** om het te maken.


|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Naam     | mywebserver        | Deze waarde samen met het domeinnaamlabel is de FQDN voor de aangepaste domeinnaam.        |
|Type     | A        | Gebruik een A-record omdat de bron een IP-adres is.        |
|TTL     | 1        | 1 wordt 1 uur lang gebruikt        |
|TTL-eenheid     | Uren        | Uren worden gebruikt als tijdmeting         |
|IP-adres     | `<your ip address>`       | Het openbare IP-adres.|

![een A-record maken](./media/dns-custom-domain/arecord.png)

Zodra de A-record `nslookup` is gemaakt, voert u het uitvoeren uit om de recordopgeloste gegevens te valideren.

![openbare ip dns-lookup](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App-service (Web Apps)

De volgende stappen nemen u door het configureren van een aangepast domein voor een app-service web-app.

Navigeer naar **App Service** en selecteer de bron die u een aangepaste domeinnaam configureert en klik op **Aangepaste domeinen**.

Let op de huidige url op het **aangepaste domeinenblad,** dit adres wordt gebruikt als alias voor de DNS-record die is gemaakt.

![aangepast domeinenblad](./media/dns-custom-domain/url.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende informatie in op het **recordsetblad toevoegen** en klik op **OK** om het te maken.


|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Naam     | mywebserver        | Deze waarde samen met het domeinnaamlabel is de FQDN voor de aangepaste domeinnaam.        |
|Type     | CNAME        | Een CNAME-record gebruiken is het gebruik van een alias. Als de bron een IP-adres gebruikt, wordt een A-record gebruikt.        |
|TTL     | 1        | 1 wordt 1 uur lang gebruikt        |
|TTL-eenheid     | Uren        | Uren worden gebruikt als tijdmeting         |
|Alias     | webserver.azurewebsites.net        | De DNS-naam waarvoor u de alias maakt, is in dit voorbeeld de webserver.azurewebsites.net DNS-naam die standaard aan de web-app wordt verstrekt.        |


![een CNAME-record maken](./media/dns-custom-domain/createcnamerecord.png)

Navigeer terug naar de app-service die is geconfigureerd voor de aangepaste domeinnaam. Klik **op Aangepaste domeinen**en klik vervolgens op **Hostnames**. Als u de CNAME-record wilt toevoegen die u hebt gemaakt, klikt u **op + Hostname toevoegen**.

![afbeelding 1](./media/dns-custom-domain/figure1.png)

Zodra het proces is voltooid, voert **u nslookup uit** om te valideren dat de naamomzetting werkt.

![afbeelding 1](./media/dns-custom-domain/finalnslookup.png)

Ga naar [Een bestaande aangepaste DNS-naam toewijzen aan Azure Web Apps voor](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)meer informatie over het toewijzen van een aangepast domein aan App-service.

Zie [Een actieve DNS-naam migreren naar Azure App Service](../app-service/manage-custom-dns-migrate-domain.md)voor meer informatie over het migreren van een actieve DNS-naam.

Als u een aangepast domein wilt aanschaffen, gaat u naar [Een aangepaste domeinnaam voor Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) kopen voor meer informatie over App Service-domeinen.

## <a name="blob-storage"></a>Blob Storage

De volgende stappen nemen u door het configureren van een CNAME-record voor een blob-opslagaccount met behulp van de methode asverify. Deze methode zorgt ervoor dat er geen downtime is.

Navigeer naar > **opslagaccounts,** selecteer uw opslagaccount en klik op **Aangepast domein**. **Storage** Notate de FQDN onder stap 2, deze waarde wordt gebruikt om de eerste CNAME record te maken

![Aangepast domein blobopslag](./media/dns-custom-domain/blobcustomdomain.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende informatie in op het **recordsetblad toevoegen** en klik op **OK** om het te maken.


|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Naam     | asverify.mystorageaccount        | Deze waarde samen met het domeinnaamlabel is de FQDN voor de aangepaste domeinnaam.        |
|Type     | CNAME        | Een CNAME-record gebruiken is het gebruik van een alias.        |
|TTL     | 1        | 1 wordt 1 uur lang gebruikt        |
|TTL-eenheid     | Uren        | Uren worden gebruikt als tijdmeting         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | De DNS-naam waarvoor u de alias maakt, is in dit voorbeeld de asverify.adatumfunctiona9ed.blob.core.windows.net DNS-naam die standaard aan het opslagaccount wordt verstrekt.        |

Navigeer terug naar uw opslagaccount door op > **Opslagopslagaccounts**te klikken, selecteer uw opslagaccount en klik op **Aangepast domein**. **Storage** Typ de alias die u hebt gemaakt zonder het voorvoegsel asverify in het tekstvak, schakel **indirecte CNAME-validatie gebruiken**in en klik op **Opslaan**. Zodra deze stap is voltooid, gaat u terug naar uw DNS-zone en maakt u een CNAME-record zonder het voorvoegsel asverify.  Na dat punt u de CNAME-record verwijderen met het voorvoegsel cdnverify.

![Aangepast domein blobopslag](./media/dns-custom-domain/indirectvalidate.png)

DNS-resolutie valideren door`nslookup`

Voor meer informatie over het toewijzen van een aangepast domein aan een eindpunt voor blobopslag moet [u een aangepaste domeinnaam configureren voor uw eindpunt voor de Blob-opslag](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

De volgende stappen nemen u mee door het configureren van een CNAME-record voor een CDN-eindpunt met behulp van de cdnverify-methode. Deze methode zorgt ervoor dat er geen downtime is.

Navigeer naar > **cdn-netwerken-profielen,** selecteer uw CDN-profiel. **Networking**

Selecteer het eindpunt waarmee u werkt en klik op **+ Aangepast domein**. Let op de **hostnaam Endpoint** als deze waarde is de record waarnaar de CNAME-record verwijst.

![Aangepast CDN-domein](./media/dns-custom-domain/endpointcustomdomain.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende informatie in op het **recordsetblad toevoegen** en klik op **OK** om het te maken.

|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Naam     | cdnverify.mycdnendpoint        | Deze waarde samen met het domeinnaamlabel is de FQDN voor de aangepaste domeinnaam.        |
|Type     | CNAME        | Een CNAME-record gebruiken is het gebruik van een alias.        |
|TTL     | 1        | 1 wordt 1 uur lang gebruikt        |
|TTL-eenheid     | Uren        | Uren worden gebruikt als tijdmeting         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | De DNS-naam waarvoor u de alias maakt, in dit voorbeeld is dit de cdnverify.adatumcdnendpoint.azureedge.net DNS-naam die standaard aan het opslagaccount wordt verstrekt.        |

Navigeer terug naar uw CDN-eindpunt door op > **Netwerk-CDN-profielen**te klikken en selecteer uw CDN-profiel. **Networking** Klik **op + Aangepast domein** en voer de alias CNAME-record in zonder het voorvoegsel van cdnverify en klik op **Toevoegen**.

Zodra deze stap is voltooid, gaat u terug naar uw DNS-zone en maakt u een CNAME-record zonder het voorvoegsel van cdnverify.  Na dat punt u de CNAME-record verwijderen met het voorvoegsel cdnverify. Ga naar [Azure CDN-inhoud toewijzen aan Een aangepast domein voor](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)meer informatie over CDN en hoe u een aangepast domein configureren zonder de tussentijdse registratiestap.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van reverse DNS voor services die worden gehost in Azure.](dns-reverse-dns-for-azure-services.md)
