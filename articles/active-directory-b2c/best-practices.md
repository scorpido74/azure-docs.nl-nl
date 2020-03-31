---
title: Aanbevolen procedures voor Azure AD B2C
titleSuffix: Azure AD B2C
description: Aanbevelingen en aanbevolen procedures waarmee u rekening moet houden bij het werken met Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136156"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Aanbevelingen en aanbevolen procedures voor Azure Active Directory B2C

De volgende aanbevolen procedures en aanbevelingen hebben betrekking op enkele van de belangrijkste aspecten van de integratie van Azure Active Directory (Azure AD) B2C in bestaande of nieuwe toepassingsomgevingen.

## <a name="fundamentals"></a>Basisprincipes

|  |  |
|--|--|
| Gebruikersstromen kiezen voor de meeste scenario's | Het Identity Experience Framework van Azure AD B2C is de kernkracht van de service. Beleid beschrijft volledig identiteitservaringen zoals aanmelden, aanmelden of profielbewerking. Om u te helpen bij het instellen van de meest voorkomende identiteitstaken, bevat de Azure AD B2C-portal vooraf gedefinieerde, configureerbare beleidsregels die gebruikersstromen worden genoemd. Met gebruikersstromen u in enkele minuten geweldige gebruikerservaringen creëren, met slechts een paar klikken. [Meer informatie over het gebruik van gebruikersstromen versus aangepast beleid](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| App-registraties | Elke toepassing (web, native) en API die wordt beveiligd, moet worden geregistreerd in Azure AD B2C. Als een app zowel een web- als een native versie van iOS en Android heeft, u deze registreren als één toepassing in Azure AD B2C met dezelfde client-id. Meer informatie over het [registreren van OIDC-, SAML-, web- en native apps.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications) Meer informatie over [toepassingstypen die kunnen worden gebruikt in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Overstappen op maandelijkse actieve gebruikers facturering | Azure AD B2C is overgestapt van maandelijksactieve verificaties naar maandelijksactieve gebruikers (MAU) facturering. De meeste klanten zullen dit model kosteneffectief vinden. [Meer informatie over maandelijkse actieve gebruikers facturering](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planning en ontwerp

Definieer uw toepassings- en servicearchitectuur, voorraadhuidige systemen en plan uw migratie naar Azure AD B2C.

