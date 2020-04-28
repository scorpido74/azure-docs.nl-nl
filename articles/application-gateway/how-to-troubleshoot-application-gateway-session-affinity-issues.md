---
title: Problemen met sessieaffiniteit oplossen
titleSuffix: Azure Application Gateway
description: In dit artikel vindt u informatie over het oplossen van problemen met sessieaffiniteit in Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: aa3617b30fe1ef9b4d4a6c5fe5aac51bff95bb92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866676"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Problemen met azure application gateway-sessieproblemen oplossen

Meer informatie over het diagnosticeren en oplossen van problemen met sessieaffiniteit met Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde server wilt behouden. Met behulp van de gatewaybeheerde cookies kan de Application Gateway het daarop volgende verkeer van een gebruikerssessie naar dezelfde server leiden voor verwerking. Dit is belangrijk wanneer de sessiestatus lokaal wordt opgeslagen op de server voor een gebruikerssessie.

## <a name="possible-problem-causes"></a>Mogelijke problemen oorzaken

Het probleem bij het onderhouden van sessieaffiniteit op basis van cookies kan optreden vanwege de volgende belangrijkste redenen:

- De instelling 'Op cookies gebaseerde affiniteit' is niet ingeschakeld
- Uw toepassing kan niet omgaan met cookie-gebaseerde affiniteit
- Toepassing maakt gebruik van cookie-gebaseerde affiniteit, maar verzoeken nog steeds stuiteren tussen back-end servers

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Controleren of de instelling 'Op basis van cookies gebaseerde affiniteit' is ingeschakeld

Soms kunnen de problemen met de sessieaffiniteit optreden wanneer u vergeet de instelling 'Cookie based affinity' in te schakelen. Volg de instructies om te bepalen of u de instelling 'Op basis van cookies gebaseerde affiniteit' hebt ingeschakeld op het tabblad HTTP-instellingen in de Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Klik in het **linkernavigatiedeelvenster** op **Alle bronnen**. Klik op de naam van de toepassingsgateway in het blad Alle bronnen. Als het geselecteerde abonnement al meerdere bronnen heeft, u de naam van de toepassingsgateway invoeren in het **filter op naam...** voor eenvoudige toegang tot de toepassingsgateway.

