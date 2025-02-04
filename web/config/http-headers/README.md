# HTTP security headers

## Theory

HTTP security headers are used to inform a client (browser) how to behave when handling a website's content. These headers are important in preventing exploitation of vulnerabilities such as XSS, Man-in-the-Middle, [clickjacking](https://owasp.org/www-community/attacks/Clickjacking), etc.

### STS ([`Strict-Transport-Security`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security))

Lets a web site tell browsers that it should only be accessed using HTTPS, instead of using HTTP

* The `max-age` directive defines the time, in seconds, that the browser should remember that a site is only to be accessed using HTTPS.
* The optional `includeSubDomains` directive defines if the rule applies to all of the site's subdomains as well

### XFO ([`X-Frame-Options`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options))

Indicates whether or not a browser should be allowed to render a page in a [`<frame>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/frame), [`<iframe>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe), [`<embed>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/embed) or [`<object>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/object). Prevents [clickjacking attacks](clickjacking.md)

* `DENY`: the page cannot be displayed in a frame
* `SAMEORIGIN`: can only be displayed in a frame on the same origin as the page itself (which depends on how browsers vendors interpret this)
* `ALLOW-FROM uri`: obsolete directive. No longer works in modern browsers.

### XCTO ([`X-Content-Type-Options`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options))&#x20;

Used by the server to indicate that the [MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics\_of\_HTTP/MIME\_types) advertised in the [`Content-Type`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) headers should be followed and not be changed. Prevents [MIME type sniffing](mime-sniffing.md) attacks

* The `nosniff` directive make the browser block a request if the request destination is of type `style` and the MIME type is not `text/css`, or of type `script` and the MIME type is not a [JavaScript MIME type](https://html.spec.whatwg.org/multipage/scripting.html#javascript-mime-type).  

### CORS ([`Cross-Origin-Resource-Sharing`](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS))&#x20;

Allows a server to indicate any [origins](https://developer.mozilla.org/en-US/docs/Glossary/Origin) (domain, scheme, or port) other than its own a browser should permit loading resources** **from.

{% content-ref url="cors-cross-origin-resource-sharing.md" %}
[cors-cross-origin-resource-sharing.md](cors-cross-origin-resource-sharing.md)
{% endcontent-ref %}

### CSP ([`Content-Security-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP))&#x20;

Restrict how the browser accesses resources. Mitigates XSS, XS-Leaks, [clickjacking](clickjacking.md)

* The `default-src` directive acts as a [fallback](https://content-security-policy.com/default-src/) for the other CSP fetch directives. If not present, CSP will permit loading resources of any origins
* The `unsafe-eval`  directive allows unsafe evaluation code such as `eval()` for JavaScript. 
* The `unsafe-inline`  directive allows execution of third-party JavaScript inline.

### XXP ([`X-XSS-Protection`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection))

Header now deprecated, only old browsers may use it. It's [debated](https://github.com/OWASP/CheatSheetSeries/issues/376) whether it's actually safe or not to use it. More harm can be done using X-XSS-Protection. Other methods can be used to prevent XSS attacks (escaping, sanitization...).&#x20;

X-XSS-Protection should not be used, or set to 0. 

## Practice

Presence and configuration of the security headers mentioned above should be checked. This can be done by inspecting HTTP responses with a proxy, with a browser, or by manually doing the request and analyzing the response.

```bash
curl --head $URL
```

In addition to that, scanner such as [CORScanner](https://github.com/chenjj/CORScanner) (Python) and [CSP-evaluator](https://csp-evaluator.withgoogle.com) (online) can quickly help inspect the state of CORS and CSP and identity potential dangerous configurations.

The [securityheaders](https://github.com/koenbuyens/securityheaders#http-strict-transport-security) (Python) script and [this online scanner](https://securityheaders.com) can also be used for that purpose.
