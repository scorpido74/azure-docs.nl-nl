---
title: Beveiliging en verificatie - Azure Event Grid IoT Edge | Microsoft Documenten
description: Beveiliging en verificatie in Gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844510"
---
# <a name="security-and-authentication"></a>Verificatie en beveiliging

Beveiliging en verificatie is een geavanceerd concept en vereist eerst vertrouwdheid met de basisprincipes van Event Grid. Begin [hier](concepts.md) als je nieuw bent in Event Grid op IoT Edge. Event Grid-module bouwt voort op de bestaande beveiligingsinfrastructuur op IoT Edge. Raadpleeg [deze documentatie](../../iot-edge/security.md) voor meer informatie en installatie.

In de volgende secties wordt in detail beschreven hoe deze instellingen zijn beveiligd en geverifieerd:

* TLS-configuratie
* Binnenkomende clientverificatie
* Uitgaande serververificatie
* Uitgaande clientverificatie

>[!IMPORTANT]
>Event Grid module security and authentication leverage's the existing infrastructure available on IoT Edge. De veronderstelling is dat het IoT Edge-subsysteem veilig is.

>[!IMPORTANT]
>De configuratie van gebeurtenisraster is **standaard beveiligd.** In de volgende subsecties worden alle opties en mogelijke waarde(s) uitgelegd die u gebruiken om aspecten van verificatie te overschrijven. Begrijp de impact voordat u wijzigingen aanbrengt. Om wijzigingen van kracht te laten worden, moet de module Gebeurtenisraster opnieuw worden geïmplementeerd.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-configuratie (a.k.a serververificatie)

De module Gebeurtenisraster host zowel HTTP- als HTTPS-eindpunten. Elke IoT Edge-module krijgt een servercertificaat toegewezen door de beveiligingsdaemon van de IoT Edge. We gebruiken het servercertificaat om het eindpunt te beveiligen. Na afloop wordt de module automatisch vernieuwd met een nieuw certificaat van de IoT Edge security daemon.

Standaard is alleen HTTPS-communicatie toegestaan. U dit gedrag overschrijven via **inbound__serverAuth__tlsPolicy** configuratie. In de volgende tabel wordt de mogelijke waarde(en) van deze eigenschap vastgelegd.

| Mogelijke waarde(n) | Beschrijving |
| ---------------- | ------------ |
| Strikt | Standaard. Hiermee schakelt u alleen HTTPS in
| Ingeschakeld | Schakelt zowel HTTP als HTTPS in
| Uitgeschakeld | Maakt alleen HTTP-

## <a name="inbound-client-authentication"></a>Binnenkomende clientverificatie

Clients zijn entiteiten die beheer- en/of runtime-bewerkingen uitvoeren. Clients kunnen andere IoT Edge-modules zijn, niet-IoT-toepassingen.

Event Grid-module ondersteunt twee typen clientverificatie:

* SAS-sleutel (Shared Access Signature)
* op basis van certificaten

Standaard is de module Gebeurtenisraster geconfigureerd om alleen verificatie op basis van certificaten te accepteren. Bij het opstarten haalt de Module Event Grid "TrustBundle" op van IoT Edge security daemon en gebruikt deze om elk clientcertificaat te valideren. Clientcertificaten die niet worden opgelost in deze `UnAuthorized`keten, worden geweigerd met .

### <a name="certificate-based-client-authentication"></a>Clientverificatie op basis van certificaten

Verificatie op basis van certificaten is standaard ingeschakeld. U ervoor kiezen om verificatie op basis van certificaten uit te schakelen via de **eigenschap inbound__clientAuth__clientCert__enabled.** In de volgende tabel wordt de mogelijke waarde(en) vastgelegd.

