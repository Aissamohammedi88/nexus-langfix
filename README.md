# nexus-langfix
Firefox forgets your language? Websites force English? Nexus LangFix overrides the HTML lang attribute and meta tags in real-time. Sovereign, local, open-source.
<div align="center">

<!-- Bannière SVG native -->
<svg width="800" height="200" viewBox="0 0 800 200" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="bgGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" stop-color="#0a0a0f"/>
      <stop offset="100%" stop-color="#151520"/>
    </linearGradient>
    <linearGradient id="neonGrad" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" stop-color="#00e5ff">
        <animate attributeName="stop-color" values="#00e5ff;#ff00c8;#00e5ff" dur="3s" repeatCount="indefinite"/>
      </stop>
      <stop offset="100%" stop-color="#ff00c8">
        <animate attributeName="stop-color" values="#ff00c8;#00e5ff;#ff00c8" dur="3s" repeatCount="indefinite"/>
      </stop>
    </linearGradient>
    <filter id="glow">
      <feGaussianBlur stdDeviation="4" result="blur"/>
      <feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge>
    </filter>
  </defs>

  <rect width="800" height="200" fill="url(#bgGrad)" rx="12"/>
  <rect x="1" y="1" width="798" height="198" fill="none" stroke="url(#neonGrad)" stroke-width="2" rx="11" opacity="0.5"/>

  <text x="400" y="95" text-anchor="middle" font-family="ui-monospace, monospace" font-size="44" font-weight="900" fill="url(#neonGrad)" filter="url(#glow)">
    NEXUS LANGFIX
    <animate attributeName="opacity" values="1;0.8;1" dur="2s" repeatCount="indefinite"/>
  </text>
  <text x="400" y="135" text-anchor="middle" font-family="ui-monospace, monospace" font-size="14" fill="#a0a0a0" letter-spacing="4">
    FORCE YOUR LANGUAGE. EVERYWHERE.
  </text>
</svg>

<br/>

