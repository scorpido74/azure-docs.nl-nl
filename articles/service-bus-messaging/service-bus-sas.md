---
title: Toegangsbeheer voor Azure Service Bus met gedeelde toegangshandtekeningen
description: Overzicht van toegangsbeheer servicebus met behulp van overzicht van gedeelde toegangshandtekeningen, details over SAS-autorisatie met Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: c381d9413c4003bc2ab9a9357ff2769e84d14c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259472"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Toegangsbeheer voor servicebussen met handtekeningen voor gedeelde toegang

*SAS (Shared Access Signatures)* is het primaire beveiligingsmechanisme voor servicebusberichten. Dit artikel bespreekt SAS, hoe ze werken en hoe ze op een platform-agnostische manier te gebruiken.

SAS bewaakt de toegang tot servicebus op basis van autorisatieregels. Deze zijn geconfigureerd op een naamruimte of een berichtenentiteit (relay, wachtrij of onderwerp). Een autorisatieregel heeft een naam, is gekoppeld aan specifieke rechten en bevat een paar cryptografische sleutels. U gebruikt de naam en sleutel van de regel via de Service Bus SDK of in uw eigen code om een SAS-token te genereren. Een client kan het token vervolgens doorgeven aan Service Bus om de autorisatie voor de gevraagde bewerking te bewijzen.

