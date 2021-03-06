---
title: Azure AD SSPR vanuit het aanmeldingsscherm van Windows 10
description: In deze zelfstudie gaat u in het aanmeldingsscherm van Windows 10 het opnieuw instellen van wachtwoorden inschakelen om zo het aantal telefonische hulpaanvragen naar de helpdesk te verminderen.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: eefb07136215d79b7c351dd4498bfeb79b6833de
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413730"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Azure AD-wachtwoord opnieuw instellen vanuit het aanmeldingsscherm

In deze zelfstudie gaat u gebruikers in staat stellen om hun wachtwoorden op het aanmeldingsscherm van Windows 10 opnieuw in te stellen. Met de nieuwe Windows-update van 10 april 2018 kunnen gebruikers met apparaten die zijn toegevoegd aan **Azure Active Directory** of **hybride Azure Active Directory**, de koppeling 'Wachtwoord opnieuw instellen' op hun aanmeldingsscherm gebruiken. Wanneer gebruikers op deze koppeling klikken, worden ze omgeleid naar dezelfde selfservice voor wachtwoordherstel (SSPR) als waarmee ze al bekend zijn.

> [!div class="checklist"]
> * De koppeling Wachtwoord opnieuw instellen configureren met Intune
> * Indien gewenst configureren met behulp van het Windows-register
> * Weten wat gebruikers te zien krijgen

## <a name="prerequisites"></a>Vereisten

* Windows-update van 10 april 2018 of nieuwere client die:
   * [Toegevoegd is aan Azure AD](../device-management-azure-portal.md) of 
   * [lid is van hybride Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md)
* Self-service voor wachtwoordherstel voor Azure AD moet zijn ingeschakeld.

## <a name="configure-reset-password-link-using-intune"></a>De koppeling Wachtwoord opnieuw instellen configureren met Intune

Intune gebruiken om de configuratie te wijzigen zodat gebruikers het wachtwoord opnieuw kunnen instellen vanuit het aanmeldingsscherm, is de meest flexibele methode. Met Intune kunt u een wijziging van de configuratie implementeren voor een specifieke groep machines die u hebt gedefinieerd. Voor deze methode is een Intune-registratie van het apparaat vereist.

### <a name="create-a-device-configuration-policy-in-intune"></a>Een beleid voor apparaatconfiguratie maken in Intune

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Intune**.
2. Maak een nieuw apparaatconfiguratieprofiel door te gaan naar **Apparaatconfiguratie** > **Profielen** > **Profiel maken**
   * Geef een beschrijvende naam op voor het profiel
   * Geef eventueel een duidelijke beschrijving van het profiel op
   * Platform **Windows 10 en hoger**
   * Profieltype **Aangepast**

   ![Profiel maken][CreateProfile]

