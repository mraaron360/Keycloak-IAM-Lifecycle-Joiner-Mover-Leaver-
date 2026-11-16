# Keycloak-IAM-Lifecycle-Joiner-Mover-Leaver-
Build a complete **Identity and Access Management (IAM)** lifecycle using **Keycloak** running in a **Docker container**, simulating the **Joiner → Mover → Leaver (JML)*
# Keycloak IAM Lifecycle: Joiner-Mover-Leaver (ByteKage Labs)

## Objective

Build an end-to-end Identity and Access Management (IAM) lifecycle using **Keycloak** running in a **Docker** container, simulating the **Joiner → Mover → Leaver (JML)** process with:

- Role-Based Access Control (RBAC)
- Multi-Factor Authentication (MFA)
- Single Sign-On (SSO) using OpenID Connect (OIDC)
- Event auditing and logging

---

## Lab Environment

- **Host:** macOS running Docker Desktop 4.x
- **Identity platform:** Keycloak 24.0 (containerized)
- **Browser:** Chrome, used for the Admin and User consoles
- **Local folder:** `~/Desktop/ByteKage Labs/JML-Keycloak/` for artifacts and logs

---

## Implementation Steps

### 1. Set up Keycloak in Docker

Created a working folder (`ByteKage Labs/JML-Keycloak`) and started Keycloak with:

```bash
docker run --name keycloak-jml -p 8080:8080 \
  -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=<YourStrongPassword> \
  -v "$HOME/Desktop/ByteKage Labs/JML-Keycloak":/artifacts \
  quay.io/keycloak/keycloak:24.0 start-dev
```

The admin console is available at [http://localhost:8080](http://localhost:8080).

### 2. Create a realm (tenant)

- Added a new realm named **ByteKageLab-Realm** to isolate users, roles, and applications.
- The realm acts as a secure tenant boundary for identity management.

### 3. Configure roles and groups (RBAC)

- Created roles: `Manager` and `Employee`
- Created groups: `HelpDesk` and `HR`
- Mapped groups to roles:
  - `HelpDesk` → `Manager`
  - `HR` → `Employee`

### 4. Create the Joiner (new user)

- Added **Barry Allen** as a new user.
- Assigned him to the `HelpDesk` group (Manager privileges).
- Required MFA setup and a forced password change on first login.

### 5. Enable MFA (TOTP)

- Configured the realm's OTP policy.
- On first login, Barry was prompted to scan a QR code with an authenticator app.
- MFA enforces strong authentication in line with Zero Trust principles.

### 6. Configure Single Sign-On (OIDC)

Created an OIDC client (`bytekage-app`) for SSO testing with the following settings:

| Setting | Value |
|---------|-------|
| Root URL | `http://localhost:8080` |
| Redirect URIs | `http://localhost:8080/*` |

Confirmed a successful login and that the application appeared in the user console.

### 7. Troubleshoot the "Page Not Found" error

- The initial SSO test failed because of an incorrect Root URL.
- Updated the Root URL and Redirect URIs.
- Validated that login redirection worked afterward.

### 8. Simulate the Mover (department transfer)

- Moved Barry from `HelpDesk` to `HR`.
- Verified that his role changed automatically from **Manager** to **Employee**.
- This confirmed that access updates dynamically during internal transfers.

### 9. Simulate the Leaver (offboarding)

- Disabled Barry's account in the user settings.
- Attempted to log in and was denied access, as expected.
- Later login failures during testing were caused by the account still being disabled, which confirmed the control was working. The account was re-enabled to continue testing.

### 10. Enable audit and event logging

Under **Realm Settings → Events**, enabled:

- `Save Events`
- `Admin Events`
- `Include Representation`

This captured both user and admin events for compliance and traceability.

---

## Troubleshooting Notes

| Issue | Cause | Fix |
|-------|-------|-----|
| SSO "Page Not Found" error | Incorrect Root URL / Redirect URI | Updated both, with Redirect URIs set to `http://localhost:8080/*` |
| Barry login failed | Account was disabled after the Leaver test | Re-enabled the account for testing |
| Logs missing | Events were not saved in the realm configuration | Enabled both User and Admin Events |

---

## Key Concepts

| Term | Definition |
|------|------------|
| **Keycloak** | Open-source IAM platform for SSO, MFA, and RBAC |
| **Docker** | Containerization tool used to isolate the Keycloak environment |
| **JML Lifecycle** | Joiner-Mover-Leaver: a framework for managing user identities |
| **OIDC (OpenID Connect)** | Protocol for federated identity and SSO |
| **RBAC** | Role-Based Access Control: access is granted based on roles |
| **MFA (TOTP)** | Adds a second authentication factor for stronger security |

---

## Value to an Organization

- **Lifecycle automation:** Demonstrates end-to-end user lifecycle control (Joiner → Mover → Leaver).
- **Strong authentication:** MFA and password rotation simulate real-world Zero Trust enforcement.
- **Governance readiness:** Includes auditing, group-based roles, and compliance logging.
- **Troubleshooting insight:** Shows how to identify misconfigurations and remediate them.

---

## Why JML Matters in the Enterprise

- **Joiner:** New hires are onboarded securely with the correct permissions.
- **Mover:** Prevents *privilege creep* when users change departments.
- **Leaver:** Revokes access promptly, reducing insider threat risk.

**When done well:**

- Access adjusts automatically through groups and policies.
- Compliance and audit readiness are built in.
- Human error and manual misconfiguration are minimized.

**When done poorly:**

- Dormant accounts become breach vectors.
- Stale permissions violate least privilege.
- Missing logs cause audit failures.

---

## Screenshots

All project screenshots, including realm creation, RBAC setup, MFA enforcement, SSO configuration, and audit logging, are available here:

<img width="1478" height="692" alt="05_part2mfarequiredscreenshot" src="https://github.com/user-attachments/assets/0602e837-f2a9-4aa8-b126-bd7a5c0beb09" />
<img width="1087" height="463" alt="04_part 2 events _enabled (1)" src="https://github.com/user-attachments/assets/b534c0d3-9043-403b-8e20-2eb6d1e2e15a" />
<img width="1087" height="463" alt="04_part 2 events _enabled" src="https://github.com/user-attachments/assets/6bf71c07-c789-4ac8-882c-1b788daec7f0" />
<img width="1098" height="385" alt="04_events_enabled" src="https://github.com/user-attachments/assets/c16eccd1-b191-4c31-b3ae-32f65d9df08e" />
<img width="1122" height="450" alt="03_part 2 b allen group hr" src="https://github.com/user-attachments/assets/9b49bcec-7e47-4b08-94ad-3ce769ae0182" />
<img width="1399" height="497" alt="02_Roles_" src="https://github.com/user-attachments/assets/e5492257-72e3-4520-9a61-abf42c00d017" />
<img width="1393" height="474" alt="02_GROUPS" src="https://github.com/user-attachments/assets/32c17da6-c1c1-4904-a95a-a690b3c6ad2b" />
<img width="1382" height="665" alt="01_realm_created" src="https://github.com/user-attachments/assets/a3f02d06-0321-46f6-b8cb-dd118bc8e2c8" />


To follow the lifecycle visually, open the images in order. They cover:

1. Realm and role setup
2. User creation and MFA
3. SSO configuration
4. Account deactivation and auditing

---

## Lessons Learned

- IAM is more than logins; it is **identity governance**.
- Proper JML automation prevents human error and privilege creep.
- Misconfigurations such as a bad redirect URI are realistic enterprise problems.
- Full auditing provides visibility, compliance, and control.

> "Access should evolve with the user, never stay behind with their old role."
