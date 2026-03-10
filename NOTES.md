# Cahier d'apprentissage

## Commandes terminal

```bash
# Ouvrir un dossier dans VS Code depuis le terminal
open -a "Visual Studio Code" /chemin/du/projet

# Installer la commande `code` (depuis VS Code)
# Cmd+Shift+P → "Shell Command: Install 'code' command in PATH"

# Vérifier les MCP servers configurés
claude mcp list

# Voir le statut et s'authentifier aux MCP servers
/mcp

# Exécuter une commande shell directement dans Claude Code
!ls
!git status

# Créer un agent personnalisé
/agents

# Invoquer un agent personnalisé
@nom-de-lagent <prompt>
```

## Étapes de configuration MCP (Gmail + Google Calendar)

1. Créer un projet sur [console.cloud.google.com](https://console.cloud.google.com/)
2. Activer Gmail API et Google Calendar API
3. Créer des identifiants OAuth 2.0 (type : Application de bureau)
4. Télécharger le fichier JSON → le sauvegarder dans `~/.gauth.json`
5. Créer `.mcp.json` à la racine du projet :
   ```json
   {
     "mcpServers": {
       "google-workspace": {
         "type": "stdio",
         "command": "npx",
         "args": ["-y", "mcp-google-workspace"],
         "env": {
           "GOOGLE_OAUTH_CREDENTIALS": "/Users/antoineachach/.gauth.json"
         }
       }
     }
   }
   ```
6. Lancer `/mcp` dans Claude Code → s'authentifier via le navigateur

> Ce soir, on a utilisé les MCP `claude.ai Gmail` et `claude.ai Google Calendar`
> (intégration native Claude.ai, sans passer par les étapes ci-dessus).

## Erreurs rencontrées

| Erreur | Cause | Solution |
|--------|-------|----------|
| `command not found: code` | La commande VS Code n'est pas dans le PATH | Utiliser `open -a "Visual Studio Code" .` ou installer via Cmd+Shift+P |

## Patterns de prompts

### Donner du contexte avant de demander une action
> "Maintenant que Gmail et Google Calendar sont connectés, construis une page HTML…"

Fonctionne bien car Claude sait quels outils sont disponibles avant d'agir.

---

### Décrire le résultat attendu, pas les étapes
> "…qui affiche : les 5 derniers emails importants, les événements du jour, et une section Priorités du jour générée par Claude en analysant tout ça."

Laisser Claude choisir comment structurer la page plutôt que de dicter le HTML.

---

### Demander une mise à jour documentée en fin de session
> "Mets à jour mon fichier NOTES.md avec un résumé de ce qu'on a fait ce soir."

Bonne pratique pour consolider les apprentissages sans effort.

---

### Déléguer l'analyse à un agent spécialisé
> "@brief-enhancer analyse mon brief.html d'aujourd'hui et donne-moi 3 actions concrètes pour la journée"

Fonctionne bien pour obtenir une analyse ciblée sans sur-expliquer. L'agent `morning-brief-optimizer` distingue automatiquement le signal du bruit et produit des actions actionnables.

## Leçons apprises ce soir

1. **Un projet utile peut démarrer en une soirée sans écrire une seule ligne de code.**
   En connectant deux MCP servers (Gmail + Google Calendar), on a construit un vrai outil personnel — brief quotidien, analyse de priorités, page HTML — uniquement avec des prompts et des fichiers de config.

2. **Les MCP servers transforment Claude en agent connecté à tes données réelles.**
   Sans MCP, Claude répond à des questions générales. Avec MCP, il lit tes vrais emails, ton vrai calendrier, et raisonne sur ta situation concrète du jour.

3. **La structure du projet compte autant que le code.**
   `CLAUDE.md` donne la mémoire du projet, `NOTES.md` capitalise les apprentissages, `.mcp.json` déclare les outils. Ces fichiers font que le projet est reproductible et que chaque prochaine session repart de là où on s'est arrêtés.

## Agents personnalisés

| Agent | Rôle | Comment l'invoquer |
|-------|------|--------------------|
| `morning-brief-optimizer` | Analyse un brief/liste de tâches et extrait 3 priorités concrètes | `@morning-brief-optimizer <contexte>` |

> Créer un agent : `/agents` dans Claude Code.