3. Selecteer het tabblad **HTTP-instellingen** onder **INSTELLINGEN**.

   ![problemen oplossen-sessie-affiniteit-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klik op **appGatewayBackendHttpInstellingen** aan de rechterkant om te controleren of u **Ingeschakeld** voor cookiegebaseerde affiniteit hebt geselecteerd.

   ![problemen oplossen-sessie-affiniteit-problemen-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



U ook de waarde controleren van de**CookieBasedAffinity**is ingesteld op *Ingeschakeld*onder**backendHttpSettingsCollection**met behulp van een van de volgende methoden:

- [Get-AzApplicationGatewayBackendhttpInstellen uitvoeren](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) in PowerShell
- Het JSON-bestand bekijken met de sjabloon Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>De toepassing kan niet omgaan met cookie-gebaseerde affiniteit

#### <a name="cause"></a>Oorzaak

De toepassingsgateway kan alleen sessiegebaseerde affiniteit uitvoeren met behulp van een cookie.

#### <a name="workaround"></a>Tijdelijke oplossing

Als de toepassing geen op cookies gebaseerde affiniteit kan verwerken, moet u een externe of interne azure load balancer of een andere oplossing van derden gebruiken.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Toepassing maakt gebruik van cookie-gebaseerde affiniteit, maar verzoeken nog steeds stuiteren tussen back-end servers

#### <a name="symptom"></a>Symptoom

U hebt de op cookies gebaseerde affiniteitsinstelling ingeschakeld, wanneer u de toepassingsgateway opent [http://website](http://website/) met behulp van een URL met een korte naam in Internet Explorer, bijvoorbeeld: , het verzoek stuitert nog steeds tussen back-endservers.

Volg de volgende instructies om dit probleem te identificeren:

1. Neem een webdebugger trace op de "Client" die verbinding maakt met de toepassing achter de Application Gateway (We gebruiken Fiddler in dit voorbeeld).
    **Tip** Als u niet weet hoe u de Fiddler moet gebruiken, schakelt u onderaan de optie "**Ik wil netwerkverkeer verzamelen en analyseren met behulp van webdebugger**".

2. Controleer en analyseer de sessielogboeken om te bepalen of de cookies die door de client worden geleverd, de ARRAffinity-gegevens hebben. Als u de ARRAffinity-gegevens, zoals**ARRAffinity=** *ARRAffinityValue*, niet vindt in de cookieset, betekent dit dat de client niet reageert met de ARRA-cookie, die wordt geleverd door de Application Gateway.
    Bijvoorbeeld:

    ![problemen oplossen-sessie-affiniteit-problemen-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![problemen oplossen-sessie-affiniteit-problemen-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

De applicatie blijft proberen om de cookie in te stellen op elk verzoek totdat het antwoord krijgt.

#### <a name="cause"></a>Oorzaak

Dit probleem treedt op omdat Internet Explorer en andere browsers de cookie met een URL met korte naam niet mogen opslaan of gebruiken.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door via een FQDN toegang te zoeken tot Application Gateway. Gebruik bijvoorbeeld [http://website.com](https://website.com/) of [http://appgw.website.com](http://website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Aanvullende logboeken om problemen op te lossen

U aanvullende logboeken verzamelen en analyseren om de problemen op te lossen gerelateerde cookie-gebaseerde sessieaffiniteit

### <a name="analyze-application-gateway-logs"></a>Logboeken van toepassingsgateway's analyseren

Volg de instructies om de logboeken van de toepassingsgateway te verzamelen:

Logboekregistratie inschakelen via de Azure-portal

1. Zoek in de [Azure-portal](https://portal.azure.com/)uw bron en klik op **Diagnostische logboeken**.

   Voor Application Gateway zijn drie logboeken beschikbaar: Access log, Performance log, Firewall log

2. Als u wilt beginnen met het verzamelen van gegevens, klikt u **op Diagnostische gegevens inschakelen**.

   ![problemen oplossen-sessie-affiniteit-problemen-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Het **pagina met de instellingen voor diagnostische** instellingen biedt de instellingen voor de diagnostische logboeken. In dit voorbeeld slaat Log Analytics de logboeken op. Klik **op Configureren** onder **Logboekanalyse** om uw werkruimte in te stellen. U kunt ook Event Hubs en een opslagaccount gebruiken om de diagnostische logboeken op te slaan.

   ![problemen oplossen-sessie-affiniteit-problemen-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Bevestig de instellingen en klik op **Opslaan**.

   ![problemen oplossen-sessie-affiniteit-problemen-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>De toegangslogboeken van de toepassingsgateway weergeven en analyseren

1. Selecteer **diagnostische logboeken** in de sectie **MONITORING** in de Azure-portal onder de bronweergave Application Gateway .

   ![problemen oplossen-sessie-affiniteit-problemen-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Selecteer aan de rechterkant "**ApplicationGatewayAccessLog**" in de vervolgkeuzelijst onder **Logboekcategorieën.**  

   ![problemen oplossen-sessie-affiniteit-problemen-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Klik in de lijst Application Gateway Access Log op het logboek dat u wilt analyseren en exporteren en exporteer het JSON-bestand.

4. Converteer het JSON-bestand dat u in stap 3 hebt geëxporteerd naar CSV-bestand en bekijk ze in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie.

5. Controleer de volgende gegevens:

- **ClientIP**– Dit is het IP-adres van de client van de verbindende client.
- **ClientPort** - Dit is de bronpoort van de verbindende client voor de aanvraag.
- **RequestQuery** : hiermee wordt de doelserver aangegeven die de aanvraag heeft ontvangen.
- **Server-Routed:** Back-end poolinstantie dat de aanvraag wordt ontvangen.
- **X-AzureApplicationGateway-LOG-ID:** Correlatie-id die voor de aanvraag wordt gebruikt. Het kan worden gebruikt om verkeersproblemen op de back-endservers op te lossen. Bijvoorbeeld: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS: HTTP-antwoordcode**die Application Gateway van de back-end heeft ontvangen.

  ![problemen oplossen-sessie-affiniteit-problemen-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Als u ziet dat twee items afkomstig zijn van dezelfde ClientIP- en Clientpoort en deze naar dezelfde back-endserver worden verzonden, betekent dit dat de toepassingsgateway correct is geconfigureerd.

Als u ziet dat er twee items afkomstig zijn van dezelfde ClientIP- en Clientpoort en deze naar de verschillende back-endservers worden verzonden, betekent dit dat het verzoek tussen backendservers stuitert, selecteert u "**Toepassing gebruikt cookiegebaseerde affiniteit, maar verzoeken die nog steeds stuiteren tussen back-endservers**aan de onderkant om dit op te lossen.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Webdebugger gebruiken om de HTTP- of HTTPS-verkeersberichten vast te leggen en te analyseren

Webfoutfouting tools zoals Fiddler, kan u helpen debug genfout webapplicaties door het vastleggen van netwerkverkeer tussen het internet en testcomputers. Met deze tools u binnenkomende en uitgaande gegevens inspecteren wanneer de browser deze ontvangt/verzendt. Fiddler, in dit voorbeeld, heeft de HTTP replay optie die u kan helpen bij het oplossen van client-side problemen met webapplicaties, met name voor authenticatie soort probleem.

Gebruik de webdebugger van uw keuze. In dit voorbeeld gebruiken we Fiddler om http- of https-verkeer vast te leggen en te analyseren, de instructies te volgen:

1. Download de Fiddler <https://www.telerik.com/download/fiddler>tool op .

    > [!NOTE]
    > Kies Fiddler4 als de opnamecomputer .NET 4 heeft geïnstalleerd. Anders kiest u Fiddler2.

2. Klik met de rechtermuisknop op de uitvoerbare installatie en voer deze uit als beheerder om te installeren.

    ![problemen oplossen-sessie-affiniteit-problemen-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Wanneer u Fiddler opent, moet deze automatisch beginnen met het vastleggen van verkeer (let op het vastleggen in de linkerbenedenhoek). Druk op F12 om het vastleggen van verkeer te starten of te stoppen.

    ![probleemproblemen oplossen-sessie-affiniteit-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Waarschijnlijk bent u geïnteresseerd in gedecodeerd HTTPS-verkeer en u HTTPS-decryptie inschakelen door **Opties voor extra** > **fiddler**in te schakelen en het selectievakje **HTTPS-verkeer decoderen**in te schakelen.

    ![probleemproblemen oplossen-sessie-affiniteit-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. U eerdere niet-gerelateerde sessies verwijderen voordat u het probleem opnieuw opgeeft door op **X** (pictogram) te klikken > **Alles verwijderen** als volgt schermafbeelding: 

    ![problemen oplossen-sessie-affiniteit-problemen-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Zodra u het probleem hebt gereproduceerd, slaat u het bestand op ter controle door **Alle** > sessies**opslaan bestand te** > **selecteren..**. 

    ![problemen oplossen-sessie-affiniteit-problemen-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Controleer en analyseer de sessielogboeken om te bepalen wat het probleem is.

    Voor voorbeelden:

- **Voorbeeld A:** U vindt een sessielogboek dat de aanvraag van de client wordt verzonden en het gaat naar het openbare IP-adres van de Toepassingsgateway, klik op dit logboek om de details te bekijken.  Aan de rechterkant, gegevens in het onderste vak is wat de Application Gateway is terug te keren naar de client. Selecteer het tabblad "RAW" en bepaal of de client een "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*" ontvangt. Als er geen cookie is, is sessieaffiniteit niet ingesteld of past de Application Gateway geen cookie toe op de client.

   > [!NOTE]
   > Deze ARRAffinity-waarde is de cookie-id die de Application Gateway instelt voor de client die naar een bepaalde back-endserver moet worden verzonden.

   ![problemen oplossen-sessie-affiniteit-problemen-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Voorbeeld B:** Het volgende sessielogboek gevolgd door het vorige is de client die terugreageert op de Application Gateway, die de ARRAAFFINITY heeft ingesteld. Als de ARRAffinity-cookie-id overeenkomt, moet het pakket worden verzonden naar dezelfde back-endserver die eerder is gebruikt. Controleer de volgende verschillende regels http-communicatie om te zien of de ARRAffinity-cookie van de klant verandert.

   ![problemen oplossen-sessie-affiniteit-problemen-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Voor dezelfde communicatiesessie mag de cookie niet worden gewijzigd. Schakel het bovenste vakje aan de rechterkant in, selecteer het tabblad 'Cookies' om te zien of de client de cookie gebruikt en terug te sturen naar de Application Gateway. Zo niet, dan is de client browser niet te houden en het gebruik van de cookie voor gesprekken. Soms kan de klant liegen.

 

## <a name="next-steps"></a>Volgende stappen

Als het probleem in de voorgaande stappen niet is opgelost, opent u een [ondersteuningsticket](https://azure.microsoft.com/support/options/).