[![Firefox](https://img.shields.io/badge/Firefox-109%2B-FF7139?style=for-the-badge&logo=firefox-browser&logoColor=white)](#)
[![License](https://img.shields.io/badge/License-NEXUS--OPEN--3.0-00ff9d?style=for-the-badge)](#)
[![Status](https://img.shields.io/badge/Status-Operational-00e5ff?style=for-the-badge)](#)

<br/>

**Firefox oublie ta langue ? Les sites forcent l'anglais ?**
**Nexus LangFix impose ta langue sur toutes les pages, en temps réel.**

**[⚡ Installation](#-installation) · [🧠 Pourquoi LangFix ?](#-pourquoi-langfix-) · [🏗️ Architecture](#️-architecture) · [🛠️ Dev](#️-développement)**

</div>

---

## 😤 Le Problème

Firefox a un défaut frustrant : il ne force **pas** la langue sur les sites web. Si un site est codé en dur pour s'afficher en anglais, ou s'il utilise ton IP pour deviner ta langue, Firefox ne fera rien. Tu te retrouves avec des interfaces en anglais, des boutons en allemand, et des messages en espagnol, même si tu as configuré Firefox en français.

Les réglages natifs de Firefox (`about:config`, `intl.accept_languages`) changent **l'en-tête HTTP** envoyé au serveur, mais ils ne forcent **pas** l'attribut `lang` de la page. Si le site ignore cet en-tête, tu es coincé.

## 💡 La Solution : Nexus LangFix

**Nexus LangFix** est une extension Firefox (Manifest V3) qui s'exécute sur **chaque page** avant même que le HTML ne soit complètement chargé. Elle :

1. **Force l'attribut `lang`** de la balise `<html>` sur la langue que tu as choisie.
2. **Corrige les balises `<meta http-equiv="content-language">`**.
3. **Impose la langue** sur tous les éléments qui ont un attribut `lang`.
4. **Fonctionne même si Firefox a oublié ta langue** ou si le site force l'anglais.

---

## ⚡ Installation

### Méthode 1 : Chargement temporaire (Pour tester)

1. Ouvre Firefox et tape `about:debugging` dans la barre d'adresse.
2. Clique sur **"Ce Firefox"** à gauche.
3. Clique sur **"Charger un module temporaire..."**.
4. Sélectionne le fichier `manifest.json` du dossier `Nexus-LangFix`.
5. L'icône LangFix apparaît dans ta barre d'outils. Clique dessus, choisis ta langue, et clique sur **Appliquer**.

### Méthode 2 : Installation permanente (Recommandée)

1. Télécharge le fichier `.xpi` depuis les [Releases](https://github.com/Aissamohammedi88/nexus-langfix/releases).
2. Ouvre Firefox et tape `about:addons`.
3. Clique sur l'icône d'engrenage ⚙️ en haut à droite.
4. Sélectionne **"Installer un module complémentaire à partir d'un fichier..."**.
5. Choisis le fichier `.xpi`.

*(Note : Pour une installation permanente, le fichier `.xpi` doit être signé par Mozilla. Si ce n'est pas le cas, utilise la Méthode 1 ou Firefox Developer Edition).*

---

## 🧠 Pourquoi LangFix ?

| Fonctionnalité | Réglage natif Firefox | **Nexus LangFix** |
|----------------|-----------------------|-------------------|
| Change la langue de l'interface Firefox | ✅ Oui | ❌ Non (ce n'est pas le but) |
| Change l'en-tête `Accept-Language` | ✅ Oui | ✅ Oui (via le content script) |
| Force l'attribut `lang` de la page | ❌ Non | **✅ Oui** |
| Corrige les balises `meta` de langue | ❌ Non | **✅ Oui** |
| Force la langue sur les éléments `lang` | ❌ Non | **✅ Oui** |
| Souverain (aucune donnée envoyée) | ✅ Oui | **✅ Oui** |

---

## 🏗️ Architecture

Nexus LangFix est une extension Firefox minimaliste (Manifest V3). Elle utilise :

1. `browser.storage.local` : Pour sauvegarder la langue choisie par l'utilisateur.
2. `browser.scripting.executeScript` : Pour appliquer la langue immédiatement sur l'onglet actif.
3. Un **content script** (`content.js`) : Qui s'exécute sur toutes les pages (`<all_urls>`) au démarrage (`document_start`).

Le code est divisé en quatre fichiers :

- `manifest.json` : Déclare les permissions (`storage`, `scripting`, `activeTab`).
- `background.js` : Initialise la langue par défaut (français) à l'installation.
- `content.js` : Le cœur du système. Force la langue sur chaque page.
- `popup.html` / `popup.js` : L'interface utilisateur (le menu qui s'ouvre au clic).

### Flux d'exécution
Utilisateur clique sur l'icône
│
▼
popup.js → choisit la langue → browser.storage.local.set()
│
▼
content.js (sur chaque page) → lit la langue → force l'attribut lang
│
▼
La page s'affiche dans la langue choisie
text


---

## 🛠️ Développement

### Prérequis

- Firefox 109+ (Manifest V3)
- Un éditeur de texte (VS Code, Vim, etc.)

### Compiler l'extension

```bash
# Cloner le dépôt
git clone https://github.com/Aissamohammedi88/nexus-langfix.git
cd nexus-langfix

# Lancer le script de build
chmod +x build.sh
./build.sh

Le script génère un fichier Nexus-LangFix.xpi prêt à être installé.
Structure du projet
text

nexus-langfix/
├── manifest.json
├── background.js
├── content.js
├── popup.html
├── popup.js
├── style.css
├── icon.png
├── build.sh
├── README.md
└── LICENSE

🤝 Contribuer

Les contributions sont les bienvenues. Ouvre une issue pour signaler un bug, ou fais une Pull Request pour ajouter une fonctionnalité.

Idées d'amélioration :

    □

    Ajouter plus de langues (japonais, coréen, arabe, etc.)
    □

    Détecter automatiquement la langue du navigateur
    □

    Ajouter un mode "auto" qui applique la langue de Firefox
    □

    Ajouter un bouton "Réinitialiser" pour revenir à la langue du site
    □

    Ajouter des raccourcis clavier

📜 License

NEXUS-OPEN-3.0 — Voir LICENSE pour les détails.

    Gratuit pour usage personnel, éducatif et de recherche.

    Usage commercial autorisé avec attribution.

    Modifications à documenter.

<div align="center"><svg width="600" height="40" viewBox="0 0 600 40" xmlns="http://www.w3.org/2000/svg"> <defs> <linearGradient id="endGrad" x1="0%" y1="0%" x2="100%" y2="0%"> <stop offset="0%" stop-color="#00e5ff" stop-opacity="0"/> <stop offset="50%" stop-color="#ff00c8" stop-opacity="1"/> <stop offset="100%" stop-color="#00e5ff" stop-opacity="0"/> </linearGradient> </defs> <line x1="0" y1="20" x2="600" y2="20" stroke="url(#endGrad)" stroke-width="2"/> <text x="300" y="28" text-anchor="middle" font-family="ui-monospace, monospace" font-size="13" fill="#a0a0a0" font-style="italic"> « Un navigateur qui parle ta langue est un navigateur qui te respecte. » <animate attributeName="opacity" values="0.4;1;0.4" dur="4s" repeatCount="indefinite"/> </text> </svg>

Made with 🌍 by Aissa Mohammedi (DGK) · 2026 · NEXUS OPEN SOURCE
</div> ```
🎯 Les "Topics" GitHub (À ajouter)

Une fois le dépôt créé, ajoute ces tags dans les "Topics" (à droite du dépôt) :
text

firefox
firefox-extension
webextension
language
i18n
localization
translation
productivity
open-source
privacy
firefox
firefox-extension
webextension
language
i18n
localization
translation
productivity
open-source
privacy