|  |  |
|--|--|
| Architect een end-to-end oplossing | Neem alle afhankelijkheden van uw toepassingen op bij het plannen van een Azure AD B2C-integratie. Houd rekening met alle services en producten die zich momenteel in uw omgeving bevinden of die mogelijk aan de oplossing moeten worden toegevoegd, bijvoorbeeld Azure-functies, CRM-systemen (Customer Relationship Management), Azure API Management-gateway en opslagservices. Houd rekening met de beveiliging en schaalbaarheid voor alle services. |
| De ervaringen van uw gebruikers documenteren | Detaileer alle gebruikersreizen die uw klanten in uw toepassing kunnen ervaren. Neem elk scherm en eventuele vertakkingsstromen op die ze kunnen tegenkomen bij interactie met de identiteits- en profielaspecten van uw toepassing. Neem bruikbaarheid, toegankelijkheid en lokalisatie op in uw planning. |
| Kies het juiste verificatieprotocol |  Zie [Scenario's en ondersteunde verificatiestromen](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)voor een uitsplitsing van de verschillende toepassingsscenario's en de aanbevolen verificatiestromen. |
| Pilot een proof-of-concept (POC) end-to-end user experience | Begin met onze [Microsoft-codevoorbeelden](code-samples.md) en [communityvoorbeelden.](https://github.com/azure-ad-b2c/samples) |
| Een migratieplan maken |Vooruitplannen kan ervoor zorgen dat migratie soepeler verloopt. Meer informatie over [gebruikersmigratie](user-migration.md).|
| Bruikbaarheid versus beveiliging | Uw oplossing moet de juiste balans vinden tussen de bruikbaarheid van toepassingen en het aanvaardbare risiconiveau van uw organisatie. |
| On-premises afhankelijkheden naar de cloud verplaatsen | Om te zorgen voor een veerkrachtige oplossing, u overwegen bestaande toepassingsafhankelijkheden naar de cloud te verplaatsen. |
| Bestaande apps migreren naar b2clogin.com | De afschaffing van login.microsoftonline.com is op 4 december 2020 van kracht geworden voor alle Azure AD B2C-tenants. [Meer informatie](b2clogin.md). |

## <a name="implementation"></a>Implementatie

Overweeg tijdens de uitvoeringsfase de volgende aanbevelingen.

|  |  |
|--|--|
| Aangepaste beleidsregels bewerken met de Azure AD B2C-extensie voor Visual Studio Code | Download Visual Studio Code en deze community-built [extensie van de Visual Studio Code Marketplace.](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) Hoewel het geen officieel Microsoft-product is, bevat de Azure AD B2C-extensie voor Visual Studio Code verschillende functies die het werken met aangepaste beleidsregels gemakkelijker maken. |
| Meer informatie over het oplossen van problemen met Azure AD B2C | Meer informatie over het [oplossen van aangepaste beleidsregels](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) tijdens de ontwikkeling. Ontdek hoe een normale verificatiestroom eruit ziet en gebruik hulpprogramma's voor het ontdekken van afwijkingen en fouten. Gebruik bijvoorbeeld [Application Insights](troubleshoot-with-application-insights.md) om uitvoerlogboeken van gebruikersreizen te bekijken. |
| Maak gebruik van onze bibliotheek met bewezen aangepaste beleidspatronen | Zoek [voorbeelden](https://github.com/azure-ad-b2c/samples) voor verschillende verbeterde Azure AD B2C-gebruikersreizen (Customer Identity and Access Management) (CIAM). |


## <a name="testing"></a>Testen

Test en automatiseer uw Azure AD B2C-implementatie.

|  |  |
|--|--|
| Rekening houden met wereldwijd verkeer | Gebruik verkeersbronnen van verschillende globale adres om de prestatie- en lokalisatievereisten te testen. Zorg ervoor dat alle HTMLs, CSS en afhankelijkheden aan uw prestatiebehoeften kunnen voldoen. |
| Functionele en ui-tests | Test de gebruikersstromen end-to-end. Voeg synthetische tests om de paar minuten met behulp van Selenium, VS Web Test, enz. |
| Pentesten | Voordat u live gaat met uw oplossing, voert u penetratietestoefeningen uit om te controleren of alle onderdelen veilig zijn, inclusief afhankelijkheden van derden. Controleer of u uw API's hebt beveiligd met toegangstokens en het juiste verificatieprotocol hebt gebruikt voor uw toepassingsscenario. Meer informatie over [penetratietesten](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) en de [Microsoft Cloud Unified Penetration Testing Rules of Engagement.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| A/B-tests | Vlucht uw nieuwe functies met een kleine, willekeurige set gebruikers voordat u uitrolt naar uw hele bevolking. Als JavaScript is ingeschakeld in Azure AD B2C, u integreren met A/B-testtools zoals Optimizely, Clarity en anderen. |
| Belasting testen | Azure AD B2C kan schalen, maar uw toepassing kan alleen worden geschaald als alle afhankelijkheden kunnen worden geschaald. Laad uw API's en CDN's. |
| Beperking |  Azure AD B2C beperkt het verkeer als er in korte tijd te veel aanvragen vanuit dezelfde bron worden verzonden. Gebruik verschillende verkeersbronnen tijdens het `AADB2C90229` testen van de belasting en behandel de foutcode op een elegante manier in uw toepassingen. |
| Automation | Gebruik CI/CD-pijplijnen (continuous integration and delivery) om testen en implementaties te automatiseren, bijvoorbeeld [Azure DevOps.](deploy-custom-policies-devops.md) |

## <a name="operations"></a>Bewerkingen

Uw Azure AD B2C-omgeving beheren.

|  |  |
|--|--|
| Meerdere omgevingen maken | Maak afzonderlijke omgevingen voor ontwikkeling, testen, preproductie en productie voor eenvoudigere bewerkingen en implementatieimplementatie. Voor elk azure AD B2C-tenants maken. |
| Versiebeheer gebruiken voor uw aangepaste beleid | Overweeg github, Azure Repos of een ander cloudbeheersysteem te gebruiken voor uw aangepaste Azure AD B2C-beleid. |
| Gebruik de Microsoft Graph API om het beheer van uw B2C-tenants te automatiseren | Microsoft Graph API's:<br/>[Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) beheren (aangepast beleid)<br/>[Sleutels](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Gebruikersstromen](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integreren met Azure DevOps | Een [CI/CD-pijplijn](deploy-custom-policies-devops.md) maakt het verplaatsen van code tussen verschillende omgevingen eenvoudig en zorgt te allen tijde voor productiebereidheid.   |
| Integreren met Azure Monitor | [Auditloggebeurtenissen](view-audit-logs.md) worden slechts zeven dagen bewaard. [Integreer met Azure Monitor](azure-monitor.md) om de logboeken te behouden voor langdurig gebruik of integreer met SIEM-tools (Security Information and Event Management) van derden om inzicht te krijgen in uw omgeving. |
| Actieve waarschuwingen en controle instellen | [Gebruikersgedrag](active-directory-b2c-custom-guide-eventlogger-appins.md) in Azure AD B2C bijhouden met behulp van Toepassingsinzichten. |


## <a name="support-and-status-updates"></a>Updates van ondersteuning en status

Blijf op de hoogte van de stand van de dienst en vind ondersteuningsopties.

|  |  |
|--|--|
| [Service-updates](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Blijf op de hoogte van Azure AD B2C-productupdates en -aankondigingen. |
| [Microsoft-ondersteuning](support-options.md) | Schik een ondersteuningsaanvraag in voor technische problemen met Azure AD B2C. Ondersteuning voor facturering en abonnementsbeheer wordt gratis aangeboden. |
| [Azure Status](https://status.azure.com/status) | Bekijk de huidige status van alle Azure-services. |