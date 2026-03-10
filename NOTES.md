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

## Agents personnalisés

| Agent | Rôle | Comment l'invoquer |
|-------|------|--------------------|
| `morning-brief-optimizer` | Analyse un brief/liste de tâches et extrait 3 priorités concrètes | `@morning-brief-optimizer <contexte>` |

> Créer un agent : `/agents` dans Claude Code.
