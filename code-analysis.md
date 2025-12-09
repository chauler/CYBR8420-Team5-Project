# Part 1

## Code-review strategy
As a team, we decided to use a hybrid approach to our code review of the ladybird browser codebase. The ladybird browser is a fairly large and complex codebase to analyze. Each of us took the misuse and assurance cases that we created in previous assignments and focused on the code that provided those capabilities. We are doing a hybrid of scenario based and weakness based code review. 

The code being C++ was going to add some complexity for automated scanning tools to perform a static analysis of the code because the tools would need to compile the code. We addressed these challenges by each team member selecting a different scanning tool to attempt. Between the five team members, we figured we should be able to get at least one or two automated code scanning tool results. We were able to successfully use Checkov and clang-analyzer, but were not able to get SonarQube to work. We also performed manual code reviews to bridge the gap of static analysis for the automated tools and in total put together a list of CWEs most relevant to our assurance cases.  

## CWE Analysis

## Summary of the manual code reviews that were performed
### CWE-201: Insertion of Sensitive Information Into Sent Data
<b>Abstraction:</b> Class

The code transmits data to another actor, but a portion of the data includes sensitive information that should not be accessible to that actor.

<b>What was reviewed:</b>

Searched the codebase for locations that implement outgoing request construction and cookie access. This identified places in the code that could lead to "Insertion of Sensitive Information Into Sent Data" (CWE-201).

>b>Key findings:</b> 

1.	In Libraries/LibWeb/Fetch/Fetching/Fetching.cpp cookies are explicitly appended to outgoing HTTP requests. If page_did_request_cookie is implemented incorrectly, it could include httpOnly cookies in contexts where they should not be exposed, for example JavaScript or it could return the cookie to the wrong origin which could leak sensitive information. 
2.	In Libraries/LibWeb/Fetch/Fetching/Fetching.cpp if the serialization of the referrer url contains sensitive information, for example query parameters with PII or authentication tokens in the URL, those could be sent to a third party in the Referer header.
3.	In Libraries/LibWeb/Fetch/Fetching/Fetching.cpp there is an unimplemented username/password prompt which creates a potential credential-flow risk. The code currently uses empty credentials and logs that prompting isn't implemented. When a prompt implementation is added later, ensure credential prompting does not accidentally auto-fill or send stored credentials into requests to unintended origins.
4.	 In Libraries/LibWeb/CookieStore/CookieStore.cpp httpOnly cookies are not provided via the VERIFY API. If an upstream cookie store accidentally returns httpOnly cookies, the VERIFY API could crash in debug or be bypassed which would expose cookies to non-HTTP APIs. 


### CWE-862: Missing Authorization
<b>Abstraction:</b> Class

The product does not perform an authorization check when an actor attempts to access a resource or perform an action.

<b>What was reviewed:</b>

Searched the codebase for places where privileged data or actions are exposed by IPC/callbacks, virtual PageClient methods, or FIXME'd navigation/security checks. These can lead to “Missing Authorization” (CWE-862)

<b>Key findings:</b>

1.	In Libraries/LibWeb/HTML/WorkerAgentParent.cpp a worker IPC callback such as when a potentially cross-origin worker asks for a cookie, the request if forwarded to the PageClient without an explicit origin or authorization checks. If the PageClient does not perform strict checks, a worker could obtain a cookie that it should not have access to. 
2.	In Services/WebWorker/PageHost.cpp the WebWorker PageHost forwards cookie requests directly to the client. Cookie requests are delegated directly to m_client.did_request_cookie with no visible check for worker identity, principal, or origin. If did_request_cookie trusts the caller context without verifying the worker's origin/principal, sensitive cookies could be leaked to worker contexts that are not authorized to read them.
3.	In Libraries/LibWeb/CookieStore/CookieStore.cpp the CookieStore relies on the PageClient to filter sensitive cookies. If PageClient inadvertently returns httpOnly cookies, or cookies it shouldn't, VERIFY may crash  and expose httpOnly cookies to JavaScript APIs.
4.	In Libraries/LibWeb/Page/Page.h the PageClient exposes virtual hooks for storing set/get values which embedders implement. The default implementation are no-op placeholders. If an embedder's override does not enforce origin/permission checks, or if some code calls these methods expecting them to perform checks, stored data could be read or written by code that lacks authorization.

