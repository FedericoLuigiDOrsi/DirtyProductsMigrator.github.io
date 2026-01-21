<p align="center">
  <img src="https://img.shields.io/badge/🛃-Dogana%20Import-C41E3A?style=for-the-badge" alt="Dogana Import"/>
</p>

<h1 align="center">DirtyProductsMigrator</h1>

<p align="center">
  <strong>Photo Quality Control Webapp per Migrazione Catalogo Legacy</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-8.0-blue?style=flat-square" alt="Version"/>
  <img src="https://img.shields.io/badge/status-production-success?style=flat-square" alt="Status"/>
  <img src="https://img.shields.io/badge/stack-HTML%20%7C%20JS%20%7C%20Airtable%20API-orange?style=flat-square" alt="Stack"/>
</p>

---

## 📋 Overview

**DirtyProductsMigrator** (aka "Dogana Import") è la webapp che gestisce il controllo qualità foto e la selezione FRONT/BACK per la migrazione del catalogo legacy DirtyTag 2.0 verso il nuovo sistema 3.0.

Funziona come punto di controllo ("dogana") dove ogni prodotto viene verificato manualmente prima di entrare nella pipeline di generazione AI.

---

## 🎯 Funzionalità

### Core Features

| Feature | Descrizione |
|---------|-------------|
| **📸 Photo Review** | Visualizzazione griglia foto prodotto da Google Drive |
| **🎯 FRONT/BACK Selection** | Click o keyboard per selezionare foto principale e retro |
| **🤖 AI Mode Config** | Selezione modalità generazione (FLAT, MANI, WORN) |
| **❌ Skip Workflow** | Scarta prodotti con foto insufficienti/bassa qualità |
| **📊 Progress Tracking** | Contatori e statistiche real-time |
| **🔍 Zoom Modal** | Ingrandimento foto per verifica dettagli |
| **⌨️ Keyboard Shortcuts** | Operatività veloce senza mouse |

### Keyboard Shortcuts

| Tasto | Azione |
|-------|--------|
| `F` | Imposta foto sotto il mouse come FRONT |
| `B` | Imposta foto sotto il mouse come BACK |
| `Enter` | Conferma selezione e passa al prossimo |
| `S` | Apre modal Skip |
| `N` | Salta al prossimo SKU senza azione |
| `Esc` | Chiude modali |
| `←` `→` | Naviga tra record |

---

## 🔄 Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                      MIGRATION WORKFLOW                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  W0.5 OLD_AUDITOR                                               │
│       ↓                                                          │
│  PHOTO_CANDIDATES (Status: NEEDS_REVIEW)                        │
│       ↓                                                          │
│  ┌─────────────────────────────────┐                            │
│  │   🛃 DOGANA IMPORT WEBAPP       │  ← Tu sei qui              │
│  │                                  │                            │
│  │   • Visualizza foto SKU         │                            │
│  │   • Seleziona FRONT/BACK        │                            │
│  │   • Configura AI Mode           │                            │
│  │   • Approva o Scarta            │                            │
│  └─────────────────────────────────┘                            │
│       ↓                                                          │
│  ✅ APPROVE → Ready_To_Migrate = TRUE                           │
│       ↓                                                          │
│  W0.7 MIGRATION_BRIDGE → PROCESS_QUEUE                          │
│       ↓                                                          │
│  W1-W4 Pipeline (RAW → AI → TEXT → LISTING)                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Componente | Tecnologia |
|------------|------------|
| **Frontend** | HTML5, CSS3, Vanilla JavaScript |
| **Database** | Airtable API |
| **Storage** | Google Drive (foto) |
| **Auth** | Airtable Personal Access Token |
| **Hosting** | GitHub Pages |

---

## 📊 Database Schema

### Tabelle Coinvolte

**Base:** DirtyTag 2.0 (`apptPbWnDkDkKEpFV`)

| Tabella | Table ID | Ruolo |
|---------|----------|-------|
| `OLD_INVENTORY` | `tblddAcLcQAyk050u` | Record prodotti legacy |
| `PHOTO_CANDIDATES` | `tbl7ogfLnHsVfFdzb` | Foto da revieware |

### Campi Modificati (OLD_INVENTORY)

| Campo | Tipo | Descrizione |
|-------|------|-------------|
| `Confirmed_Front_FileId` | Text | Google Drive FileID foto FRONT |
| `Confirmed_Back_FileId` | Text | Google Drive FileID foto BACK |
| `Photo_Audit_Status` | Single Select | NEEDS_REVIEW → COMPLIANT/SKIPPED |
| `Migration_AI_Mode` | Single Select | FLAT, MANI, WORN |
| `Ready_To_Migrate` | Checkbox | Trigger per W0.7 |
| `Skip_Reason` | Single Select | Motivo scarto |
| `Skip_Notes` | Long Text | Note aggiuntive |
| `Skipped_At` | DateTime | Timestamp scarto |
| `Requires_Reshoot` | Checkbox | Flag per nuovo shooting |

