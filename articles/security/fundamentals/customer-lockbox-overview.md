---
title: Klantvergrendelingsbox voor Microsoft Azure
description: Technisch overzicht van Customer Lockbox voor Microsoft Azure, dat controle biedt over toegang tot cloudproviderwanneer Microsoft mogelijk toegang moet krijgen tot klantgegevens.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561966"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Klantvergrendelingsbox voor Microsoft Azure

> [!NOTE]
> Als u deze functie wilt gebruiken, moet uw organisatie een [Azure-ondersteuningsplan](https://azure.microsoft.com/support/plans/) hebben met een minimaal niveau van **ontwikkelaar.**

Customer Lockbox voor Microsoft Azure biedt klanten een interface om verzoeken om toegang tot klantgegevens te controleren en goed te keuren of af te wijzen. Het wordt gebruikt in gevallen waarin een Microsoft-technicus toegang nodig heeft tot klantgegevens tijdens een ondersteuningsverzoek.

In dit artikel wordt geverslagd over de manier waarop klantlockbox-aanvragen worden gestart, bijgehouden en opgeslagen voor latere beoordelingen en audits.

Customer Lockbox is nu algemeen beschikbaar en momenteel ingeschakeld voor externe desktoptoegang tot virtuele machines.

## <a name="workflow"></a>Werkstroom

In de volgende stappen wordt een typische werkstroom voor een klantvergrendelingsaanvraag beschreven.

1. Iemand bij een organisatie heeft een probleem met zijn Azure-workload.

2. Nadat deze persoon het probleem oplost, maar het niet kan oplossen, openen ze een ondersteuningsticket van de [Azure-portal.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) Het ticket is toegewezen aan een Azure Customer Support Engineer.

3. Een Azure Support Engineer beoordeelt de serviceaanvraag en bepaalt de volgende stappen om het probleem op te lossen.

4. Als de ondersteuningstechnicus het probleem niet kan oplossen met behulp van standaardhulpprogramma's en telemetrie, is de volgende stap het aanvragen van verhoogde machtigingen met behulp van een Just-In-Time (JIT)-toegangsservice. Deze aanvraag kan van de oorspronkelijke ondersteuningstechnicus zijn. Of het kan van een andere technicus zijn omdat het probleem is geëscaleerd naar het Azure DevOps-team.

5. Nadat de toegangsaanvraag is ingediend door de Azure Engineer, evalueert de Just-In-Time-service de aanvraag rekening houdend met factoren zoals:
    - De reikwijdte van de resource
    - Of de aanvrager een geïsoleerde identiteit is of multi-factor authenticatie gebruikt
    - Machtigingsniveaus

    Op basis van de JIT-regel kan dit verzoek ook een goedkeuring van interne Microsoft-fiatteurs bevatten. De goedkeurder kan bijvoorbeeld de lead Voor klantenondersteuning of de DevOps-manager zijn.

6. Wanneer het verzoek directe toegang tot klantgegevens vereist, wordt een klantlockbox-aanvraag gestart. Bijvoorbeeld toegang tot extern bureaublad tot de virtuele machine van een klant.

    De aanvraag is nu in een **status met klantmelding,** in afwachting van de goedkeuring van de klant voordat u toegang verleent.

7. Bij de klantorganisatie ontvangt de gebruiker die de [rol Eigenaar](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) voor het Azure-abonnement heeft, een e-mail van Microsoft om hen op de hoogte te stellen van het in behandeling zijnde toegangsverzoek. Voor klantvergrendelingsaanvragen is deze persoon de aangewezen goedkeurder.

    Voorbeeld e-mail:

    ![Azure Customer Lockbox - e-mailmelding](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. De e-mailmelding bevat een koppeling naar het **customer lockbox-blad** in de Azure-portal. Via deze koppeling meldt de aangewezen goedkeurder zich aan bij de Azure-portal om alle in behandeling zijnde aanvragen die hun organisatie heeft voor Customer Lockbox weer te geven:

    ![Azure Customer Lockbox - bestemmingspagina](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   De aanvraag blijft vier dagen in de wachtrij van de klant staan. Na deze tijd vervalt het toegangsverzoek automatisch en wordt er geen toegang verleend aan Microsoft-technici.

9. Om de details van de in behandeling zijnde aanvraag te krijgen, kan de aangewezen goedkeurder de aanvraag voor het slotvak selecteren in **aanvragen in behandeling:**

    ![Azure Customer Lockbox - bekijk de in behandeling zijnde aanvraag](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. De aangewezen goedkeurder kan ook de **SERVICE REQUEST-id** selecteren om het ondersteuningsticketverzoek te bekijken dat door de oorspronkelijke gebruiker is gemaakt. Deze informatie geeft context voor waarom Microsoft Support is ingeschakeld en de geschiedenis van het gerapporteerde probleem. Bijvoorbeeld:

    ![Azure Customer Lockbox - bekijk de aanvraag voor ondersteuningstickets](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Na het bekijken van het verzoek selecteert de aangewezen goedkeurder **Goedkeuren** of **Weigeren:**

    ![Azure-klantenkluisje - selecteer Goedkeuren of Weigeren](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Als gevolg van de selectie:
    - **Goedkeuren:** toegang wordt verleend aan de Microsoft-technicus. De toegang wordt verleend voor een standaardperiode van acht uur.
    - **Weigeren:** het verzoek om verhoogde toegang door de Microsoft-technicus wordt afgewezen en er wordt geen verdere actie ondernomen.

Voor controledoeleinden worden de acties die in deze werkstroom worden uitgevoerd, vastgelegd in [klantenlockboxaanvraaglogboeken.](#auditing-logs)

## <a name="auditing-logs"></a>Logboeken controleren

Logboeken van de klantlockbox worden opgeslagen in activiteitslogboeken. Selecteer **activiteitslogboeken** in de Azure-portal om controlegegevens met betrekking tot aanvragen voor klantvergrendeling weer te geven. U filteren op specifieke acties, zoals:
- **Aanvraag lockbox weigeren**
- **Aanvraag voor lockboxen maken**
- **Aanvraag lockbox goedkeuren**
- **Aanvraag lockbox verlopen**

Bijvoorbeeld:

![Azure Customer Lockbox - activiteitslogboeken](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Ondersteunde services en scenario's in algemene beschikbaarheid

De volgende services en scenario's zijn momenteel algemeen beschikbaar voor Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Toegang tot extern bureaublad tot virtuele machines

Customer Lockbox is momenteel ingeschakeld voor aanvragen voor extern bureaublad-toegang tot virtuele machines. De volgende workloads worden ondersteund:
- Platform as a service (PaaS) - Azure Cloud Services (webrol en werkrol)
- Infrastructure as a service (IaaS) - Windows en Linux (alleen Azure Resource Manager)
- Virtuele machineschaalset - Windows en Linux

> [!NOTE]
> IaaS Classic-exemplaren worden niet ondersteund door Customer Lockbox. Als u workloads hebt die worden uitgevoerd op IaaS Classic-exemplaren, raden we u aan deze te migreren van De implementatiemodellen van Classic naar Resource Manager. Zie Door [platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager voor](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)instructies.

#### <a name="detailed-audit-logs"></a>Gedetailleerde controlelogboeken

Voor scenario's die externe desktoptoegang omvatten, u Windows-gebeurtenislogboeken gebruiken om de acties van de Microsoft-technicus te bekijken. Overweeg Azure Security Center te gebruiken om uw gebeurtenislogboeken te verzamelen en de gegevens naar uw werkruimte te kopiëren voor analyse. Zie [Gegevensverzameling in Azure Security Center](../../security-center/security-center-enable-data-collection.md)voor meer informatie.

## <a name="supported-services-and-scenarios-in-preview"></a>Ondersteunde services en scenario's in preview

De volgende services zijn nu in preview voor Customer Lockbox:

- Azure Storage

- Azure SQL Database

- Azure Data Explorer

- Virtuele machines (nu ook toegang tot geheugendumps en beheerde schijven)

- Azure-abonnementsoverdrachten

Als u Customer Lockbox wilt inschakelen voor deze voorbeeldaanbiedingen voor uw organisatie, meldt u zich aan voor [Customer Lockbox voor Azure Public Preview](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Uitsluitingen

Aanvragen voor klantvergrendeling worden niet geactiveerd in de volgende scenario's voor technische ondersteuning:

- Een Microsoft-technicus moet een activiteit uitvoeren die buiten de standaardwerkprocedures valt. Bijvoorbeeld om services te herstellen of te herstellen in onverwachte of onvoorspelbare scenario's.

- Een Microsoft-technicus heeft toegang tot het Azure-platform als onderdeel van het oplossen van problemen en heeft per ongeluk toegang tot klantgegevens. Het Azure Network Team voert bijvoorbeeld probleemoplossing uit die resulteert in een pakketopname op een netwerkapparaat. Als de klant de gegevens echter heeft versleuteld terwijl deze onderweg waren, kan de technicus de gegevens niet lezen.

## <a name="next-steps"></a>Volgende stappen

Customer Lockbox is automatisch beschikbaar voor alle klanten die een [Azure-ondersteuningsabonnement](https://azure.microsoft.com/support/plans/) hebben met een minimaal niveau van **ontwikkelaar.**

Wanneer u een in aanmerking komend ondersteuningsplan hebt, is er geen actie vereist die u nodig hebt om Customer Lockbox in te schakelen. Klant Lockbox-aanvragen worden gestart door een Microsoft-technicus als deze actie nodig is om een ondersteuningsticket te ontwikkelen dat is ingediend van iemand in uw organisatie.
