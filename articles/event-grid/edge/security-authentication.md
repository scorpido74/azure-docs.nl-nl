---
title: Beveiliging en verificatie-Azure Event Grid IoT Edge | Microsoft Docs
description: Beveiliging en verificatie in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992156"
---
# <a name="security-and-authentication"></a>Verificatie en beveiliging

Beveiliging en verificatie is een geavanceerd concept en vereist eerst kennis met Event Grid basis beginselen. Begin [hier](concepts.md) als u geen ervaring hebt met Event Grid op IOT Edge. Event Grid module bouwt voort op de bestaande beveiligings infrastructuur op IoT Edge. Raadpleeg [deze documentatie](../../iot-edge/security.md) voor meer informatie en installatie.

In de volgende secties wordt beschreven hoe deze instellingen worden beveiligd en geverifieerd:

* TLS-configuratie
* Verificatie van inkomende clients
* Uitgaande server authenticatie
* Verificatie van uitgaande clients

>[!IMPORTANT]
>De bestaande infra structuur die beschikbaar is op IoT Edge van Event Grid module beveiliging en verificatie De veronderstelling is dat IoT Edge subsysteem veilig is.

>[!IMPORTANT]
>Event Grid configuratie is **standaard beveiligd**. In de volgende subsecties worden alle opties en mogelijke waarden beschreven die u kunt gebruiken om aspecten van verificatie te overschrijven. Meer informatie over de impact voordat u wijzigingen aanbrengt. Om de wijzigingen van kracht te laten worden, moet de Event Grid-module opnieuw worden geïmplementeerd.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-configuratie (a. k. a Server-verificatie)

Event Grid-module fungeert zowel HTTP-als HTTPS-eind punten. Aan elke IoT Edge-module is een server certificaat toegewezen door de beveiligings-daemon van de IoT Edge. We gebruiken het server certificaat om het eind punt te beveiligen. Na verloop van tijd wordt de module automatisch vernieuwd met een nieuw certificaat van de IoT Edge Security daemon.

Standaard is alleen HTTPS-communicatie toegestaan. U kunt dit gedrag negeren via de **binnenkomende: serverAuth: tlsPolicy** -configuratie. In de volgende tabel worden de mogelijke waarden van deze eigenschap vastgelegd.

| Mogelijke waarde (n) | Beschrijving |
| ---------------- | ------------ |
| Streng | Standaard. Alleen HTTPS inschakelen
| Ingeschakeld | Maakt zowel HTTP als HTTPS mogelijk
| Uitgeschakeld | Schakelt alleen HTTP in

## <a name="inbound-client-authentication"></a>Verificatie van inkomende clients

Clients zijn entiteiten die beheer en/of runtime-bewerkingen uitvoeren. Clients kunnen andere IoT Edge modules zijn, niet-IoT-toepassingen.

De module Event Grid ondersteunt twee typen client verificatie:-

* Op basis van Shared Access Signature (SAS)-sleutel
* Op basis van certificaten

