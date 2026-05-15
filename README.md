# SDL (Sicherheit Dienst Leistungen)

SDL ist eine Webanwendung für einen Sicherheitsdienstleister und läuft live unter `parsec.vip`.

## Technischer Stack

- **Backend:** Laravel 8, PHP 8
- **Datenbank:** MySQL 8 (`parsec_db`)
- **Frontend:** Tailwind CSS, Bootstrap 5, Alpine.js
- **Build:** Laravel Mix, Webpack
- **Authentifizierung:** Laravel Sanctum + Google Authenticator (2FA)
- **Rollenverwaltung:** Laratrust

## Rollenmodell (7 Rollen)

1. **superadministrator** – Vollzugriff inklusive RAW SQL Query Builder
2. **administrator** – Verwaltung von Benutzern, Diensten und Reports
3. **manager** – Verwaltung eigener Dienste, Objektschutz und Aufträge
4. **secretar** – Büroadministration, Dateien und Nachrichten
5. **user** – Eigenes Profil, eigene Dienste und Stunden
6. **jobsucher** – Zugriff auf Bewerbungsportal ohne Login
7. **auftraggeber** – Zugriff auf Auftragsportal ohne Login

Alle authentifizierten Routen laufen über:
`auth`, `verified`, `prevent-back-history`, `refresh_time`, `2fa`.

## Controller und Routen

- `SuperadministratorController` → `/superadministrator` (nur `superadministrator`)
- `AdministratorController` → `/administrator` (nur `administrator`)
- `ManagerController` → `/manager` (nur `manager`)
- `SecretarController` → `/secretar` (nur `secretar`)
- `UserController` → `/user` (nur `user`)
- `HomeController` → `/home` (alle eingeloggten Benutzer)
- `OrderController` → `/order` (Manager und höher)
- `FileController` → `/file` (alle eingeloggten Benutzer)
- `NfcrecordController` → `/objektschutzs/{token}` (NFC-Chip-Scans)
- `JobsucheController` → `/jobsucher` (ohne Login)
- `PaymentController` → `/payment` (Manager und höher)
- `PriceController` → `/price` (Administrator und höher)
- `PersonalStammdatenController` → `/personalstammdaten` (ohne Login)
- `GooleAuthenticateController` → `/2fa/{id}` (eingeloggte Benutzer)

## Datenmodell (13 Modelle)

Alle Modelle verwenden **SoftDeletes** (`deleted_at`).

Wichtige Modelle:

- **User:** `personal_id`, `idnumber`, `firstname`, `lastname`, `sex`, `telefon`, `address`, `zipcode`, `city`, `bio`, `manager`, `birthday`, `photo`, `logo`, `email`, `password`, `company`, `position`, `country`, `last_seen`, `last_login`, `register_time`, `recommender`
- **Dienst:** `manager`, `dienstort`, `datum`, `dienstende`, `menge`, `notiz`, `progress` (many-to-many zu User via `dienst_user`, Zusatzfeld `accept`)
- **Auftrag:** `manager_id`, `auftrag`, `auftrag_id`, `beginn`, `ende`, `position_1` bis `position_7`, `notiz`
- **Objektschutz:** `manager`, `title`, `nfctoken_count`, `progress` (Relationen zu User und NFC-Tokens)
- **stunden:** Größte Tabelle (9000+ Einträge), speichert Arbeitsstunden

Weitere Modelle: `Order`, `Payment`, `Price`, `File`, `Message`, `Role`, `Permission`.

## Projektstruktur

- `app/Http/Controllers` – 14 Controller + `Auth/` mit 14 Auth-Controllern
- `app/Models` – 13 Modelle
- `database/migrations` – 30+ Migrationen
- `database/*.sql` – Live-Datenbankdump
- `deployment` – öffentlicher SSH-Deploy-Key
- `public` – Web-Root
- `resources/views` – Blade-Templates (`main`, `superadministrator`, `administrator`, `manager`, `secretar`, `user`)
- `routes/web.php` – alle Web-Routen
- `vendor`, `node_modules` – Abhängigkeiten
- `.env` – Laufzeitkonfiguration (nicht hardcodieren)

## Unverhandelbare Regeln

1. **Rollensystem nicht entfernen oder vereinfachen** (`role:X` in jeder relevanten Routengruppe)
2. **SoftDeletes immer nutzen**, kein `forceDelete` in Produktion
3. **NFC-System nicht ändern** ohne ausdrücklichen Auftrag (`/objektschutzs/{token}` und Token-Format unveränderlich)
4. **Query Builder nur für `role:superadministrator`**
5. **DB-Änderungen nur per Migration**
6. **Keine Zugangsdaten im Code**, nur `.env`/`config`

## Entwicklungs-Konventionen

- PHP 8 + Laravel-8-Konventionen
- Klassen (Modelle/Controller): PascalCase
- Datenbankspalten: snake_case
- Views: Blade (kein React/Vue)
- JS-Interaktivität: Alpine.js
- Datenbankzugriffe: Eloquent ORM
- RAW SQL (`DB::select`) nur im Superadministrator Query Builder
- Datei-Uploads: `storage/app/files` über `Storage`-Facade
- Neue Features immer mit Migration

## Roadmap

- **Hohe Priorität:** PWA Mobile App, automatische Lohnabrechnung
- **Mittlere Priorität:** GPS-Tracking für Wachen, REST API für externe Auftraggeber, Push-Benachrichtigungen
- **Niedrige Priorität:** Kalender-Synchronisation (Google/Outlook)
