React + Vite + Electron (Windows .exe)

This project packages a React (Vite) application into a Windows .exe using Electron and electron-builder.

Prerequisites

Node.js 18+

npm

Windows OS (for .exe build)

1. Create React + Vite project
npm create vite@latest electron-react
cd electron-react
npm install


Choose:

Framework: React

Variant: TypeScript

2. Install Electron dependencies
npm install --save-dev electron electron-builder concurrently wait-on

3. Rename Vite config

Rename the Vite config file to avoid ESM/CommonJS conflicts:

vite.config.ts → vite.config.mjs

4. Create Electron entry

Create a new folder in the project root:

electron/
  └── main.ts

electron/main.ts
import { app, BrowserWindow } from 'electron'
import path from 'path'

function createWindow() {
  const win = new BrowserWindow({
    width: 1000,
    height: 700,
    webPreferences: {
      contextIsolation: true
    }
  })

  win.loadFile(path.join(__dirname, '../dist/index.html'))
}

app.whenReady().then(createWindow)

5. Add Electron TypeScript config

Create tsconfig.electron.json in the root directory:

{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "outDir": "electron-dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["electron/**/*.ts"]
}

6. Update package.json
Add main entry & build config
{
  "main": "electron-dist/main.js",
  "build": {
    "appId": "com.yourapp.electronreact",
    "productName": "ElectronReactApp",
    "directories": {
      "output": "release"
    },
    "files": [
      "dist/**/*",
      "electron-dist/**/*"
    ],
    "win": {
      "target": "nsis"
    }
  }
}

Add scripts
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "build:electron": "tsc -p tsconfig.electron.json",
  "electron": "wait-on dist/index.html && electron .",
  "dev:electron": "concurrently \"npm run dev\" \"npm run electron\"",
  "dist": "npm run build && npm run build:electron && electron-builder"
}

7. Clean install (recommended)
rm -rf dist electron-dist release node_modules package-lock.json
npm install

8. Build Windows executable
npm run dist


The Windows .exe installer will be generated in:

release/

Build Flow Summary

Vite builds React app → dist/

TypeScript compiles Electron → electron-dist/

Electron loads React HTML

electron-builder packages everything → Windows .exe


=====================================================================================================


# React + TypeScript + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Babel](https://babeljs.io/) (or [oxc](https://oxc.rs) when used in [rolldown-vite](https://vite.dev/guide/rolldown)) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## React Compiler

The React Compiler is not enabled on this template because of its impact on dev & build performances. To add it, see [this documentation](https://react.dev/learn/react-compiler/installation).

## Expanding the ESLint configuration

If you are developing a production application, we recommend updating the configuration to enable type-aware lint rules:

```js
export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      // Other configs...

      // Remove tseslint.configs.recommended and replace with this
      tseslint.configs.recommendedTypeChecked,
      // Alternatively, use this for stricter rules
      tseslint.configs.strictTypeChecked,
      // Optionally, add this for stylistic rules
      tseslint.configs.stylisticTypeChecked,

      // Other configs...
    ],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
      // other options...
    },
  },
])
```

You can also install [eslint-plugin-react-x](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-x) and [eslint-plugin-react-dom](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-dom) for React-specific lint rules:

```js
// eslint.config.js
import reactX from 'eslint-plugin-react-x'
import reactDom from 'eslint-plugin-react-dom'

export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      // Other configs...
      // Enable lint rules for React
      reactX.configs['recommended-typescript'],
      // Enable lint rules for React DOM
      reactDom.configs.recommended,
    ],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
      // other options...
    },
  },
])
```
