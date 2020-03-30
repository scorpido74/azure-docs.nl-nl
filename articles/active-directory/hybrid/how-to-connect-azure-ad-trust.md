---
title: Azure AD Connect - AD FS-vertrouwensrelatie beheren met Azure AD met Azure AD Connect | Microsoft Documenten
description: Operationele details van Azure AD-vertrouwensafhandeling door Azure AD-verbinding.
keywords: AD FS, ADFS, AD FS-beheer, AAD Connect, Connect, Azure AD, vertrouwen, AAD, claim, claimregels, uitgifte, transformatie, regels, back-up, herstel
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3e521fb7668305ce511aaddd63ed2cce8dfed0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331725"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>AD FS-vertrouwensrelatie met Azure AD beheren met behulp van Azure AD Connect

## <a name="overview"></a>Overzicht

Azure AD Connect kan federatie tussen on-premises Active Directory Federation Service (AD FS) en Azure AD beheren. Dit artikel geeft een overzicht van:

* De verschillende instellingen die zijn geconfigureerd op de vertrouwensrelatie door Azure AD Connect
* De regels voor uitgiftetransformatie (claimregels) die zijn ingesteld door Azure AD Connect
* Een back-up maken en uw claimregels herstellen tussen upgrades en configuratie-updates. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Instellingen die worden beheerd door Azure AD Connect

Azure AD Connect beheert **alleen** instellingen met betrekking tot Azure AD-vertrouwensrelatie. Azure AD Connect wijzigt geen instellingen voor andere vertrouwensrelaties van relying party in AD FS. In de volgende tabel worden instellingen weergegeven die worden beheerd door Azure AD Connect.

| Instelling | Beschrijving |
| :--- | :--- |
| Certificaatondertekeningscertificaat | Azure AD Connect kan worden gebruikt om de vertrouwensrelatie opnieuw in te stellen en opnieuw te maken met Azure AD. Azure AD Connect maakt een eenmalige onmiddellijke rollover van tokenondertekeningscertificaten voor AD FS en werkt de instellingen van de Azure AD-domeinfederatie bij.|
| Tokenondertekeningsalgoritme | Microsoft raadt aan om SHA-256 te gebruiken als het algoritme voor tokenondertekening. Azure AD Connect kan detecteren of het tokenondertekeningsalgoritme is ingesteld op een waarde die minder veilig is dan SHA-256. Het zal de instelling bijwerken naar SHA-256 in de volgende mogelijke configuratiebewerking. Andere vertrouwensrelatie van relying party moet worden bijgewerkt om het nieuwe tokenondertekeningscertificaat te gebruiken. |
| Azure AD-vertrouwensid | Azure AD Connect stelt de juiste id-waarde in voor de Azure AD-vertrouwensrelatie. AD FS identificeert op unieke wijze de Azure AD-vertrouwensrelatie met behulp van de id-waarde. |
| Azure AD-eindpunten | Azure AD Connect zorgt ervoor dat de eindpunten die zijn geconfigureerd voor de AD-vertrouwensrelatie in Azure altijd volgens de meest recente aanbevolen waarden voor tolerantie en prestaties. |
| Regels voor uitgiftetransformatie | Er zijn verschillende claimregels die nodig zijn voor een optimale prestaties van functies van Azure AD in een federatieve instelling. Azure AD Connect zorgt ervoor dat de AD-vertrouwensrelatie van Azure altijd is geconfigureerd met de juiste set aanbevolen claimregels. |
| Alternatieve id | Als synchronisatie is geconfigureerd om alternatieve id te gebruiken, configureert Azure AD Connect AD FS om verificatie uit te voeren met behulp van alternatieve-id. |
| Automatische metagegevens-update | Vertrouwen in Azure AD is geconfigureerd voor automatische metagegevensbijwerken. AD FS controleert periodiek de metagegevens van Azure AD-vertrouwensrelaties en houdt deze up-to-date voor het geval deze aan de Azure AD-kant wordt gewijzigd. |
| Geïntegreerde Windows-verificatie (IWA) | Tijdens de hybride Azure AD-joinbewerking is IWA ingeschakeld voor apparaatregistratie om hybride Azure AD-join voor downlevel-apparaten te vergemakkelijken |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Uitvoeringsstromen en federatie-instellingen geconfigureerd door Azure AD Connect

