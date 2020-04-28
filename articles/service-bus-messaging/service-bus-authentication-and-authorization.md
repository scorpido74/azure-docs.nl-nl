---
title: Verificatie en autorisatie Azure Service Bus | Microsoft Docs
description: Verifieer apps om te Service Bus met de verificatie van Shared Access Signature (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70013248"
---
# <a name="service-bus-authentication-and-authorization"></a>Vereenvoudigde Service Bus-verificatie en -autorisatie

Toepassingen krijgen toegang tot Azure Service Bus bronnen met behulp van Shared Access Signature-token verificatie (SAS). Met SAS bieden toepassingen een token aan Service Bus dat is ondertekend met een symmetrische sleutel die bekend is bij de token Uitgever en Service Bus (dus gedeeld) en die sleutel rechtstreeks is gekoppeld aan een regel waarmee specifieke toegangs rechten worden verleend, zoals de machtiging voor het ontvangen/belui Steren of verzenden van berichten. SAS-regels worden geconfigureerd in de naam ruimte of rechtstreeks op entiteiten zoals een wachtrij of onderwerp, waardoor het toegangs beheer nauw keurig wordt.

SAS-tokens kunnen rechtstreeks worden gegenereerd door een Service Bus-client, of ze kunnen worden gegenereerd door een eind punt voor het uitgeven van tussenliggende tokens waarmee de client communiceert. Een systeem kan er bijvoorbeeld voor zorgen dat de client een Active Directory verificatie-eind punt voor autorisatie beveiliging aanroept om de identiteits-en systeemtoegangs rechten te bewijzen, en de webservice retourneert vervolgens het juiste Service Bus-token. Deze SAS-token kan gemakkelijk worden gegenereerd met de Service Bus-token provider die is opgenomen in de Azure SDK. 

> [!IMPORTANT]
> Als u Azure Active Directory Access Control (ook wel Access Control Service of ACS) gebruikt met Service Bus, ziet u dat de ondersteuning voor deze methode nu beperkt is en dat u uw toepassing moet migreren om SAS te gebruiken. Zie [dit blog bericht](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) en [dit artikel](service-bus-migrate-acs-sas.md)voor meer informatie.

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory-integratie (Azure AD) voor Service Bus bronnen biedt op rollen gebaseerd toegangs beheer (RBAC) voor nauw keurige controle over de toegang van een client tot resources. U kunt op rollen gebaseerd toegangs beheer (RBAC) gebruiken om machtigingen te verlenen aan de beveiligingsprincipal, die een gebruiker, een groep of een Application Service-Principal kan zijn. De beveiligingsprincipal wordt door Azure AD geverifieerd om een OAuth 2,0-token te retour neren. Het token kan worden gebruikt voor het autoriseren van een aanvraag voor toegang tot een Service Bus resource (wachtrij, onderwerp, enz.).

Raadpleeg de volgende artikelen voor meer informatie over verificatie met Azure AD:

- [Verifiëren met beheerde identiteiten](service-bus-managed-service-identity.md)
- [Verifiëren vanuit een toepassing](authenticate-application.md)

> [!IMPORTANT]
> Het autoriseren van gebruikers of toepassingen die gebruikmaken van het OAuth 2,0-token dat wordt geretourneerd door Azure AD, biedt een superieure beveiliging en gebruiks vriendelijk gebruik van Shared Access signatures (SAS). Met Azure AD hoeft u geen tokens op te slaan in uw code en mogelijke beveiligings problemen met Risico's. U wordt aangeraden gebruik te maken van Azure AD als dat mogelijk is met uw Azure Service Bus toepassingen. 


## <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang
Met [SAS-verificatie](service-bus-sas.md) kunt u een gebruiker toegang verlenen tot Service Bus resources, met specifieke rechten. SAS-verificatie in Service Bus omvat de configuratie van een cryptografische sleutel met bijbehorende rechten op een Service Bus bron. Clients kunnen vervolgens toegang krijgen tot deze bron door een SAS-token te presen teren, dat bestaat uit de bron-URI waartoe toegang wordt verkregen en een verloop datum dat met de geconfigureerde sleutel is ondertekend.

U kunt sleutels voor SAS voor een Service Bus naam ruimte configureren. De sleutel is van toepassing op alle Messa ging-entiteiten binnen die naam ruimte. U kunt ook sleutels configureren voor Service Bus-wacht rijen en-onderwerpen. SAS wordt ook ondersteund op [Azure relay](../service-bus-relay/relay-authentication-and-authorization.md).

Als u SAS wilt gebruiken, kunt u een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -object configureren in een naam ruimte, wachtrij of onderwerp. Deze regel bestaat uit de volgende elementen:

* *Naam*van de regel: geeft aan.
* *PrimaryKey*: een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Secundaire sleutel*: een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Rechten*: vertegenwoordigt de **verzameling van**verleende, **verzonden**of **beheerde** rechten.

Autorisatie regels die op het niveau van de naam ruimte zijn geconfigureerd, kunnen toegang verlenen tot alle entiteiten in een naam ruimte voor clients met tokens die zijn ondertekend met behulp van de bijbehorende sleutel. U kunt Maxi maal 12 dergelijke autorisatie regels configureren voor een Service Bus naam ruimte, wachtrij of onderwerp. Standaard wordt een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) met alle rechten voor elke naam ruimte geconfigureerd wanneer deze voor het eerst wordt ingericht.

Voor toegang tot een entiteit vereist de client een SAS-token dat is gegenereerd met behulp van een specifieke [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Het SAS-token wordt gegenereerd met behulp van de HMAC-SHA256 van een resource teken reeks die bestaat uit de resource-URI waarvoor toegang wordt gevraagd en een verval datum met een cryptografische sleutel die is gekoppeld aan de autorisatie regel.

Ondersteuning voor SAS-verificatie voor Service Bus is opgenomen in de Azure .NET SDK-versie 2,0 en hoger. SAS bevat ondersteuning voor een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle Api's die een connection string als para meter accepteren, bieden ondersteuning voor SAS-verbindings reeksen.

## <a name="next-steps"></a>Volgende stappen

- Ga door met het lezen van [Service Bus verificatie met hand tekeningen voor gedeelde toegang](service-bus-sas.md) voor meer informatie over SAS.
- [Migratie van Azure Active Directory-Access Control (ACS) naar Shared Access Signature-autorisatie](service-bus-migrate-acs-sas.md).
- [Wijzigingen in naam ruimten die zijn ingeschakeld voor ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Zie [Azure relay-verificatie en-autorisatie](../service-bus-relay/relay-authentication-and-authorization.md)voor informatie over Azure relay verificatie en autorisatie. 

