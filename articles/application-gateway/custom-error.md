---
title: Aangepaste foutpagina's van Azure Application Gateway maken
description: In dit artikel ziet u hoe u aangepaste foutpagina's van application gateway maakt. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129861"
---
# <a name="create-application-gateway-custom-error-pages"></a>Aangepaste foutpagina's van Application Gateway maken

Met Application Gateway kunt u aangepaste foutpagina's maken in plaats van standaardfoutpagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.

U bijvoorbeeld uw eigen onderhoudspagina definiëren als uw webtoepassing niet bereikbaar is. U ook een ongeautoriseerde toegangspagina maken als een kwaadwillig verzoek naar een webtoepassing wordt verzonden.

Aangepaste foutpagina's worden ondersteund voor de volgende twee scenario's:

- **Onderhoudspagina** - Deze aangepaste foutpagina wordt verzonden in plaats van een 502 slechte gatewaypagina. Het wordt weergegeven wanneer Application Gateway geen backend heeft om verkeer naar te leiden. Bijvoorbeeld wanneer er gepland onderhoud is gepland of wanneer een onvoorziene probleem gevolgen heeft voor de toegang tot back-endpool.
- **Ongeautoriseerde toegangspagina** - Deze aangepaste foutpagina wordt verzonden in plaats van een pagina met ongeautoriseerde toegang van 403. Het wordt weergegeven wanneer de Application Gateway WAF kwaadaardig verkeer detecteert en blokkeert.

Als een fout afkomstig is van de backend servers, dan is het doorgegeven langs ongewijzigd terug naar de beller. Er wordt geen aangepaste foutpagina weergegeven. Toepassingsgateway kan een aangepaste foutpagina weergeven wanneer een aanvraag de backend niet kan bereiken.

Aangepaste foutpagina's kunnen worden gedefinieerd op globaal niveau en op listenerniveau:

- **Globaal niveau** - de foutpagina is van toepassing op verkeer voor alle webtoepassingen die op die toepassingsgateway zijn geïmplementeerd.
- **Listenerniveau** - de foutpagina wordt toegepast op verkeer dat op die listener is ontvangen.
- **Beide** - de aangepaste foutpagina die op listenerniveau is gedefinieerd, overschrijft de één set op globaal niveau.

Als u een aangepaste foutpagina wilt maken, moet u het:

- een HTTP-antwoordstatuscode.
- de bijbehorende locatie voor de foutpagina. 
- een openbaar toegankelijke Azure-opslagblob voor de locatie.
- een *.htm- of *.html-extensietype. 

De grootte van de foutpagina moet minder dan 1 MB bedragen. Als er afbeeldingen zijn gekoppeld op de foutpagina, moeten ze openbaar toegankelijke absolute URL's of base64 gecodeerde afbeelding inline op de aangepaste foutpagina. Relatieve koppelingen met afbeeldingen op dezelfde bloblocatie worden momenteel niet ondersteund. 

Nadat u een foutpagina hebt opgegeven, downloadt de toepassingsgateway deze van de locatie van de opslagblob en slaat deze op naar de cache van de lokale toepassingsgateway. Vervolgens wordt de foutpagina rechtstreeks vanuit de toepassingsgateway weergegeven. Als u een bestaande aangepaste foutpagina wilt wijzigen, moet u een andere bloblocatie in de configuratie van de toepassingsgateway aanwijzen. De toepassingsgateway controleert niet periodiek de bloblocatie om nieuwe versies op te halen.

## <a name="portal-configuration"></a>Portalconfiguratie

1. Navigeer naar Application Gateway in de portal en kies een toepassingsgateway.

    ![ag-overzicht](media/custom-error/ag-overview.png)
2. Klik **op Luisteraars** en navigeer naar een bepaalde listener waar u een foutpagina wilt opgeven.

    ![Toepassinggatewaylisteners](media/custom-error/ag-listener.png)
3. Configureer een aangepaste foutpagina voor een fout van 403 WAF of een 502-onderhoudspagina op listenerniveau.

    > [!NOTE]
    > Aangepaste foutpagina's op algemeen niveau maken vanuit de Azure-portal wordt momenteel niet ondersteund.

4. Geef een openbaar toegankelijke blob-URL op voor een bepaalde foutstatuscode en klik op **Opslaan**. De Application Gateway is nu geconfigureerd met de aangepaste foutpagina.

   ![Foutcodes van application Gateway](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-configuratie

U Azure PowerShell gebruiken om een aangepaste foutpagina te configureren. Een globale aangepaste foutpagina bijvoorbeeld:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Of een foutpagina op listenerniveau:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Zie [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) en [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Back-endstatus, diagnostische logboeken en statistieken voor Application Gateway voor](application-gateway-diagnostics.md)informatie over application gateway-diagnostiek.