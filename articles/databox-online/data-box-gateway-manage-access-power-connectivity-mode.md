---
title: De toegang tot het apparaat, de kracht en de connectiviteits modus Azure Data Box Gateway
description: Hierin wordt beschreven hoe u de toegangs-, Power-en connectiviteits modus beheert voor het Azure Data Box Gateway apparaat waarmee gegevens kunnen worden overgebracht naar Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 27b6d8ca61ed10b5c7362e089fe94d8d64164878
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743859"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>De toegang, de kracht en de connectiviteits modus voor uw Azure Data Box Gateway beheren

In dit artikel wordt beschreven hoe u de modus toegang, kracht en connectiviteit beheert voor uw Azure Data Box Gateway. Deze bewerkingen worden uitgevoerd via de lokale webgebruikersinterface of de Azure Portal. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Apparaattoegang beheren
> * Connectiviteits modus beheren
> * Energie beheren

## <a name="manage-device-access"></a>Apparaattoegang beheren

De toegang tot uw Data Box Gateway-apparaat wordt bepaald door het gebruik van een apparaatwachtwoord. U kunt het wacht woord wijzigen via de lokale webgebruikersinterface. U kunt het wacht woord van het apparaat ook opnieuw instellen in de Azure Portal.

### <a name="change-device-password"></a>Wachtwoord voor apparaat wijzigen

Volg deze stappen in de lokale gebruikers interface om het wacht woord van het apparaat te wijzigen.

