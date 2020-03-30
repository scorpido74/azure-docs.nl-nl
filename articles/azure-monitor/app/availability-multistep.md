---
title: Monitor met webtests in meerdere stappen - Azure Application Insights
description: Webtests in meerdere stappen instellen om uw webtoepassingen te bewaken met Azure Application Insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655937"
---
# <a name="multi-step-web-tests"></a>Webtests met meerdere stappen

U een opgenomen reeks URL's en interacties met een website volgen via webtests met meerdere stappen. In dit artikel wordt u door het proces van het maken van een multi-step webtest met Visual Studio Enterprise.

> [!NOTE]
> Webtests in meerdere stappen zijn afhankelijk van Visual Studio-webtestbestanden. Er werd [aangekondigd](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) dat Visual Studio 2019 de laatste versie zal zijn met webtest functionaliteit. Het is belangrijk om te begrijpen dat, hoewel er geen nieuwe functies worden toegevoegd, webtestfunctionaliteit in Visual Studio 2019 momenteel nog steeds wordt ondersteund en ook tijdens de ondersteuningslevenscyclus van het product zal worden ondersteund. Het productteam van Azure Monitor heeft hier vragen beantwoord over de toekomst van multi-step [beschikbaarheidstests.](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)  

## <a name="pre-requisites"></a>Vereisten

* Visual Studio 2017 Enterprise of hoger.
* Visual Studio-webprestaties en laadtesttools.

Om de vereiste testtools te vinden. Start de **hulpprogramma's voor** > het opsporen en**testen** > van**webprestaties en belastingstesten**voor**afzonderlijke componenten** > van Visual Studio Installer.

