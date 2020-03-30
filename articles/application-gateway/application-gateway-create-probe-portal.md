---
title: Een aangepaste sonde maken met behulp van de portal
titleSuffix: Azure Application Gateway
description: Meer informatie over het maken van een aangepaste sonde voor application gateway met behulp van de portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074611"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Een aangepaste sonde voor application gateway maken met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure-portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

In dit artikel voegt u een aangepaste statussonde toe aan een bestaande toepassingsgateway via de Azure-portal. Met behulp van de statussondes bewaakt Azure Application Gateway de status van de resources in de back-endgroep.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen toepassingsgateway hebt, gaat u [naar Een toepassingsgateway maken](application-gateway-create-gateway-portal.md) om een toepassingsgateway te maken om mee te werken.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Probe voor Application Gateway v2 SKU maken

Sondes worden geconfigureerd in een proces in twee stappen via de portal. De eerste stap is het invoeren van de waarden die nodig zijn voor de configuratie van de sonde. In de tweede stap test u de backendstatus met behulp van deze sondeconfiguratie en slaat u de sonde op. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Sondeeigenschappen invoeren

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als je nog geen account hebt, kun je je aanmelden voor een [gratis proefperiode van een maand.](https://azure.microsoft.com/free)

2. Klik in het deelvenster Favorieten van Azure Portal op Alle resources. Klik op de toepassingsgateway in het blad Alle bronnen. Als het abonnement dat u hebt geselecteerd, al verschillende resources heeft, kunt u partners.contoso.net invoeren in het vak Filteren op naam… voor eenvoudige toegang tot de toepassingsgateway.

3. Selecteer **Statussondes** en selecteer **Toevoegen** om een nieuwe statussonde toe te voegen.

   ![Nieuwe sonde toevoegen][4]

4. Vul op de pagina **Statussondes toevoegen** de vereiste informatie voor de sonde in en selecteer **OK**wanneer deze is voltooid.

   |**Instelling** | **Waarde** | **Details**|
   |---|---|---|
   |**Naam**|aangepaste sonde|Deze waarde is een vriendelijke naam gegeven aan de sonde die toegankelijk is in het portaal.|
   |**Protocol**|HTTP of HTTPS | Het protocol dat de gezondheidssonde gebruikt. |
   |**Host**|D.w.z contoso.com|Deze waarde is de naam van de virtuele host (anders dan de VM-hostnaam) die op de toepassingsserver wordt uitgevoerd. De sonde wordt verzonden naar (protocol)://(host naam):(port van httpsetting)/urlPath.  Dit is van toepassing wanneer multi-site is geconfigureerd op Application Gateway. Als de Application Gateway is geconfigureerd voor één site, voert u '127.0.0.1' in.|
   |**Kies hostnaam uit backend HTTP-instellingen**|Ja of nee|Hiermee stelt u de *hostkopin* de sonde in op de hostnaam van de back-endbron in de back-endpool die is gekoppeld aan de HTTP-instelling waaraan deze sonde is gekoppeld. Speciaal vereist in het geval van backends met meerdere tenants, zoals Azure-app-service. [Meer informatie](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Pad**|/ of een ander pad|De rest van de volledige url voor de aangepaste sonde. Een geldig pad begint met '/'. Voor het standaardpad\/van http: /contoso.com gewoon '/' |
   |**Interval (secs)**|30|Hoe vaak de sonde wordt uitgevoerd om te controleren op gezondheid. Het wordt afgeraden om de onderste dan 30 seconden in te stellen.|
   |**Time-out (secs)**|30|De hoeveelheid tijd die de sonde wacht voordat de timing uit. Als binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de sonde gemarkeerd als mislukt. Het time-outinterval moet hoog genoeg zijn om ervoor te zorgen dat de back-endstatuspagina beschikbaar is. Houd er rekening mee dat de time-outwaarde niet groter mag zijn dan de 'Interval'-waarde die wordt gebruikt in deze sondeinstelling of de waarde 'Time-out aanvragen' in de HTTP-instelling die aan deze sonde wordt gekoppeld.|
|**Ongezonde drempelwaarde**|3|Aantal opeenvolgende mislukte pogingen om als ongezond te worden beschouwd. De drempelwaarde kan worden ingesteld op 1 of meer.|
   |**Ondesondematchingvoorwaarden gebruiken**|Ja of nee|Standaard wordt een HTTP(S)-antwoord met statuscode tussen 200 en 399 als gezond beschouwd. U het acceptabele bereik van backend-responscode of backendreactiebody wijzigen. [Meer informatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP-instellingen**|selectie uit vervolgkeuzelijst|Probe wordt gekoppeld aan de hier geselecteerde HTTP-instelling(en) en controleert daarom de status van die backendpool die is gekoppeld aan de geselecteerde HTTP-instelling. Het gebruikt dezelfde poort voor de sondeaanvraag als de poort die wordt gebruikt in de geselecteerde HTTP-instelling. U alleen de HTTP-instelling(en) kiezen die niet zijn gekoppeld aan een andere aangepaste sonde. <br>Houd er rekening mee dat alleen de HTTP-instelling(s) beschikbaar zijn voor koppeling die hetzelfde protocol hebben als het protocol dat in deze sondeconfiguratie is gekozen en dezelfde status hebben voor de *http-instellingsschakelaar Pick Host-naam van backend.*|
   
   > [!IMPORTANT]
   > De sonde controleert de status van de backend alleen wanneer deze is gekoppeld aan een of meer HTTP-instellingen(en). Het controleert back-endresources van back-endpools die zijn gekoppeld aan de HTTP-instelling(en) waaraan deze sonde is gekoppeld. De sondeaanvraag wordt verzonden naar http://(hostname):(port van httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Backend-status testen met de sonde

Nadat u de sondeeigenschappen hebt ingevoerd, u de status van de back-endresources testen om te controleren of de configuratie van de sonde correct is en of de back-endresources werken zoals verwacht.

1. Selecteer **Testen** en noteer het resultaat van de sonde. De toepassingsgateway test de status van alle backendbronnen in de backendpools die zijn gekoppeld aan de HTTP-instelling(en) die voor deze sonde worden gebruikt. 

   ![Backend-status testen][5]

2. Als er ongezonde backendbronnen zijn, controleert u de kolom **Details** om de reden van de ongezonde status van de resource te begrijpen. Als de resource als niet-inord is gemarkeerd vanwege een onjuiste configuratie van de sonde, selecteert u de koppeling **Teruggaan naar de sonde** en bewerkt u de configuratie van de sonde. Als de resource anders als niet-gezond is gemarkeerd vanwege een probleem met de backend, los je de problemen met de backendbron op en test je de backend opnieuw door de koppeling **Teruggaan naar probe te** selecteren en **Test**te selecteren.

   > [!NOTE]
   > U ervoor kiezen om de sonde op te slaan, zelfs met ongezonde backend bronnen, maar het wordt niet aanbevolen. Dit komt omdat de Application Gateway verwijdert die backend middelen uit de backend pool die zijn vastgesteld dat ongezond door de sonde. In het geval dat er geen gezonde bronnen in een backend-groep zijn, hebt u geen toegang tot uw toepassing en krijgt u een foutmelding van 502.

   ![Sonderesultaat weergeven][6]

3. Selecteer **Toevoegen** om de sonde op te slaan. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Probe voor Application Gateway v1 SKU maken

Sondes worden geconfigureerd in een proces in twee stappen via de portal. De eerste stap is het maken van de sonde. In de tweede stap voegt u de sonde toe aan de backend http-instellingen van de toepassingsgateway.

### <a name="create-the-probe"></a><a name="createprobe"></a>De sonde maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als je nog geen account hebt, kun je je aanmelden voor een [gratis proefperiode van een maand.](https://azure.microsoft.com/free)

2. Selecteer in het deelvenster Favorieten in Azure Portal **Alle resources**. Selecteer de toepassingsgateway op de pagina **Alle bronnen.** Als het abonnement dat u hebt geselecteerd, al verschillende resources heeft, kunt u partners.contoso.net invoeren in het vak Filteren op naam… voor eenvoudige toegang tot de toepassingsgateway.

3. Selecteer **Probes** en selecteer **Toevoegen** om een sonde toe te voegen.

   ![Probe-blad toevoegen met ingevulde informatie][1]

4. Vul op het **sondeblad Gezondheid toevoegen** de vereiste informatie voor de sonde in en selecteer OK wanneer deze is **voltooid.**

   |**Instelling** | **Waarde** | **Details**|
   |---|---|---|
   |**Naam**|aangepaste sonde|Deze waarde is een vriendelijke naam gegeven aan de sonde die toegankelijk is in het portaal.|
   |**Protocol**|HTTP of HTTPS | Het protocol dat de gezondheidssonde gebruikt. |
   |**Host**|D.w.z contoso.com|Deze waarde is de naam van de virtuele host (anders dan de VM-hostnaam) die op de toepassingsserver wordt uitgevoerd. De sonde wordt verzonden naar (protocol)://(host naam):(port van httpsetting)/urlPath.  Dit is van toepassing wanneer multi-site is geconfigureerd op Application Gateway. Als de Application Gateway is geconfigureerd voor één site, voert u '127.0.0.1' in.|
   |**Kies hostnaam uit backend HTTP-instellingen**|Ja of nee|Hiermee stelt u de *hostkopin* de sonde in op de hostnaam van de back-endbron in de back-endpool die is gekoppeld aan de HTTP-instelling waaraan deze sonde is gekoppeld. Speciaal vereist in het geval van backends met meerdere tenants, zoals Azure-app-service. [Meer informatie](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Pad**|/ of een ander pad|De rest van de volledige url voor de aangepaste sonde. Een geldig pad begint met '/'. Voor het standaardpad\/van http: /contoso.com gewoon '/' |
   |**Interval (secs)**|30|Hoe vaak de sonde wordt uitgevoerd om te controleren op gezondheid. Het wordt afgeraden om de onderste dan 30 seconden in te stellen.|
   |**Time-out (secs)**|30|De hoeveelheid tijd die de sonde wacht voordat de timing uit. Als binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de sonde gemarkeerd als mislukt. Het time-outinterval moet hoog genoeg zijn om ervoor te zorgen dat de back-endstatuspagina beschikbaar is. Houd er rekening mee dat de time-outwaarde niet groter mag zijn dan de 'Interval'-waarde die wordt gebruikt in deze sondeinstelling of de waarde 'Time-out aanvragen' in de HTTP-instelling die aan deze sonde wordt gekoppeld.|
|**Ongezonde drempelwaarde**|3|Aantal opeenvolgende mislukte pogingen om als ongezond te worden beschouwd. De drempelwaarde kan worden ingesteld op 1 of meer.|
   |**Ondesondematchingvoorwaarden gebruiken**|Ja of nee|Standaard wordt een HTTP(S)-antwoord met statuscode tussen 200 en 399 als gezond beschouwd. U het acceptabele bereik van backend-responscode of backendreactiebody wijzigen. [Meer informatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > De hostnaam is niet hetzelfde als de naam van de server. Deze waarde is de naam van de virtuele host die op de toepassingsserver wordt uitgevoerd. De sonde wordt verzonden naar http://(host name):(port van httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Sonde toevoegen aan de gateway

Nu de sonde is gemaakt, is het tijd om het toe te voegen aan de gateway. Sondeinstellingen zijn ingesteld op de backend http-instellingen van de toepassingsgateway.

1. Klik op **HTTP-instellingen** op de toepassingsgateway om het configuratieblad weer te geven op de huidige backend http-instellingen in het venster.

   ![venster https-instellingen][2]

2. Schakel op de pagina **appGatewayBackEndhttpSettings** het selectievakje **Aangepaste sonde gebruiken** in en kies de sonde die is gemaakt in de sectie [De sonde maken](#createprobe) in de vervolgkeuzelijst Aangepaste **sonde.**
   Klik als u klaar bent, op **Opslaan** en de instellingen worden toegepast.

## <a name="next-steps"></a>Volgende stappen

Bekijk de status van de backendresources zoals bepaald door de sonde met behulp van de [backend-statusweergave](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
