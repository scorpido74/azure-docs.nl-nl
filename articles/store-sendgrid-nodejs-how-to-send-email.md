---
title: De e-mailservice SendGrid gebruiken (Node.js) | Microsoft Documenten
description: Meer informatie over het verzenden van e-mail met de e-mailservice SendGrid op Azure. Codevoorbeelden die zijn geschreven met de API Node.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60931701"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>E-mail verzenden via SendGrid vanaf Node.js

In deze handleiding wordt uitgelegd hoe u veelvoorkomende programmeertaken uitvoeren met de e-mailservice SendGrid op Azure. De voorbeelden worden geschreven met behulp van de Node.js API. De scenario's die onder meer worden behandeld, zijn **het maken van e-mail,** **het verzenden van e-mail,** **het toevoegen van bijlagen,** **het gebruik van filters**en het bijwerken **van eigenschappen**. Zie de sectie [Volgende stappen](#next-steps) voor meer informatie over SendGrid en het verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid Email Service?

SendGrid is een [cloudgebaseerde e-mailservice] die betrouwbare [transactionele e-maillevering,]schaalbaarheid en realtime analyses biedt, samen met flexibele API's die aangepaste integratie eenvoudig maken. Veelvoorkomende sendgrid-gebruiksscenario's zijn:

* Automatisch ontvangstbewijzen naar klanten verzenden
* Het beheren van distributielijsten voor het verzenden van klanten maandelijkse e-flyers en speciale aanbiedingen
* Realtime statistieken verzamelen voor zaken als geblokkeerde e-mail en reactievermogen van klanten
* Rapporten genereren om trends te identificeren
* Vragen van klanten doorsturen
* E-mailmeldingen van uw toepassing

Zie voor meer [https://sendgrid.com](https://sendgrid.com)informatie .

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Verwijzen naar de module SendGrid Node.js

De SendGrid-module voor Node.js kan via de node package manager (npm) worden geïnstalleerd met behulp van de volgende opdracht:

```bash
npm install sendgrid
```

Na de installatie u de module in uw toepassing nodig hebben met behulp van de volgende code:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Met de module SendGrid worden de functies **SendGrid** en **E-mail** geëxporteerd.
**SendGrid** is verantwoordelijk voor het verzenden van e-mail via de Web API, terwijl **e-mail** een e-mailbericht inkapselt.

## <a name="how-to-create-an-email"></a>Hoe: Een e-mail maken

Als u een e-mailbericht maakt met de module SendGrid, wordt eerst een e-mailbericht gemaakt met de functie E-mail en vervolgens verzonden met de functie SendGrid. Het volgende is een voorbeeld van het maken van een nieuw bericht met de functie E-mail:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

U ook een HTML-bericht opgeven voor clients die dit ondersteunen door de eigenschap html in te stellen. Bijvoorbeeld:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Als u zowel de tekst- als de html-eigenschappen instelt, u een sierlijke terugval naar tekstinhoud instellen voor clients die HTML-berichten niet kunnen ondersteunen.

Zie [sendgrid-nodejs][sendgrid-nodejs]voor meer informatie over alle eigenschappen die worden ondersteund door de functie E-mail.

## <a name="how-to-send-an-email"></a>Hoe: Een e-mail verzenden

Nadat u een e-mailbericht hebt gemaakt met de functie E-mail, u het verzenden via de web-API van SendGrid. 

### <a name="web-api"></a>Web-API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Terwijl de bovenstaande voorbeelden het doorgeven van een e-mailobject en de terugbelfunctie weergeven, u ook rechtstreeks de verzendfunctie aanroepen door e-maileigenschappen rechtstreeks op te geven. Bijvoorbeeld:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Hoe: Een bijlage toevoegen
Bijlagen kunnen aan een bericht worden toegevoegd door de bestandsnaam(en) en pad(en) op te geven in de eigenschap **bestanden.** In het volgende voorbeeld wordt het verzenden van een bijlage aangetoond:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> Bij het gebruik van de eigenschap **bestanden** moet het bestand toegankelijk zijn via [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Als het bestand dat u wilt koppelen wordt gehost in Azure Storage, zoals in een Blob-container, moet u het bestand eerst kopiëren naar lokale opslag of naar een Azure-station voordat het als bijlage kan worden verzonden met de eigenschap **bestanden.**
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Hoe: Filters gebruiken om voetteksten en tracking in te schakelen

SendGrid biedt extra e-mailfunctionaliteit door het gebruik van filters. Dit zijn instellingen die kunnen worden toegevoegd aan een e-mailbericht om specifieke functionaliteit in te schakelen, zoals het inschakelen van kliktracking, Google analytics, het bijhouden van abonnementen, enzovoort. Zie [Filterinstellingen][Filter Settings]voor een volledige lijst met filters .

Filters kunnen op een bericht worden toegepast met de eigenschap **filters.**
Elk filter wordt opgegeven door een hash met filterspecifieke instellingen.
In de volgende voorbeelden worden de filterfilters voor het bijhouden van de voettekst en klikken gedemonstreerd:

### <a name="footer"></a>Voettekst

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Klik op Bijhouden

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Hoe: E-maileigenschappen bijwerken

Sommige e-maileigenschappen kunnen worden overschreven met **setProperty** of worden toegevoegd met **addProperty.** U bijvoorbeeld extra geadresseerden toevoegen door

```javascript
email.addTo('jeff@contoso.com');
```

of een filter instellen met behulp van

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Zie [sendgrid-nodejs voor][sendgrid-nodejs]meer informatie .

## <a name="how-to-use-additional-sendgrid-services"></a>Hoe: Extra SendGrid-services gebruiken

SendGrid biedt webgebaseerde API's die u gebruiken om extra SendGrid-functionaliteit uit uw Azure-toepassing te gebruiken. Zie de [SendGrid API-documentatie][SendGrid API documentation]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu u de basisbeginselen van de SendGrid-e-mailservice hebt geleerd, volgt u deze koppelingen voor meer informatie.

* SendGrid Node.js module repository: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API-documentatie:<https://sendgrid.com/docs>
* Speciale sendGrid-aanbieding voor Azure-klanten:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[cloudgebaseerde e-mailservice]: https://sendgrid.com/email-solutions
[transactionele e-mailbezorging]: https://sendgrid.com/transactional-email