### CWE-488: Exposure of Data Element to Wrong Session
<b>Abstraction:</b> Base

The product does not sufficiently enforce boundaries between the states of different sessions, causing data to be provided to, or used by, the wrong session.
Data can "bleed" from one session to another through member variables of singleton objects, such as Servlets, and objects from a shared pool.

<b>What was reviewed:</b>

Searched the codebase for places where critical variables or state (origins, principals, reserved clients, credentials, cookies, environment IDs) are assigned or overwritten from untrusted inputs or without proper checks. This can lead to “Exposure of Data Element to Wrong Session (CWE-488)

<b>Key findings</b>

1.	In Libraries/LibWeb/HTML/Scripting/WorkerEnvironmentSettingsObject.cpp the worker environment uses serialized outside settings to set top-level origin. The worker settings object receives top_level_origin from outside_settings which is a SerializedEnvironmentSettingsObject. This is serialized data. If that serialized data comes from an untrusted or malicious source, the worker's top-level origin, which is a critical security identifier used in trust decisions, can be overwritten. 
2.	In Libraries/ LibWeb/HTML/Scripting/ WindowsEnvironmentSettingsObject.cpp the windows environment creation_url and top-level origin are set from caller parameters. The creation_url and top_level_origin are key environment attributes used for same-origin decisions and secure-context determinations. They are taken directly from the caller's parameters. If an attacker can cause these parameters to be supplied with malicious values (e.g., via crafted IPC or API misuse), critical environment identity can be overwritten, undermining origin-based protections. 
3.	In Libraries/LibWeb/Fetch/Request.cpp the request constructor accepts origin and referrer requests from input requests. The Request object’s origin and referrer are assigned from input_request. Those fields affect CORS, service worker handling, and header generation. If input_request originates from untrusted sources or is constructed incorrectly, a request can carry a spoofed origin/referrer. If Spoofed origins/referrers are enable and can bypass same-origin checks, incorrect service worker routing, or leaking/accepting privileged behavior could occur.

### CWE-922: Insecure Storage of Sensitive Information
<b>Abstraction:</b> Class
The product stores sensitive information without properly limiting read or write access by unauthorized actors.
If read access is not properly restricted, then attackers can steal the sensitive information. If write access is not properly restricted, then attackers can modify and possibly delete the data, causing incorrect results and possibly a denial of service.

<b>What was reviewed:</b>

Search the codebase for places where the engine persists or exposes sensitive browser data (cookies, localStorage, other storage). Focused on CookieJar / StorageJar / persistence code paths and collected the most security-sensitive places that read/write persisted storage or expose persisted data to APIs. This could lead to “Insure Storage of Sensitive Information” (CWE-922) 

<b>Key findings</b>

1.	In Libraries/LibWebView/CookieJar.cpp the CookieJar loads persisted cookies and starts a periodic synchronization timer. All cookies are loaded eagerly into process memory. (There is a FIXME in the code to address this), which increases the attack surface.
2.	In Libraries/LibWebView/CookieJar.cpp persisted storage has direct access to Database and insert/select operations. Persisted storage provides direct database access. How the DB is created/opened and what permissions are applied will determine sensitivity of stored cookies. If the database is created in a location accessible by other users or processes, cookies may be exfiltrated from disk. If the database insertion code doesn't correctly enforce sameSite/secure/httpOnly semantics before writing, sensitive cookies could be persisted inadvertently.
3.	In Libraries/LibWebView/StorageJar.cpp the StorageJar persists web storage (localStorage-like APIs) and exposes set/get item paths. Any sensitive user data saved via localStorage/sessionStorage will be persisted. There is no evidence here of encryption or access controls at the storage layer. LocalStorage can be abused to persist tokens or PII and survive process restarts. If storage persists to a shared DB file with lax permissions, another user or process could read it.

### CWE-257: Storing Password in a Recoverable Format
<b>Abstraction:</b> Base
The storage of passwords in a recoverable format makes them subject to password reuse attacks by malicious users. In fact, it should be noted that recoverable encrypted passwords provide no significant benefit over plaintext passwords since they are subject not only to reuse by malicious attackers but also by malicious insiders. If a system administrator can recover a password directly, or use a brute force search on the available information, the administrator can use the password on other accounts.

