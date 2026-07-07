# ckan-n8n-workflows

Sammlung von n8n-Workflows zur Automatisierung von CKAN-Prozessen.

Die Workflows ermöglichen die Externalisierung und Automatisierung von 
CKAN-Abläufen mit n8n, um betriebliche Integrationen und wiederkehrende 
Prozesse einfacher umzusetzen.

---

Inhalt

* [Installation](#installation)
* [Administrative Workflows](#administrative-workflows)
* [Autoren-bezogene Workflows](#autoren-bezogene-workflows)
* [Hinweise](#hinweise)

---

# Installation

Voraussetzungen

* laufende n8n-Instanz
* Zugriff auf eine CKAN-Instanz
* CKAN Site URL
* CKAN API Token (wenn Änderungen in CKAn durchgeführt werden sollen)

Workflow importieren

1. Workflow-Datei oder RAW-GitHub-URL kopieren
2. In n8n:
   * `Workflows`
   * `Import from URL`
3. Workflow importieren
4. Sicherstellen dass CKAN_SITE_URL und CKAN_N8N_API_TOKEN als Environment Variablen in n8n zur Verfügung stehen. Beim ersten Workflow sollten die Credentials im CKAN-Node entsprechend eingegeben werden (als `{{ $env["CKAN_SITE_URL"] }}` und `{{ $env["CKAN_N8N_API_TOKEN"] }}`)
5. Konfiguration anpassen (je nach Workflow)


---

# Administrative Workflows

## DCAT-AP.de Kategorien einrichten

Die Standard-DCAT-AP.de Kategorien werden in CKAN angelegt. Und bei Bedarf auch gelöscht.
Quelle: [EU Vocabularies: data-theme](https://op.europa.eu/de/web/eu-vocabularies/concept-scheme/-/resource?uri=http://publications.europa.eu/resource/authority/data-theme)

* Hinzufügen der 13 DCAT-AP.de Kategorien/Gruppen (DCAT-AP Sprechweise: data themes)
* Vorhandene Kategorien werden erkannt und übersprungen
* Die Gruppenbeschreibungen sind auf deutsch
* Die Namen der Kategorien entsprechen dem DCAT-AP.de Standard (Beispiel: `agri`)
* Icons werden nicht konfiguriert

Getestet mit CKAN 2.11 und self-hosted n8n

Download/n8n-Url: [n8n-admin-workflows/dcatapde-groups.json](https://github.com/ondics/ckan-n8n-workflows/raw/refs/heads/main/n8n-admin-workflows/dcatapde-groups.json)

---

## PEGELONLINE Pegelstände nach CKAN aktualisieren

Aktuelle Wasserstandsmesswerte werden alle 10 Minuten von der PEGELONLINE-API abgerufen und im CKAN DataStore gespeichert. 
Neue Messwerte werden eingefügt, geänderte aktualisiert, unveränderte übersprungen.

* Alle 13 Schritte sind nummeriert und mit erklärenden Sticky Notes dokumentiert
* Konfiguration zentral in Node 02 Konfiguration bearbeiten (CKAN-URL, Resource-ID, Messstellen)
* Beliebig viele Messstellen konfigurierbar, einzeln aktivier- und deaktivierbar
* Messstellen werden anhand ihrer PEGELONLINE-UUID identifiziert
* CKAN-API-Token wird als n8n Credential hinterlegt, nicht im Workflow


Konfiguration: 
* Sicherstellen dass CKAN Datensatz mit einer Ressource für Messwerte existiert. Die Ressource ist ein Datastore mit folgenden Feldern (kann im Tabellendesigner angelegt werden):
  * `zeitstempel`: Timestamp
  * `messstelle_id`: Integer
  * `pegelstand_in_m`: Numeric
* Anpassen der CKAN Endpunkte (im Node `2: Konfiguration`):
  * `ckanBaseUrl`: 'https://DEINE-CKAN-INSTANZ.de',
  * `pegelwerteResourceId`: 'DEINE_CKAN_DATASTORE_RESOURCE_ID',
* Messtellen festlegen (im Node `2: Konfiguration`): per Recherche unter [PegelOnline](https://pegelonline.wsv.de/gast/pegeltabelle) (im Node 2: Konfiguration`)

Quelle: [PEGELONLINE REST-API v2](https://www.pegelonline.wsv.de/webservice/guideRestapi)

Getestet mit CKAN 2.11 und self-hosted n8n

Download/n8n-URL:[ n8n-admin-workflows/pegelstaende-ckan-pegelonline-template.json](https://github.com/ondics/ckan-n8n-workflows/raw/refs/heads/main/n8n-admin-workflows/pegelstaende-ckan-pegelonline-template.json)

---
## CKAN Datensatzprüfung und Erinnerungsmails

Prüft CKAN-Datensätze auf lange nicht aktualisierte Inhalte und erstellt Vorschau-, Test- oder Versandmails an Sysadmin und/oder Datensatzkontakte.
Die CKAN-Abfrage holt maximal 1000 Datensätze und nutzt `metadata_modified`; private Datensätze werden mit `include_private: true` berücksichtigt.

Konfiguration: 
* Prüfzeitraum und Versandmodus werden im Node `Formular: Datensatzprüfung starten` gewählt.
* Zeitplan bei Bedarf im Node `Schedule Trigger` anpassen.
* Beim Zeittrigger stehen die Standardwerte für Prüfzeitraum und Versandmodus im Node `Zeittrigger-Formularwerte setzen`.
  
Benötigte Environment-Variablen:

* `{{ $env["SYSADMIN_EMAIL"]}}` 
* `{{ $env["TEST_EMAIL"]}}` 
* `{{ $env["EMAIL_FROM"]}}`


SMTP-Credentials im Node `E-Mail senden` nach dem Import manuell auswählen. Die Environment-Variablen `SYSADMIN_EMAIL`, `TEST_EMAIL` und `EMAIL_FROM` steuern nur Empfänger- und Absenderadressen im Workflow

Getestet mit CKAN 2.11 und self-hosted n8n.

Download/n8n-URL:[ n8n-admin-workflows/ckan-datensatzpruefung-erinnerungsmails.json](https://github.com/ondics/ckan-n8n-workflows/raw/refs/heads/main/n8n-admin-workflows/ckan-datensatzpruefung-erinnerungsmails.json)


# Autoren-bezogene Workflows

## schaumermal, was da noch kommt...

noch nix.

# Hinweise

* Die Workflows dienen als Referenzimplementierungen.
* Anpassungen an projektspezifische Anforderungen sind in der Regel erforderlich.
* Vor produktivem Einsatz wird ein Test in einer Entwicklungs- oder Staging-Umgebung empfohlen.
* Ondics haftet nicht für die korrekte Funktionsweise der Workflows.
* Viel Erfolg damit. Automatisiert und spart, wo Ihr könnt!

---

# Credits

Danke an die, die solche Arbeiten erst ermöglichen: 

* [CKAN](https://github.com/ckan/ckan)
* [n8n](https://n8n.io)

---

# Autor

**ondics GmbH**
Esslingen am Neckar, Deutschland, Baden-Württemberg, "im Schwäbischen onder Stuttgart"
https://ondics.de
[info@ondics.de](mailto:info@ondics.de)
