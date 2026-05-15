<div align="center">

# 🛡️ SDL — Sicherheit Dienst Leistungen

**Professionelle Webanwendung für Sicherheitsdienstleister**

[![PHP](https://img.shields.io/badge/PHP-8.0-777BB4?style=flat-square&logo=php&logoColor=white)](https://www.php.net/)
[![Laravel](https://img.shields.io/badge/Laravel-8.x-FF2D20?style=flat-square&logo=laravel&logoColor=white)](https://laravel.com/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat-square&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3.x-38B2AC?style=flat-square&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Alpine.js](https://img.shields.io/badge/Alpine.js-3.x-8BC0D0?style=flat-square&logo=alpinedotjs&logoColor=white)](https://alpinejs.dev/)
[![License](https://img.shields.io/badge/Lizenz-Proprietär-red?style=flat-square)](LICENSE)

🌐 **Live unter:** [parsec.vip](https://parsec.vip)

</div>

---

## 📋 Inhaltsverzeichnis

- [Über das Projekt](#-über-das-projekt)
- [Technischer Stack](#-technischer-stack)
- [Schnellstart](#-schnellstart)
- [Projektstruktur](#-projektstruktur)
- [Rollenmodell](#-rollenmodell)
- [Controller & Routen](#-controller--routen)
- [Datenmodell](#-datenmodell)
- [Sicherheitsregeln](#-sicherheitsregeln)
- [Entwicklungskonventionen](#-entwicklungskonventionen)
- [Roadmap](#-roadmap)

---

## 🔍 Über das Projekt

SDL (**S**icherheit **D**ienst **L**eistungen) ist eine vollständige Unternehmensplattform für professionelle Sicherheitsdienstleister. Die Anwendung deckt den gesamten Betriebsablauf ab – von der Personalverwaltung über Dienstplanung und Objektschutz bis hin zu Abrechnung und Auftragsvergabe.

**Kernfunktionen:**

- 👥 **Personalverwaltung** – vollständige Stammdaten, Rollen und Berechtigungen
- 📅 **Dienstplanung** – Einsatzplanung mit Bestätigungs-Workflow
- 🏢 **Objektschutz** – NFC-basierte Kontrollgänge mit Token-Scanning
- 💰 **Abrechnung** – Stundenverwaltung, Lohnberechnung und Zahlungen
- 📁 **Dokumentenmanagement** – Datei-Upload und Messaging
- 📝 **Bewerbungsportal** – öffentlich zugänglich ohne Login
- 📋 **Auftragsportal** – Auftraggeber-Zugang ohne Login

---

## 🛠️ Technischer Stack

| Bereich | Technologie |
|---|---|
| **Backend** | Laravel 8, PHP 8 |
| **Datenbank** | MySQL 8 (`parsec_db`) |
| **Frontend** | Tailwind CSS, Bootstrap 5, Alpine.js |
| **Build-Tool** | Laravel Mix, Webpack |
| **Authentifizierung** | Laravel Sanctum + Google Authenticator (2FA) |
| **Rollenverwaltung** | Laratrust |
| **Datei-Speicherung** | Laravel `Storage`-Facade → `storage/app/files` |

---

## 🚀 Schnellstart

### Voraussetzungen

- PHP ≥ 8.0
- Composer
- Node.js & npm
- MySQL 8

### Installation

```bash
# 1. Repository klonen
git clone https://github.com/abbasrobaei/SDL-.git
cd SDL-

# 2. PHP-Abhängigkeiten installieren
composer install

# 3. Node-Abhängigkeiten installieren
npm install

# 4. Umgebungskonfiguration einrichten
cp .env.example .env
php artisan key:generate

# 5. Datenbankverbindung in .env konfigurieren (KEINE Zugangsdaten im Code!)
# DB_HOST, DB_PORT, DB_DATABASE, DB_USERNAME, DB_PASSWORD

# 6. Datenbank migrieren
php artisan migrate

# 7. Assets bauen
npm run dev        # Entwicklung
npm run production # Produktion

# 8. Lokalen Server starten
php artisan serve
```

> ⚠️ **Wichtig:** Alle Zugangsdaten ausschließlich über die `.env`-Datei konfigurieren — niemals direkt im Code!

---

## 📁 Projektstruktur

```
SDL-/
├── app/
│   ├── Http/
│   │   └── Controllers/        # 14 Haupt-Controller + 14 Auth-Controller
│   └── Models/                 # 13 Eloquent-Modelle (alle mit SoftDeletes)
├── database/
│   ├── migrations/             # 30+ Migrationen
│   └── *.sql                   # Live-Datenbankdump
├── resources/
│   └── views/                  # Blade-Templates
│       ├── main/
│       ├── superadministrator/
│       ├── administrator/
│       ├── manager/
│       ├── secretar/
│       └── user/
├── routes/
│   └── web.php                 # Alle Web-Routen
├── public/                     # Web-Root
├── deployment/                 # SSH-Deploy-Key (öffentlich)
└── .env                        # Laufzeitkonfiguration (nicht einchecken!)
```

---

## 👥 Rollenmodell

Die Anwendung verwendet **7 Rollen** mit abgestuften Berechtigungen. Alle authentifizierten Routen durchlaufen die Middleware-Kette: `auth` → `verified` → `prevent-back-history` → `refresh_time` → `2fa`.

| Rolle | Beschreibung | Login erforderlich |
|---|---|---|
| `superadministrator` | Vollzugriff inkl. RAW SQL Query Builder | ✅ |
| `administrator` | Verwaltung von Benutzern, Diensten und Reports | ✅ |
| `manager` | Eigene Dienste, Objektschutz und Aufträge | ✅ |
| `secretar` | Büroadministration, Dateien und Nachrichten | ✅ |
| `user` | Eigenes Profil, eigene Dienste und Stunden | ✅ |
| `jobsucher` | Bewerbungsportal | ❌ |
| `auftraggeber` | Auftragsportal | ❌ |

> Das Rollensystem darf **nicht** entfernt oder vereinfacht werden. Jede relevante Routengruppe muss `role:X` als Middleware enthalten.

---

## 🗺️ Controller & Routen

| Controller | Route | Zugriff |
|---|---|---|
| `SuperadministratorController` | `/superadministrator` | `superadministrator` |
| `AdministratorController` | `/administrator` | `administrator` |
| `ManagerController` | `/manager` | `manager` |
| `SecretarController` | `/secretar` | `secretar` |
| `UserController` | `/user` | `user` |
| `HomeController` | `/home` | Alle eingeloggten Benutzer |
| `OrderController` | `/order` | Manager und höher |
| `FileController` | `/file` | Alle eingeloggten Benutzer |
| `NfcrecordController` | `/objektschutzs/{token}` | NFC-Chip-Scans |
| `JobsucheController` | `/jobsucher` | Öffentlich (kein Login) |
| `PaymentController` | `/payment` | Manager und höher |
| `PriceController` | `/price` | Administrator und höher |
| `PersonalStammdatenController` | `/personalstammdaten` | Öffentlich (kein Login) |
| `GooleAuthenticateController` | `/2fa/{id}` | Eingeloggte Benutzer |

---

## 🗃️ Datenmodell

Alle **13 Modelle** verwenden `SoftDeletes` — in Produktion wird niemals `forceDelete` eingesetzt.

<details>
<summary><strong>Modelle im Detail aufklappen</strong></summary>

### User
`personal_id` · `idnumber` · `firstname` · `lastname` · `sex` · `telefon` · `address` · `zipcode` · `city` · `bio` · `manager` · `birthday` · `photo` · `logo` · `email` · `password` · `company` · `position` · `country` · `last_seen` · `last_login` · `register_time` · `recommender`

### Dienst
`manager` · `dienstort` · `datum` · `dienstende` · `menge` · `notiz` · `progress`
→ Many-to-Many zu `User` über `dienst_user` (Zusatzfeld: `accept`)

### Auftrag
`manager_id` · `auftrag` · `auftrag_id` · `beginn` · `ende` · `position_1` … `position_7` · `notiz`

### Objektschutz
`manager` · `title` · `nfctoken_count` · `progress`
→ Relationen zu `User` und NFC-Tokens

### stunden
Größte Tabelle (9.000+ Einträge) — speichert alle Arbeitsstunden.

### Weitere Modelle
`Order` · `Payment` · `Price` · `File` · `Message` · `Role` · `Permission`

</details>

---

## 🔒 Sicherheitsregeln

> Diese Regeln sind **unverhandelbar** und dürfen nicht umgangen werden.

| # | Regel |
|---|---|
| 1 | **Rollensystem:** `role:X`-Middleware in jeder relevanten Routengruppe — kein Entfernen oder Vereinfachen |
| 2 | **SoftDeletes:** Immer aktiv; kein `forceDelete` in Produktion |
| 3 | **NFC-System:** Route `/objektschutzs/{token}` und Token-Format sind unveränderlich |
| 4 | **Query Builder:** RAW SQL (`DB::select`) ausschließlich für `role:superadministrator` |
| 5 | **Migrationen:** Alle Datenbankänderungen ausschließlich per Migration |
| 6 | **Keine Secrets im Code:** Zugangsdaten nur über `.env` und `config/` — niemals hardcoden |

---

## 📐 Entwicklungskonventionen

| Bereich | Konvention |
|---|---|
| **Sprache** | PHP 8, Laravel-8-Konventionen |
| **Klassen** (Modelle/Controller) | `PascalCase` |
| **Datenbankspalten** | `snake_case` |
| **Templates** | Blade (kein React / kein Vue) |
| **JS-Interaktivität** | Alpine.js |
| **Datenbankzugriff** | Eloquent ORM |
| **RAW SQL** | Nur im Superadministrator Query Builder (`DB::select`) |
| **Datei-Uploads** | `storage/app/files` über `Storage`-Facade |
| **Neue Features** | Immer mit zugehöriger Migration |

---

## 🗓️ Roadmap

| Priorität | Feature | Status |
|---|---|---|
| 🔴 Hoch | PWA Mobile App | 🔲 Geplant |
| 🔴 Hoch | Automatische Lohnabrechnung | 🔲 Geplant |
| 🟡 Mittel | GPS-Tracking für Wachen | 🔲 Geplant |
| 🟡 Mittel | REST API für externe Auftraggeber | 🔲 Geplant |
| 🟡 Mittel | Push-Benachrichtigungen | 🔲 Geplant |
| 🟢 Niedrig | Kalender-Synchronisation (Google/Outlook) | 🔲 Geplant |

---

<div align="center">

**© SDL — Sicherheit Dienst Leistungen** · [parsec.vip](https://parsec.vip)

</div>