3. **Instellingen** configureren
   * **Voeg de volgende OMA-URI-instelling toe** om de koppeling Wachtwoord opnieuw instellen in te schakelen
      * Geef een beschrijvende naam op om uit te leggen wat de instelling doet
      * Geef eventueel een duidelijke beschrijving van de instelling
      * **OMA-URI** ingesteld op `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Gegevenstype** ingesteld op **Geheel getal**
      * **Waarde** ingesteld op **1**
      * Klik op **OK**
   * Klik op **OK**
4. Klik op **Maken**.

### <a name="assign-a-device-configuration-policy-in-intune"></a>Een beleid voor apparaatconfiguratie toewijzen in Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Een groep maken waarop het beleid voor apparaatconfiguratie moet worden toegepast

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Azure Active Directory**.
2. Blader naar **Gebruikers en groepen** > **Alle groepen** > **Nieuwe groep**
3. Geef een naam op voor de groep en kies onder **Type lidmaatschap** de optie **Toegewezen**
   * Kies onder **Leden** de aan Azure AD toegevoegde Windows 10-apparaten waarop u het beleid wilt toepassen.
   * Klik op **Selecteren**.
4. Klik op **Maken**.

Meer informatie over het maken van groepen vindt u in het artikel [Toegang tot resources beheren met Azure Active Directory-groepen](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Beleid voor apparaatconfiguratie toewijzen aan een apparaatgroep

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Intune**.
2. Zoek het apparaatconfiguratieprofiel dat u eerder hebt gemaakt door te gaan naar **Apparaatconfiguratie** > **Profielen** en te klikken op het profiel
3. Het profiel toewijzen aan een groep apparaten 
   * Klik op **Toewijzingen** > onder **Opnemen** > **Groepen selecteren die moeten worden opgenomen**
   * Selecteer de groep die u eerder hebt gemaakt en klik op **Selecteren**
   * Klik op **Opslaan**

   ![Toewijzing][Assignment]

U hebt nu een beleid voor apparaatconfiguratie gemaakt en toegewezen om de koppeling Wachtwoord opnieuw instellen op het aanmeldingsscherm in te schakelen met Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>De koppeling Wachtwoord opnieuw instellen configureren met het register

Het wordt aanbevolen deze methode alleen te gebruiken om de gewijzigde instelling te testen.

1. Meld u met administratorreferenties aan op de Windows-pc
2. Voer **regedit** uit als administrator
3. Stel de volgende registersleutel in
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Wat gebruikers zien

Welke wijzigingen zien de gebruikers nadat het beleid is geconfigureerd en toegewezen? Hoe weten ze dat ze hun wachtwoord opnieuw kunnen instellen via het aanmeldingsscherm?

![Aanmeldingsscherm][LoginScreen]

Wanneer gebruikers zich proberen aan te melden, zien ze nu de koppeling Wachtwoord opnieuw instellen die toegang biedt tot de selfservice voor wachtwoordherstel vanuit het aanmeldingsscherm. Deze functionaliteit maakt het mogelijk dat gebruikers hun wachtwoord opnieuw instellen zonder dat ze een ander apparaat moeten gebruiken om toegang te krijgen tot een webbrowser.
Wanneer gebruikers zich proberen aan te melden, zien ze nu de koppeling Wachtwoord opnieuw instellen die toegang biedt tot de selfservice voor wachtwoordherstel vanuit het aanmeldingsscherm. Deze functionaliteit maakt het mogelijk dat gebruikers hun wachtwoord opnieuw instellen zonder dat ze een ander apparaat moeten gebruiken om toegang te krijgen tot een webbrowser.

Uw gebruikers vinden hulp voor het gebruik van deze functie in [Uw wachtwoord voor werk of school opnieuw instellen](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Algemene problemen

Wanneer u deze functie test met Hyper-V, wordt de koppeling 'Wachtwoord opnieuw instellen' niet weergegeven.

* Ga naar de VM die u voor de test gebruikt, klik op **Weergave** en schakel **Uitgebreide sessie** uit.

Wanneer u deze functionaliteit test met Extern bureaublad, wordt de koppeling Wachtwoord opnieuw instellen niet weergegeven.

* Wachtwoordherstel wordt momenteel niet ondersteund vanaf een extern bureaublad.

Als het Windows-vergrendelingsscherm wordt uitgeschakeld met een registersleutel of groepsbeleid, is **Wachtwoord opnieuw instellen** niet beschikbaar.

## <a name="clean-up-resources"></a>Resources opschonen

Als u besluit dat u de functionaliteit die u hebt geconfigureerd als onderdeel van deze zelfstudie niet meer wilt gebruiken, verwijdert u het Intune-apparaatconfiguratieprofiel dat u hebt gemaakt of de registersleutel.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gebruikers in staat gesteld om hun wachtwoorden op het aanmeldingsscherm van Windows 10 opnieuw in te stellen. Ga verder met de volgende zelfstudie om te zien hoe Azure Identity Protection kan worden geïntegreerd in de selfservice voor het opnieuw instellen van wachtwoorden en Multi-Factor Authentication-ervaringen.

> [!div class="nextstepaction"]
> [Risico's beoordelen bij het aanmelden](tutorial-risk-based-sspr-mfa.md)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Een Intune-apparaatconfiguratieprofiel maken om de koppeling Wachtwoord opnieuw instellen op het aanmeldingsscherm van Windows 10 in te schakelen"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune-beleid voor apparaatconfiguratie toewijzen aan een groep Windows 10-apparaten"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "De koppeling Wachtwoord opnieuw instellen op het aanmeldingsscherm van Windows 10"
