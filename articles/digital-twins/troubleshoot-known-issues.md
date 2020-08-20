---
title: Bekende problemen-Azure Digital Apparaatdubbels
description: Hulp krijgen bij het herkennen en beperken van bekende problemen met Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 0c008061d2d4fafa96eda934d5026c92839a0bdb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661483"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekende problemen in azure Digital Apparaatdubbels

Dit artikel bevat informatie over bekende problemen met Azure Digital Apparaatdubbels.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-client fout: ongeldige aanvraag" in Cloud Shell

Opdrachten in Cloud Shell kunnen af en toe mislukken met de fout ' 400-client fout: ongeldige aanvraag voor URL: http://localhost:50342/oauth2/token ' gevolgd door de volledige Stack tracering.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

U kunt dit oplossen door de opdracht opnieuw uit te voeren `az login` en de volgende aanmeldings stappen te volt ooien.

Daarna kunt u de opdracht opnieuw uitvoeren...

### <a name="possible-causes"></a>Mogelijke oorzaken

Dit is het resultaat van een bekend probleem in Cloud Shell: het [*verkrijgen van tokens van Cloud shell af en toe mislukt met 400-client fout: ongeldige aanvraag*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>Roltoewijzing ontbreekt na installatie van script

Sommige gebruikers kunnen problemen ondervinden met het toewijzings deel van de functie [*: een exemplaar en verificatie instellen (script)*](how-to-set-up-instance-scripted.md). Het script geeft geen fout aan, maar de rol *Azure Digital Apparaatdubbels Owner (preview)* is niet aan de gebruiker toegewezen, en dit heeft gevolgen voor de mogelijkheid om andere resources op weg te maken.

Als u wilt bepalen of uw roltoewijzing is ingesteld nadat het script is uitgevoerd, volgt u de instructies in de sectie toewijzing van gebruikersrol [*controleren*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) van het artikel Setup. Als uw gebruiker niet met deze rol wordt weer gegeven, is dit van invloed op het probleem.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

U kunt dit oplossen door de roltoewijzing hand matig in te stellen met behulp van de CLI of de Azure Portal. 

Volg deze instructies:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Mogelijke oorzaken

Voor gebruikers die zijn aangemeld met een persoonlijk [Microsoft-account (MSA)](https://account.microsoft.com/account), is de principal-id van uw gebruiker die u aanduidt in opdrachten zoals deze kan verschillen van de aanmeldings-e-mail van uw gebruiker, waardoor het script moeilijker kan worden gedetecteerd en gebruikt om de functie correct toe te wijzen.

## <a name="issue-with-interactive-browser-authentication"></a>Probleem met interactieve browser verificatie

Wanneer u verificatie code in uw Azure Digital Apparaatdubbels-toepassingen schrijft met de nieuwste versie (versie **1.2.0**) van de ** [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -bibliotheek**, kunnen er problemen optreden met de methode [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

De betrokken methode wordt gebruikt in de volgende artikelen: 
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)
* [*Instructies: de Azure Digital Apparaatdubbels-Api's en Sdk's gebruiken*](how-to-use-apis-sdks.md)

Het probleem bevat de fout melding ' Azure. Identity. AuthenticationFailedException ' bij het verifiëren van de verificatie in een browser venster. Het browser venster kan niet volledig worden gestart, of lijkt om de gebruiker te verifiëren, terwijl de client toepassing nog steeds mislukt met de fout.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Als u dit wilt oplossen, moet u uw toepassingen expliciet gebruiken voor Azure. Identity versie **1.1.1**. Met deze versie van de bibliotheek moet de browser naar verwachting laden en verifiëren.

>[!NOTE]
> Het is niet voldoende om de bibliotheek toe te voegen zonder een versie op te geven, omdat dat nog steeds de meest recente **1.2.0**is. U moet versie **1.1.1** expliciet opgeven.

### <a name="possible-causes"></a>Mogelijke oorzaken

Dit is gerelateerd aan een openstaand probleem met de nieuwste versie van de Azure. Identity Library (versie **1.2.0**): er [*kan niet worden geverifieerd wanneer InteractiveBrowserCredential wordt gebruikt*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

U ziet dit probleem als u versie **1.2.0** in uw Azure Digital apparaatdubbels-toepassing gebruikt, of als u de bibliotheek toevoegt aan uw project zonder een versie op te geven (zoals dat ook de nieuwste versie is).

## <a name="next-steps"></a>Volgende stappen

Lees meer over beveiliging en machtigingen op Azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)