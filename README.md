# A  URI definition for Hotspot2.0 Profile Provisioning

Version: 0.3 (work in progress)

Date: 2024.05.28 

<hr>

**TAKE NOTE**

**This is an initial proposal used to submit the URI definition for allocation of a provisional URI scheme by IANA. The IANA registration for the "hs20" URI scheme is available here: https://www.iana.org/assignments/uri-schemes/prov/hs20. The expectation is that this specification will be refined, following feedback from implementors.**

<hr>

## Undertakings and Limitation of Liability

This Document and all the information contained in this Document is provided on an ‘as is’ basis without warranty of any kind, either expressed or implied, including, but not limited to, the implied warranties of merchantability, fitness for particular purpose, or non-infringement.


In addition, the WBA (and all other organizations who may have contributed to this document) makes no representations or warranties about the accuracy, completeness, or suitability for any purpose of the information. The information may contain technical inaccuracies or typographical errors. All liabilities of the WBA (and all other organizations who may have contributed to this document) howsoever arising for any such inaccuracies, errors, incompleteness, suitability, merchantability, fitness, and non-infringement are expressly excluded to the fullest extent permitted by law. None of the contributors make any representation or offer to license any of their intellectual property rights to the other, or to any third party. Nothing in this information or communication shall be relied on by any recipient. 


The WBA also disclaims any responsibility for identifying the existence of or for evaluating the applicability of any claimed copyrights, patents, patent applications, or other intellectual property rights, and will take no position on the validity or scope of any such rights. The WBA takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this document or the extent to which any license under such rights might or might not be available; nor does it represent that it has made any effort to identify any such rights. 


Neither the WBA nor any of the other organizations who may have contributed to this document will be liable for loss or damage arising out of or in connection with the use of this information. This is a comprehensive limitation of liability that applies to all damages of any kind, including (without limitation) compensatory, direct, indirect, or consequential damages, loss of data, income or profit, loss of or damage to property and claims of third parties.

<hr>

# 1.	Introduction

This document defines the Hotspot 2.0 (HS20) Profile Provisioning Uniform Resource Identifier (URI) [RFC3986] scheme designed for automated provisioning of a sub-set of Hotspot 2.0 configuration data as defined by Wi-Fi Alliance in [PASSPOINT]. 

> Note: The legacy term Hotspot2.0/HS20 is used in this URI definition instead of Passpoint® to avoid any trademark issues as identified in section 3.4 of [RFC5378]. Furthermore, the "2.0" is not meant to refer to a particular version of the URI, rather to the industry term "hotspot 2.0".

## 1.1 Objective

A 'HS20 profile provisioning' URI identifies resource keys and values that correspond to the Wi-Fi Alliance Passpoint PerProviderSubscription Management Object (PPS-MO) [PASSPOINT].

For a device to use Hostpsot2.0 based authentication, the device requires a valid credential to be configured together with an associated PPS-MO. Historically, Wi-Fi Alliance has specified a number of options for independently configuring the PPS-MO, including OMA Device Management and SOAP XML options. These protocols have seen un-even adoption across the device ecosystem and their use has since been deprecated.

This scheme is specified to enable embedded PPS-MO attributes to be included in a URI.  The intention is to re-use the existing subjectAltName URI type definition in [RFC5280] to allow inclusion of the HS20 Profile Provisioning URI in an x.509v3 certificate that has been issued to a device, simplifying the provisioning of Hotspot2.0 based configuration for EAP-TLS credentials.

# 2.	URI Registration Information

Following guidelines defined in RFC 7595.

|   |   |
| --- | --- |
| Scheme name	|	hs20 |
| Status	| provisional |
| Application/protocols that use this scheme	|	URI Type SubjectAltName extension |
| Contact	|	Bruno Tomas, bruno@wballiance.com |
| Change Controller	|	WBA OpenRoaming Work Group |
| References		|	This markdown document represents the most recent draft of the URI syntax. |

# 3.	hs20 URI Scheme Syntax

This document uses the Augmented Backus-Naur Form (ABNF) of [RFC5234].