<b>What was reviewed:</b>

Search the codebase for hardcoded passwords (SWE-257), API keys, default secrets, plaintext storage of credentials, 'password', 'secret', 'token', 'api_key', 'Authorization', 'basic', 'credentials', 'key', 'private', 'TODO: store', 'FIXME: password'

<b>Key findings</b>

1.	In Libraries/LibWeb/CredentialManagement/PasswordCredentials.h  PasswordCredential stores password as plain String (TODO to use secret type). Passwords are held in a normal String (m_password) and exposed via password() method that returns a String constant. The TODO explicitly notes the intent to switch to a secret container, but it is not yet done. Secrets in regular String objects are often immutable buffers that may be copied, swapped to disk via core dumps, swapped to swapfile, or logged unintentionally. 
2.	In Tests/LibWeb/Text/expected/Fetch/fetch-requst-url-search-params.txt there is an exposed plain text username and password.
3.	In Libraries/LibWeb/Cypto/Cryptokey.cpp the CryptoKey serialization includes "for_storage" path. In this scenario, private key material may be serialized. The code supports structured serialization with a for_storage flag that implies keys could be serialized for persistent storage which can possibly include private key material Private keys and secret symmetric keys, if serialized to storage as plaintext or in an extractable form, are secrets.

### CWE-940: Improper Verification of Source of a Communication Channel
<b>Abstraction:</b> Base
The product establishes a communication channel to handle an incoming request that has been initiated by an actor, but it does not properly verify that the request is coming from the expected origin.

<b>What was reviewed:</b>
Files related to encryption of network communications: LibTLS, LibCrypto/OpenSSL.cpp.

<b>Key findings</b>
Hostname verification is explicitly enabled in TLSv12.cpp. Handshake errors abort the connection, abandoning connections where verification fails.
OpenSSL cypher suites are not specified, instead relying on OpenSSL defaults. If the defaults are insecure, such as allowing aNULL cipher, this could lead to weakened security.

### CWE-605: Multiple Binds to the Same Port
<b>Abstraction:</b> Variant
When multiple sockets are allowed to bind to the same port, other services on that port may be stolen or spoofed.

<b>What was reviewed:</b>
Files for separate processes that may open sockets that could bind to the same port: Services/ImageDecoder, Services/WebContent, Services/WebDriver. Looking for socket creation and bind calls with SO_REUSEADDR options used.

<b>Key findings</b>
Went through all services that are separate processed that might open sockets. They all use an abstraction layer for socket creation (Core::Socket) that does not use the SO_REUSEADDR option when binding sockets.

### CWE-322: Key Exchange without Entity Authentication
<b>Abstraction:</b> Base
The product performs a key exchange with an actor without verifying the identity of that actor.

<b>What was reviewed:</b>
Checked that the TLS implementation verifies the server certificate against trusted root CAs. Also checked for proper hostname verification and minimum TLS version enforcement.

<b>Key findings</b>
Certificate trust is delegated to the OS certificate store. TLS 1.2 is the minimum version allowed. Hostnames are verified by default in the TLS handshake.

### CWE-347: Improper Verification of Cryptographic Signature
<b>Abstraction:</b> Base
The product does not verify, or incorrectly verifies, the cryptographic signature for data.

<b>What was reviewed:</b>
LibCrypto/Hash for signature verification functions, from the perspective of verifying hashes for script integrity. 

<b>Key findings</b>
The hash functions are in place (MD5, SHA1/2 files), but they are largely wrappers for OpenSSL functions. Relying on OpenSSL (a high-trust third party) for correct implementation.

### CWE-285: Improper Authorization
<b>Abstraction:</b> Class

The product does not perform or incorrectly performs an authorization check when an actor attempts to access a resource or perform an action.

<b>What was reviewed:</b>