> [!NOTE]
> Azure Service Bus ondersteunt het toestaan van toegang tot een naamruimte van een ServiceBus en de entiteiten daarvan met Azure Active Directory (Azure AD). Het toestaan van gebruikers of toepassingen met OAuth 2.0-token die door Azure AD is geretourneerd, biedt superieure beveiliging en gebruiksgemak ten opzichte van sas (Shared Access Signatures). Met Azure AD is het niet nodig om de tokens in uw code op te slaan en potentiële beveiligingsproblemen te riskeren.
>
> Microsoft raadt aan om Azure AD waar mogelijk te gebruiken met uw Azure Service Bus-toepassingen. Raadpleeg voor meer informatie de volgende artikelen:
> - [Een toepassing verifiëren en autoriseren met Azure Active Directory om toegang te krijgen tot Azure Service Bus-entiteiten](authenticate-application.md).
> - [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure Service Bus-bronnen](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Overzicht van SAS

Gedeelde toegangshandtekeningen zijn een autorisatiemechanisme op basis van claims met behulp van eenvoudige tokens. Met behulp van SAS worden sleutels nooit doorgegeven aan de draad. Sleutels worden gebruikt om cryptografisch tekeninformatie die later kan worden geverifieerd door de service. SAS kan worden gebruikt vergelijkbaar met een gebruikersnaam en wachtwoord schema waar de klant in het bezit is van een autorisatie regel naam en een overeenkomende sleutel. SAS kan ook worden gebruikt vergelijkbaar met een federatief beveiligingsmodel, waarbij de client een tijdelijk en ondertekend toegangstoken ontvangt van een beveiligingstokenservice zonder ooit in het bezit te komen van de ondertekeningssleutel.

SAS-verificatie in ServiceBus is geconfigureerd met de naam [Shared Access Authorization Rules](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) met bijbehorende toegangsrechten en een paar primaire en secundaire cryptografische sleutels. De toetsen zijn 256-bits waarden in base64-weergave. U regels configureren op naamruimteniveau, op [servicebusrelais,](../service-bus-relay/relay-what-is-it.md) [wachtrijen](service-bus-messaging-overview.md#queues)en [onderwerpen](service-bus-messaging-overview.md#topics).

Het token [Voor gedeelde toeganghandtekening](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) bevat de naam van de gekozen autorisatieregel, de URI van de resource die wordt geopend, een verlopen moment en een HMAC-SHA256-cryptografische handtekening die over deze velden wordt berekend met behulp van de primaire of de secundaire cryptografische sleutel van de gekozen autorisatieregel.

## <a name="shared-access-authorization-policies"></a>Beleid voor toegangsautorisatie voor gedeelde toegang

Elke servicebusnaamruimte en elke servicebus-entiteit heeft een beleid voor toegangsautorisatie voor gedeelde toegang dat bestaat uit regels. Het beleid op naamruimteniveau is van toepassing op alle entiteiten in de naamruimte, ongeacht hun individuele beleidsconfiguratie.

Voor elke autorisatiebeleidsregel bepaalt u drie gegevens: **naam,** **bereik**en **rechten**. De **naam** is precies dat; een unieke naam binnen dat bereik. De scope is eenvoudig genoeg: het is de URI van de resource in kwestie. Voor een naamruimte van een servicebus is het bereik de volledig `https://<yournamespace>.servicebus.windows.net/`gekwalificeerde domeinnaam (FQDN), zoals .

De rechten die door de beleidsregel worden verleend, kunnen een combinatie zijn van:

* 'Verzenden' - Verleent het recht om berichten te sturen naar de entiteit
* 'Listen' - Verleent het recht om te luisteren (relais) of ontvangen (wachtrij, abonnementen) en alle gerelateerde berichtafhandeling
* 'Beheren' - verleent het recht om de topologie van de naamruimte te beheren, inclusief het maken en verwijderen van entiteiten

Het recht 'Beheren' omvat de rechten 'Verzenden' en 'Ontvangen'.

Een naamruimte- of entiteitsbeleid kan maximaal 12 regels voor gedeelde toegangsautorisatie bevatten, die ruimte bieden voor drie sets regels, die elk betrekking hebben op de basisrechten en de combinatie van Verzenden en luisteren. Deze limiet onderstreept dat het SAS-beleidsarchief niet bedoeld is als gebruikers- of serviceaccountarchief. Als uw toepassing toegang moet verlenen tot Service Bus op basis van gebruikers- of serviceidentiteiten, moet deze een beveiligingstokenservice implementeren die SAS-tokens uitgeeft na een verificatie- en toegangscontrole.

Aan een autorisatieregel wordt een *primaire sleutel* en een *secundaire sleutel toegewezen.* Dit zijn cryptografisch sterke sleutels. Verlies ze niet of lek ze niet - ze zijn altijd beschikbaar in de [Azure-portal.][Azure portal] U een van de gegenereerde sleutels gebruiken en u ze op elk gewenst moment regenereren. Als u een sleutel in het beleid regenereert of wijzigt, worden alle eerder uitgegeven tokens op basis van die sleutel direct ongeldig. Doorgaande verbindingen die op basis van dergelijke tokens zijn gemaakt, blijven echter werken totdat het token verloopt.

Wanneer u een naamruimte voor servicebus maakt, wordt automatisch een beleidsregel met de naamruimte **rootbeheerGedeeldtoegangssleutel** gemaakt. Met dit beleid machtigingen beheren voor de volledige naamruimte. Het wordt aanbevolen dat u deze regel behandelt als een **beheerd rootaccount** en deze niet gebruikt in uw toepassing. U aanvullende beleidsregels maken op het tabblad **Configureren** voor de naamruimte in de portal, via Powershell of Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuratie voor verificatie van gedeelde toegangshandtekening

U de regel [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configureren voor naamruimten, wachtrijen of onderwerpen van servicebus. Het configureren van een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) voor een Service Bus-abonnement wordt momenteel niet ondersteund, maar u regels gebruiken die zijn geconfigureerd voor een naamruimte of onderwerp om de toegang tot abonnementen te beveiligen. Zie het voorbeeld [Van SAS-verificatie (Shared Access Signature) gebruiken met servicebusabonnementen](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) voor een werkvoorbeeld dat deze procedure illustreert.

![Sas](./media/service-bus-sas/service-bus-namespace.png)

In dit cijfer zijn de autorisatieregels *manageRuleNS,* *sendRuleNS*en *listenRuleNS* van toepassing op zowel wachtrij Q1 als onderwerp T1, terwijl *listenRuleQ* en *sendRuleQ* alleen van toepassing zijn op q1- en *sendRuleT-wachtrijen,* alleen van toepassing zijn op onderwerp T1.

## <a name="generate-a-shared-access-signature-token"></a>Een token voor gedeelde toegangshandtekeningen genereren

Elke client die toegang heeft tot de naam van een naam van een autorisatieregel naam en een van de ondertekeningssleutels kan een SAS-token genereren. Het token wordt gegenereerd door een tekenreeks in de volgende indeling te maken:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**- Token expiry instant. Gehele getal dat seconden `00:00:00 UTC` sinds het tijdvak op 1 Januari 1970 (unix tijdvak) weerspiegelt wanneer het teken verloopt.
* **`skn`**- Naam van de autorisatieregel.
* **`sr`**- URI van de bron die wordt geopend.
* **`sig`**- Handtekening.

Het `signature-string` is de SHA-256 hash berekend over de resource URI **(scope** zoals beschreven in de vorige sectie) en de tekenreeksweergave van het token-verlopen-direct, gescheiden door LF.

De hash-berekening lijkt op de volgende pseudo-code en retourneert een hashwaarde van 256 bits/32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Het token bevat de niet-gehashte waarden, zodat de ontvanger de hash opnieuw kan berekenen met dezelfde parameters, om te controleren of de uitgever in het bezit is van een geldige ondertekeningssleutel.

De bron URI is de volledige URI van de Service Bus-bron waartoe toegang wordt geclaimd. Bijvoorbeeld, `http://<namespace>.servicebus.windows.net/<entityPath>` of `sb://<namespace>.servicebus.windows.net/<entityPath>`; dat wil `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`zeggen, . 

**De URI moet [procent-gecodeerd](https://msdn.microsoft.com/library/4fkewx0t.aspx)zijn.**

De regel voor gedeelde toegangsautorisatie die wordt gebruikt voor ondertekening, moet worden geconfigureerd op de entiteit die is opgegeven door deze URI of door een van de hiërarchische ouders. Bijvoorbeeld, `http://contoso.servicebus.windows.net/contosoTopics/T1` of `http://contoso.servicebus.windows.net` in het vorige voorbeeld.

Een SAS-token is geldig voor `<resourceURI>` alle resources `signature-string`die vooraf zijn gekoppeld aan de gebruikte .

## <a name="regenerating-keys"></a>Toetsen regenereren

Het wordt aanbevolen om de sleutels die worden gebruikt in het object [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) periodiek te regenereren. De primaire en secundaire sleutelsleuven bestaan, zodat u toetsen geleidelijk roteren. Als uw toepassing over het algemeen de primaire sleutel gebruikt, u de primaire sleutel kopiëren naar de secundaire sleutelsleuf en alleen vervolgens de primaire sleutel regenereren. De nieuwe primaire sleutelwaarde kan vervolgens worden geconfigureerd in de clienttoepassingen, die blijven toegang hebben met behulp van de oude primaire sleutel in de secundaire sleuf. Zodra alle clients zijn bijgewerkt, u de secundaire sleutel regenereren om de oude primaire sleutel eindelijk met pensioen te laten gaan.

Als u weet of vermoedt dat een sleutel is gecompromitteerd en u de sleutels moet intrekken, u zowel de [primaire sleutel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) als de [secundaire sleutel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) van een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)regenereren, vervangen door nieuwe sleutels. Deze procedure maakt alle tokens ongeldig die zijn ondertekend met de oude sleutels.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Verificatie van gedeelde toegangshandtekeningen met servicebus

De als volgt beschreven scenario's omvatten de configuratie van autorisatieregels, het genereren van SAS-tokens en clientautorisatie.

Zie Verificatie van [gedeelde toegangshandtekening met Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)voor een volledig werkend voorbeeld van een Service Bus-toepassing die de configuratie illustreert en sas-autorisatie gebruikt. Een gerelateerd voorbeeld dat het gebruik illustreert van SAS-autorisatieregels die zijn geconfigureerd voor naamruimten of onderwerpen om Service Bus-abonnementen te beveiligen, is hier beschikbaar: [Verificatie van De Gedeelde Toegang handtekening (SAS) gebruiken met Service Bus-abonnementen](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Regels voor beheerde toegangsautorisatie voor een entiteit

Met Service Bus .NET Framework-bibliotheken hebt u toegang tot een object [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) dat is geconfigureerd in een wachtrij of onderwerp van servicebus via de verzameling [Autorisatieregels](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) in de bijbehorende [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) of [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

In de volgende code ziet u hoe u autorisatieregels voor een wachtrij toevoegt.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Beheerhandtekening voor gedeelde toegang gebruiken

Toepassingen die de Azure .NET SDK gebruiken met de Service Bus .NET-bibliotheken kunnen SAS-autorisatie gebruiken via de klasse [SharedAccessSignatureTokenProvider.](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) De volgende code illustreert het gebruik van de tokenprovider om berichten naar een wachtrij voor servicebus te verzenden. Als alternatief voor het gebruik dat hier wordt weergegeven, u ook een eerder uitgegeven token doorgeven aan de fabrieksmethode van de tokenprovider.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

U de tokenprovider ook rechtstreeks gebruiken voor het uitgeven van tokens om door te geven aan andere clients.

Verbindingstekenreeksen kunnen een regelnaam *(SharedAccessKeyName)* en regelsleutel *(SharedAccessKey*) of een eerder uitgegeven token *(SharedAccessSignature)* bevatten. Wanneer deze aanwezig zijn in de verbindingstekenreeks die wordt doorgegeven aan een constructor of fabrieksmethode die een verbindingstekenreeks accepteert, wordt de SAS-tokenprovider automatisch gemaakt en bevolkt.

Als u SAS-autorisatie wilt gebruiken met servicebusrelais, u SAS-sleutels gebruiken die zijn geconfigureerd in de naamruimte van de servicebus. Als u expliciet een relais maakt op het object[namespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) met een [relaydescription,](/dotnet/api/microsoft.servicebus.messaging.relaydescription)u de SAS-regels alleen voor dat relais instellen. Als u SAS-autorisatie wilt gebruiken met Service Bus-abonnementen, u SAS-sleutels gebruiken die zijn geconfigureerd in een naamruimte van een servicebus of op een onderwerp.

## <a name="use-the-shared-access-signature-at-http-level"></a>De handtekening voor gedeelde toegang gebruiken (op HTTP-niveau)

Nu u weet hoe u gedeelde toegangshandtekeningen maken voor entiteiten in servicebus, bent u klaar om een HTTP-BERICHT uit te voeren:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Vergeet niet, dit werkt voor alles. U SAS maken voor een wachtrij, onderwerp of abonnement.

Als u een afzender of client een SAS-token geeft, hebben deze niet direct de sleutel en kunnen ze de hash niet omkeren om deze te verkrijgen. Als zodanig heb je controle over wat ze kunnen openen, en voor hoe lang. Een belangrijk ding om te onthouden is dat als u de primaire sleutel in het beleid wijzigt, alle gedeelde toegangshandtekeningen die hiermee zijn gemaakt, ongeldig worden gemaakt.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>De handtekening voor gedeelde toegang gebruiken (op AMQP-niveau)

In de vorige sectie zag u hoe u het SAS-token gebruiken met een HTTP POST-verzoek voor het verzenden van gegevens naar de ServiceBus. Zoals u weet, u in veel scenario's toegang krijgen tot Service Bus via het Advanced Message Queuing Protocol (AMQP), dat het voorkeursprotocol is dat u om prestatieredenen gebruiken. Het SAS-tokengebruik met AMQP wordt beschreven in het document [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) dat sinds 2013 in concept werkt, maar vandaag de dag goed wordt ondersteund door Azure.

Voordat de uitgever gegevens naar Service Bus gaat verzenden, moet de uitgever het SAS-token in een AMQP-bericht naar een goed gedefinieerde AMQP-knooppunt met de naam **$cbs** verzenden (u het zien als een speciale wachtrij die door de service wordt gebruikt om alle SAS-tokens te verkrijgen en te valideren). De uitgever moet het veld **ReplyTo** opgeven in het AMQP-bericht. dit is het knooppunt waarin de service antwoordt op de uitgever met het resultaat van de tokenvalidatie (een eenvoudig aanvraag-/antwoordpatroon tussen uitgever en service). Dit antwoordknooppunt wordt gemaakt "on the fly", sprekend over "dynamische creatie van externe knooppunt" zoals beschreven door de AMQP 1.0-specificatie. Nadat is gecontroleerd of het SAS-token geldig is, kan de uitgever verder gaan en gegevens naar de service verzenden.

In de volgende stappen wordt uitgelegd hoe u het SAS-token met AMQP-protocol verzendt met de [AMQP.NET Lite-bibliotheek.](https://github.com/Azure/amqpnetlite) Dit is handig als u de officiële Service Bus SDK (bijvoorbeeld op WinRT, .NET Compact Framework,\#.NET Micro Framework en Mono) niet gebruiken in C. Natuurlijk is deze bibliotheek handig om te begrijpen hoe op claims gebaseerde beveiliging werkt op AMQP-niveau, omdat u zag hoe het werkt op HTTP-niveau (met een HTTP POST-aanvraag en het SAS-token dat binnen de koptekst Autorisatie is verzonden). Als u niet zo'n diepgaande kennis over AMQP nodig hebt, u de officiële Service Bus SDK gebruiken met .NET Framework-toepassingen, die het voor u zullen doen.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

De `PutCbsToken()` methode ontvangt de *verbinding* (AMQP-verbindingsklasseinstantie zoals die wordt geleverd door de [AMQP .NET Lite-bibliotheek)](https://github.com/Azure/amqpnetlite)die de TCP-verbinding met de service vertegenwoordigt en de *parameter sasToken* die het SAS-token is dat moet worden verzonden.

> [!NOTE]
> Het is belangrijk dat de verbinding wordt gemaakt met **SASL-verificatiemechanisme ingesteld op ANONIEM** (en niet de standaard plain met gebruikersnaam en wachtwoord die wordt gebruikt wanneer u het SAS-token niet hoeft te verzenden).
>
>

Vervolgens maakt de uitgever twee AMQP-koppelingen voor het verzenden van het SAS-token en het ontvangen van het antwoord (het tokenvalidatieresultaat) van de service.

Het AMQP-bericht bevat een reeks eigenschappen en meer informatie dan een eenvoudig bericht. Het SAS-token is de hoofdtekst van het bericht (met behulp van de constructor). De eigenschap **'ReplyTo'** is ingesteld op de naam van het knooppunt voor het ontvangen van het validatieresultaat op de koppeling met de ontvanger (u de naam wijzigen als u wilt en deze wordt dynamisch gemaakt door de service). De laatste drie toepassings-/aangepaste eigenschappen worden door de service gebruikt om aan te geven wat voor bewerking deze moet uitvoeren. Zoals beschreven in de ONTWERP-specificatie van het CBS, moeten dit de **bewerkingsnaam** ("put-token"), het **type token** (in dit geval a) `servicebus.windows.net:sastoken`en de **"naam" zijn van het publiek** waarop het token van toepassing is (de gehele entiteit).

Nadat het SAS-token op de afzenderkoppeling is verzonden, moet de uitgever het antwoord lezen op de koppeling met de ontvanger. Het antwoord is een eenvoudig AMQP-bericht met een eigenschap van de toepassing met de naam **"statuscode"** die dezelfde waarden kan bevatten als een HTTP-statuscode.

## <a name="rights-required-for-service-bus-operations"></a>Rechten vereist voor servicebusactiviteiten

In de volgende tabel worden de toegangsrechten weergegeven die nodig zijn voor verschillende bewerkingen op servicebusbronnen.

| Bewerking | Claim vereist | Claimscope |
| --- | --- | --- |
| **Namespace** | | |
| Autorisatieregel configureren op een naamruimte |Beheren |Een naamruimteadres |
| **Serviceregister** | | |
| Privébeleid opsommen |Beheren |Een naamruimteadres |
| Beginnen met luisteren op een naamruimte |Luisteren |Een naamruimteadres |
| Berichten verzenden naar een listener in een naamruimte |Verzenden |Een naamruimteadres |
| **Wachtrij** | | |
| Een wachtrij maken |Beheren |Een naamruimteadres |
| Een wachtrij verwijderen |Beheren |Een geldig wachtrijadres |
| Wachtrijen opsommen |Beheren |/$Resources/Wachtrijen |
| De beschrijving van de wachtrij weergeven |Beheren |Een geldig wachtrijadres |
| Autorisatieregel configureren voor een wachtrij |Beheren |Een geldig wachtrijadres |
| Naar de wachtrij verzenden |Verzenden |Een geldig wachtrijadres |
| Berichten ontvangen vanuit een wachtrij |Luisteren |Een geldig wachtrijadres |
| Berichten verlaten of voltooien nadat het bericht is ontvangen in de peek-lock-modus |Luisteren |Een geldig wachtrijadres |
| Een bericht uitstellen voor later ophalen |Luisteren |Een geldig wachtrijadres |
| Deadletter een bericht |Luisteren |Een geldig wachtrijadres |
| De status weergeven die is gekoppeld aan een berichtwachtrijsessie |Luisteren |Een geldig wachtrijadres |
| De status instellen die is gekoppeld aan een berichtwachtrijsessie |Luisteren |Een geldig wachtrijadres |
| Een bericht plannen voor latere levering; Bijvoorbeeld, [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Luisteren | Een geldig wachtrijadres
| **Onderwerp** | | |
| Een onderwerp maken |Beheren |Een naamruimteadres |
| Een onderwerp verwijderen |Beheren |Een geldig onderwerpadres |
| Onderwerpen opsommen |Beheren |/$Resources/Onderwerpen |
| De onderwerpbeschrijving downloaden |Beheren |Een geldig onderwerpadres |
| Autorisatieregel configureren voor een onderwerp |Beheren |Een geldig onderwerpadres |
| Verzenden naar het onderwerp |Verzenden |Een geldig onderwerpadres |
| **Abonnement** | | |
| Een abonnement maken |Beheren |Een naamruimteadres |
| Abonnement verwijderen |Beheren |.. /myTopic/abonnementen/mySubscription |
| Abonnementen opsommen |Beheren |.. /myTopic/abonnementen |
| Beschrijving van een abonnement downloaden |Beheren |.. /myTopic/abonnementen/mySubscription |
| Berichten verlaten of voltooien nadat het bericht is ontvangen in de peek-lock-modus |Luisteren |.. /myTopic/abonnementen/mySubscription |
| Een bericht uitstellen voor later ophalen |Luisteren |.. /myTopic/abonnementen/mySubscription |
| Deadletter een bericht |Luisteren |.. /myTopic/abonnementen/mySubscription |
| De status aan een onderwerpsessie weergeven |Luisteren |.. /myTopic/abonnementen/mySubscription |
| De status instellen die is gekoppeld aan een onderwerpsessie |Luisteren |.. /myTopic/abonnementen/mySubscription |
| **Regels** | | |
| Een regel maken |Beheren |.. /myTopic/abonnementen/mySubscription |
| Een regel verwijderen |Beheren |.. /myTopic/abonnementen/mySubscription |
| Regels opsommen |Beheren of luisteren |.. /myTopic/Abonnementen/mySubscription/Rules

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over de Service Bus-berichtenservice

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Wachtrijen voor servicebussen gebruiken](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