```
;-------------------------------------------------------------------------------
; URI based Hotspot2.0 Profile Provisioning
;
; Wireless Broadband Alliance Inc.
; 2024-05-28
;-------------------------------------------------------------------------------

hs20uri  = "hs20://" authority "/" [ keyValues ]

keyValues     = keyValuePair *( ";" keyValuePair )

keyValuePair  =  "home_ois" "=" OI *[ "%2C" OI] 
keyValuePair  =/ "required_home_ois" "=" OI *[ "%2C" OI]              
keyValuePair  =/ "roaming_consortiums" "=" OI *[ "%2C" OI]

keyValuePair  =/ "domain" "=" FQDN *[ "%2C" FQDN]
keyValuePair  =/ "domain_suffix_match" "=" FQDN *[ "%2C" FQDN]
keyValuePair  =/ "roaming_partner" "=" FQDN "%2C" MATCH "%2C" PRIORITY 
                                       "%2C" COUNTRYCODE

keyValuePair  =/ "excluded_ssid" "=" SSID *[ "%2C" SSID]

keyValuePair  =/ "priority" "=" PRIORITY
keyValuePair  =/ "ocsp" "=" STAPLING
keyValuePair  =/ "realm" "=" FQDN
keyValuePair  =/ "username" "=" pctencoded-username


;-------------------------------------------------------------------------------
; Definitions
;-------------------------------------------------------------------------------

authority               = ALPHA *( ALPHA / DIGIT / "-" / "." )
DIGIT                   = %x30-39 ; 0-9
NZDIGIT                 = %x31-39 ; 1-9
U4DIGIT                 = %x30-34 ; 0-4
U5DIGIT                 = %x30-35 ; 0-5
ALPHA                   = %x41-5A / %x61-7A
unreservedAndPctEncoded = unreserved / pct-encoded 
unreserved              = ALPHA / DIGIT / "-" / "." / "_" / "~"
pct-encoded             = "%" UPPERHEXDIG UPPERHEXDIG
UPPERHEXDIG             = DIGIT / "A" / "B" / "C" / "D" / "E" / "F"
LOWERHEXDIG             = DIGIT / "a" / "b" / "c" / "d" / "e" / "f"
OI                      = 6*10LOWERHEXDIG
MATCH                   = "0" / "1"
                        ; 0 exact match.
                        ; 1 include subdomains.

PRIORITY                = DIGIT / (NZDIGIT DIGIT) / ("1" DIGIT DIGIT) / 
                          ("2" U4DIGIT DIGIT) / ("2" U5DIGIT U5DIGIT)
                        ; 0...255

COUNTRYCODE             = 2*ALPHA / "%2A" 
                        ; where %2A is the percent encoding of "*"
FQDN                    = 1*( label "." ) label
pctencoded-username	    = pctencodedstring *("." pctencodedstring)
pctencodedstring        = 1*pctencdedutf8-atext
pctencdedutf8-atext	    = ALPHA/ DIGIT /
                          UTF8-xtra-char /
                          "%21" / "%23" / "%24" / "%25" /
                          "%26" / "%27" / "%2A" / "%2B" /
                          "%2D" / "%2F" / "%3D" / "%3F" /
                          "%5E" / "%5F" / "%60" / "%7B" /
                          "%7C" / "%7D" / "%7E" 
SSIDCHAR                = unreservedAndPctEncoded
SSID                    = 0*32SSIDCHAR

STAPLING		    = "0" / "1" / "2"
                        ; 0 do not use OCSP stapling.
                        ; 1 try to use OCSP stapling.
                        ; 2 require valid OCSP stapling response.

; start definition from RFC 7542
label                   = utf8-rtext *(ldh-str)
ldh-str                 = *( utf8-rtext / "-" ) utf8-rtext
utf8-rtext              = ALPHA / DIGIT / UTF8-xtra-char
UTF8-xtra-char          = UTF8-2 / UTF8-3 / UTF8-4
UTF8-2                  = %xC2-DF UTF8-tail
UTF8-3                  = %xE0 %xA0-BF UTF8-tail /
                          %xE1-EC 2( UTF8-tail ) /
                          %xED %x80-9F UTF8-tail /
                          %xEE-EF 2( UTF8-tail )
UTF8-4                  = %xF0 %x90-BF 2( UTF8-tail ) /
                          %xF1-F3 3( UTF8-tail ) /
                          %xF4 %x80-8F 2( UTF8-tail )
UTF8-tail               = %x80-BF 
; end definition from RFC 7542
 
; NOTE – standardized definitions above will be updated to be inherited from 
; IETF RFCs, but are included in this draft to assist with ABNF checking

;-------------------------------------------------------------------------------
; Descriptions of key-Value Pairs
;-------------------------------------------------------------------------------

;-------------------------------------------------------------------------------
; OI Policy Description
;-------------------------------------------------------------------------------

; 1) value of home_ois key corresponds to one or more percent encoded, comma 
; (",") delimited Organizational Identifiers (OIs) identifying the access 
; networks that support authentication with this certificate. 
; 2) value of required_home_ois key corresponds to one or more percent encoded, 
; comma (",") delimited OIs that are required to be advertised by an AP for the 
; credential to be considered matching.
; 3) value of roaming_consortiums key corresponds to one or more percent 
; encoded, comma (",") delimited OIs identifying the roaming consortiums of 
; which the provider of the certificate credential is a member. The list is 
; sorted from the most preferred one to the least preferred one.

;-------------------------------------------------------------------------------
; FQDN Policy Description
;-------------------------------------------------------------------------------

; 1) value of domain key corresponds to one or more percent encoded, comma 
; (",") delimited FQDNs that are compared against the Domain Name List 
; advertised by an AP used to indicate that the AP is operated by the Home SP.
; 2) value of domain_suffix_match key corresponds to the FQDN used as a suffix 
; match against the AAA server certificate.
; 3) value of roaming_partner key encodes a concatenation of FQDN, MATCH 
; PRIORITY and COUNTRYCODE, separated by percent encoded comma(",").

;-------------------------------------------------------------------------------
; SSID Policy Description
;-------------------------------------------------------------------------------

; 1) value of excluded_ssid key corresponds to one or more percent encoded, 
; comma (",") delimited SSIDs that are used to exclude specific SSIDs from 
; matching with the network. 

;-------------------------------------------------------------------------------
; Credential Policy Description
;-------------------------------------------------------------------------------

; 1) value of priority key corresponds to the priority of the certificate 
; credential, i.e., PRIORITY. The higher the priority, the more preferred the 
; credential.
; 2) value of ocsp key corresponds the OCSP stapling policy.
; 3) the value of realm key encodes the realm associated with the credential.
; 3) the value of username key encodes the username to be used with the 
; credential.
```

