# WSO QR

**Turning every Workspace ONE UEM chore into a self-service action.**

A self-hosted companion tool for Workspace ONE UEM: bulk QR-based Android Enterprise enrollment, day-2 device actions (Sync, Clear Passcode, Exit Kiosk, Assist), and self-service device migration between UEM consoles — all from one admin panel, with a self-service portal for the people actually holding the devices.

> **Note:** This repository hosts installer releases only. The source code is maintained in a private repository. See the [Releases](../../releases) tab for downloadable installer packages.

## Free QR generator — no install needed

**[Open the QR generator](https://franeksoftsf.github.io/WS1QRGen/)**

A single-page tool that builds Android Enterprise device-owner provisioning QR codes. Free, no
account, no install — and everything happens in your browser: nothing is uploaded, stored or
logged, and the page keeps working after you save it to disk.

- **Workspace ONE UEM** — server, Group ID and an optional staging account, or token-based
  registration where the token travels in the Group ID field
- **Enrollment URL shortcut** — paste a link carrying `serverurl` and `gid` from your
  console and the fields fill themselves
- **Android Management API** — from an enrollment token, or from a ready-made payload your console
  or the API handed you
- **WiFi left to the device** — by default the code carries no network settings, so the device shows
  its own picker and the WiFi password never ends up in the QR code. Embedding the network is still
  one dropdown away
- **Locale and time zone** from the full Android lists, with search
- **Save and reload your settings**, print an instruction sheet for whoever does the enrolling, and
  download the code as PNG

The tool covers a single device at a time. Bulk enrollment from CSV, day-2 actions and migration
between consoles are what the installable application below is for.

## What it does

- **QR provisioning** — generate Android Enterprise enrollment codes per device, from a single admin panel or bulk CSV import
- **Self-service portal (SSP)** — lets whoever is setting up a device pick their own group/WiFi and generate a QR code themselves, without an admin pre-creating a device record
- **Day-2 device actions** — Sync, Clear Passcode, Exit Kiosk, and Assist, right next to each device, no trip into the full UEM console needed
- **Device migration between UEM consoles** — assign a migration from the panel, the device gets wiped automatically, and the person holding it gets one self-service link: reset, scan, done. Includes a simulation mode for testing the flow without a real wipe
- **Automatic email notifications** for assigned migrations, in the right language, based on the device group's locale
- **Optional Active Directory integration** — automatic password rotation on enrollment for shared/kiosk device accounts
- **Multi-console support** — manage several Workspace ONE consoles from a single panel
- **Role-based permissions**, SAML SSO login (Omnissa Workspace ONE Access), 5-language UI (PL/EN/DE/ES/FR)

## Requirements

- Windows Server with IIS
- .NET 10 Hosting Bundle
- SQL Server or PostgreSQL
- A Workspace ONE UEM console (or several) with API access
- PowerShell run **as Administrator** for deployment

## Installation

1. Download the latest release ZIP (`WsoQr-X.Y.Z.zip`) from the [Releases](../../releases) page
2. Extract the ZIP to a staging folder on the target Windows Server — this is a temporary working location, **not** the final install path (e.g. `C:\Temp\WsoQr\`)
3. Open PowerShell **as Administrator**, `cd` into the extracted folder, and run `Deploy.ps1` — it's included in every release package alongside the application files

### `Deploy.ps1` parameters

| Parameter | Required | Description |
|---|---|---|
| `-ZipPath` | Yes | Path to the release ZIP you downloaded |
| `-HostName` | Yes | The hostname the site will be reachable at (e.g. `uem-enroll.yourcompany.com`) |
| `-InstallPath` | No | Where the application actually gets installed (default: `C:\Program Files\WSOQRGENERATOR`) |
| `-AppPoolName` / `-SiteName` | No | IIS Application Pool / Site name (sensible defaults provided) |
| `-CertificateThumbprint` | No | Use an existing certificate for HTTPS instead of generating a self-signed one |
| `-SkipHttps` | No | Skip HTTPS binding entirely (not recommended outside local testing) |
| `-UpdateOnly` | No | Use when upgrading an existing installation instead of doing a fresh install — preserves configuration, database, and certificates |

### First install

```powershell
.\Deploy.ps1 -ZipPath "C:\Temp\WsoQr\WsoQr-1.8.0.zip" -HostName "uem-enroll.yourcompany.com"
```

The application gets installed to `-InstallPath` (default `C:\Program Files\WSOQRGENERATOR`) — the staging folder you extracted the ZIP into is not the final location and can be deleted afterward.

### Upgrading an existing installation

```powershell
.\Deploy.ps1 -ZipPath "C:\Temp\WsoQr\WsoQr-1.9.0.zip" -HostName "uem-enroll.yourcompany.com" -UpdateOnly
```

After deployment, open `https://<HostName>/` in a browser — a built-in setup wizard walks you through database and SAML configuration on first launch.

## Trial

Every installation includes a built-in **90-day trial**, starting from first launch. During the trial period you have access to the full feature set — nothing is limited or gated.

## What's not there yet

- No SOTI / Intune support yet — Workspace ONE is the only implemented UEM provider so far. The longer-term goal is the same self-service migration pattern working for devices coming in from Intune, SOTI, or MEMDM too, not just between two Workspace ONE tenants
- Migration today assumes fully-managed, device-owner (COBO) devices — Work Profile / COPE support is next
- No bulk migration scheduling yet — one device at a time from the panel

## Feedback

This is an early-stage, actively developed tool. If you run Workspace ONE UEM and this solves (or almost solves) a real problem for you, I'd genuinely like to hear about it — what's missing, what doesn't fit your environment, what would make it actually useful for your setup.

#WeAreOmnissa