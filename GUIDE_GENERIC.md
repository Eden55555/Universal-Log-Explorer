# Guide d'Utilisation - Version Générique
## Log Analyzer - Generic & Configurable

Cette version générique permet de configurer l'outil pour analyser n'importe quel type de logs, sans être limité à un format spécifique.

---

## 🚀 Démarrage Rapide

### 1. Première Utilisation

Lors de la première ouverture, une fenêtre de configuration s'affiche automatiquement. Vous devez :

1. **Choisir un template** ou créer un format personnalisé
2. **Définir les colonnes** à afficher
3. **Configurer les parsers** (patterns regex)
4. **Tester** avec une ligne d'exemple
5. **Sauvegarder** la configuration

### 2. Charger un Fichier

Une fois la configuration sauvegardée :
- Glissez-déposez un fichier de logs
- Ou cliquez sur la zone de drop
- Le fichier sera analysé selon votre configuration

---

## ⚙️ Configuration

### Templates Prédéfinis

L'outil propose plusieurs templates :

1. **Apache Common Log Format**
   - Format : `IP user-ident userid [date] "method path protocol" status size`
   - Colonnes : IP, Date/Time, Method, Path, Status, Size

2. **Apache Combined Log Format**
   - Format : Apache avec referrer et user agent
   - Colonnes : IP, Date/Time, Method, Path, Status, Size, Referer, User Agent

3. **Nginx Access Log**
   - Format : `IP - - [date] "method path protocol" status size`
   - Colonnes : IP, Date/Time, Method, Path, Status, Size

4. **JSON Logs**
   - Format : Logs structurés en JSON
   - Colonnes : Timestamp, Level, Message, Source, IP

5. **Syslog**
   - Format : Standard syslog
   - Colonnes : Date/Time, Host, Facility, Level, Message

6. **Custom Format**
   - Format : À définir par l'utilisateur
   - Colonnes : À définir par l'utilisateur

### Créer une Configuration Personnalisée

#### 1. Définir les Colonnes

Pour chaque colonne, vous pouvez définir :
- **Field key** : Clé du champ dans les données parsées (ex: `date`, `level`, `message`)
- **Column label** : Libellé affiché dans l'en-tête (ex: "Date/Time", "Level")
- **Visible** : Afficher ou masquer la colonne
- **Sortable** : Permettre le tri sur cette colonne

**Exemple** :
```
Field key: date
Column label: Date/Time
Visible: ✓
Sortable: ✓
```

#### 2. Configurer les Parsers

Un parser définit comment extraire les données d'une ligne de log.

**Composants d'un parser** :
- **Name** : Nom du parser (ex: "Apache Common")
- **Regex Pattern** : Expression régulière pour matcher les lignes
- **Field Mapping** : Mapping des groupes regex vers les champs
- **JSON Parser** : Cocher si c'est un parser JSON

**Exemple pour Apache Common** :
```
Regex: ^(\S+) \S+ \S+ \[(.*?)\] "(\S+) (\S+) \S+" (\d{3}) (\d+|-)
Field Mapping: {
  "ip": "$1",
  "date": "$2",
  "method": "$3",
  "path": "$4",
  "status": "$5",
  "size": "$6"
}
```

**Exemple pour JSON** :
```
Regex: ^\s*\{.*\}\s*$
Field Mapping: {
  "timestamp": "$.timestamp || $.time",
  "level": "$.level",
  "message": "$.message || $.msg"
}
JSON Parser: ✓
```

#### 3. Tester la Configuration

Utilisez la section "Test with Sample Line" :
1. Collez une ligne de log d'exemple
2. Vérifiez que les champs sont correctement extraits
3. Ajustez les patterns si nécessaire

---

## 📝 Exemples de Configuration

### Exemple 1 : Logs Custom avec Timestamp

**Format de log** :
```
2024-01-15 10:30:45 [INFO] User login: john@example.com
```

**Configuration** :
- **Colonnes** :
  - `timestamp` → "Date/Time" (visible, sortable)
  - `level` → "Level" (visible, sortable)
  - `message` → "Message" (visible, non-sortable)

- **Parser** :
  - **Regex** : `^(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) \[(\w+)\] (.+)$`
  - **Field Mapping** :
    ```json
    {
      "timestamp": "$1",
      "level": "$2",
      "message": "$3"
    }
    ```

### Exemple 2 : Logs avec ID Utilisateur

**Format de log** :
```
[2024-01-15T10:30:45] ERROR user:12345 service:AuthService "Login failed"
```

**Configuration** :
- **Colonnes** :
  - `date` → "Date" (visible, sortable)
  - `level` → "Level" (visible, sortable)
  - `userId` → "User ID" (visible, sortable)
  - `source` → "Source" (visible, sortable)
  - `message` → "Message" (visible, non-sortable)

- **Parser** :
  - **Regex** : `^\[(.*?)\] (\w+) user:(\d+) service:(\w+) "(.+)"$`
  - **Field Mapping** :
    ```json
    {
      "date": "$1",
      "level": "$2",
      "userId": "$3",
      "source": "$4",
      "message": "$5"
    }
    ```

### Exemple 3 : Logs JSON Structurés

**Format de log** :
```json
{"timestamp":"2024-01-15T10:30:45Z","level":"ERROR","service":"AuthService","message":"Login failed","userId":12345}
```

