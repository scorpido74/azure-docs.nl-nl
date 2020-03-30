---
title: Problemen met groepslicentietoewijzing oplossen - Azure Active Directory | Microsoft Documenten
description: Problemen met licentietoewijzing identificeren en oplossen wanneer u azure Active Directory-groepslicenties gebruikt
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025699"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Problemen met licentietoewijzing voor een groep in Azure Active Directory identificeren en oplossen

Groepslicenties in Azure Ad (Azure AD) met groepslicenties. In dit artikel leggen we uit waarom gebruikers in deze status terecht kunnen komen.

Wanneer u licenties rechtstreeks aan individuele gebruikers toewijst, zonder groepslicenties te gebruiken, kan de toewijzingsbewerking mislukken. Wanneer u bijvoorbeeld de PowerShell-cmdlet `Set-MsolUserLicense` uitvoert op een gebruikerssysteem, kan de cmdlet mislukken om vele redenen die verband houden met bedrijfslogica. Er kunnen bijvoorbeeld onvoldoende licenties zijn of een conflict tussen twee serviceplannen die niet tegelijkertijd kunnen worden toegewezen. Het probleem wordt onmiddellijk aan u gemeld.

Wanneer u groepslicenties gebruikt, kunnen dezelfde fouten optreden, maar deze vinden op de achtergrond plaats terwijl de Azure AD-service licenties toevertrouwt. Om deze reden kunnen de fouten niet onmiddellijk aan u worden meegedeeld. In plaats daarvan worden ze opgenomen op het object van de gebruiker en vervolgens gerapporteerd via het beheerportaal. De oorspronkelijke intentie om de gebruiker een licentie te geven gaat nooit verloren, maar deze is opgenomen in een foutstatus voor toekomstig onderzoek en oplossing.

## <a name="find-license-assignment-errors"></a>Fouten in licentietoewijzing zoeken

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Gebruikers in een foutstatus in een groep zoeken

