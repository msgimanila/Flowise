<!-- markdownlint-disable MD030 -->
 Looking at your repo, Flowise uses:

**Frontend (`packages/ui`)** — React + Vite
**Backend (`packages/server`)** — Node.js + Express + TypeORM

---

## The critical issue I also notice

Your branch is **475 commits behind** `FlowiseAI/Flowise:main` — that's very far behind. You're on an old version of Flowise which may have bugs that are already fixed upstream.

Also I can see `vercel.json` was created inside the `packages` folder (the commit says "Create vercel.json" under packages directory) — it needs to be at the **root** level alongside `package.json`, `Dockerfile`, etc.

---

## Back to your question — best deployment path

Since Flowise is **React + Node.js/Express**, Vercel alone won't run it fully. The backend needs a proper server environment.

**Easiest full solution — Railway (free tier):**

1. Go to [railway.app](https://railway.app) → Login with GitHub
2. **New Project** → **Deploy from GitHub repo** → select `msgimanila/Flowise`
3. Railway auto-detects Node.js and runs it
4. Add **PostgreSQL** plugin inside Railway
5. Set your ENV vars (`DATABASE_*`, `FLOWISE_USERNAME`, `FLOWISE_PASSWORD`, `FLOWISE_SECRETKEY_OVERWRITE`)
6. Deploy — both frontend and backend run together on one URL

No split deployment needed, no `vercel.json` needed, no build config headaches. Want me to walk you through it step by step?
<p align="center"> 
<img src="https://github.com/FlowiseAI/Flowise/blob/main/images/flowise_white.svg#gh-light-mode-only">
<img src="https://github.com/FlowiseAI/Flowise/blob/main/images/flowise_dark.svg#gh-dark-mode-only">
</p>


Yes, Railway handles the database too. You have two options:

---

## Option 1 — Keep Supabase (Recommended since you already set it up)

```
GitHub Repo
    ↓
Vercel (Frontend) ←→ Railway (Backend) ←→ Supabase (Database)
```

Railway just connects to your existing Supabase Postgres using the same `DATABASE_*` ENV vars you already configured. Nothing changes on the Supabase side.

Just add these ENV vars in Railway:
```
DATABASE_TYPE=postgres
DATABASE_HOST=db.xxxx.supabase.co
DATABASE_PORT=5432
DATABASE_USER=postgres
DATABASE_PASSWORD=yourpassword
DATABASE_NAME=postgres
DATABASE_SSL=true
FLOWISE_USERNAME=admin
FLOWISE_PASSWORD=yourpassword
FLOWISE_SECRETKEY_OVERWRITE=yourkey
```

---

## Option 2 — Use Railway's built-in Postgres

Railway can provision its own Postgres database with one click — but since you already have Supabase set up and working, there's no reason to switch.

---

## Your final architecture

| Service | Platform | Purpose |
|---|---|---|
| Frontend UI | Vercel | React app users visit |
| Backend API | Railway | Node.js Express server |
| Database | Supabase | Stores all flows, credentials, chat history |
| File Storage | S3 (optional) | Uploaded files |

---

Since you already have Supabase configured, **Option 1 is the path of least resistance** — Railway just plugs into it. Ready to set up Railway?

Yes! The drag and drop workflow builder will work perfectly. Here's why:

---

## How it works visually

```
Your Browser
     ↓
Vercel (serves the React UI)
     ↓  ↑
Railway (backend API handles all the logic)
     ↓  ↑
Supabase (saves your workflows/flows)
```

When you drag and drop nodes in the canvas:
1. **Vercel** serves the visual UI (the canvas, nodes, connections)
2. Every action (save flow, load nodes, run chain) calls the **Railway** backend API
3. **Supabase** stores your saved flows permanently

---

## Everything that will work ✅

- Drag and drop canvas
- Adding/connecting nodes
- Saving chatflows
- Creating credentials (OpenAI keys etc.)
- Running/testing flows
- Chat interface
- Document store
- API endpoints
- Everything Flowise normally does

---

## The only thing Vercel does

Vercel **only** serves the HTML/JS/CSS files to your browser. Once loaded, your browser talks **directly to Railway** for all the actual functionality. Vercel is essentially just a CDN for the UI files.

---

## One important thing to check

In your `packages/ui` code there should be a config file that sets the API base URL. We need to make sure `VITE_BASE_URL` is properly set in Vercel pointing to your Railway URL — otherwise the UI won't know where to send API calls.

Ready to set up Railway so we can get the full thing working?

[![Release Notes](https://img.shields.io/github/release/FlowiseAI/Flowise)](https://github.com/FlowiseAI/Flowise/releases)
[![Discord](https://img.shields.io/discord/1087698854775881778?label=Discord&logo=discord)](https://discord.gg/jbaHfsRVBW)
[![Twitter Follow](https://img.shields.io/twitter/follow/FlowiseAI?style=social)](https://twitter.com/FlowiseAI)
[![GitHub star chart](https://img.shields.io/github/stars/FlowiseAI/Flowise?style=social)](https://star-history.com/#FlowiseAI/Flowise)
[![GitHub fork](https://img.shields.io/github/forks/FlowiseAI/Flowise?style=social)](https://github.com/FlowiseAI/Flowise/fork)

English | [繁體中文](./i18n/README-TW.md) | [简体中文](./i18n/README-ZH.md) | [日本語](./i18n/README-JA.md) | [한국어](./i18n/README-KR.md)

<h3>Build AI Agents, Visually</h3>
<a href="https://github.com/FlowiseAI/Flowise">
<img width="100%" src="https://github.com/FlowiseAI/Flowise/blob/main/images/flowise_agentflow.gif?raw=true"></a>

## ⚡Quick Start

Download and Install [NodeJS](https://nodejs.org/en/download) >= 18.15.0

1. Install Flowise
    ```bash
    npm install -g flowise
    ```
2. Start Flowise

    ```bash
    npx flowise start
    ```

3. Open [http://localhost:3000](http://localhost:3000)

## 🐳 Docker

### Docker Compose

1. Clone the Flowise project
2. Go to `docker` folder at the root of the project
3. Copy `.env.example` file, paste it into the same location, and rename to `.env` file
4. `docker compose up -d`
5. Open [http://localhost:3000](http://localhost:3000)
6. You can bring the containers down by `docker compose stop`

### Docker Image

1. Build the image locally:
    ```bash
    docker build --no-cache -t flowise .
    ```
2. Run image:

    ```bash
    docker run -d --name flowise -p 3000:3000 flowise
    ```

3. Stop image:
    ```bash
    docker stop flowise
    ```

## 👨‍💻 Developers

Flowise has 3 different modules in a single mono repository.

-   `server`: Node backend to serve API logics
-   `ui`: React frontend
-   `components`: Third-party nodes integrations
-   `api-documentation`: Auto-generated swagger-ui API docs from express

### Prerequisite

-   Install [PNPM](https://pnpm.io/installation)
    ```bash
    npm i -g pnpm
    ```

### Setup

1.  Clone the repository

    ```bash
    git clone https://github.com/FlowiseAI/Flowise.git
    ```

2.  Go into repository folder

    ```bash
    cd Flowise
    ```

3.  Install all dependencies of all modules:

    ```bash
    pnpm install
    ```

4.  Build all the code:

    ```bash
    pnpm build
    ```

    <details>
    <summary>Exit code 134 (JavaScript heap out of memory)</summary>  
      If you get this error when running the above `build` script, try increasing the Node.js heap size and run the script again:

        export NODE_OPTIONS="--max-old-space-size=4096"
        pnpm build

    </details>

5.  Start the app:

    ```bash
    pnpm start
    ```

    You can now access the app on [http://localhost:3000](http://localhost:3000)

6.  For development build:

    -   Create `.env` file and specify the `VITE_PORT` (refer to `.env.example`) in `packages/ui`
    -   Create `.env` file and specify the `PORT` (refer to `.env.example`) in `packages/server`
    -   Run

        ```bash
        pnpm dev
        ```

    Any code changes will reload the app automatically on [http://localhost:8080](http://localhost:8080)

## 🌱 Env Variables

Flowise support different environment variables to configure your instance. You can specify the following variables in the `.env` file inside `packages/server` folder. Read [more](https://github.com/FlowiseAI/Flowise/blob/main/CONTRIBUTING.md#-env-variables)

## 📖 Documentation

[Flowise Docs](https://docs.flowiseai.com/)

## 🌐 Self Host

Deploy Flowise self-hosted in your existing infrastructure, we support various [deployments](https://docs.flowiseai.com/configuration/deployment)

-   [AWS](https://docs.flowiseai.com/configuration/deployment/aws)
-   [Azure](https://docs.flowiseai.com/configuration/deployment/azure)
-   [Digital Ocean](https://docs.flowiseai.com/configuration/deployment/digital-ocean)
-   [GCP](https://docs.flowiseai.com/configuration/deployment/gcp)
-   [Alibaba Cloud](https://computenest.console.aliyun.com/service/instance/create/default?type=user&ServiceName=Flowise社区版)
-   <details>
      <summary>Others</summary>

    -   [Railway](https://docs.flowiseai.com/configuration/deployment/railway)

        [![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/pn4G8S?referralCode=WVNPD9)

    -   [Render](https://docs.flowiseai.com/configuration/deployment/render)

        [![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://docs.flowiseai.com/configuration/deployment/render)

    -   [HuggingFace Spaces](https://docs.flowiseai.com/deployment/hugging-face)

        <a href="https://huggingface.co/spaces/FlowiseAI/Flowise"><img src="https://huggingface.co/datasets/huggingface/badges/raw/main/open-in-hf-spaces-sm.svg" alt="HuggingFace Spaces"></a>

    -   [Elestio](https://elest.io/open-source/flowiseai)

        [![Deploy on Elestio](https://elest.io/images/logos/deploy-to-elestio-btn.png)](https://elest.io/open-source/flowiseai)

    -   [Sealos](https://template.sealos.io/deploy?templateName=flowise)

        [![Deploy on Sealos](https://sealos.io/Deploy-on-Sealos.svg)](https://template.sealos.io/deploy?templateName=flowise)

    -   [RepoCloud](https://repocloud.io/details/?app_id=29)

        [![Deploy on RepoCloud](https://d16t0pc4846x52.cloudfront.net/deploy.png)](https://repocloud.io/details/?app_id=29)

      </details>

## ☁️ Flowise Cloud

[Get Started with Flowise Cloud](https://flowiseai.com/)

## 🙋 Support

Feel free to ask any questions, raise problems, and request new features in [discussion](https://github.com/FlowiseAI/Flowise/discussions)

## 🙌 Contributing

Thanks go to these awesome contributors

<a href="https://github.com/FlowiseAI/Flowise/graphs/contributors">
<img src="https://contrib.rocks/image?repo=FlowiseAI/Flowise" />
</a>

See [contributing guide](CONTRIBUTING.md). Reach out to us at [Discord](https://discord.gg/jbaHfsRVBW) if you have any questions or issues.
[![Star History Chart](https://api.star-history.com/svg?repos=FlowiseAI/Flowise&type=Timeline)](https://star-history.com/#FlowiseAI/Flowise&Date)

## 📄 License

Source code in this repository is made available under the [Apache License Version 2.0](LICENSE.md).