Azure AD Connect werkt niet alle instellingen voor Azure AD-vertrouwensrelatie bij tijdens configuratiestromen. De gewijzigde instellingen zijn afhankelijk van welke taak of uitvoeringsstroom wordt uitgevoerd. In de volgende tabel worden de instellingen weergegeven die van invloed zijn op verschillende uitvoeringsstromen.

| Uitvoeringsstroom | Getroffen instellingen |
| :--- | :--- |
| Eerste pas installatie (express) | Geen |
| First pass installatie (nieuwe AD FS boerderij) | Er wordt een nieuwe AD FS-farm gemaakt en er wordt een vertrouwensrelatie met Azure AD gemaakt. |
| Installatie van eerste pas (bestaande AD FS-farm, bestaande Azure AD-vertrouwensrelatie) | Azure AD-vertrouwensid, regels voor uitgiftetransformatie, Azure AD-eindpunten, Alternatieve-id (indien nodig), automatische metagegevensupdate |
| Azure AD-vertrouwensrelatie opnieuw instellen | Tokenondertekeningscertificaat, Tokenondertekeningsalgoritme, Azure AD-vertrouwensid, Uitgiftetransformatieregels, Azure AD-eindpunten, Alternatieve-id (indien nodig), automatische metagegevensupdate |
| Federatieserver toevoegen | Geen |
| WAP-server toevoegen | Geen |
| Apparaatopties | Uitgifte transformatie regels, IWA voor apparaatregistratie |
| Federatief domein toevoegen | Als het domein voor de eerste keer wordt toegevoegd, dat wil zeggen dat de instelling verandert van één domeinfederatie naar multidomeinfederatie : Azure AD Connect maakt de vertrouwensrelatie opnieuw helemaal opnieuw. Als de vertrouwensrelatie met Azure AD al is geconfigureerd voor meerdere domeinen, worden alleen uitgiftetransformatieregels gewijzigd |
| TLS bijwerken | Geen |

Tijdens alle bewerkingen, waarin elke instelling wordt gewijzigd, maakt Azure AD Connect een back-up van de huidige vertrouwensinstellingen bij **%ProgramData%\AADConnect\ADFS**

