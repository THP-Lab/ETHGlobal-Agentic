# ETHGlobal-Agentic

this is a repo for the ETHGlobal-Agentic hackathon

## Quick Start

```sh
git clone https://github.com/thp-lab/ETHGlobal-Agentic.git
```

```sh
cd ETHGlobal-Agentic
```

### Add submodules

```sh
 git submodule init
```

```sh
git submodule update
```

### Install and run Eliza

```sh
cd eliza
```

```sh
pnpm install --no-frozen-lockfile
```

```sh
pnpm build
```

Get an OPEN IA API key or Antrhopic API key and add it to the .env file

```sh
cp .env.example .env
```

Launch the Eliza agent with the Niki character

```sh
 pnpm --filter "@elizaos/agent" start --isRoot "--character=characters/niki/niki.character.json"
```

### Install and run the VSCode extension

```sh
npm run install:all
```

You can now use the Debugger extension to run the extension in a new VSCode window.