![Schermafbeelding van de gebruikersinterface van de installatie-installatie van Visual Studio met afzonderlijke onderdelen die zijn geselecteerd met een selectievakje naast het item voor hulpprogramma's voor webprestaties en belastingcontrole](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Multi-step webtests hebben extra kosten verbonden aan hen. Voor meer informatie raadpleeg de [officiële prijsgids](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Een webtest in meerdere stappen opnemen 

> [!WARNING]
> We raden niet langer aan om de multi-step recorder te gebruiken. De recorder is ontwikkeld voor statische HTML-pagina's met basisinteracties en biedt geen functionele ervaring voor moderne webpagina's.

Voor begeleiding bij het maken van Visual Studio webtests raadpleeg t.a.v. de [officiële Documentatie Visual Studio 2019.](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)

## <a name="upload-the-web-test"></a>De webtest uploaden

1. Selecteer in de portal Application Insights in het deelvenster Beschikbaarheid de optie > **Testtesttype** > **multistapstest** **maken**.

2. Stel de testlocaties, frequentie- en waarschuwingsparameters in.

### <a name="frequency--location"></a>Frequentie & locatie

|Instelling| Uitleg
|----|----|----|
|**Testfrequentie**| Hiermee bepaalt u hoe vaak de test wordt uitgevoerd vanaf elke testlocatie. Met een standaardfrequentie van vijf minuten en vijf testlocaties wordt uw site gemiddeld per minuut getest.|
|**Testlocaties**| Zijn de plaatsen van waar onze servers webverzoeken naar uw URL sturen. **Ons minimum aantal aanbevolen testlocaties is vijf** om ervoor te zorgen dat u problemen op uw website onderscheiden van netwerkproblemen. U kunt maximaal 16 locaties selecteren.

### <a name="success-criteria"></a>Succescriteria

|Instelling| Uitleg
|----|----|----|
| **Time-out van de test** |Verlaag deze waarde om te worden gewaarschuwd voor trage reacties. De test wordt als mislukt beschouwd als er binnen deze periode geen reactie van uw site is ontvangen. Als u **Parse onafhankelijke aanvragen** hebt geselecteerd, moeten alle afbeeldingen, stijlbestanden, scripts en andere afhankelijke resources binnen deze periode worden ontvangen.|
| **HTTP-antwoord** | De geretourneerde statuscode die als een succes wordt geteld. 200 is de code die aangeeft dat er een normale webpagina is geretourneerd.|
| **Inhoudsovereenkomst** | Een string, zoals "Welkom!" Er wordt getest of er in elke respons een exacte (hoofdlettergevoelige) overeenkomst wordt gevonden. Het moet een eenvoudige tekenreeks zijn, zonder jokertekens. Als uw pagina-inhoud wordt gewijzigd, moet u deze tekenreeks mogelijk ook bijwerken. **Alleen Engelse tekens worden ondersteund met inhoudsovereenkomst** |

### <a name="alerts"></a>Waarschuwingen

|Instelling| Uitleg
|----|----|----|
|**Bijna realtime (Preview)** | We raden u aan near-realtime waarschuwingen te gebruiken. Het configureren van dit type waarschuwing gebeurt nadat uw beschikbaarheidstest is gemaakt.  |
|**Klassiek** | We raden niet langer aan om klassieke waarschuwingen te gebruiken voor nieuwe beschikbaarheidstests.|
|**Drempelwaarde voor waarschuwingslocatie**|Wij raden minimaal 3/5 locaties aan. De optimale relatie tussen waarschuwingslocatiedrempel en het aantal testlocaties is **waarschuwingslocatiedrempel** = **aantal testlocaties - 2, met een minimum van vijf testlocaties.**|

## <a name="configuration"></a>Configuratie

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Tijd en willekeurige getallen in uw test aansluiten

Stel dat u een hulpprogramma test dat tijdsafhankelijke gegevens ontvangt van een externe feed (bijvoorbeeld een feed met aandelenkoersen). Wanneer u uw webtest opneemt, moet u specifieke tijden gebruiken, maar u stelt deze in als testparameters: StartTime en EndTime.

![Mijn ontzagwekkende screenshot van de voorraadapp](./media/availability-multistep/app-insights-72webtest-parameters.png)

Wanneer u de test uitvoert, moet EndTime altijd de huidige tijd zijn. StartTime moet een kwartier in het verleden liggen.

De Web Test Date Time Plugin biedt de manier om parametertijden te verwerken.

1. Voeg een webtestinvoegtoepassing toe voor elke gewenste variabele parameterwaarde. Kies in de werkbalk van de webtest de optie **Webtestinvoegtoepassing toevoegen**.
    
    ![Webtest-plug-in toevoegen](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    In dit voorbeeld gebruiken we twee exemplaren van de invoegtoepassing Date Time. Een exemplaar is voor "15 minuten geleden" en een ander voor “nu”.

2. Open de eigenschappen van elke invoegtoepassing. Geef de invoegtoepassing een naam en stel deze zodanig in dat de huidige tijd wordt gebruikt. Stel voor een van de toepassingen Minuten toevoegen in op -15.

    ![Contextparameters](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Gebruik in de webtestparameters {{plug-in name}} om te verwijzen naar de naam van de invoegtoepassing.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Upload uw test nu naar de portal. Telkens wanneer de test wordt uitgevoerd, worden er dynamische waarden gebruikt.

### <a name="dealing-with-sign-in"></a>Omgaan met aanmelden

Als uw gebruikers zich aanmelden bij uw app, hebt u verschillende functies om de aanmelding te simuleren, zodat u pagina’s na het aanmelden kunt testen. Welke aanpak u gebruikt, hangt af van het type beveiliging van de app.

In alle gevallne moet u een account maken in uw toepassing voor testdoeleinden. Beperk indien mogelijk de machtigingen voor dit testaccount, zodat webtests echte gebruikers niet beïnvloeden.

**Eenvoudige gebruikersnaam en wachtwoord** Neem een webtest op de gebruikelijke manier op. Verwijder eerst de cookies.

**SAML-verificatie**

|Naam van eigenschap| Beschrijving|
|----|-----|
| Publiek Uri | Het publiek URI voor de SAML token.  Dit is de URI voor de Access Control Service (ACS) – inclusief ACS-naamruimte en hostnaam. |
| Certificaatwachtwoord | Het wachtwoord voor het clientcertificaat dat toegang geeft tot de ingesloten privésleutel. |
| Clientcertificaat  | De waarde van het clientcertificaat met privésleutel in de gecodeerde indeling Base64. |
| Naam-id | De naam-id voor het token |
| Niet na | De tijdspanne waarvoor het token geldig is.  De standaardinstelling is 5 minuten. |
| Niet voor | De tijdspanne waarvoor een token dat in het verleden is gemaakt, geldig is (om de tijd scheefte adressen).  De standaardinstelling is (negatief) 5 minuten. |
| Parameternaam doelcontext | De contextparameter die de gegenereerde bewering ontvangt. |


**Klantgeheim** Als uw app een aanmeldingsroute heeft waarbij een klantgeheim is, gebruikt u die route. Azure Active Directory (AAD) is een voorbeeld van een service die aanmelden met een clientgeheim bevat. In AAD is het klantgeheim de App Key.

Hier is een voorbeeldwebtest van een Azure web-app met een App Key:

![Voorbeeldschermafbeelding](./media/availability-multistep/client-secret.png)

Token van AAD krijgen met klantgeheim (AppKey).
Bearer token uit antwoord halen.
API oproepen met bearer token in de autorisatie-header.
Zorg ervoor dat de webtest een echte client is - dat wil zeggen, het heeft een eigen app in AAD - en gebruik de clientId + app-toets. Uw dienst onder test heeft ook een eigen app in AAD: de appID URI van deze app wordt weerspiegeld in de webtest in het resourceveld.

### <a name="open-authentication"></a>Open verificatie
Een voorbeeld van open verificatie is het aanmelden met uw Microsoft- of Google-account. Veel apps die OAuth gebruiken, bieden een alternatief met clientgeheim, zodat uw eerste tactiek moet zijn deze mogelijkheid te onderzoeken.

Als uw test moet aanmelden met OAuth, is de algemene benadering:

Gebruik een hulpprogramma zoals Fiddler om het verkeer tussen de webbrowser, de verificatiesite en uw app te onderzoeken.
Voer twee of meer aanmeldingen uit via verschillende apparaten en browsers, of met lange periodes ertussen (zodat de tokens verlopen).
Vergelijk de verschillende sessies om te bepalen welk token is doorgegeven door de verificatiesite en daarna, na het aanmelden, wordt doorgegeven aan uw appserver.
Neem een webtest op met Visual Studio.
Maak parameters van de tokens. Stel de parameter in wanneer er een token wordt geretourneerd van de verificator en gebruik deze in de query voor de site. (Visual Studio probeert de testparameters toe te voegen, maar voegt de parameters voor de tokens niet correct toe.)

## <a name="troubleshooting"></a>Problemen oplossen

Specifiek [artikel over probleemoplossing](troubleshoot-availability.md).

## <a name="next-steps"></a>Volgende stappen

* [Beschikbaarheidswaarschuwingen](availability-alerts.md)
* [Webtests voor url-ping](monitor-web-app-availability.md)