### Skip Reasons

| Codice | Descrizione |
|--------|-------------|
| `TOO_FEW_PHOTOS` | Troppe poche foto |
| `NO_INTERNAL_LABELS` | Etichette interne mancanti |
| `LOW_QUALITY_PHOTOS` | Foto bassa qualità |
| `MISSING_FRONT_BACK` | Front/Back non identificabili |
| `OTHER` | Altro (con note) |

---

## 🚀 Setup & Deploy

### Prerequisiti

- Account GitHub
- Airtable Personal Access Token con scope:
  - `data.records:read`
  - `data.records:write`
  - `schema.bases:read`

### Deploy su GitHub Pages

```bash
# Clone repository
git clone https://github.com/FedericoLuigiDOrsi/DirtyProductsMigrator.github.io.git

# Il file index.html è già configurato
# GitHub Pages serve automaticamente da branch main
```

### Configurazione Token

1. Apri la webapp
2. Inserisci il token Airtable nel campo dedicato
3. Il token viene salvato in `localStorage` del browser

---

## 📱 Interfaccia

### Layout Principale

```
┌─────────────────────────────────────────────────────────────────┐
│  🛃 Dogana Import — DirtyTag Photo QC v8                        │
│                                                                  │
│  [Stats: Pending 247 | Done 153 | Skipped 12]    [🔧 Debug]    │
├─────────────────────────────────────────────────────────────────┤
│  [Filter: Status ▼] [Category ▼] [Brand ▼]      [◀ Prev][Next ▶]│
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SKU: MF-2411          Brand: MONCLER          Category: MF     │
│  Photos: 6             Status: NEEDS_REVIEW                      │
│                                                                  │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐              │
│  │ 📷1 │ │ 📷2 │ │ 📷3 │ │ 📷4 │ │ 📷5 │ │ 📷6 │              │
│  │FRONT│ │BACK │ │     │ │     │ │     │ │     │              │
│  └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘              │
│                                                                  │
│  Selected: ✅ FRONT: IMG_1234.jpg  ✅ BACK: IMG_1235.jpg        │
│                                                                  │
│  AI Mode: [FLAT ▼]                                               │
│                                                                  │
│  [❌ Scarta]                              [✅ Conferma Selezione]│
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Troubleshooting

### Problemi Comuni

| Problema | Causa | Soluzione |
|----------|-------|-----------|
| Foto non caricano | Token scaduto/invalido | Reinserisci token |
| Errore al salvataggio | Campo read-only o tipo errato | Verifica schema Airtable |
| Skip non funziona | Boolean formattato male | Già fixato in v8 (usa 0/1) |
| Nessun record | Filtro troppo restrittivo | Controlla filtri attivi |

### Debug Mode

Clicca il pulsante 🔧 Debug per visualizzare:
- Log operazioni
- Chiamate API
- Errori dettagliati

---

## 📚 Documentazione Correlata

| Documento | Contenuto |
|-----------|-----------|
| [GUIDE_PHOTO_QC_WEBAPP.md](https://github.com/FedericoLuigiDOrsi/dirtytag-system/blob/main/GUIDE_PHOTO_QC_WEBAPP.md) | Guida utente completa |
| [MIGRATION_SYSTEM_DOCS.md](https://github.com/FedericoLuigiDOrsi/dirtytag-system/blob/main/MIGRATION_SYSTEM_DOCS.md) | Architettura migrazione |
| [WORKFLOWS_MIGRATION.md](https://github.com/FedericoLuigiDOrsi/dirtytag-system/blob/main/WORKFLOWS_MIGRATION.md) | Workflow W0.5, W0.7, W1 |

---

## 🔗 Links

| Risorsa | URL |
|---------|-----|
| **Webapp Live** | https://federicoluigidorsi.github.io/DirtyProductsMigrator.github.io/ |
| **Sistema Principale** | https://github.com/FedericoLuigiDOrsi/dirtytag-system |
| **AI Support Chat** | https://notebooklm.google.com/notebook/7b62519e-9fbf-4d40-bf47-2f43c0fd0b28 |

---

## 📄 License

Proprietario — Tutti i diritti riservati.

---

## 👤 Author

**Federico Luigi D'Orsi** — [@FedericoLuigiDOrsi](https://github.com/FedericoLuigiDOrsi)