# 4.	Scheme Semantics

A keyValuePair in the hs20 profile provisioning URI identifies the resource in the PPS management object and its value.  

The URI defined provisioning is intended to be used with a device certificate credential. The URIs are encoded in the Subject Alt Name using the URI type as defined in [RFC5820] and are associated with the certificate-based credential. 

The HS20 profile provisioning defines static data where the validity of the profile parameters is linked to the validity of the certificate. Profile parameters can only be updated by the re-issuance of an end-entity certificate.

# 5.	Encoding considerations

The ABNF of the URI scheme syntax in section 3 reuses the pct-coded in section 3.1 of [RFC3986].

## 5.1	Example encoding

### 5.1.1	Example from Passpoint Specification

The Passpoint specification [PASSPOINT] includes an example of the PPS-MO Provisioned Data Set which is used for example encoding.

| Home SP Child Node	|  Data |
| -----| -----| 
|FQDN	|sp-blue.com|
|HomeOIList/x1/HomeOI	|001d2e|
|HomeOIList/x1/HomeOIRequired	|FALSE|
|OtherHomePartners/f1/FQDN	|example.com|
|RoamingConsortiumOI	|001bc50050, 001bc500b5|

|Policy Child Node	| Data|
|---|----|
|PreferredRoamingPartnerList/x1/FQDN_Match	|sp-blue.com,exactMatch|
|PreferredRoamingPartnerList/x1/Priority	|10|
|PreferredRoamingPartnerList/x1/Country	|*\|
|PreferredRoamingPartnerList/x2/FQDN_Match	|sp-green.com,includeSubdomains|
|PreferredRoamingPartnerList/x2/Priority	|140|
|PreferredRoamingPartnerList/x2/Country|	*|
|PreferredRoamingPartnerList/x3/FQDN_Match	|sp-orange.com,exactMatch|
|PreferredRoamingPartnerList/x3/Priority	|5|
|PreferredRoamingPartnerList/x3/Country	|*|

Corresponding URI based provisioning:

