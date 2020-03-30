---
title: Limieten en configuratie
description: Servicelimieten, zoals duur, doorvoer en capaciteit, plus configuratiewaarden, zoals IP-adressen die u toestaan, voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 418be090e7ff78ec0089c115c9884ffeffdda871
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284016"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informatie over limieten en configuratie voor Azure Logic Apps

In dit artikel worden de limieten en configuratiegegevens beschreven voor het maken en uitvoeren van geautomatiseerde werkstromen met Azure Logic Apps. Zie [Limieten en configuratie in Power Automate](https://docs.microsoft.com/flow/limits-and-config)voor Power Automate .

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definitielimieten

Hier zijn de limieten voor een enkele logische app-definitie:

| Name | Limiet | Opmerkingen |
| ---- | ----- | ----- |
| Acties per werkstroom | 500 | Als u deze limiet wilt verlengen, u desbehoefte geneste werkstromen toevoegen. |
| Toegestane nestdiepte voor acties | 8 | Als u deze limiet wilt verlengen, u desbehoefte geneste werkstromen toevoegen. |
| Werkstromen per regio per abonnement | 1000 | |
| Triggers per werkstroom | 10 | Wanneer u in de codeweergave werkt, niet de ontwerper |
| Limiet voor scopecases | 25 | |
| Variabelen per werkstroom | 250 | |
| Tekens per expressie | 8.192 | |
| Maximale grootte voor`trackedProperties` | 16.000 tekens |
| Naam `action` voor of`trigger` | 80 tekens | |
| Lengte van`description` | 256 tekens | |
| Maximale`parameters` | 50 | |
| Maximale`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Duur- en bewaarlimieten uitvoeren

Dit zijn de limieten voor één logische app:Here are the limits for a single logic app run:

| Name | Limiet voor meerdere tekens | Beperking van de integratieserviceomgeving | Opmerkingen |
|------|--------------------|---------------------------------------|-------|
| Uitvoeringsduur | 90 dagen | 366 dagen | De duur van de uitvoering wordt berekend aan de hand van de begintijd van een run en de limiet die *bij het begintijd* is opgegeven door de werkstroominstelling, [**Geschiedenisbehoud uitvoeren in dagen**](#change-duration). <p><p>Zie Duurvan de looptijd wijzigen als u de standaardlimiet van 90 dagen wilt [wijzigen.](#change-duration) |
| Retentie uitvoeren in opslag | 90 dagen | 366 dagen | Run retentie wordt berekend aan de hand van de begintijd van een run en de limiet die *op de huidige tijd* is opgegeven door de werkstroominstelling, [**Geschiedenisbehoud uitvoeren in dagen**](#change-retention). Of een run nu wordt voltooid of een time-out heeft, de bewaarberekening gebruikt altijd de begintijd van de run. Wanneer de duur van een run de *huidige* bewaarlimiet overschrijdt, wordt de run uit de rungeschiedenis verwijderd. <p><p>Als u deze instelling wijzigt, wordt de huidige limiet altijd gebruikt voor het berekenen van retentie, ongeacht de vorige limiet. Als u bijvoorbeeld de bewaarlimiet verlaagt van 90 dagen naar 30 dagen, wordt een run van 60 dagen verwijderd uit de runengeschiedenis. Als u de bewaartermijn verhoogt van 30 dagen naar 60 dagen, blijft een run van 20 dagen oud nog 40 dagen in de runengeschiedenis. <p><p>Zie Behoud van uitvoeren wijzigen in de opslag als u de standaardlimiet van 90 dagen wilt [wijzigen.](#change-retention) |
| Minimale herhalingsinterval | 1 seconde | 1 seconde ||
| Maximaal herhalingsinterval | 500 dagen | 500 dagen ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>Run-duur wijzigen en retentie in opslag uitvoeren

Voer de standaardlimiet voor de duur van de run en de opslag uit om de standaardlimiet voor runsduur en -opslag uit te voeren. Als u de maximale limiet wilt verhogen, [neemt u contact op met het Team Logische Apps](mailto://logicappsemail@microsoft.com) voor hulp bij uw vereisten.

> [!NOTE]
> Voor logische apps in Azure met meerdere tenant's is de standaardlimiet van 90 dagen hetzelfde als de maximumlimiet. U deze waarde alleen maar verlagen.
> Voor logische apps in een integratieserviceomgeving u de standaardlimiet van 90 dagen verlagen of verhogen.

1. Ga naar de [Azure-portal.](https://portal.azure.com) Zoek en selecteer **logica-apps**in het zoekvak van de portal.

1. Selecteer en open vervolgens uw logische app in de Logic App Designer.

1. Selecteer **werkstroominstellingen**in het menu van de logische app .

1. Selecteer Onder **Runtime-opties**in de lijst **Geschiedenisbehoud uitvoeren in dagen** de optie **Aangepast**.

1. Sleep de schuifregelaar om het gewenste aantal dagen te wijzigen.

1. Wanneer u klaar bent, selecteert u op de werkbalk **Werkstroominstellingen** de optie **Opslaan**.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limieten voor gelijktijdigheid, lussen en debatching

Dit zijn de limieten voor één logische app:Here are the limits for a single logic app run:

| Name | Limiet | Opmerkingen |
| ---- | ----- | ----- |
| Gelijktijdigheid activeren | - Onbeperkt wanneer de gelijktijdigheidscontrole is uitgeschakeld <p><p>- 25 is de standaardlimiet wanneer het gelijktijdigheidsbesturingselement is ingeschakeld, die niet ongedaan kan worden gemaakt nadat u het besturingselement hebt ingeschakeld. U de standaardwaarde wijzigen in een waarde tussen 1 en 50. | Deze limiet beschrijft het hoogste aantal logische app-exemplaren dat tegelijkertijd of parallel kan worden uitgevoerd. <p><p>**Opmerking**: Wanneer gelijktijdigheid is ingeschakeld, wordt de SplitOn-limiet verlaagd tot 100 artikelen voor [het debatcheren van arrays](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Zie [Trigger gelijktijdigheidslimiet](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) wijzigen of [Exemplaren opeenvolgend activeren](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger)als u de standaardlimiet wilt wijzigen in een waarde tussen 1 en 50. |
| Maximale wachttijden | - Zonder gelijktijdigheid is het minimum aantal wachtritten 1, terwijl het maximumaantal 50 is. <p><p>- Bij gelijktijdigheid is het minimum aantal wachtruns 10 plus het aantal gelijktijdige runs (trigger gelijktijdigheid). U het maximumaantal tot 100 inclusief wijzigen. | Deze limiet beschrijft het hoogste aantal logische app-exemplaren dat kan wachten om uit te voeren wanneer uw logische app al de maximale gelijktijdige exemplaren uitvoert. <p><p>Zie [Wachttijdrunslimiet wijzigen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)als u de standaardlimiet wilt wijzigen. |
| Voor elk array-items | 100.000 | Deze limiet beschrijft het hoogste aantal matrixitems dat een lus 'voor elke' kan verwerken. <p><p>Als u grotere arrays wilt filteren, u de [queryactie](logic-apps-perform-data-operations.md#filter-array-action)gebruiken. |
| Voorelke gelijktijdigheid | 20 is de standaardlimiet wanneer het gelijktijdigheidsbesturingselement is uitgeschakeld. U de standaardwaarde wijzigen in een waarde tussen 1 en 50. | Deze limiet is het hoogste aantal "voor elke" lus iteraties die kunnen worden uitgevoerd op hetzelfde moment, of in parallel. <p><p>Zie ['Voor elke' gelijktijdigheidslimiet wijzigen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) of [Loop 'voor elke' lussen achtereenvolgens](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)wijzigen in een waarde tussen 1 en 50. |
| SplitOn-items | - 100.000 zonder trigger gelijktijdigheid <p><p>- 100 met trigger gelijktijdigheid | Voor triggers die een array retourneren, u een expressie opgeven die een eigenschap 'SplitOn' gebruikt die [arrayitems splitst of debatches in meerdere werkstroominstanties](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) voor verwerking, in plaats van een 'Foreach'-lus te gebruiken. Deze expressie verwijst naar de array die moet worden gebruikt voor het maken en uitvoeren van een werkstroominstantie voor elk arrayitem. <p><p>**Opmerking:** Wanneer gelijktijdigheid is ingeschakeld, wordt de SplitOn-limiet verlaagd tot 100 artikelen. |
| Until-iteraties | - Standaard: 60 <p><p>- Maximum: 5.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Doorvoerlimieten

Hier zijn de limieten voor een enkele logische app-definitie:

### <a name="multi-tenant-logic-apps-service"></a>Logic Apps-service met meerdere tenantn

| Name | Limiet | Opmerkingen |
| ---- | ----- | ----- |
| Actie: Uitvoeringen per 5 minuten | 100.000 is de standaardlimiet, maar 300.000 is de maximale limiet. | Zie Uw logische app uitvoeren in de [modus 'Hoge doorvoer'](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)in voorbeeld voor het wijzigen van de standaardlimiet. U de werkbelasting ook over meer dan één logische app verdelen als dat nodig is. |
| Actie: Gelijktijdige uitgaande oproepen | ~2500 | U het aantal gelijktijdige aanvragen verminderen of de duur zo nodig verkorten. |
| Eindpunt runtime: gelijktijdige inkomende oproepen | ~1.000 | U het aantal gelijktijdige aanvragen verminderen of de duur zo nodig verkorten. |
| Eindpunt runtime: gesprekken per 5 minuten lezen  | 60.000 | U de werkbelasting zo nodig over meer dan één app verdelen. |
| Eindpunt runtime: oproepen per 5 minuten aanroepen | 45.000 | U de werkbelasting zo nodig over meer dan één app verdelen. |
| Inhoudsdoorvoer per 5 minuten | 600 MB | U de werkbelasting zo nodig over meer dan één app verdelen. |
||||

### <a name="integration-service-environment-ise"></a>Integratieserviceomgeving (ISE)

Hier volgen de doorvoerlimieten voor de Premium SKU:

| Name | Limiet | Opmerkingen |
|------|-------|-------|
| Uitvoeringslimiet voor basiseenheden | Systeem-throttled wanneer de infrastructuurcapaciteit 80% bereikt | Biedt ~ 4.000 actie-uitvoeringen per minuut, dat is ~ 160 miljoen actie-uitvoeringen per maand | |
| Uitvoeringslimiet voor schaaleenheden | Systeem-throttled wanneer de infrastructuurcapaciteit 80% bereikt | Elke schaaleenheid kan ~ 2.000 extra actie-uitvoeringen per minuut bieden, wat ~ 80 miljoen meer actie-uitvoeringen per maand is | |
| Maximale schaaleenheden die u toevoegen | 10 | |
||||

Neem [contact op met het Logic Apps-team](mailto://logicappsemail@microsoft.com) voor hulp bij uw vereisten om boven deze limieten te gaan of om deze limieten te overschrijden of om deze limieten uit te voeren.

> [!NOTE]
> De [Ontwikkelaar SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) heeft geen gepubliceerde limieten omdat deze SKU geen service-level overeenkomst (SLA) of mogelijkheden heeft om op te schalen.
> Gebruik deze SKU alleen voor het experimenteren, ontwikkelen en testen, niet voor productie- of prestatietests.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gatewaylimieten

Azure Logic Apps ondersteunt schrijfbewerkingen, waaronder inserts en updates, via de gateway. Deze bewerkingen hebben echter [beperkingen op hun laadvermogengrootte](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-limieten

Hier zijn de limieten voor één uitgaande of binnenkomende HTTP-oproep:

#### <a name="timeout"></a>Time-out

Sommige connectorbewerkingen voeren asynchrone aanroepen of luisteren naar webhook-aanvragen, dus de time-out voor deze bewerkingen kan langer zijn dan deze limieten. Zie voor meer informatie de technische details voor de specifieke connector en ook [Werkstroomtriggers en -acties.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

| Name | Limiet voor meerdere tekens | Beperking van de integratieserviceomgeving | Opmerkingen |
|------|--------------------|---------------------------------------|-------|
| Uitgaande aanvraag | 120 seconden <br>(2 minuten) | 240 seconden <br>(4 minuten) | Voorbeelden van uitgaande aanvragen zijn oproepen van HTTP-triggers. <p><p>**Tip:** Gebruik voor langere bewerkingen een [asynchrone pollingpatroon](../logic-apps/logic-apps-create-api-app.md#async-pattern) of een [tot lus.](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action) |
| Binnenkomend verzoek | 120 seconden <br>(2 minuten) | 240 seconden <br>(4 minuten) | Voorbeelden van binnenkomende aanvragen zijn oproepen die zijn ontvangen door aanvraagtriggers en webhook-triggers. <p><p>**Opmerking:** Als de oorspronkelijke beller het antwoord wil ontvangen, moeten alle stappen in het antwoord binnen de limiet zijn voltooid, tenzij u een andere logische app als een geneste werkstroom aanroept. Zie [Logische-apps bellen, activeren of nesten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie. |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Berichtgrootte

| Name | Limiet voor meerdere tekens | Beperking van de integratieserviceomgeving | Opmerkingen |
|------|--------------------|---------------------------------------|-------|
| Berichtgrootte | 100 MB | 200 MB | Ise-gelabelde connectors gebruiken de ISE-limiet, niet de niet-ISE-connectorlimieten. <p><p>Zie Grote berichten verwerken met chunking om deze limiet te [omzeilen.](../logic-apps/logic-apps-handle-large-messages.md) Sommige connectors en API's ondersteunen echter mogelijk geen chunking of zelfs de standaardlimiet. |
| Berichtgrootte met chunking | 1 GB | 5 GB | Deze limiet is van toepassing op acties die native chunking ondersteunen of u chunking in hun runtime-configuratie laten inschakelen. <p><p>Voor de integratieserviceomgeving ondersteunt de Logic Apps-engine deze limiet, maar connectors hebben hun eigen chunking-limieten tot aan de enginelimiet, zie bijvoorbeeld de [API-referentie van de Azure Blob Storage-connector.](https://docs.microsoft.com/connectors/azureblob/) Zie Grote berichten verwerken met chunking voor meer informatie over [chunking.](../logic-apps/logic-apps-handle-large-messages.md) |
|||||

#### <a name="character-limits"></a>Tekenlimieten

| Name | Opmerkingen |
|------|-------|
| Limiet voor evaluatie van expressie | 131.072 tekens | De `@concat()` `@base64()` `@string()` expressies kunnen niet langer zijn dan deze limiet. |
| URL-tekenlimiet aanvragen | 16.384 tekens |
|||

#### <a name="retry-policy"></a>Beleid voor opnieuw proberen

| Name | Limiet | Opmerkingen |
| ---- | ----- | ----- |
| Nieuwe pogingen | 90 | De standaard is 4. Als u de standaardwaarde wilt wijzigen, gebruikt u de [parameter Beleid opnieuw proberen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximale vertraging nieuwe poging | 1 dag | Als u de standaardwaarde wilt wijzigen, gebruikt u de [parameter Beleid opnieuw proberen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimale vertraging nieuwe poging | 5 seconden | Als u de standaardwaarde wilt wijzigen, gebruikt u de [parameter Beleid opnieuw proberen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Aangepaste verbindingslimieten

Dit zijn de limieten voor aangepaste connectors die u maken met web-API's.

| Name | Limiet voor meerdere tekens | Beperking van de integratieserviceomgeving | Opmerkingen |
|------|--------------------|---------------------------------------|-------|
| Aantal aangepaste connectors | 1000 per Azure-abonnement | 1000 per Azure-abonnement ||
| Aantal aanvragen per minuut voor een aangepaste connector | 500 aanvragen per minuut per verbinding | 2.000 aanvragen per minuut per *aangepaste connector* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Beheerde identiteiten

| Name | Limiet |
|------|-------|
| Beheerde identiteiten per logische app | De door het systeem toegewezen identiteit of de identiteit van de gebruiker van 1 gebruiker |
| Aantal logische apps met een beheerde identiteit in een Azure-abonnement per regio | 250 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integratieaccountlimieten

Elk Azure-abonnement heeft de volgende integratieaccountlimieten:

* Eén [gratis integratieaccount per](../logic-apps/logic-apps-pricing.md#integration-accounts) Azure-gebied

* 1.000 totale integratieaccounts, inclusief integratieaccounts in alle [integratieserviceomgevingen (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) voor zowel Ontwikkelaars- als [Premium SKU's.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Elke ISE, ontwikkelaar [of Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)is beperkt tot 5 totale integratieaccounts:

  | ISE SKU | Integratieaccountlimieten |
  |---------|----------------------------|
  | **Premium** | 5 totaal - [Alleen standaardaccounts,](../logic-apps/logic-apps-pricing.md#integration-accounts) waaronder één standaardaccount gratis. Er zijn geen gratis of basic-accounts toegestaan. |
  | **Developer** | 5 totaal - [Gratis](../logic-apps/logic-apps-pricing.md#integration-accounts) (beperkt tot 1 account) en [Standaard](../logic-apps/logic-apps-pricing.md#integration-accounts) gecombineerd, of alle Standaard accounts. Er zijn geen Basic-accounts toegestaan. Gebruik de [Developer SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) voor het experimenteren, ontwikkelen en testen, maar niet voor productie- of prestatietests. |
  |||

Extra kosten zijn van toepassing op integratieaccounts die u toevoegt buiten de integratieaccounts die bij een ISE zijn opgenomen. Zie het [prijsmodel van Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over hoe prijzen en facturering werken voor ISE's. Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijstarieven.

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefactlimieten per integratieaccount

Hier zijn de limieten voor het aantal artefacten voor elke integratieaccountlaag.
Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijstarieven. Zie het [prijsmodel voor Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)voor meer informatie over hoe prijzen en facturering werken voor integratieaccounts.

> [!NOTE]
> Gebruik de laag Gratis alleen voor verkennende scenario's, niet voor productiescenario's. Deze laag beperkt de doorvoer en het gebruik en heeft geen service-level overeenkomst (SLA).

| Artefact | Gratis | Basic | Standard |
|----------|------|-------|----------|
| EDI-handelsovereenkomsten | 10 | 1 | 1000 |
| EDI handelspartners | 25 | 2 | 1000 |
| Kaarten | 25 | 500 | 1000 |
| Schema 's | 25 | 500 | 1000 |
| Assembly's | 10 | 25 | 1000 |
| Certificaten | 25 | 2 | 1000 |
| Batchconfiguraties | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Capaciteitslimieten voor artefacten

| Artefact | Limiet | Opmerkingen |
| -------- | ----- | ----- |
| Assembly | 8 MB | Als u bestanden van meer dan 2 MB wilt uploaden, gebruikt u een [Azure-opslagaccount en blobcontainer.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
| Kaart (XSLT-bestand) | 8 MB | Als u bestanden van meer dan 2 MB wilt uploaden, gebruikt u de [API VOOR DE REST-API voor Azure Logic Apps - Kaarten](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Opmerking:** de hoeveelheid gegevens of records die een kaart met succes kan verwerken, is gebaseerd op de limieten voor berichtgrootte en actie-time-out in Azure Logic Apps. Als u bijvoorbeeld een HTTP-actie gebruikt op basis van [http-berichtgrootte en time-outlimieten,](#request-limits)kan een kaart gegevens verwerken tot de limiet voor de grootte van het HTTP-bericht als de bewerking binnen de HTTP-time-outlimiet is voltooid. |
| Schema | 8 MB | Als u bestanden van meer dan 2 MB wilt uploaden, gebruikt u een [Azure-opslagaccount en blobcontainer.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Doorvoerlimieten

| Eindpunt runtime | Gratis | Basic | Standard | Opmerkingen |
|------------------|------|-------|----------|-------|
| Gesprekken per 5 minuten lezen | 3000 | 30,000 | 60.000 | U de werkbelasting indien nodig over meer dan één account verdelen. |
| Oproepen per 5 minuten aanroepen | 3000 | 30,000 | 45.000 | U de werkbelasting indien nodig over meer dan één account verdelen. |
| Oproepen per 5 minuten bijhouden | 3000 | 30,000 | 45.000 | U de werkbelasting indien nodig over meer dan één account verdelen. |
| Gelijktijdige oproepen blokkeren | ~1.000 | ~1.000 | ~1.000 | Hetzelfde geldt voor alle SKU's. U het aantal gelijktijdige aanvragen verminderen of de duur zo nodig verkorten. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-protocol (AS2, X12, EDIFACT) berichtgrootte

Hier volgen de limieten voor de grootte van berichten die van toepassing zijn op B2B-protocollen:

| Name | Limiet voor meerdere tekens | Beperking van de integratieserviceomgeving | Opmerkingen |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 50 MB | v2 - 200 MB <br>v1 - 50 MB | Van toepassing op decoderen en coderen |
| X12 | 50 MB | 50 MB | Van toepassing op decoderen en coderen |
| EDIFACT | 50 MB | 50 MB | Van toepassing op decoderen en coderen |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logische apps uitschakelen of verwijderen

Wanneer u een logische app uitschakelt, worden er geen nieuwe uitvoeringen geinstantieerd.
Alle lopende en lopende runs gaan door tot ze klaar zijn, wat tijd kan kosten om te voltooien.

Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt.
Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd.
Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Firewallconfiguratie: IP-adressen en servicetags

De IP-adressen die Azure Logic Apps gebruikt voor inkomende en uitgaande oproepen, zijn afhankelijk van het gebied waar uw logische app bestaat. *Alle* logische apps in dezelfde regio gebruiken dezelfde IP-adresbereiken. Sommige [Power Automate-oproepen,](https://docs.microsoft.com/power-automate/getting-started) zoals **HTTP-** en HTTP + **OpenAPI-aanvragen,** gaan rechtstreeks via de Azure Logic Apps-service en komen van de IP-adressen die hier worden vermeld. Zie [Limieten en configuratie in Power Automate](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration)voor meer informatie over IP-adressen die door Power Automate worden gebruikt.

> [!TIP]
> Om de complexiteit te verminderen wanneer u beveiligingsregels maakt, u optioneel [servicetags](../virtual-network/service-tags-overview.md)gebruiken in plaats van de IP-adressen van Logic Apps voor elke regio op te geven, die later in deze sectie worden beschreven. Deze tags werken in de regio's waar de Logic Apps-service beschikbaar is:
>
> * **LogicAppsManagement:** vertegenwoordigt de binnenkomende IP-adresvoorvoegsels voor de Logic Apps-service.
> * **LogicApps:** vertegenwoordigt de uitgaande IP-adresvoorvoegsels voor de Logic Apps-service.

* Als u de oproepen wilt ondersteunen die uw logische apps rechtstreeks voeren met [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)en andere HTTP-verzoeken, stelt u uw firewall in met alle [inkomende](#inbound) *en* [uitgaande IP-adressen](#outbound) die worden gebruikt door de Logic Apps-service, op basis van de regio's waar uw logische apps bestaan. Deze adressen worden weergegeven onder de **binnenkomende** en **uitgaande** koppen in deze sectie en worden gesorteerd op regio.

* Als u de oproepen wilt ondersteunen die [door Microsoft beheerde connectors](../connectors/apis-list.md) voeren, stelt u uw firewall in met *alle* [uitgaande](#outbound) IP-adressen die door deze connectors worden gebruikt, op basis van de regio's waar uw logische apps bestaan. Deze adressen worden in deze sectie onder de **rubriek Uitgaan** weergegeven en worden gesorteerd op regio.

* Als u communicatie wilt inschakelen voor logische apps die worden uitgevoerd in een integratieserviceomgeving (ISE), moet u ervoor zorgen dat u [deze poorten opent.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* Als uw logische apps problemen hebben met de toegang tot Azure-opslagaccounts die [firewalls en firewallregels](../storage/common/storage-network-security.md)gebruiken, hebt u [verschillende opties om toegang in te schakelen.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Logische apps hebben bijvoorbeeld geen directe toegang tot opslagaccounts die firewallregels gebruiken en in dezelfde regio bestaan. Als u echter de [uitgaande IP-adressen voor beheerde connectors in uw regio](../logic-apps/logic-apps-limits-and-config.md#outbound)toestaat, hebben uw logische apps toegang tot opslagaccounts die zich in een andere regio bevinden, behalve wanneer u de Azure Table Storage- of Azure Queue Storage-connectors gebruikt. Als u toegang wilt krijgen tot uw tabelopslag of wachtrijopslag, u in plaats daarvan de HTTP-trigger en acties gebruiken. Zie [Access-opslagaccounts achter firewalls voor](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)andere opties .

* Voor aangepaste connectors zijn [Azure Government](../azure-government/documentation-government-overview.md)en Azure [China 21Vianet](https://docs.microsoft.com/azure/china/)geen vaste of gereserveerde IP-adressen beschikbaar.

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Binnenkomende IP-adressen

In deze sectie worden alleen de binnenkomende IP-adressen voor de Azure Logic Apps-service weergegeven. Om de complexiteit te verminderen wanneer u beveiligingsregels maakt, u optioneel de [servicetag](../virtual-network/service-tags-overview.md) **LogicAppsManagement**gebruiken in plaats van inkomende IP-adresvoorvoegsels van Logic Apps voor elke regio op te geven. Deze tag werkt in de regio's waar de Logic Apps-service beschikbaar is. Als u Azure Government hebt, raadpleegt u [Azure Government - Binnenkomende IP-adressen](#azure-government-inbound).

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Azure met meerdere tenant - Binnenkomende IP-adressen

| Multi-tenant regio | IP |
|---------------------|----|
| Australië - oost | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australië - zuidoost | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazilië - zuid | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Canada - midden | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Canada - oost | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| India - centraal | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| VS - centraal | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Azië - oost | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| VS - oost | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| VS - oost 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Frankrijk - centraal | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Frankrijk - zuid | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japan - oost | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan - west | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea - centraal | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea - zuid | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| VS - noord-centraal | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa - noord | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Zuid-Afrika Noord | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Zuid-Afrika West | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| VS - zuid-centraal | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| India - zuid | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azië - zuidoost | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Verenigd Koninkrijk Zuid | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Verenigd Koninkrijk West | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| VS - west-centraal | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa -west | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| India - west | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| VS - west | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| VS - west 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure-overheid - Binnenkomende IP-adressen

| Azure-overheidsregio | IP |
|-------------------------|----|
| VS (overheid) - Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| VS (overheid) - Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| VS (overheid) - Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD Central | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Uitgaande IP-adressen

In deze sectie worden de uitgaande IP-adressen voor de Azure Logic Apps-service en beheerde connectors weergegeven. Om de complexiteit te verminderen wanneer u beveiligingsregels maakt, u optioneel de [servicetag](../virtual-network/service-tags-overview.md) **LogicApps**gebruiken in plaats van ip-adresvoorvoegsels van uitgaande Logic Apps voor elke regio op te geven. Deze tag werkt in de regio's waar de Logic Apps-service beschikbaar is. Gebruik de IP-adressen voor beheerde connectors. Als u Azure Government hebt, raadpleegt u [Azure Government - Uitgaande IP-adressen](#azure-government-outbound).

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Azure met meerdere tenant - Uitgaande IP-adressen

| Regio | IP-logica-apps | IP beheerde connectors |
|--------|---------------|-----------------------|
| Australië - oost | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Australië - zuidoost | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Brazilië - zuid | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Canada - midden | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Canada - oost | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| India - centraal | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| VS - centraal | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Azië - oost | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| VS - oost | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139 |
| VS - oost 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Frankrijk - centraal | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Frankrijk - zuid | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Japan - oost | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Japan - west | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Korea - centraal | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Korea - zuid | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| VS - noord-centraal | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Europa - noord | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Zuid-Afrika Noord | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Zuid-Afrika West | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| VS - zuid-centraal | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| India - zuid | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Azië - zuidoost | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Verenigd Koninkrijk Zuid | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Verenigd Koninkrijk West | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| VS - west-centraal | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Europa -west | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| India - west | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| VS - west | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| VS - west 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure-overheid - Uitgaande IP-adressen

| Regio | IP-logica-apps | IP beheerde connectors |
|--------|---------------|-----------------------|
| VS (overheid) - Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91 |
| VS (overheid) - Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225 |
| VS (overheid) - Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
| US DoD Central | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136 |
||||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Meer informatie over [veelvoorkomende voorbeelden en scenario's](../logic-apps/logic-apps-examples-and-scenarios.md)