Focused on the parts of the code and documentation that describe how RequestServer serves as a network broker for renderer-like processes: Documentation / architecture - Documentation/Browser/ProcessArchitecture.md (multi-process model; WebContent → RequestServer → network). Network broker implementation - Services/RequestServer/* (HTTP/HTTPS and WebSocket job creation, LibCurl integration, connection cache). Callers / clients - WebContent / WebView integration paths that initiate RequestServer jobs. 


<b>Key findings</b>

1.	In Services/RequestServer/ConnectionFromClient.cpp, RequestServer builds outgoing HTTP/WebSocket jobs on behalf of its clients (WebContent, workers, etc.) using parameters received over IPC. If the RequestServer side only validates request shape (URL, method, headers) but does not bind the request to the caller’s security context (origin, profile, permission set), then any renderer that can reach RequestServer could perform network actions it is not authorized for such as cross-profile requests, access to internal endpoints, or bypassing per-tab restrictions.
2.	The architecture document notes that each WebContent process gets its own RequestServer instance for uploads/downloads. If that one-to-one mapping is not strictly enforced or is later relaxed (for example, multiple WebContent clients sharing one RequestServer instance, or non-WebContent clients using the same IPC endpoint), authorization decisions may need extra per-client checks. Missing or inconsistent checks can become improper authorization issues.
3.	Any trusted capability exposed over its IPC API such as opening arbitrary TCP connections, following redirects, or sending non-HTTP traffic must be guarded by authorization logic. If future embedders add new IPC messages or privileged network features and forget to enforce “who is allowed to call this,” those become CWE-285 weaknesses even if the core RequestServer code is otherwise correct.
4.  Because RequestServer is a separate process, OS-level privileges and sandboxing may differ from WebContent. If RequestServer runs with broader filesystem or network permissions than WebContent, but the IPC layer treats all clients equally, this can allow less-privileged components to borrow RequestServer’s privileges unless the API performs explicit authorization for sensitive actions.

### CWE-441: Unintended Proxy or Intermediary (“Confused Deputy”)
<b>Abstraction:</b> Class

The product receives a request or directive from an upstream component but does not sufficiently preserve or enforce the original source when forwarding it to an external actor, causing the product to act as an unintended proxy or intermediary. 

<b>What was reviewed:</b>

Process architecture - The documentation that describes RequestServer as the process that actually “uses networking protocols (HTTP/HTTPS) to request files from the outside world” on behalf of WebContent. Request forwarding paths - Services/RequestServer/ConnectionFromClient.cpp for how incoming IPC requests are translated into LibCurl jobs and outbound connections. RequestServer connection caching and URL handling (ConnectionCache.cpp, AK::URL::serialized_host usage) as seen in upstream issue reports. 


<b>Key findings</b>

1.	RequestServer’s role matches the CWE-441 threat model: a component receives requests from less-privileged clients and forwards them to external network endpoints using its own network privileges. If RequestServer does not track the client who requested and does not enforce per-client policies, any client that can send IPC messages could potentially ask RequestServer to contact internal IPs/ports and abuse unusual schemes or protocols exposed via LibCurl backends such as FTP and custom schemes if those are enabled but not filtered by policy.
2.	CWE-441 examples often arise when a product forwards traffic on behalf of a client without enforcing its own access rules, effectively letting the client tunnel through firewalls or network boundaries. Because RequestServer runs as a separate process that may have different OS-level network visibility than the WebContent sandbox, it can become that “network deputy.” If WebContent can send arbitrary HTTP headers or arbitrary target hosts/ports through IPC, Ladybird risks becoming a generic network proxy.
3.	Even if Ladybird never exposes RequestServer directly to untrusted external clients, a compromised WebContent process could use the RequestServer API as a stepping stone to move laterally or exfiltrate data by having a more trusted process speak on its behalf.

### CWE-923: Improper Restriction of Communication Channel to Intended Endpoints
<b>Abstraction:</b> Class

The product establishes a communication channel to (or from) an endpoint for privileged or protected operations, but it does not properly ensure that it is communicating with the correct endpoint.  

<b>What was reviewed:</b>

Inter-process communication - The conceptual RequestServer IPC endpoints used by WebContent and potentially other clients. Backend network and DNS layers - Mention of the stub resolver (LibDNS::Resolver) and its integration with RequestServer/LibCurl in external discussions. The architecture doc’s discussion that RequestServer relies on a global LookupServer service for DNS queries. 


<b>Key findings</b>

1.	Multiple communication channels must be correctly “bound” to their intended endpoints: WebContent → RequestServer: The IPC channel should ensure that only the associated WebContent instance can issue network requests to its RequestServer. If other local processes can spoof this endpoint or connect to the same IPC interface, they could inherit the same network privileges, which maps directly to CWE-923. RequestServer → LookupServer/DNS: If RequestServer relies on a separate DNS resolver (LookupServer or LibDNS stub resolver) without verifying that it is the legitimate system resolver, malicious local processes could impersonate or MITM that resolver and control where outgoing connections go.
2.	For external network connections, RequestServer translates a URL and options into a LibCurl job. External references show that WebSocket and HTTP handling are implemented in single files such as ConnectionFromClient.cpp, which manage how endpoints and protocols are chosen. If RequestServer does not enforce constraints like: “Only connect to the hostname derived from the original URL and DNS resolution,” “Do not follow redirects to disallowed schemes/hosts,” or “Do not reuse connection state from one origin for another,” then it risks mis-binding communication channels and talking to unintended endpoints, which is the core of CWE-923.
3.	As the browser evolves by additions such as adding support for system proxies, HTTP/2/3, or custom backends, any new connection path must ensure that it is bound to the correct intended remote endpoint, and the IPC channel only allows authorized local clients to use that path. Otherwise, both local spoofing and remote redirection attacks may fall under CWE-923.


### CWE-770: Allocation of Resources Without Limits or Throttling
<b>Abstraction:</b> Class

The product allocates a reusable resource or group of resources on behalf of an actor without imposing limits on the size or number of resources that can be allocated. 

<b>What was reviewed:</b>

Runtime behavior under load, GitHub issue “Page with too many files” (#3185), where navigating to a Wikipedia page with many external resources causes RequestServer to log repeated errors: StartRequest: Failed to create pipe: pipe2: Too many open files (errno=24) while CPU spikes to 100%. Network job handling, Services/RequestServer/* which is responsible for creating per-request jobs and managing connections for each WebContent client. 


<b>Key findings</b>

1.	The “Too many open files (errno=24)” logs from RequestServer on a resource-heavy page show that, under stress, the process can hit OS-level descriptor limits just by handling a single page with many resources. This is strong evidence that RequestServer will eagerly allocate OS resources like pipes, connections, and file descriptors on behalf of content without effectively throttling or pooling them.
2.	From a CWE-770 perspective, this suggests the per-tab or per-origin limits on outstanding requests, connections, or open pipes may either not exist or not be tuned for worst-case pages. A malicious page instead of just a heavy Wikipedia page, could deliberately exploit this by issuing many parallel requests via JavaScript, <img> floods, or WebSockets, consuming all available descriptors in the RequestServer process and causing denial of service for other tabs.
3.	Because every tab’s networking goes through RequestServer, a resource exhaustion in that process can have browser-wide impact in that wew connections for other tabs may fail and existing connections might be dropped or starved if the process cannot allocate additional pipes or sockets.
4.  The stress-test issue also implies that logging “too many open files” is not sufficient mitigation: the underlying resource allocation still failed, and the browser needs work to gracefully degrade. Without defensive throttling and bounds, this is the kind of behavior CWE-770 describes.

### CWE-295: Improper Certificate Validation


<b>Abstraction:</b> Class


<b>Why this CWE applies:</b>


Certificate validation is central to the “Secure Web Connection” misuse case. If the certificate chain, trust anchors, or validation process is incomplete or incorrect, an attacker can impersonate a legitimate website and intercept secure communication.

<b>What was reviewed:</b>

I analyzed the TLS handshake and certificate validation logic in:
•	Libraries/LibTLS/Handshake.cpp
•	Libraries/LibTLS/Certificate.cpp
•	Certificate-to-URL matching flow via RequestServer
•	Trust store and chain verification behavior

<b>Key findings:</b>

1.	Certificate chain depth and trust-path construction are not clearly documented.
2.	It is unclear whether full X.509 path validation (issuer/subject matching, basic constraints, key usage) is strictly enforced.
3.	Certificate validation behavior appears partially delegated to platform trust store logic.
4.	Lack of clarity makes it difficult to confirm if all validation checks run consistently across environments.

### CWE-297: Improper Validation of Certificate with Host Mismatch

<b>Abstraction:</b> Class

<b>Why this CWE applies:</b>

A certificate must match the requested hostname (CN/SAN matching). Missing or incorrect hostname validation allows man-in-the-middle attackers to use valid certificates for the wrong domain.

<b>What was reviewed:</b>

•	Hostname extraction via Libraries/LibURL/*
•	TLS handshake code that should compare hostname with certificate SAN entries
•	Integration points where RequestServer initiates secure connections

<b>Key findings:</b>

1.	No clearly identifiable hostname-matching function in the TLS validation path.
2.	SAN/CN comparison logic is not prominently visible.
3.	If hostname validation is incomplete, an attacker could present a certificate for a different domain and still pass validation.
4.	This is one of the highest-risk gaps for HTTPS correctness.

### CWE-299: Improper Check for Certificate Revocation

<b>Abstraction:</b>  Class

<b>Why this CWE applies:</b>

Browsers must detect revoked certificates to block compromised sites. If revocation is not checked (via OCSP or CRL), a revoked certificate may be accepted as valid.

<b>What was reviewed:</b>

•	Libraries/LibTLS/* modules for OCSP/CRL logic
•	HSTS implementation in LibWeb
•	RequestServer secure connection paths

<b>Key findings:</b>

1.	No implementation for OCSP or CRL revocation checking.
2.	HSTS does not enforce certificate revocation.
3.	Browser currently relies on static trust store state, not dynamic revocation events.
4.	This creates a blind spot where compromised certificates remain trusted.

### CWE-326: Inadequate Encryption Strength

<b>Abstraction:</b> Class

<b>Why this CWE applies:</b>

HTTPS security depends on rejecting weak cipher suites and disabling outdated TLS versions. Lack of strict enforcement can allow downgrade attacks or insecure cryptographic primitives.

<b>What was reviewed:</b>

•	LibTLS/Handshake.cpp cipher negotiation
•	Protocol version handling
•	Crypto primitives and key sizes during TLS setup

<b>Key findings:</b>

1.	No visible mechanism enforcing minimum TLS version (e.g., TLS 1.2+).
2.	It is unclear whether weak ciphers (e.g., RC4, null, export-grade) are filtered out.
3.	Crypto library defaults may behave securely, but explicit hardening is not documented.
4.	This merits enhancement to avoid cryptographic downgrade paths.

### CWE-20: Improper Input Validation

<b>Abstraction:</b> Class

<b>Why this CWE applies:</b>

The browser accepts untrusted input through URLs, HTTP responses, TLS record parsing, and policy objects. Without strict validation, malformed inputs could bypass assumptions or cause memory issues.

<b>What was reviewed:</b>

•	Libraries/LibURL/Parser.cpp
•	HTTP header and body handling in RequestServer
•	TLS record parsing in LibTLS
•	HSTS and trust-store metadata processing

<b>Key findings:</b>

1.	URL parsing is robust but may need stricter checks for deeply nested or malformed URLs.
2.	HTTP header parsing should enforce strict size and format limits.
3.	TLS record parsing uses buffers vulnerable to misreads if bounds are not enforced.
4.	Trust-store inputs are accepted without deep validation, increasing risk if store integrity is compromised.

### CWE-119 / CWE-120: Buffer Overflow / Unsafe Buffer Handling

<b>Abstraction:</b> Base

<b>Why this CWE applies:</b>

C++ code handling TLS messages, records, and parsing is susceptible to buffer misuse, especially when static buffers or loops read untrusted data.

<b>What was reviewed:</b>

(Automated scan using Flawfinder)
•	Libraries/LibTLS/TLSv12.cpp
•	Libraries/LibURL/*
•	Services/RequestServer/*

<b>Key findings:</b>

1.	Static buffers are used for TLS record operations (possible overflow scenarios).
2.	Two loop-based reads lack explicit boundary validation.
3.	WebSocketCurl code also contains buffer access patterns needing additional size checks.
4.	All are low-to-medium severity but warrant manual review.

Summary of the automated code reviews that were performed

# Part 2
There is a lot of repeat in part 2 of what was asked for in part 1.  In part 1, we include a summary of the manual and automated code review findings. In part 2 we add in the perceived risk in our hypothetical operational environments (which I think is a professional workspace)
For the planned contribution to the open-sourced project, we can pick one of the vulnerabilities that the automated code scanning identified. 