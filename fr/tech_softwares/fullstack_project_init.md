# Documentation : Création d'un Projet Fullstack Node.js / React

## Objectif

Ce guide fournit une méthode standardisée pour démarrer un projet fullstack avec Node.js et React, en utilisant des outils modernes tels qu'ESLint, Prettier, et Husky. Cette version inclut une configuration complète pour garantir une qualité de code élevée dès le début et un exemple concret d’interconnexion backend/frontend.

---

## Prérequis

Avant de commencer, assurez-vous d’avoir installé :

1. **Node.js (v18 ou plus récent)** : [Télécharger Node.js](https://nodejs.org/)
2. **npm ou Yarn** : npm est installé avec Node.js. Pour installer Yarn : `npm install -g yarn`
3. **Git** : [Télécharger Git](https://git-scm.com/downloads)
4. **Visual Studio Code** : [Télécharger VS Code](https://code.visualstudio.com/)

Pour **Manjaro** :

- Installez Node.js et npm : `sudo pamac install nodejs npm`
- Installez Git : `sudo pamac install git`
- Installez VS Code (Code OSS) : `sudo pamac install code`

---

## Étape 1 : Initialiser le Projet Backend avec Node.js

### 1. Créer le projet backend

```bash
mkdir mon-projet-fullstack
cd mon-projet-fullstack
mkdir backend
cd backend
npm init -y
```

### 2. Installer les dépendances nécessaires

```bash
npm install express cors dotenv
npm install --save-dev typescript @types/node @types/express ts-node nodemon
```

### 3. Configurer TypeScript

```bash
npx tsc --init
```

Modifiez `tsconfig.json` :

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true
  }
}
```

### 4. Ajouter un endpoint `/api/message`

Créez le dossier `src` et ajoutez `src/index.ts` :

```typescript
import express from "express";
import cors from "cors";

const app = express();
app.use(cors());
app.use(express.json());

// Nouveau endpoint
app.get("/api/message", (req, res) => {
  res.json({ message: "Hello from the backend!" });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

### 5. Ajouter Nodemon

Créez un fichier `nodemon.json` :

```json
{
  "watch": ["src"],
  "ext": "ts",
  "exec": "ts-node src/index.ts"
}
```

Ajoutez le script dans `package.json` :

```json
"scripts": {
  "start": "tsc && node dist/index.js",
  "dev": "nodemon",
  "test": "jest"
}
```

---

## Étape 2 : Initialiser le Projet Frontend avec React et Vite

### 1. Créer le frontend avec Vite

```bash
cd ..
npm create vite@latest frontend -- --template react-ts
cd frontend
```

### 2. Installer les dépendances nécessaires

```bash
npm install @mui/material @emotion/react @emotion/styled
npm install --save-dev eslint prettier eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-react-refresh typescript-eslint @eslint/js globals husky @testing-library/react @testing-library/jest-dom
```

### 3. Configurer Husky

Initialisez Husky :

```bash
npx husky init
```

Ajoutez le contenu au fichier généré `.husky/pre-commit` :

```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run lint:fix
```

### 4. Ajouter les scripts dans `package.json`

Assurez-vous que les scripts suivants sont présents :

```json
"scripts": {
  "dev": "vite",
  "build": "tsc -b && vite build",
  "lint": "eslint .",
  "lint:fix": "eslint . --fix",
  "preview": "vite preview",
  "prepare": "husky",
  "test": "jest"
},
```

### 5. Mettre à jour `App.tsx` pour interagir avec le backend

Modifiez `App.tsx` pour inclure un appel à l’API backend :

```tsx
import React, { useEffect, useState } from "react";
import axios from "axios";

const App: React.FC = () => {
  const [message, setMessage] = useState<string>("");

  useEffect(() => {
    axios
      .get("http://localhost:5000/api/message")
      .then((response) => {
        setMessage(response.data.message);
      })
      .catch((error) => {
        console.error("Error fetching message:", error);
      });
  }, []);

  return (
    <div style={{ textAlign: "center", marginTop: "20px" }}>
      <h1>Fullstack Project Example</h1>
      <p>{message || "Loading..."}</p>
    </div>
  );
};

export default App;
```

---

## Étape 3 : Configurer les Tests

### 1. Configurer Jest pour le Backend

Installez Jest :

```bash
npm install --save-dev jest ts-jest @types/jest
```

Configurez Jest :

```bash
npx ts-jest config:init
```

### 2. Configurer Testing Library pour le Frontend

Installez Testing Library :

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

Exemple de test :

```typescript
import { render, screen } from "@testing-library/react";
import App from "../App";

test("renders the App component", () => {
  render(<App />);
  const linkElement = screen.getByText(/learn react/i);
  expect(linkElement).toBeInTheDocument();
});
```

---

## Résultat attendu

- **Backend** : Un endpoint `/api/message` qui renvoie un message JSON.
- **Frontend** : Un appel API via Axios pour récupérer ce message et l'afficher.

---

## Résumé des Changements Automatisés

- **Husky** : Garantit le linting automatique avant chaque commit.
- **ESLint + Prettier** : Analyse et formatage du code pour garantir une qualité élevée.
- **Testing Library** : Tests unitaires pour React, complétés par Jest pour le backend.

---

## Conclusion

En suivant ce guide, vous avez un projet fullstack moderne avec un backend Node.js, un frontend React, et une configuration prête pour le linting, le formatage, les tests, et l’interconnexion backend/frontend.