De module Event Grid is standaard geconfigureerd om alleen op certificaten gebaseerde verificatie te accepteren. Bij het opstarten haalt Event Grid-module ' TrustBundle ' op uit IoT Edge Security daemon en gebruikt deze om een client certificaat te valideren. Client certificaten die niet worden omgezet in deze keten, worden geweigerd met `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Client authenticatie op basis van certificaten

Verificatie op basis van certificaten is standaard ingeschakeld. U kunt ervoor kiezen op certificaten gebaseerde verificatie uit te scha kelen via de eigenschap **Inkomend: clientAuth: clientCert: ingeschakeld**. In de volgende tabel worden mogelijke waarden vastgelegd.

| Mogelijke waarde (n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Vereist dat alle aanvragen in de module Event Grid een client certificaat presen teren. Bovendien moet u **inkomend verkeer configureren: clientAuth: clientCert: Source**.
| onwaar | Laat een client niet afdwingen dat het certificaat wordt weer gegeven.

In de volgende tabel worden mogelijke waarden voor **inkomend verkeer vastgelegd: clientAuth: clientCert: Source**

| Mogelijke waarde (n) | Beschrijving |
| ---------------- | ------------ |
| IoT Edge | Standaard. Maakt gebruik van de Trustbundle van de IoT Edge om alle client certificaten te valideren.

Als een client een zelfondertekende presenteert, worden deze aanvragen standaard door de module Event Grid geweigerd. U kunt zelfondertekende client certificaten toestaan via de **binnenkomende: clientAuth: clientCert: allowUnknownCA** -eigenschap. In de volgende tabel worden mogelijke waarden vastgelegd.

| Mogelijke waarde (n) | Beschrijving |
| ----------------  | ------------|
| waar | Standaard. Toestaan dat zelfondertekende certificaten correct worden weer gegeven.
| onwaar | Mislukt als er zelfondertekende certificaten worden weer gegeven.

>[!IMPORTANT]
>In productie scenario's wilt u mogelijk **Inkomend: clientAuth: clientCert: allowUnknownCA** instellen op **False**.

### <a name="sas-key-based-client-authentication"></a>SAS-client authenticatie op basis van sleutel

Naast op certificaten gebaseerde verificatie kan de module Event Grid ook op SAS-sleutel gebaseerde verificatie uitvoeren. SAS-sleutel is een geheim dat is geconfigureerd in de Event Grid-module, die het moet gebruiken om alle binnenkomende oproepen te valideren. Clients moeten het geheim opgeven in de HTTP-header ' AEG-SAS-key '. De aanvraag wordt afgewezen met `UnAuthorized` als deze niet overeenkomt.

De configuratie voor het beheren van SAS-sleutel verificatie is **Inkomend: clientAuth: sasKeys: ingeschakeld**.

| Mogelijke waarde (n) | Beschrijving  |
| ----------------  | ------------ |
| waar | Hiermee staat u verificatie op basis van SAS-sleutel toe. Vereist **Inkomend: clientAuth: sasKeys: Key1** of **Inkomend: clientAuth: sasKeys: Key2**
| onwaar | Standaard. Verificatie op basis van SAS-sleutel is uitgeschakeld.

 **Inkomend: clientAuth: sasKeys: Key1** en **inbound: clientAuth: sasKeys: Key2** zijn sleutels waarmee u de Event grid module configureert om te controleren op inkomende aanvragen. Ten minste één van de sleutels moet worden geconfigureerd. Client die de aanvraag maakt, moet de sleutel als onderdeel van de aanvraag header '**AEG-SAS-Key**' presen teren. Als beide sleutels zijn geconfigureerd, kan de client een van de sleutels bevatten.

> [!NOTE]
>U kunt beide verificatie methoden configureren. In een dergelijk geval wordt de SAS-sleutel eerst gecontroleerd en alleen als dit mislukt, wordt de verificatie op basis van certificaten uitgevoerd. Een aanvraag kan alleen worden uitgevoerd als slechts een van de verificatie methoden slaagt.

## <a name="outbound-client-authentication"></a>Verificatie van uitgaande clients

De client in de uitgaande context verwijst naar de module Event Grid. De bewerking die wordt uitgevoerd, levert gebeurtenissen aan abonnees. Geabonneerde modules worden beschouwd als de-server.

Aan elke IoT Edge-module wordt een identiteits certificaat toegewezen door de beveiligings-daemon van de IoT Edge. We gebruiken het identiteits certificaat voor uitgaande oproepen. Na verloop van tijd wordt de module automatisch vernieuwd met een nieuw certificaat van de IoT Edge Security daemon.

De configuratie voor het beheren van de uitgaande client verificatie is **uitgaand: clientAuth: clientCert: ingeschakeld**.

| Mogelijke waarde (n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Vereist dat alle uitgaande aanvragen van de module Event Grid een certificaat presen teren. Er moet een **uitgaand verkeer worden geconfigureerd: clientAuth: clientCert: Source**.
| onwaar | Event Grid-module niet vereist om het certificaat weer te geven.

De configuratie waarmee de bron voor het certificaat wordt beheerd, is **uitgaand: clientAuth: clientCert: Source**.

| Mogelijke waarde (n) | Beschrijving |
| ---------------- | ------------ |
| IoT Edge | Standaard. Maakt gebruik van het identiteits certificaat van de module dat is geconfigureerd door IoT Edge Security daemon.

### <a name="outbound-server-authentication"></a>Uitgaande server authenticatie

Een van de doel typen voor een Event Grid-abonnee is webhook. Standaard worden alleen HTTPS-eind punten geaccepteerd voor dergelijke abonnees.

De configuratie voor het beheren van het doel beleid voor webhooks **: webhook: httpsOnly**.

| Mogelijke waarde (n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Alleen abonnees met een HTTPS-eind punt zijn toegestaan.
| onwaar | Hiermee kunnen abonnees een HTTP-of HTTPS-eind punt hebben.

Event Grid-module valideert standaard het server certificaat van de abonnee. U kunt de validatie overs Laan door uitgaande berichten te overschrijven **: webhook: skipServerCertValidation**. Mogelijke waarden zijn:-

| Mogelijke waarde (n) | Beschrijving |
| ----------------  | ------------ |
| waar | Valideer het server certificaat van de abonnee niet.
| onwaar | Standaard. Het server certificaat van de abonnee valideren.

Als het certificaat van de abonnee zelf is ondertekend, worden deze abonnees door standaard Event Grid-module geweigerd. Als u een zelfondertekend certificaat wilt toestaan, kunt u **uitgaand verkeer overschrijven: webhook: allowUnknownCA**. In de volgende tabel worden de mogelijke waarden vastgelegd.

| Mogelijke waarde (n) | Beschrijving |
| ----------------  | ------------ |
| waar | Standaard. Toestaan dat zelfondertekende certificaten correct worden weer gegeven.
| onwaar | Mislukt als er zelfondertekende certificaten worden weer gegeven.

>[!IMPORTANT]
>In productie scenario's moet u **uitgaand instellen: webhook: allowUnknownCA** in **Onwaar**.

> [!NOTE]
>IoT Edge omgeving genereert zelfondertekende certificaten. Aanbeveling is het genereren van certificaten die zijn uitgegeven door geautoriseerde certificerings instanties voor werk belastingen voor productie en stel de eigenschap **allowUnknownCA** in op inkomend en uitgaand naar **Onwaar**.

## <a name="summary"></a>Samenvatting

Een Event Grid module is **standaard beveiligd**. We raden u aan deze standaard waarden te bewaren voor uw productie-implementaties.

Hieronder vindt u de richt lijnen die u kunt gebruiken bij het configureren van:-

* Sta alleen HTTPS-aanvragen in de module toe.
* Alleen client authenticatie op basis van certificaten toestaan. Sta alleen certificaten toe die zijn uitgegeven door bekende certificerings instanties. Zelfondertekende certificaten niet toestaan.
* Client verificatie op basis van SASKey niet toestaan.
* Het identiteits certificaat van Event Grid module altijd presen teren bij uitgaande oproepen.
* Alleen HTTPS-abonnees voor webhook-doel typen toestaan.
* Valideer altijd het server certificaat van de abonnee voor webhook-doel typen. Sta alleen certificaten toe die zijn uitgegeven door bekende certificerings instanties. Zelfondertekende certificaten niet toestaan.

Event Grid module wordt standaard geïmplementeerd met de volgende configuratie:-

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
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