1. Ga in de lokale web-UI naar **onderhoud > wachtwoord wijziging**.
2. Voer het huidige wacht woord en vervolgens het nieuwe wacht woord in. Het opgegeven wacht woord moet tussen 8 en 16 tekens lang zijn. Het wacht woord moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Bevestig het nieuwe wacht woord.

    ![Wachtwoord wijzigen](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klik op **wacht woord wijzigen**.
 
### <a name="reset-device-password"></a>Wacht woord van apparaat opnieuw instellen

Voor de werk stroom opnieuw instellen is niet vereist dat de gebruiker het oude wacht woord intrekt en is nuttig wanneer het wacht woord verloren is gegaan. Deze werk stroom wordt uitgevoerd in de Azure Portal.

1. Ga in het Azure Portal naar **overzicht > het beheerders wachtwoord opnieuw**in te stellen.

    ![Wachtwoord opnieuw instellen](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Voer het nieuwe wacht woord in en bevestig het. Het opgegeven wacht woord moet tussen 8 en 16 tekens lang zijn. Het wacht woord moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Klik op **Opnieuw instellen**.

    ![Wacht woord 2 opnieuw instellen](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Toegang tot de bedrijfsresources

Als u uw Azure Stack Edge Pro/Data Box Gateway-, IoT Hub-en Azure Storage-resource wilt maken, moet u machtigingen hebben als Inzender of hoger op het niveau van een resource groep. U moet ook de bijbehorende resource providers registreren. Voor bewerkingen waarbij de activerings sleutel en referenties betrokken zijn, zijn er ook machtigingen voor het Azure Active Directory Graph API vereist. Deze worden beschreven in de volgende secties.

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API-machtigingen beheren

Bij het genereren van de activerings sleutel voor het Azure Stack Edge Pro-apparaat of het uitvoeren van bewerkingen waarvoor referenties zijn vereist, hebt u machtigingen nodig voor de Microsoft Graph-API. De bewerkingen die referenties nodig hebben, kunnen zijn:

-  Een share maken met een gekoppeld opslag account.
-  Het maken van een gebruiker die toegang heeft tot de shares op het apparaat.

U moet toegang hebben `User` tot Active Directory Tenant als u dit moet kunnen doen `Read all directory objects` . U kunt geen gast gebruiker zijn omdat ze geen machtigingen hebben voor `Read all directory objects` . Als u een gast bent, worden de bewerkingen, zoals het genereren van een activerings sleutel, het maken van een share op uw Azure Stack Edge Pro-apparaat en het maken van een gebruiker mislukt.

Zie [Microsoft Graph permissions Reference](https://docs.microsoft.com/graph/permissions-reference)(Engelstalig) voor meer informatie over het verlenen van toegang aan gebruikers om de API te Microsoft Graph.

### <a name="register-resource-providers"></a>Resourceprovider registreren

Als u een resource in azure wilt inrichten (in het Azure Resource Manager model), hebt u een resource provider nodig die het maken van die resource ondersteunt. Als u bijvoorbeeld een virtuele machine wilt inrichten, moet u een resource provider micro soft. Compute in het abonnement hebben.
 
Resourceproviders worden geregistreerd op het niveau van het abonnement. Nieuwe Azure-abonnementen worden standaard vooraf geregistreerd bij een lijst met veelgebruikte resourceproviders. De resource provider voor micro soft. DataBoxEdge is niet opgenomen in deze lijst.

U hoeft geen toegangs machtigingen voor het abonnements niveau te verlenen zodat gebruikers resources zoals ' micro soft. DataBoxEdge ' kunnen maken binnen hun resource groepen waarvoor ze eigendoms rechten hebben, op voor waarde dat de resource providers voor deze resources al zijn geregistreerd.

Voordat u probeert een resource te maken, moet u ervoor zorgen dat de resource provider is geregistreerd in het abonnement. Als de resource provider niet is geregistreerd, moet u ervoor zorgen dat de gebruiker die de nieuwe resource maakt, voldoende rechten heeft om de vereiste resource provider te registreren op het abonnements niveau. Als u dit nog niet hebt gedaan, ziet u de volgende fout:

*Het abonnement \<Subscription name> heeft geen machtigingen voor het registreren van de resource provider (s): micro soft. DataBoxEdge.*


Voer de volgende opdracht uit om een lijst met geregistreerde resource providers in het huidige abonnement op te halen:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Voor Azure Stack Edge Pro-apparaat `Microsoft.DataBoxEdge` moet worden geregistreerd. Als u de registratie wilt registreren `Microsoft.DataBoxEdge` , moet u de volgende opdracht uitvoeren:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Zie [fouten voor de registratie van de resource provider oplossen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)voor meer informatie over het registreren van een resource provider.

## <a name="manage-connectivity-mode"></a>Connectiviteits modus beheren

Naast de standaard normale modus kan uw apparaat ook worden uitgevoerd in een gedeeltelijk ontkoppelde of niet-verbonden modus. Elk van deze modi wordt hieronder beschreven:

- **Gedeeltelijk verbroken** : in deze modus kan het apparaat geen gegevens uploaden naar de shares, maar kan ook worden beheerd via de Azure Portal.

    Deze modus wordt doorgaans gebruikt wanneer op een satelliet netwerk met een Data limiet het doel is om het gebruik van de netwerk bandbreedte te minimaliseren. Mini maal netwerk verbruik kan nog steeds optreden voor bewerkingen voor het controleren van apparaten.

- **Verbinding verbroken** : in deze modus is het apparaat volledig losgekoppeld van de Cloud en worden de Cloud-uploads en down loads uitgeschakeld. Het apparaat kan alleen worden beheerd via de lokale webgebruikersinterface.

    Deze modus wordt doorgaans gebruikt wanneer u uw apparaat offline wilt zetten.

Voer de volgende stappen uit om de modus apparaat te wijzigen:

1. Ga in de lokale web-UI van uw apparaat naar **configuratie > Cloud instellingen**.
2. Schakel de **Cloud upload en down load**uit.
3. Als u het apparaat wilt uitvoeren in een gedeeltelijk niet-verbonden modus, schakelt u **Azure Portal-beheer**in.

    ![Connectiviteits modus](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Als u het apparaat in de modus zonder verbinding wilt uitvoeren, schakelt u **Azure Portal beheer**uit. Het apparaat kan nu alleen worden beheerd via de lokale webgebruikersinterface.

    ![Connectiviteits modus 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Energie beheren

U kunt het virtuele apparaat afsluiten of opnieuw opstarten met behulp van de lokale webgebruikersinterface. We raden u aan de shares vóór de herstart offline te zetten op de host en vervolgens op het apparaat. Deze actie minimaliseert de kans op beschadigde gegevens.

1. Ga in de lokale web-UI naar **onderhoud > energie-instellingen**.
2. Klik op **Afsluiten** of **opnieuw opstarten** , afhankelijk van wat u wilt doen.

    ![Energie-instellingen](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om door te gaan.

> [!NOTE]
> Als u het virtuele apparaat uitschakelt, moet u het apparaat starten via het beheer van Hyper Visor.