1. Open de groep voor de overzichtspagina en selecteer **Licenties**. Er verschijnt een melding als er gebruikers in een foutstatus zijn.

   ![Groeps- en foutmeldingenbericht](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Selecteer de melding om een lijst met alle betrokken gebruikers te openen. U elke gebruiker afzonderlijk selecteren om meer details te zien.

   ![lijst met gebruikers in groepslicentiefoutstatus](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Als u alle groepen wilt zoeken die ten minste één fout bevatten, selecteert u in het **Azure Active Directory-blad** **Licenties**en selecteert u **Overzicht**. Er wordt een informatievak weergegeven wanneer groepen uw aandacht nodig hebben.

   ![Overzicht en informatie over groepen in foutstatus](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Selecteer het vak om een lijst met alle groepen met fouten te bekijken. U elke groep selecteren voor meer details.

   ![Overzicht en lijst van groepen met fouten](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

De volgende secties geven een beschrijving van elk potentieel probleem en de manier om het op te lossen.

## <a name="not-enough-licenses"></a>Niet genoeg licenties

**Probleem:** Er zijn niet genoeg beschikbare licenties voor een van de producten die in de groep is opgegeven. U moet meer licenties voor het product aanschaffen of ongebruikte licenties vrijmaken van andere gebruikers of groepen.

Als u wilt zien hoeveel licenties beschikbaar zijn, gaat u naar Alle producten van Azure Active > **Directory-licenties** > **All products**. **Azure Active Directory**

Als u wilt zien welke gebruikers en groepen licenties verbruiken, selecteert u een product. Onder **Gelicentieerde gebruikers**ziet u een lijst met alle gebruikers die licenties rechtstreeks of via een of meer groepen hebben toegewezen. Onder **Gelicentieerde groepen**ziet u alle groepen die die producten hebben toegewezen.

**PowerShell:** PowerShell-cmdlets melden deze fout als _CountViolation_.

## <a name="conflicting-service-plans"></a>Conflicterende serviceplannen

**Probleem:** Een van de producten die in de groep is opgegeven, bevat een serviceplan dat in strijd is met een ander serviceplan dat al via een ander product aan de gebruiker is toegewezen. Sommige serviceplannen zijn zo geconfigureerd dat ze niet aan dezelfde gebruiker kunnen worden toegewezen als een ander gerelateerd serviceplan.

Overweeg het volgende scenariovoorbeeld. Een gebruiker heeft een licentie voor Office 365 Enterprise *E1* rechtstreeks toegewezen, waarbij alle abonnementen zijn ingeschakeld. De gebruiker is toegevoegd aan een groep waaraan het Office 365 Enterprise *E3-product* is toegewezen. Het E3-product bevat serviceplannen die niet kunnen overlappen met de plannen die zijn opgenomen in E1, dus de groepslicentietoewijzing mislukt met de fout 'Conflicterende serviceplannen'. In dit voorbeeld zijn de conflicterende serviceplannen:

- SharePoint Online (Abonnement 2) is in conflict met SharePoint Online (Abonnement 1).
- Exchange Online (Abonnement 2) is in conflict met Exchange Online (Abonnement 1).

Om dit conflict op te lossen, moet u twee van de plannen uitschakelen. U de E1-licentie uitschakelen die rechtstreeks aan de gebruiker is toegewezen. U moet ook de volledige groepslicentietoewijzing wijzigen en de plannen in de E3-licentie uitschakelen. U ook besluiten om de E1-licentie van de gebruiker te verwijderen als deze overbodig is in het kader van de E3-licentie.

De beslissing over het oplossen van conflicterende productlicenties is altijd van de beheerder. Azure AD lost licentieconflicten niet automatisch op.

**PowerShell:** PowerShell-cmdlets melden deze fout als _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andere producten zijn afhankelijk van deze licentie

**Probleem:** Een van de producten die in de groep is opgegeven, bevat een serviceplan dat moet zijn ingeschakeld om een ander serviceplan in een ander product te laten functioneren. Deze fout treedt op wanneer Azure AD het onderliggende serviceplan probeert te verwijderen. Dit kan bijvoorbeeld gebeuren wanneer u de gebruiker uit de groep verwijdert.

Om dit probleem op te lossen, moet u ervoor zorgen dat het vereiste plan nog steeds via een andere methode aan gebruikers wordt toegewezen of dat de afhankelijke services zijn uitgeschakeld voor die gebruikers. Daarna u de groepslicentie van die gebruikers op de juiste manier verwijderen.

**PowerShell:** PowerShell-cmdlets melden deze fout als _AfhankelijkheidSschending_.

## <a name="usage-location-isnt-allowed"></a>Gebruikslocatie is niet toegestaan

**Probleem:** Sommige Microsoft-services zijn niet op alle locaties beschikbaar vanwege lokale wet- en regelgeving. Voordat u een licentie aan een gebruiker toewijzen, moet u de eigenschap **Gebruikslocatie** voor de gebruiker opgeven. U de locatie opgeven onder de sectie > **Gebruikersprofielinstellingen** > **Settings** in de Azure-portal. **User**

Wanneer Azure AD probeert een groepslicentie toe te wijzen aan een gebruiker wiens gebruikslocatie niet wordt ondersteund, mislukt dit en wordt een fout op de gebruiker opgeslagen.

Als u dit probleem wilt oplossen, verwijdert u gebruikers van niet-ondersteunde locaties uit de gelicentieerde groep. Als de huidige locatiewaarden voor gebruik niet de werkelijke gebruikerslocatie vertegenwoordigen, u deze wijzigen zodat de licenties de volgende keer correct worden toegewezen (als de nieuwe locatie wordt ondersteund).

**PowerShell:** PowerShell-cmdlets melden deze fout als _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Wanneer Azure AD groepslicenties toewijst, erven gebruikers zonder een opgegeven gebruikslocatie de locatie van de map. We raden beheerders aan de juiste gebruikslocatiewaarden voor gebruikers in te stellen voordat ze groepslicenties gebruiken om te voldoen aan lokale wet- en regelgeving.

## <a name="duplicate-proxy-addresses"></a>Proxyadressen dupliceren

Als u Exchange Online gebruikt, zijn sommige gebruikers in uw tenant mogelijk onjuist geconfigureerd met dezelfde proxyadreswaarde. Wanneer groepslicenties een licentie aan een dergelijke gebruiker proberen toe te wijzen, mislukt deze en wordt weergegeven "Proxyadres wordt al gebruikt".

> [!TIP]
> Voer de volgende PowerShell-cmdlet uit tegen Exchange Online om te zien of er een duplicaat proxyadres is:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Zie ['Proxyadres wordt al gebruikt' in Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online)voor meer informatie over dit probleem. Het artikel bevat ook informatie over [hoe u verbinding maken met Exchange Online met behulp van externe PowerShell.](https://technet.microsoft.com/library/jj984289.aspx)

Nadat u proxy-adresproblemen voor de getroffen gebruikers hebt opgelost, moet u de verwerking van licenties op de groep forceren om ervoor te zorgen dat de licenties nu kunnen worden toegepast.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Wijziging van kenmerk Azure AD Mail en ProxyAddresses

**Probleem:** Tijdens het bijwerken van licentietoewijzing voor een gebruiker of een groep, ziet u mogelijk dat het kenmerk Azure AD Mail en ProxyAddresses van sommige gebruikers wordt gewijzigd.

Als u de licentietoewijzing op een gebruiker bijwerkt, wordt de berekening van het proxyadres geactiveerd, waardoor gebruikerskenmerken kunnen worden gewijzigd. Zie dit artikel over [hoe het kenmerk proxyAdressen wordt ingevuld in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)om de exacte reden van de wijziging te begrijpen en het probleem op te lossen.

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException in auditlogs

**Probleem:** Gebruiker heeft LicenseAssignmentAttributeConcurrencyException voor licentietoewijzing in auditlogs.
Wanneer groepslicenties gelijktijdige licentietoewijzing van dezelfde licentie aan een gebruiker proberen te verwerken, wordt deze uitzondering op de gebruiker geregistreerd. Dit gebeurt meestal wanneer een gebruiker lid is van meer dan één groep met dezelfde toegewezen licentie. AZure AD zal opnieuw proberen de gebruikerslicentie te verwerken en zal het probleem oplossen. Er is geen actie vereist van de klant om dit probleem op te lossen.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Meer dan één productlicentie die aan een groep is toegewezen

U meer dan één productlicentie aan een groep toewijzen. U bijvoorbeeld Office 365 Enterprise E3 en Enterprise Mobility + Security aan een groep toewijzen om eenvoudig alle meegeleverde services voor gebruikers in te schakelen.

Azure AD probeert alle licenties die in de groep zijn opgegeven, toe te wijzen aan elke gebruiker. Als Azure AD een van de producten niet kan toewijzen vanwege problemen met bedrijfslogica, worden de andere licenties in de groep ook niet toegewezen. Een voorbeeld is als er niet genoeg licenties zijn voor iedereen of als er conflicten zijn met andere services die zijn ingeschakeld voor de gebruiker.

U de gebruikers zien die niet zijn toegewezen en controleren welke producten door dit probleem worden beïnvloed.

## <a name="when-a-licensed-group-is-deleted"></a>Wanneer een gelicentieerde groep wordt verwijderd

U moet alle licenties verwijderen die aan een groep zijn toegewezen voordat u de groep verwijderen. Het verwijderen van licenties van alle gebruikers in de groep kan echter enige tijd in beslag nemen. Tijdens het verwijderen van licentietoewijzingen uit een groep kunnen er fouten optreden als de gebruiker een afhankelijke licentie heeft toegewezen of als er een conflictprobleem met proxyadres is dat het verwijderen van de licentie verbiedt. Als een gebruiker een licentie heeft die afhankelijk is van een licentie die wordt verwijderd vanwege groepsverwijdering, wordt de licentietoewijzing aan de gebruiker geconverteerd van overgenomen naar direct.

Overweeg bijvoorbeeld een groep die Office 365 E3/E5 heeft toegewezen met een Skype voor Bedrijven-serviceabonnement ingeschakeld. Stel je ook voor dat een paar leden van de groep licenties voor audiovergaderingen rechtstreeks hebben toegewezen. Wanneer de groep wordt verwijderd, proberen licenties op groepsbasis Office 365 E3/E5 van alle gebruikers te verwijderen. Omdat audiovergaderingen afhankelijk zijn van Skype voor Bedrijven, worden voor alle gebruikers met toegewezen audiovergaderingen groepslicenties de Office 365 E3/E5-licenties geconverteerd naar directe licentietoewijzing.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Licenties beheren voor producten met vereisten

Sommige Microsoft Online-producten waarvan u mogelijk eigenaar bent, zijn *add-ons.* Add-ons vereisen een vereiste serviceplan dat moet worden ingeschakeld voor een gebruiker of een groep voordat ze een licentie kunnen krijgen toegewezen. Met groepslicenties vereist het systeem dat zowel de vereiste als de add-on serviceplannen in dezelfde groep aanwezig zijn. Dit wordt gedaan om ervoor te zorgen dat alle gebruikers die worden toegevoegd aan de groep het volledig werkende product kunnen ontvangen. Kijk eens naar het volgende voorbeeld:

Microsoft Workplace Analytics is een add-on product. Het bevat één serviceplan met dezelfde naam. We kunnen dit serviceplan alleen toewijzen aan een gebruiker of groep wanneer een van de volgende vereisten ook is toegewezen:

- Exchange Online (Abonnement 1)
- Exchange Online (Abonnement 2)

Als we proberen dit product alleen aan een groep toe te wijzen, retourneert de portal een meldingsbericht. Als we de objectdetails selecteren, wordt het volgende foutbericht weergegeven:

  "Licentiebewerking is mislukt. Zorg ervoor dat de groep de benodigde services heeft voordat u een afhankelijke service toevoegt of verwijdert. **De service Microsoft Workplace Analytics vereist dat Exchange Online (Abonnement 2) ook wordt ingeschakeld.**"

Als u deze add-onlicentie aan een groep wilt toewijzen, moeten we ervoor zorgen dat de groep ook het vereiste serviceplan bevat. We kunnen bijvoorbeeld een bestaande groep bijwerken die al het volledige Office 365 E3-product bevat en het add-on-product eraan toevoegen.

Het is ook mogelijk om een zelfstandige groep te maken die alleen de minimaal vereiste producten bevat om de add-on te laten werken. Het kan worden gebruikt om alleen geselecteerde gebruikers voor het add-on-product in licentie te geven. Op basis van het vorige voorbeeld wijst u de volgende producten toe aan dezelfde groep:

- Office 365 Enterprise E3 met alleen het Exchange Online -serviceplan (Plan 2) ingeschakeld
- Microsoft Workplace Analytics

Vanaf nu verbruiken alle gebruikers die aan deze groep zijn toegevoegd één licentie van het E3-product en één licentie van het Workplace Analytics-product. Tegelijkertijd kunnen deze gebruikers lid zijn van een andere groep die hen het volledige E3-product geeft, en ze verbruiken nog steeds slechts één licentie voor dat product.

> [!TIP]
> U meerdere groepen maken voor elk serviceplan met een vereiste. Als u bijvoorbeeld zowel Office 365 Enterprise E1 als Office 365 Enterprise E3 voor uw gebruikers gebruikt, u twee groepen maken om Microsoft Workplace Analytics een licentie te geven: een die E1 als voorwaarde gebruikt en de andere die E3 gebruikt. Hiermee u de add-on distribueren naar E1- en E3-gebruikers zonder extra licenties te verbruiken.

## <a name="force-group-license-processing-to-resolve-errors"></a>Verwerking van groepslicenties forceren om fouten op te lossen

Afhankelijk van de stappen die u hebt genomen om de fouten op te lossen, kan het nodig zijn om handmatig de verwerking van een groep te activeren om de gebruikersstatus bij te werken.

Als u bijvoorbeeld bepaalde licenties vrijmaakt door directe licentietoewijzingen van gebruikers te verwijderen, moet u de verwerking activeren van groepen die voorheen niet alle gebruikersleden volledig licentie hebben gegeven. Als u een groep opnieuw wilt verwerken, gaat u naar het groepsvenster, opent u **Licenties**en selecteert u de knop **Opnieuw verwerken** op de werkbalk.

## <a name="force-user-license-processing-to-resolve-errors"></a>Gebruikerslicentieverwerking dwingen om fouten op te lossen

Afhankelijk van de stappen die u hebt genomen om de fouten op te lossen, kan het nodig zijn om handmatig de verwerking van een gebruiker te activeren om de gebruikersstatus bij te werken.

Nadat u bijvoorbeeld een probleem met dubbele proxyadres voor een getroffen gebruiker hebt opgelost, moet u de verwerking van de gebruiker activeren. Als u een gebruiker opnieuw wilt verwerken, gaat u naar het gebruikersvenster, opent u **Licenties**en selecteert u de knop **Opnieuw verwerken** op de werkbalk.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over andere scenario's voor licentiebeheer via groepen:

* [Wat is groepsgebaseerde licenties in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licenties toewijzen aan een groep in Azure Active Directory](licensing-groups-assign.md)
* [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](licensing-groups-migrate-users.md)
* [Gebruikers migreren tussen productlicenties met groepslicenties in Azure Active Directory](licensing-groups-change-licenses.md)
* [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](licensing-group-advanced.md)
* [PowerShell-voorbeelden voor groepslicenties in Azure Active Directory](licensing-ps-examples.md)
