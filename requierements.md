# Guide d'intégration d'Eliza dans Cline

Ce guide détaille les étapes nécessaires pour remplacer les LLMs (Large Language Models) intégrés dans Cline par Eliza.

## But principal

1. Enlever tous les LLMs (Large Language Models) intégrés ou configurés dans Cline.
2. Faire en sorte que Cline utilise Eliza comme seul modèle pour répondre aux requêtes.
3. Modifier le code de Cline pour appeler Eliza via ton serveur existant.

## Prérequis

- Node.js et npm installés
- Git installé
- Un fork du projet Cline
- Un serveur Eliza fonctionnel

## Étapes d'implémentation

### 1. Analyser les appels LLMs dans Cline

1. Cloner ton fork de Cline :
```bash
git clone https://github.com/<ton-utilisateur>/cline.git
cd cline
```

2. Repérer où Cline utilise des LLMs :
```bash
grep -r "LLM" .
```

Fichiers à surveiller :
- Tout ce qui fait référence à des modèles comme OpenAI, Hugging Face, ou Azure AI
- Les fichiers où une configuration de LLM peut être définie, comme `settings.json` ou `config.ts`

### 2. Supprimer les dépendances inutiles

1. Supprimer les dépendances de LLM dans le projet :
```bash
npm uninstall openai huggingface
```

2. Nettoyer le code :
Si tu vois quelque chose comme :
```typescript
import { OpenAI } from 'openai';

const llm = new OpenAI({ apiKey: '...' });
llm.generateResponse("question");
```
Remplacer ou commenter ce code, car nous allons appeler Eliza à la place.

### 3. Créer une fonction pour appeler ton serveur Eliza

Dans le dossier `src`, créer un fichier `elizaClient.ts` :

```typescript
import axios from 'axios';

export async function askEliza(question: string): Promise<string> {
    try {
        const response = await axios.post('http://localhost:5000/ask', { question });
        return response.data.answer; // On suppose que l'API d'Eliza renvoie une clé "answer"
    } catch (error) {
        console.error('Erreur lors de la requête à Eliza :', error);
        throw new Error('Impossible de contacter Eliza.');
    }
}
```

### 4. Connecter Eliza à Cline

Exemple d'utilisation dans les composants :

```typescript
import { askEliza } from './elizaClient';

async function handleUserQuery(userInput: string) {
    const elizaResponse = await askEliza(userInput);
    console.log('Eliza a répondu :', elizaResponse);
}
```

### 5. Personnaliser les paramètres de Cline

Dans le fichier de configuration (par exemple, `config.ts`) :

```typescript
export const elizaConfig = {
    serverUrl: 'http://localhost:5000'
};
```

### 6. Tester l'intégration

1. Démarrer Eliza et Cline en parallèle :
   - Eliza : Lancer ton serveur Discord comme d'habitude
   - Cline : Lancer ton fork :
```bash
yarn watch
./scripts/code.sh
```

2. Points de vérification :
   - Le serveur Eliza est bien en écoute
   - Les requêtes arrivent au bon endpoint (http://localhost:5000/ask)
   - Les logs côté Cline ne montrent pas d'erreurs

### 7. Améliorations et fonctionnalités avancées

1. Ajouter un historique des réponses :
   - Si tu veux conserver l'historique des questions/réponses dans Cline
   - Stocker dans un tableau ou une base de données locale

2. Créer une interface dédiée :
   - Possibilité d'ajouter une vue personnalisée dans Cline pour interagir avec Eliza

### 8. Mettre à jour ton dépôt

1. Valider tes modifications :
```bash
git add .
git commit -m "Intégration d'Eliza comme unique modèle"
git push origin main
```

2. Partager et tester avec d'autres pour s'assurer que tout fonctionne comme prévu.

## Support et débogage

Si des erreurs surviennent, vérifier que :
- Le serveur Eliza est bien en écoute
- Les requêtes atteignent le bon endpoint
- Les logs ne montrent pas d'erreurs
