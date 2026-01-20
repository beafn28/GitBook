# Stored Cross-Site Scripting (XSS) in admin/components.php

Product: GetSimple CMS

Affected Version: 3.4.0.9

Vulnerable Component: Sidebar – Edit Components

Vulnerability Type: Stored Cross-Site Scripting (XSS)

### Description

A **Stored Cross-Site Scripting (XSS)** vulnerability exists in **GetSimple CMS version 3.4.0.9**, within the `admin/components.php` file, specifically in the **Edit Components** functionality.

The application allows administrators to insert arbitrary HTML content into the _Sidebar_ component without proper input validation, sanitization, or output encoding. As a result, malicious JavaScript code can be persistently stored and later executed in the context of any user who visits the affected page.

Because the injected payload is saved and rendered automatically, the attack triggers every time the homepage is accessed.

### Steps to Reproduce

1. Log in to the GetSimple CMS admin panel.
2. Navigate to **Theme → Edit Components**.
3. Select the **Sidebar** component.
4. Insert the following payload:

```html
<script>alert("XSS")</script>
```

5. Save the changes.
6. Visit the homepage of the website.
7. The JavaScript payload executes automatically in the browser.

### Impact

An attacker exploiting this vulnerability may be able to:

* Execute arbitrary JavaScript in visitors’ browsers.
* Steal session cookies, including administrator sessions.
* Perform unauthorized actions on behalf of authenticated users.
* Inject malicious content or redirect users to external sites.
* Compromise the confidentiality, integrity, and trustworthiness of the website.

In environments where multiple users have access to the admin panel, the risk is significantly increased.

### Recommended Mitigation

* Properly **sanitize and validate user-supplied HTML content** before storage.
* Apply **output encoding** when rendering dynamic content.
* Use a **whitelist-based HTML sanitizer** (e.g., HTML Purifier).
* Explicitly disallow `<script>` tags and inline JavaScript in components.
* Enforce stricter permission controls for users allowed to edit components.

### Security Note

Allowing unsanitized script execution in CMS components effectively enables persistent client-side code execution, creating an ideal attack surface for session hijacking and privilege escalation.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