![Azure AD Connect-pagina met bericht over bestaande Azure AD-vertrouwensback-up](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Voorafgaand aan versie 1.1.873.0 bestond de back-up alleen uit regels voor uitgiftetransformatie en er werd een back-up gemaakt in het logboekbestand wizard trace.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regels voor uitgiftetransformatie die zijn ingesteld door Azure AD Connect

Azure AD Connect zorgt ervoor dat de AD-vertrouwensrelatie van Azure altijd is geconfigureerd met de juiste set aanbevolen claimregels. Microsoft raadt aan Azure AD Connect te gebruiken voor het beheren van uw Azure AD-vertrouwensrelatie. In deze sectie worden de ingestelde regels voor uitgiftetransformatie en de beschrijving ervan weergegeven.

| Regelnaam | Beschrijving |
| --- | --- |
| Probleem UPN | Met deze regel wordt de waarde van de gebruikersnaam opgevraagd vanaf het kenmerk dat is geconfigureerd in synchronisatie-instellingen voor gebruikersprincipalname.|
| Queryobjectguid en msdsconsistencyguid voor aangepaste ImmutableId-claim | Deze regel voegt een tijdelijke waarde toe in de pijplijn voor objectguid- en msdsconsistencyd-waarde als deze bestaat |
| Controleer op het bestaan van msdsconsistencyguid | Op basis van de vraag of de waarde voor msdsconsistencyguid bestaat of niet, stellen we een tijdelijke vlag in om te sturen wat te gebruiken als Onveranderlijke Id |
| Msdsconsistencyguid als onveranderlijke id uitgeven als deze bestaat | Msdsconsistencyguid als Onveranderlijke id uitgeven als de waarde bestaat |
| ProbleemobjectGuidRule als de msdsConsistencyGuid-regel niet bestaat | Als de waarde voor msdsconsistencyguid niet bestaat, wordt de waarde van objectguid uitgegeven als ImmutableId |
| Naamid van uitgifte | Met deze regel wordt waarde voor de claim voor naam-id's gegeven.|
| Accounttype uitgeven voor computers die zijn verbonden met een domein | Als de entiteit die wordt geverifieerd een domeinlid apparaat is, geeft deze regel het accounttype af als DJ die een domein aanduidt dat is samengevoegd |
| AccountType met de waarde GEBRUIKER uitgeven wanneer het geen computeraccount is | Als de entiteit die wordt geverifieerd een gebruiker is, geeft deze regel het accounttype af als Gebruiker |
| Issue issuerid wanneer het geen computeraccount is | Met deze regel wordt de waarde van de issuerId verwijs wanneer de authenticerende entiteit geen apparaat is. De waarde wordt gemaakt via een regex, die is geconfigureerd door Azure AD Connect. De regex wordt gemaakt nadat rekening wordt gehouden met alle domeinen die worden gefedereerd met Azure AD Connect. |
| Probleem probleem voor DJ-computer auth | Deze regel geeft de waarde van de issuerId af wanneer de authenticerende entiteit een apparaat is |
| Probleem onpremobjectguid voor computers die zijn verbonden met een domein | Als de entiteit die wordt geverifieerd een domein-samengevoegd apparaat is, geeft deze regel de on-premises objectguid voor het apparaat af |
| Ga door de primaire SID | Deze regel geeft de primaire SID van de authenticerende entiteit |
| Pass through claim - insideCorporateNetwork | Met deze regel wordt een claim weergegeven waarmee Azure AD weet of de verificatie afkomstig is van het bedrijfsnetwerk of extern |
| Pass Through Claim – Psso |   |
| Aanvragen voor het verlopen van wachtwoorden afgeven | Met deze regel worden drie claims voor de vervaldatum van het wachtwoord, het aantal dagen waarop het wachtwoord verloopt en de entiteit die wordt geverifieerd en URL waar moet worden gerouteerd voor het wijzigen van het wachtwoord, wordt uitgevoerd.|
| Pass through claim – authnmethodsreferences | De waarde in de claim die onder deze regel is uitgegeven, geeft aan welk type verificatie voor de entiteit is uitgevoerd |
| Pass through claim - multifactorauthenticationinstant | De waarde van deze claim geeft de tijd op, in UTC, wanneer de gebruiker voor het laatst meervoudige factorverificatie heeft uitgevoerd. |
| Pass through claim - AlternateLoginID | Met deze regel wordt de AlternateLoginID-claim uitgevoerd als de verificatie is uitgevoerd met alternatieve inlog-id. |

> [!NOTE]
> De claimregels voor Issue UPN en ImmutableId verschillen als u niet-standaardkeuze gebruikt tijdens de Configuratie van Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Regels voor uitgiftetransformatie herstellen

Azure AD Connect-versie 1.1.873.0 of hoger maakt een back-up van de Azure AD-vertrouwensinstellingen wanneer er een update wordt uitgevoerd naar de aamservice-vertrouwensinstellingen van Azure. Er wordt een back-up gemaakt van de au-to-trainingsvertrouwensrelaties **op %ProgramData%\AADConnect\ADFS**. De bestandsnaam is in de&lt;volgende&gt;-&lt;&gt;indeling AadTrust- datumtijd .txt, bijvoorbeeld - AadTrust-20180710-150216.txt

![Een schermafbeelding van voorbeeld een back-up van Azure AD-vertrouwensrelatie](./media/how-to-connect-azure-ad-trust/backup.png)

U de regels voor uitgiftetransformatie herstellen met de voorgestelde stappen hieronder

1. De gebruikersinterface van AD FS-beheer openen in Serverbeheer
2. Open de azure AD-vertrouwenseigenschappen door **AD FS &gt; Relying Party Trusts &gt; Microsoft Office 365 Identity Platform &gt; Te gaan bewerken van claimuitgiftebeleid**
3. Klik op **Regel toevoegen**
4. Selecteer in de sjabloon claimregel de optie Claims verzenden met een aangepaste regel en klik op **Volgende**
5. De naam van de claimregel kopiëren uit back-upbestand en deze plakken in de naam van de **regel claim**
6. Kopieer de claimregel van back-upbestand naar het tekstveld voor **aangepaste regel** en klik op **Voltooien**

> [!NOTE]
> Zorg ervoor dat uw aanvullende regels niet in strijd zijn met de regels die zijn geconfigureerd door Azure AD Connect.

## <a name="next-steps"></a>Volgende stappen
* [Active Directory Federation Services beheren en aanpassen met Azure AD Connect](how-to-connect-fed-management.md)
