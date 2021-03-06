---
title: Toevoegen van een Salesforce-SAML-provider met behulp van aangepaste beleidsregels in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over hoe u Azure Active Directory B2C aangepaste beleidsregels maken en beheren.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1307fc455cacde81cb25ad58c5e99df21f126568
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448251"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: Meld u aan met behulp van Salesforce-accounts via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel leest u hoe u [aangepast beleid](active-directory-b2c-overview-custom.md) voor het instellen van de aanmelding voor gebruikers van een specifieke Salesforce-organisatie.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-ad-b2c-setup"></a>Installatie van de Azure AD B2C

Zorg ervoor dat u alle laten hoe u zien de stappen hebt uitgevoerd naar [aan de slag met aangepaste beleidsregels](active-directory-b2c-get-started-custom.md) in Azure Active Directory B2C (Azure AD B2C).

Deze omvatten:

* Een Azure AD B2C-tenant maken.
* Maak een Azure AD B2C-toepassing.
* Twee groepsbeleid-engine-toepassingen registreren.
* Instellen van sleutels.
* Instellen van het starter-pack.

### <a name="salesforce-setup"></a>SalesForce-instellingen

In dit artikel nemen we aan dat u hebt al het volgende gedaan:

* Aangemeld voor een Salesforce-account. U kunt zich registreren voor een [gratis account van de Developer Edition](https://developer.salesforce.com/signup).
* [Stel een mijn domein](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) voor uw Salesforce-organisatie.

## <a name="set-up-salesforce-so-users-can-federate"></a>Salesforce instellen, zodat gebruikers kunnen federeren

Om te communiceren met Salesforce van Azure AD B2C, moet u de Salesforce-metagegevens-URL ophalen.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce instellen als een id-provider

> [!NOTE]
> In dit artikel gaan we ervan uit u [Salesforce Lightning ervaring](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Aanmelden bij Salesforce](https://login.salesforce.com/). 
2. In het menu links onder **instellingen**, vouw **identiteit**, en klik vervolgens op **id-Provider**.
3. Klik op **id-Provider inschakelen**.
4. Onder **selecteert u het certificaat**, selecteert u het certificaat dat u wilt dat Salesforce gebruiken om te communiceren met Azure AD B2C. (U kunt het standaard-certificaat gebruiken.) Klik op **Opslaan**. 

### <a name="create-a-connected-app-in-salesforce"></a>Een verbonden app maken in Salesforce

1. Op de **id-Provider** pagina, Ga naar **serviceproviders**.
2. Klik op **serviceproviders worden nu gemaakt via Apps die zijn verbonden. Klik hier.**
3. Onder **basisinformatie**, voer de vereiste waarden voor uw verbonden app.
4. Onder **Web-Appinstellingen**, selecteer de **SAML inschakelen** selectievakje.
5. In de **entiteit-ID** en voer de volgende URL. Zorg ervoor dat u de waarde voor vervangen `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. In de **ACS URL** en voer de volgende URL. Zorg ervoor dat u de waarde voor vervangen `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Laat de standaardwaarden voor alle andere instellingen.
8. Ga naar de onderkant van de lijst en klik vervolgens op **opslaan**.

### <a name="get-the-metadata-url"></a>De metagegevens-URL ophalen

1. Klik op de overzichtspagina van uw verbonden app **beheren**.
2. Kopieer de waarde voor **detectie-eindpunt voor metagegevens**, en vervolgens op te slaan. U gebruikt deze verderop in dit artikel.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce-gebruikers instellen om te federeren

1. Op de **beheren** pagina van uw verbonden app, gaat u naar **profielen**.
2. Klik op **profielen beheren**.
3. Selecteer de profielen (of groepen gebruikers) die u wilt federeren met Azure AD B2C. Als een systeembeheerder bent, selecteert u de **systeembeheerder** selectievakje, zodat u kunt federeren met uw Salesforce-account.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Een handtekeningcertificaat voor Azure AD B2C genereren

Aanvragen verzonden naar de Salesforce moeten worden ondertekend door Azure AD B2C. Voor het genereren van een ondertekend certificaat, opent u Azure PowerShell en voer de volgende opdrachten.

> [!NOTE]
> Zorg ervoor dat u de naam van tenant en het wachtwoord in de twee belangrijkste regels bijwerken.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Het SAML-handtekeningcertificaat toevoegen aan Azure AD B2C

Het handtekeningcertificaat uploaden naar uw Azure AD B2C-tenant: 

1. Ga naar uw Azure AD B2C-tenant. Klik op **instellingen** > **Identity-Ervaringsframework** > **Beleidssleutels**.
2. Klik op **+ toevoegen**, en vervolgens:
    1. Klik op **opties** > **uploaden**.
    2. Voer een **naam** (bijvoorbeeld SAMLSigningCert). Het voorvoegsel *B2C_1A_* wordt automatisch toegevoegd aan de naam van uw sleutel.
    3. Als uw certificaat selecteren, schakelt u **uploaden bestand besturingselement**. 
    4. Wachtwoord van het certificaat die u hebt ingesteld in het PowerShell-script.
3. Klik op **Create**.
4. Controleer of u een sleutel (bijvoorbeeld B2C_1A_SAMLSigningCert) hebt gemaakt. Noteer de volledige naam (met inbegrip van *B2C_1A_*). U wordt naar deze sleutel later in het beleid verwijzen.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Maken van de claimprovider Salesforce SAML in het Basisbeleid

U moet Salesforce definiëren als een claimprovider, zodat gebruikers kunnen zich aanmelden met behulp van Salesforce. Met andere woorden, moet u het eindpunt dat Azure AD B2C met communiceren zullen opgeven. Het eindpunt wordt *bieden* een set *claims* die Azure AD B2C gebruikt om te controleren dat een specifieke gebruiker is geverifieerd. Om dit te doen, Voeg een `<ClaimsProvider>` voor Salesforce in het bestand uitbreiding van uw beleid:

1. Open het extensiebestand (TrustFrameworkExtensions.xml) in uw werkmap.
2. Zoek de `<ClaimsProviders>` sectie. Als deze niet bestaat, kunt u deze onder het hoofdknooppunt maken.
3. Toevoegen van een nieuwe `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Onder de `<ClaimsProvider>` knooppunt:

1. Wijzig de waarde voor `<Domain>` naar een unieke waarde die onderscheidt `<ClaimsProvider>` van andere id-providers.
2. Werk de waarde voor `<DisplayName>` naar een weergavenaam voor de claimprovider. Deze waarde wordt op dit moment niet gebruikt.

### <a name="update-the-technical-profile"></a>Het technische profiel bijwerken

Als u een SAML-token van Salesforce, definiëren de protocollen die door Azure AD B2C wordt gebruikt om te communiceren met Azure Active Directory (Azure AD). Dit doen in de `<TechnicalProfile>` element van `<ClaimsProvider>`:

1. Bijwerken van de ID van de `<TechnicalProfile>` knooppunt. Deze ID wordt gebruikt om te verwijzen naar dit technisch profiel van andere onderdelen van het beleid.
2. Werk de waarde voor `<DisplayName>`. Deze waarde wordt weergegeven op de knop aanmelden op de aanmeldingspagina.
3. Werk de waarde voor `<Description>`.
4. SalesForce maakt gebruik van het SAML 2.0-protocol. Zorg ervoor dat de waarde voor `<Protocol>` is **SAML2**.

Update de `<Metadata>` sectie in het voorgaande XML-bestand in overeenstemming met de instellingen voor uw specifieke Salesforce-account. Update voor waarden van de metagegevens in het XML-bestand:

1. Werk de waarde van `<Item Key="PartnerEntity">` met de metagegevens-URL voor Salesforce die u eerder hebt gekopieerd. Deze heeft de volgende indeling: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. In de `<CryptographicKeys>` sectie, werk de waarde voor beide exemplaren van `StorageReferenceId` op de naam van de sleutel van het handtekeningcertificaat (bijvoorbeeld B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensiebestand voor verificatie

Het beleid is nu geconfigureerd zodat Azure AD B2C weet hoe om te communiceren met Salesforce. Upload het bestand uitbreiding van uw beleid, om te controleren of dat er problemen met tot nu toe. Het bestand te uploaden uitbreiding van uw beleid:

1. In uw Azure AD B2C-tenant, gaat u naar de **alle beleidsregels** blade.
2. Selecteer de **het beleid overschrijven als deze bestaat** selectievakje.
3. Upload het extensiebestand (TrustFrameworkExtensions.xml). Zorg ervoor dat deze validatie niet is mislukt.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registreren van de claimprovider Salesforce SAML naar een gebruikersbeleving

Voeg vervolgens de Salesforce-SAML-identiteitsprovider toe op een van uw gebruiker reizen. Op dit moment wordt de id-provider is ingesteld, maar het is niet beschikbaar is op een van de gebruiker registreren of aanmelden pagina's. De id-provider toevoegen aan een aanmeldingspagina, maak eerst een kopie van een bestaande sjabloon voor de gebruikersbeleving. Vervolgens, de sjabloon aanpassen zodat het bevat ook de Azure AD-id-provider.

1. Open het bestand basis van uw beleid (bijvoorbeeld TrustFrameworkBase.xml).
2. Zoek de `<UserJourneys>` -element en kopieer de gehele `<UserJourney>` waarde, met inbegrip van Id = 'SignUpOrSignIn'.
3. Open het extensiebestand (bijvoorbeeld TrustFrameworkExtensions.xml). Zoek de `<UserJourneys>` element. Als het element niet bestaat, maakt.
4. Plak de gehele gekopieerd `<UserJourney>` als onderliggende site van de `<UserJourneys>` element.
5. Wijzig de naam van de ID van de nieuwe `<UserJourney>` (bijvoorbeeld SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>De id-provider knop weergeven

De `<ClaimsProviderSelection>` element is vergelijkbaar met een knop van de id-provider op een pagina voor registreren of aanmelden. Door toe te voegen een `<ClaimsProviderSelection>` -element voor Salesforce, een nieuwe knop wordt weergegeven wanneer een gebruiker gaat u naar deze pagina. Om weer te geven op de knop identity provider:

1. In de `<UserJourney>` die u hebt gemaakt, vindt de `<OrchestrationStep>` met `Order="1"`.
2. Voeg de volgende XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Stel `TargetClaimsExchangeId` naar een logische waarde. We raden aan volgen dezelfde conventie als anderen (bijvoorbeeld  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>De id-provider knop koppelen aan een actie

Nu dat u een id-provider-knop op locatie hebt, een koppeling naar een actie. In dit geval is de actie voor Azure AD B2C om te communiceren met Salesforce voor het ontvangen van een SAML-token. U kunt dit doen door een koppeling van het technische profiel voor uw Salesforce SAML claimprovider:

1. In de `<UserJourney>` knooppunt, vinden de `<OrchestrationStep>` met `Order="2"`.
2. Voeg de volgende XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Update `Id` op dezelfde waarde die u eerder hebt gebruikt voor `TargetClaimsExchangeId`.
4. Update `TechnicalProfileReferenceId` naar de `Id` van het technische profiel u eerder hebt gemaakt (bijvoorbeeld ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>De bijgewerkte extensiebestand uploaden

U klaar bent met de extensiebestand wijzigt. Opslaan en upload dit bestand. Zorg ervoor dat alle validaties slagen.

### <a name="update-the-relying-party-file"></a>De relying party-bestand bijwerken

Werk vervolgens de relying party (RP)-bestand dat initieert de gebruikersbeleving die u hebt gemaakt:

1. Maak een kopie van SignUpOrSignIn.xml in uw werkmap. Wijzig de naam van het (bijvoorbeeld SignUpOrSignInWithAAD.xml).
2. Open het nieuwe bestand en update de `PolicyId` voor het kenmerk `<TrustFrameworkPolicy>` met een unieke waarde. Dit is de naam van uw beleid (bijvoorbeeld SignUpOrSignInWithAAD).
3. Wijzig de `ReferenceId` kenmerk in `<DefaultUserJourney>` zodat deze overeenkomen met de `Id` van de nieuwe gebruikersbeleving die u hebt gemaakt (bijvoorbeeld SignUpOrSignUsingContoso).
4. Sla uw wijzigingen op en upload het bestand.

## <a name="test-and-troubleshoot"></a>Testen en problemen oplossen

Als u wilt testen het aangepaste beleid dat u net hebt geüpload, in de Azure-portal, gaat u naar de beleidsblade op en klik vervolgens op **nu uitvoeren**. Als dit mislukt, Zie [aangepaste beleidsproblemen](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Volgende stappen

Feedback geven aan [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