| Mogelijke waarde(n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Voor alle aanvragen in de module Gebeurtenisraster moet een clientcertificaat worden ingediend. Daarnaast moet u **inbound__clientAuth__clientCert__source**configureren.
| false | Dwing een klant niet om een certificaat te presenteren.

In de volgende tabel wordt de mogelijke waarde(en) voor **inbound__clientAuth__clientCert__source**

| Mogelijke waarde(n) | Beschrijving |
| ---------------- | ------------ |
| IoT Edge | Standaard. Gebruikt de Vertrouwensbundel van IoT Edge om alle clientcertificaten te valideren.

Als een client een zelfondertekende, standaard presenteert, zal de module Gebeurtenisraster dergelijke aanvragen afwijzen. U ervoor kiezen om zelfondertekende clientcertificaten toe te staan via **inbound__clientAuth__clientCert__allowUnknownCA** eigenschap. In de volgende tabel wordt de mogelijke waarde(en) vastgelegd.

| Mogelijke waarde(n) | Beschrijving |
| ----------------  | ------------|
| waar | Standaard. Hiermee kunnen zelfondertekende certificaten met succes worden gepresenteerd.
| false | Wordt mislukt als zelfondertekende certificaten worden weergegeven.

>[!IMPORTANT]
>In productiescenario's u **inbound__clientAuth__clientCert__allowUnknownCA** instellen op **false**.

### <a name="sas-key-based-client-authentication"></a>SAS-clientverificatie op basis van sleutels

Naast verificatie op basis van certificaten kan de Event Grid-module ook SAS Key-gebaseerde verificatie doen. SAS-toets is als een geheim geconfigureerd in de Event Grid-module die het moet gebruiken om alle binnenkomende oproepen te valideren. Clients moeten het geheim opgeven in de HTTP Header 'aeg-sas-key'. Aanvraag wordt afgewezen `UnAuthorized` als het niet overeenkomt.

De configuratie voor het beheren van SAS-verificatie op basis van sleutels is **inbound__clientAuth__sasKeys__enabled.**

| Mogelijke waarde(n) | Beschrijving  |
| ----------------  | ------------ |
| waar | Hiermee u verificatie op basis van SAS-sleutels gebruiken. Vereist **inbound__clientAuth__sasKeys__key1** of **inbound__clientAuth__sasKeys__key2**
| false | Standaard. Verificatie op basis van SAS Key is uitgeschakeld.

 **inbound__clientAuth__sasKeys__key1** en **inbound__clientAuth__sasKeys__key2** zijn sleutels waarmee u de module Gebeurtenisraster configureert om te controleren op binnenkomende aanvragen. Ten minste één van de sleutels moet worden geconfigureerd. De klant die het verzoek indient, moet de sleutel presenteren als onderdeel van de**aeg-sas-key**van de aanvraagheader. Als beide sleutels zijn geconfigureerd, kan de client een van de sleutels presenteren.

> [!NOTE]
>U beide verificatiemethoden configureren. In een dergelijk geval wordt de SAS-sleutel eerst gecontroleerd en alleen als dat mislukt, wordt de verificatie op basis van certificaten uitgevoerd. Als een verzoek om te slagen, hoeft slechts één van de verificatiemethoden te slagen.

## <a name="outbound-client-authentication"></a>Uitgaande clientverificatie

Client in uitgaande context verwijst naar de module Gebeurtenisraster. De bewerking die wordt uitgevoerd is het leveren van evenementen aan abonnees. Een abonnement op modules wordt beschouwd als de server.

Elke IoT Edge-module krijgt een identiteitscertificaat toegewezen door de beveiligingsdaemon van de IoT Edge. We gebruiken het identiteitscertificaat voor uitgaande gesprekken. Na afloop wordt de module automatisch vernieuwd met een nieuw certificaat van de IoT Edge security daemon.

De configuratie voor het beheren van uitgaande clientverificatie is **outbound__clientAuth__clientCert__enabled**.

| Mogelijke waarde(n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Voor alle uitgaande aanvragen van de module Gebeurtenisraster moet een certificaat worden ingediend. Moet **outbound__clientAuth__clientCert__source**configureren .
| false | De module Gebeurtenisraster hoeft het certificaat niet te presenteren.

De configuratie die de bron voor het certificaat regelt, is **outbound__clientAuth__clientCert__source**.

| Mogelijke waarde(n) | Beschrijving |
| ---------------- | ------------ |
| IoT Edge | Standaard. Gebruikt het identiteitscertificaat van de module geconfigureerd door IoT Edge security daemon.

### <a name="outbound-server-authentication"></a>Uitgaande serververificatie

Een van de doeltypen voor een eventgridabonnee is "Webhook". Standaard worden alleen HTTPS-eindpunten geaccepteerd voor dergelijke abonnees.

De configuratie om het webhook-bestemmingsbeleid **te beheren outbound__webhook__httpsOnly**.

| Mogelijke waarde(n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Hiermee staan alleen abonnees met HTTPS-eindpunt toe.
| false | Hiermee kunnen abonnees met http- of HTTPS-eindpunt worden gebruikt.

De module Gebeurtenisraster valideert standaard het servercertificaat van de abonnee. U validatie overslaan door **outbound__webhook__skipServerCertValidation**te overschrijven. Mogelijke waarden zijn:

| Mogelijke waarde(n) | Beschrijving |
| ----------------  | ------------ |
| waar | Valideer het servercertificaat van de abonnee niet.
| false | Standaard. Het servercertificaat van abonnees valideren.

Als het certificaat van een abonnee zelf is ondertekend, worden dergelijke abonnees standaard geweigerd door de module Gebeurtenisraster. Als u zelfondertekend certificaat wilt toestaan, u **outbound__webhook__allowUnknownCA**overschrijven. In de volgende tabel wordt de mogelijke waarde(en) vastgelegd.

| Mogelijke waarde(n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Hiermee kunnen zelfondertekende certificaten met succes worden gepresenteerd.
| false | Wordt mislukt als zelfondertekende certificaten worden weergegeven.

>[!IMPORTANT]
>In productiescenario's wilt u **outbound__webhook__allowUnknownCA** instellen op **false.**

> [!NOTE]
>De IoT Edge-omgeving genereert zelfondertekende certificaten. Aanbeveling is het genereren van certificaten uitgegeven door geautoriseerde CA's voor productieworkloads en **set toestaanUnknownCA** eigenschap op zowel inkomende als uitgaande naar **false**.

## <a name="summary"></a>Samenvatting

Een module Event Grid is **standaard beveiligd**. We raden u aan deze standaardwaarden voor uw productie-implementaties te behouden.

De volgende zijn de leidende principes te gebruiken tijdens het configureren:

* Alleen HTTPS-aanvragen toestaan in de module.
* Alleen clientverificatie op basis van certificaten toestaan. Sta alleen certificaten toe die zijn uitgegeven door bekende CA's. Zelfondertekende certificaten niet toestaan.
* SASKey-gebaseerde clientverificatie weigeren.
* Presenteer altijd het identiteitscertificaat van de Event Grid-module bij uitgaande oproepen.
* Alleen HTTPS-abonnees toestaan voor Webhook-doeltypen.
* Valideer altijd het servercertificaat van abonnees voor webhook-doeltypen. Alleen certificaten toestaan die zijn uitgegeven door bekende CA's. Zelfondertekende certificaten niet toestaan.

Standaard wordt de module Gebeurtenisraster geïmplementeerd met de volgende configuratie:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