```
URI=hs20://sp-blue.com/domain=sp-blue.com%2Cexample.com;home_ois=001d2e;roaming_consortiums=001b50050%2C001bc500b5;roaming_partner=sp-blue.com%2C0%2C10%2C%2A;roaming_partner=sp-green.com%2C1%2C140%2C%2A;roaming_partner=sp-orange.com%2C0%2C5%2C%2A
```
### 5.1.2	IEEE 802.1AR Initial Dev-ID Example

A certificate of an initial device ID (IDevID) certificate can be used with the following URIs to enable the automated provisioning of realm and RCOI information to allow the device to leverage the OpenRoaming federation [OPENROAMING] together with an EAP-TLS exchange defined in IEEE 802.1AR to authenticate onto third party networks, e.g.,  to then enable the provisioning of a locally significant device ID certificate, or other any other type of OpenRoaming credential.

```
URI=hs20://arbutus-widgets.com/roaming_consortiums=5a03ba0000;realm=onboarding-service.arbutus-widgets.com;ocsp=1
```

### 5.1.3	IEEE 802.1AR Locally Significant Dev-ID Example

A certificate of a locally significant device ID (LDevID) certificate can be used with the following URI to enable the automated provisioning of realm and RCOI information to allow the device to leverage the OpenRoaming federation together with an EAP-TLS exchange defined in IEEE 802.1AR to authenticate onto third party networks.

```
URI=hs20://enterprise.com/ roaming_consortiums=5a03ba0000%2C5a03ba0200;realm=aaa.enterprise.com;ocsp=1![image](https://github.com/mark-grayson/rpi4/assets/25058707/b39ca0dd-79ee-48a8-bea9-dadd254749c6)
```

# 6.	Interoperability considerations

This initial proposal is being used to submit the URI definition for allocation of a provisional URI scheme, rather than a permanent one because i) there are very limited numbers of implementations at this time, and ii) we expect the specification to be refined over the next year following feedback from implementors.

# 7.	Security considerations

The HS20 URI is expected to be encoded in the Subject Alt Name URI Type. The URI type Subject Alt Name will be signed by the certificate issuer and protected from modification.

The contents of the HS20 URI will be visible to a party relying on the security and authenticity of the device’s certificate. For a relying party (RP) to receive the device’s certificate, it MUST have presented a server certificate to the device that is rooted to a trust anchor configured on the device. If the EAP-TLS exchange uses TLS1.2, the HS20 URI will be visible to the Relying Party plus the Wi-Fi access network provider (ANP) onto which the Hotspot2.0 device is attempting to access. If the EAP-TLS exchange uses TLS1.3, the HS20 URI will only be visible to the relying party. 

The RP/ANP with visibility to the Subject Alt Name will be able to recover the policy encoded in the HS20 URI, which may include the priority of particular FQDN matches and/or specific SSIDs that have been configured to be excluded, as well as the Home SP. The RP/ANP will be able to recover the home SP configuration information, which may include the home OI and/or the FQDN of the home SP and/or the RCOIs configured in the profile. 

One of the primary use cases of the HS20 URI is to facilitate the provisioning of Hotspot2.0 credentials to be used with the OpenRoaming federation [OPENROAMIMG]. In such a scenario, the URI is used to encode the well-known OpenRoaming RCOI, together with username and realm elements, as described in section 5.1.3. The username and realm values are already visible to the RP/ANP in the User-Name attribute used in the RADIUS exchange. The OpenRoaming RCOI will have been configured on the ANP’s Wi-Fi infrastructure to trigger the Passpoint authentication exchange and may also be visible to the RP in the WFA defined “HS2.0 roaming consortium” RADIUS vendor specific attribute [PASSPOINT]. In such a use case, there is no additional Passpoint profile information exposed using the URI not already known to the RP/ANP.

# References

* [OPENROAMING] "WBA OpenRoaming Wireless Federation", Work in Progress, Internet-Draft, draft-tomas-openroaming-02, 16 February 2024, https://datatracker.ietf.org/doc/html/draft-tomas-openroaming-02
* [PASSPOINT] "Passpoint® Specification", Version 3.2.23, Wi-Fi Alliance, https://www.wi-fi.org/file/passpoint-specification-package 
* [RFC3986] "Uniform Resource Identifier (URI): Generic Syntax", https://datatracker.ietf.org/doc/html/rfc3986
* [RFC5280] "Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile", https://datatracker.ietf.org/doc/html/rfc5280 
* [RCC5378] "Rights Contributors Provide to the IETF Trust", https://www.rfc-editor.org/rfc/rfc5378 

