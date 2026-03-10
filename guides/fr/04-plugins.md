# Plugins (Serveurs MCP)

Claude Code etend ses capacites grace aux plugins MCP (Model Context Protocol). Ceux-ci donnent a Claude acces a des outils et services externes.

## Plugins recommandes

### GitHub (INDISPENSABLE)

Accedez aux issues GitHub, pull requests, recherche de code et gestion de repositories directement depuis Claude.

```bash
claude mcp add github -- npx -y @anthropic-ai/mcp-remote@latest https://api.githubcopilot.com/mcp/
```

**Ce que cela permet :**
- Lire et creer des issues
- Creer et examiner des pull requests
- Rechercher du code dans les repositories
- Gerer les branches et les releases

### Context7 (INDISPENSABLE)

Obtenez la documentation a jour de n'importe quelle bibliotheque, directement dans le contexte.

```bash
claude mcp add context7 -- npx -y @anthropic-ai/mcp-remote@latest https://mcp.context7.com/mcp
```

**Ce que cela permet :**
- Consulter la documentation API actuelle de n'importe quel package npm/pip/maven
- Plus d'APIs hallucinees -- Claude verifie la documentation reelle
- Particulierement utile pour les bibliotheques qui evoluent rapidement

### Claude Memory (RECOMMANDE)

Memoire persistante cross-session. Claude retient les decisions, les patterns et le contexte entre les conversations.

```bash
claude mcp add claude-mem -- npx -y @anthropic-ai/mcp-remote@latest https://mcp.claude-mem.com/mcp
```

**Ce que cela permet :**
- Sauvegarder les decisions et patterns importants
- Rechercher dans le travail passe a travers les sessions
- Construire progressivement la connaissance du projet
- Eviter de redecouvrir les memes choses

### Playwright (OPTIONNEL)

Automatisation du navigateur pour le test et le debogage d'applications web.

```bash
claude mcp add playwright -- npx -y @anthropic-ai/mcp-remote@latest https://mcp.playwright.dev/mcp
```

**Ce que cela permet :**
- Naviguer sur des pages web, remplir des formulaires, cliquer sur des boutons
- Prendre des captures d'ecran pour verification visuelle
- Executer des tests en navigateur
- Deboguer des problemes frontend

## Gestion des plugins

```bash
# Lister les plugins installes
claude mcp list

# Supprimer un plugin
claude mcp remove <name>

# Les configurations des plugins sont stockees dans ~/.claude.json
```

## Conseils

- Les plugins sont disponibles automatiquement dans toutes les conversations
- Ils consomment du contexte lorsqu'ils sont utilises, donc Claude ne les invoque que lorsque c'est pertinent
- Vous pouvez installer des serveurs MCP supplementaires -- consultez [modelcontextprotocol.io](https://modelcontextprotocol.io) pour le registre
