---
title: Azure Service Bus-verificatie en -autorisatie | Microsoft Documenten
description: Verifieer apps naar Service Bus met SAS-verificatie (Shared Access Signature).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013248"
---
# <a name="service-bus-authentication-and-authorization"></a>Vereenvoudigde Service Bus-verificatie en -autorisatie

Toepassingen krijgen toegang tot Azure Service Bus-bronnen met SAS-tokenverificatie (Shared Access Signature). Met SAS presenteren toepassingen een token aan Service Bus dat is ondertekend met een symmetrische sleutel die zowel bekend is bij de tokenuitgever als de Service Bus (vandaar "gedeeld") en die sleutel is rechtstreeks gekoppeld aan een regel die specifieke toegangsrechten verleent, zoals de toestemming om berichten te ontvangen/te luisteren of te verzenden. SAS-regels zijn geconfigureerd op de naamruimte of rechtstreeks op entiteiten zoals een wachtrij of onderwerp, waardoor toegangsbeheer met fijne korrels mogelijk is.

SAS-tokens kunnen rechtstreeks door een Service Bus-client worden gegenereerd, of ze kunnen worden gegenereerd door een tussenliggend eindpunt voor tokenuitgifte waarmee de client samenwerkt. Een systeem kan bijvoorbeeld vereisen dat de client een active directory-autorisatiebeveiligd webserviceeindpunt aanroept om zijn identiteits- en systeemtoegangsrechten te bewijzen, en de webservice retourneert vervolgens het juiste ServiceBus-token. Dit SAS-token kan eenvoudig worden gegenereerd met behulp van de Service Bus-tokenprovider die is opgenomen in de Azure SDK. 

> [!IMPORTANT]
> Als u Azure Active Directory Access Control (ook wel Access Control Service of ACS genoemd) met Service Bus gebruikt, moet u er rekening mee houden dat de ondersteuning voor deze methode nu beperkt is en dat u uw toepassing moet migreren om SAS te gebruiken. Voor meer informatie, zie [deze blogpost](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) en [dit artikel](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD)-integratie voor Service Bus-bronnen biedt rbac (role-based access control) voor fijnmazige controle over de toegang van een client tot resources. U rbac (role-based access control) gebruiken om machtigingen te verlenen aan beveiligingsprincipal, die mogelijk een gebruiker, een groep of een hoofdvoorde van de toepassingsservice is. De beveiligingsprincipal is geverifieerd door Azure AD om een OAuth 2.0-token terug te sturen. Het token kan worden gebruikt om een aanvraag om toegang te krijgen tot een Service Bus-bron (wachtrij, onderwerp, enz.) te autoriseren.

Zie de volgende artikelen voor meer informatie over het verifiëren met Azure AD:

- [Verifiëren met beheerde identiteiten](service-bus-managed-service-identity.md)
- [Verifiëren vanuit een toepassing](authenticate-application.md)

> [!IMPORTANT]
> Het toestaan van gebruikers of toepassingen met OAuth 2.0-token die door Azure AD is geretourneerd, biedt superieure beveiliging en gebruiksgemak ten opzichte van sas (Shared Access Signatures). Met Azure AD is het niet nodig om de tokens in uw code op te slaan en potentiële beveiligingsproblemen te riskeren. We raden u aan Azure AD waar mogelijk te gebruiken met uw Azure Service Bus-toepassingen. 


## <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang
[Met SAS-verificatie](service-bus-sas.md) u een gebruiker toegang verlenen tot Service Bus-bronnen, met specifieke rechten. SAS-verificatie in Service Bus omvat de configuratie van een cryptografische sleutel met bijbehorende rechten op een Service Bus-bron. Clients kunnen vervolgens toegang krijgen tot die bron door een SAS-token te presenteren, dat bestaat uit de toegang tot de bron URI en een vervaldatum die is ondertekend met de geconfigureerde sleutel.

U sleutels voor SAS configureren op een naamruimte van een servicebus. De sleutel is van toepassing op alle berichtenentiteiten binnen die naamruimte. U ook sleutels configureren in wachtrijen en onderwerpen van ServiceBus. SAS wordt ook ondersteund op [Azure Relay.](../service-bus-relay/relay-authentication-and-authorization.md)

Als u SAS wilt gebruiken, u een [object SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configureren in een naamruimte, wachtrij of onderwerp. Deze regel bestaat uit de volgende elementen:

* *KeyName:* identificeert de regel.
* *PrimaryKey:* een cryptografische sleutel die wordt gebruikt om SAS-tokens te ondertekenen/valideren.
* *SecondaryKey:* een cryptografische sleutel die wordt gebruikt om SAS-tokens te ondertekenen/valideren.
* *Rechten*: vertegenwoordigt de verzameling **van verleende rechten luisteren,** **verzenden**of **beheren.**

Autorisatieregels die zijn geconfigureerd op naamruimteniveau, kunnen toegang verlenen tot alle entiteiten in een naamruimte voor clients met tokens die zijn ondertekend met de bijbehorende sleutel. U maximaal 12 van dergelijke autorisatieregels configureren voor een naamruimte, wachtrij of onderwerp van servicebus. Standaard is een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) met alle rechten geconfigureerd voor elke naamruimte wanneer deze voor het eerst wordt ingericht.

Om toegang te krijgen tot een entiteit, heeft de client een SAS-token nodig dat is gegenereerd met een specifieke [SharedAccessAuthorizationRule.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Het SAS-token wordt gegenereerd met behulp van de HMAC-SHA256 van een resourcetekenreeks die bestaat uit de bron URI waarvoor toegang wordt geclaimd, en een vervaldatum met een cryptografische sleutel die is gekoppeld aan de autorisatieregel.

SAS-verificatieondersteuning voor Service Bus is opgenomen in de Azure .NET SDK-versies 2.0 en hoger. SAS bevat ondersteuning voor een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle API's die een verbindingstekenreeks als parameter accepteren, bevatten ondersteuning voor SAS-verbindingstekenreeksen.

## <a name="next-steps"></a>Volgende stappen

- Lees verder [Service Bus-verificatie met Gedeelde toegangshandtekeningen](service-bus-sas.md) voor meer informatie over SAS.
- Migreren [van Azure Active Directory Access Control (ACS) naar de machtiging voor gedeelde toegangshandtekening](service-bus-migrate-acs-sas.md).
- [Wijzigingen in naamruimten met ACS-ingeschakeld](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Zie Verificatie en autorisatie van Azure Relay voor overeenkomstige informatie over azure [relay-verificatie en -autorisatie](../service-bus-relay/relay-authentication-and-authorization.md). 

