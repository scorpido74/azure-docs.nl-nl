---
title: Bekende problemen-Azure Digital Apparaatdubbels
description: Hulp krijgen bij het herkennen en beperken van bekende problemen met Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: e152c0227008dd12088660b2390a8d0a5f54de96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290775"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekende problemen in azure Digital Apparaatdubbels

Dit artikel bevat informatie over bekende problemen met Azure Digital Apparaatdubbels.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-client fout: ongeldige aanvraag" in Cloud Shell

Opdrachten in Cloud Shell kunnen af en toe mislukken met de fout ' 400-client fout: ongeldige aanvraag voor URL: http://localhost:50342/oauth2/token ' gevolgd door de volledige Stack tracering.

Voor Azure Digital Apparaatdubbels is dit van invloed op de volgende opdracht groepen:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

U kunt dit oplossen door de opdracht opnieuw `az login` uit te voeren in Cloud shell en volgende aanmeldings stappen te volt ooien. Daarna kunt u de opdracht opnieuw uitvoeren.

Een alternatieve oplossing is om [de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) op uw computer te installeren, zodat u Azure cli-opdrachten lokaal kunt uitvoeren. Dit probleem treedt niet op bij de lokale CLI.

### <a name="possible-causes"></a>Mogelijke oorzaken

Dit is het resultaat van een bekend probleem in Cloud Shell: het [*verkrijgen van tokens van Cloud shell af en toe mislukt met 400-client fout: ongeldige aanvraag*](https://github.com/Azure/azure-cli/issues/11749).

Dit geeft een probleem met het Azure Digital Apparaatdubbels instance-verificatie tokens en de standaard verificatie op basis van [beheerde id's](../active-directory/managed-identities-azure-resources/overview.md) van de Cloud shell. De stappen voor probleem oplossing voor het uitvoeren van `az login` switches van beheerde identiteits verificatie, waardoor dit probleem wordt omgeleid.

Dit heeft geen invloed op de Azure Digital Apparaatdubbels-opdrachten van de `az dt` `az dt endpoint` -of-opdracht groepen, omdat ze gebruikmaken van een ander type verificatie token (op basis van arm), die geen problemen ondervindt met de beheerde identiteits verificatie van de Cloud shell.

## <a name="missing-role-assignment-after-scripted-setup"></a>Roltoewijzing ontbreekt na installatie van script

Sommige gebruikers kunnen problemen ondervinden met het toewijzings deel van de functie [*: een exemplaar en verificatie instellen (script)*](how-to-set-up-instance-scripted.md). Het script geeft geen fout aan, maar de rol *Azure Digital Apparaatdubbels Owner (preview)* is niet met succes aan de gebruiker toegewezen. dit probleem heeft gevolgen voor de mogelijkheid om nog andere resources te maken.

Als u wilt bepalen of uw roltoewijzing is ingesteld nadat het script is uitgevoerd, volgt u de instructies in de sectie toewijzing van gebruikersrol [*controleren*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) van het artikel Setup. Als uw gebruiker niet met deze rol wordt weer gegeven, is dit van invloed op het probleem.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

U kunt dit oplossen door de roltoewijzing hand matig in te stellen met behulp van de CLI of de Azure Portal. 

Volg deze instructies:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Mogelijke oorzaken

Voor gebruikers die zijn aangemeld met een persoonlijk [Microsoft-account (MSA)](https://account.microsoft.com/account), is de principal-id van uw gebruiker die u aanduidt in opdrachten zoals deze kan verschillen van de e-mail berichten van uw gebruiker, waardoor het script moeilijker kan worden gedetecteerd en gebruikt om de functie correct toe te wijzen.

## <a name="issue-with-interactive-browser-authentication"></a>Probleem met interactieve browser verificatie

Wanneer u verificatie code in uw Azure Digital Apparaatdubbels-toepassingen schrijft met versie **1.2.0** van de ** [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -bibliotheek**, kunnen er problemen optreden met de [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) -methode.

Dit is niet de meest recente versie van de bibliotheek. De meest recente versie is **1.2.2**.

De betrokken methode wordt gebruikt in de volgende artikelen: 
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)
* [*Instructies: de Azure Digital Apparaatdubbels-Api's en Sdk's gebruiken*](how-to-use-apis-sdks.md)

Het probleem bevat de fout melding ' Azure. Identity. AuthenticationFailedException ' bij het verifiëren van de verificatie in een browser venster. Het browser venster kan niet volledig worden gestart, of lijkt om de gebruiker te verifiëren, terwijl de client toepassing nog steeds mislukt met de fout.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Werk uw toepassingen bij om versie 1.2.2 te gebruiken om dit probleem op te lossen `Azure.Identity` . **1.2.2** Met deze versie van de bibliotheek moet de browser naar verwachting laden en verifiëren.

### <a name="possible-causes"></a>Mogelijke oorzaken

Dit is gerelateerd aan een openstaand probleem met de meest recente versie van de `Azure.Identity` bibliotheek (versie **1.2.0**): er [*kan niet worden geverifieerd wanneer InteractiveBrowserCredential wordt gebruikt*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

U ziet dit probleem als u versie **1.2.0** in uw Azure Digital apparaatdubbels-toepassing gebruikt, of als u de bibliotheek toevoegt aan uw project zonder een versie op te geven (zoals dat ook de nieuwste versie is).

## <a name="next-steps"></a>Volgende stappen

Lees meer over beveiliging en machtigingen op Azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)