**Configuration** :
- **Colonnes** :
  - `timestamp` → "Timestamp" (visible, sortable)
  - `level` → "Level" (visible, sortable)
  - `service` → "Service" (visible, sortable)
  - `message` → "Message" (visible, non-sortable)
  - `userId` → "User ID" (visible, sortable)

- **Parser** :
  - **Regex** : `^\s*\{.*\}\s*$`
  - **Field Mapping** :
    ```json
    {
      "timestamp": "$.timestamp",
      "level": "$.level",
      "service": "$.service",
      "message": "$.message",
      "userId": "$.userId"
    }
    ```
  - **JSON Parser** : ✓

---

## 🔧 Fonctionnalités

### Configuration
- ✅ Templates prédéfinis (Apache, Nginx, JSON, Syslog, etc.)
- ✅ Configuration personnalisée complète
- ✅ Éditeur de regex avec test en temps réel
- ✅ Sauvegarde de configuration dans localStorage
- ✅ Modification de configuration à tout moment

### Analyse
- ✅ Virtual scrolling pour gros fichiers
- ✅ Recherche avec highlight
- ✅ Tri par colonnes (si configuré)
- ✅ Filtrage en temps réel
- ✅ Export CSV
- ✅ Statistiques

### Détails
- ✅ Panneau de détails avec onglets (RAW/JSON/Metadata)
- ✅ Recherche dans les détails
- ✅ Bookmarks/Favoris
- ✅ Copie de contenu

### Interface
- ✅ Thème sombre/clair
- ✅ Interface responsive
- ✅ Navigation clavier
- ✅ Accessibilité ARIA

---

## 📖 Guide de Regex

### Groupes de Capture

Dans les regex, utilisez des groupes de capture `()` pour extraire des valeurs :

```regex
^(\d{4}-\d{2}-\d{2}) \[(\w+)\] (.+)$
```

- `$1` = Date (premier groupe)
- `$2` = Level (deuxième groupe)
- `$3` = Message (troisième groupe)

### Field Mapping

Le field mapping lie les groupes regex aux champs :

```json
{
  "date": "$1",
  "level": "$2",
  "message": "$3"
}
```

### JSON Path

Pour les parsers JSON, utilisez des chemins JSON :

```json
{
  "timestamp": "$.timestamp",
  "level": "$.level",
  "message": "$.message || $.msg"
}
```

Le `||` permet de spécifier des alternatives si le premier champ n'existe pas.

---

## 💡 Conseils

### 1. Commencez par un Template

Si votre format ressemble à un template existant, utilisez-le comme point de départ et modifiez-le.

### 2. Testez avec Plusieurs Lignes

Testez votre configuration avec différentes lignes de log pour vous assurer qu'elle fonctionne dans tous les cas.

### 3. Utilisez des Groupes Nommés (Optionnel)

Vous pouvez utiliser des groupes nommés dans vos regex pour plus de clarté, mais le field mapping utilise toujours `$1`, `$2`, etc.

### 4. Gérer les Cas Spéciaux

Si certaines lignes ne matchent pas, créez un parser de fallback qui capture tout le reste :

```json
{
  "name": "Fallback",
  "regex": ".*",
  "fieldMapping": {
    "message": "$0"
  }
}
```

### 5. Sauvegardez Plusieurs Configurations

Vous pouvez exporter/sauvegarder vos configurations pour les réutiliser plus tard.

---

## 🔄 Modifier la Configuration

À tout moment, cliquez sur le bouton **"⚙️ Configure"** dans le header pour :
- Modifier les colonnes
- Ajouter/modifier des parsers
- Changer de template
- Tester avec de nouvelles lignes

La configuration est sauvegardée automatiquement dans localStorage.

---

## 📋 Format de Configuration

La configuration est stockée au format JSON :

```json
{
  "name": "My Custom Format",
  "columns": [
    {
      "key": "date",
      "label": "Date/Time",
      "visible": true,
      "sortable": true
    }
  ],
  "parsers": [
    {
      "name": "My Parser",
      "regex": "^pattern$",
      "fieldMapping": {
        "date": "$1"
      },
      "isJson": false
    }
  ]
}
```

---

## ❓ FAQ

### Q: Comment ajouter une nouvelle colonne ?

**R** : Cliquez sur "⚙️ Configure", puis sur "+ Add Column" dans la section "Define Columns".

### Q: Mon regex ne fonctionne pas, que faire ?

**R** : 
1. Testez votre regex dans un outil en ligne (regex101.com)
2. Utilisez la section "Test with Sample Line" pour déboguer
3. Vérifiez que les groupes de capture correspondent au field mapping

### Q: Comment gérer plusieurs formats dans le même fichier ?

**R** : Ajoutez plusieurs parsers. L'outil essaiera chaque parser dans l'ordre jusqu'à trouver une correspondance.

### Q: Puis-je exporter ma configuration ?

**R** : Oui, la configuration est dans localStorage. Vous pouvez l'exporter via la console du navigateur :
```javascript
localStorage.getItem('logAnalyzerConfig')
```

### Q: Comment réinitialiser la configuration ?

**R** : Dans la console du navigateur :
```javascript
localStorage.removeItem('logAnalyzerConfig');
location.reload();
```

---

**Version générique créée le** : 2024  
**Fichier** : `index_generic.html`

**Bon analyse de logs ! 🚀**


