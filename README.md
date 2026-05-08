# Lapse Archive
>[!CAUTION]
>
>**TECHNICAL AUDIT NOTICE**
>
>This repository is **a static archive** preserved for forensic auditing and youth safeguarding oversight.
>
>All technical liability, privacy inquiries, or complaints regarding these operations **must be directed to Hack Club's leadership and engineering staff** via the contact information on the [Hack Club GitHub organization](https://github.com/orgs/hackclub/people) or on the official [Hack Club website](https://hackclub.com).

> [!IMPORTANT]
> 
> **LEGAL NOTICE & ARCHIVE PROTOCOL**
>
> * No Support of Content: The archiver **does not support, endorse, or condone** any viewpoints, ideologies, or organizational practices expressed, implied, or implemented within this repository.
>
> * No Warranties: This archive is **provided "as-is" for transparency and forensic purposes**; no warranties of any kind are provided regarding the functionality, safety, or legality of the contained code.
>
> * Non-Production Warning: **The archiver strictly does not condone the use of this software in a production environment and/or with real-world user data.** This code implements surveillance and profiling mechanisms that may violate international privacy standards and youth safeguarding regulations.
>
> * Purpose of Archive: **This repository is maintained strictly for security and privacy audits, legal documentation, historical archival, and forensic research using simulated/synthetic data.** It serves as a public interest case study in the degradation of nonprofit transparency and as a technical reference for the ethical failures of centralized youth surveillance infrastructure.
>
> * Non-Affiliation: The archiver (the owner of this fork) **is not a contributor to this project**, has **no affiliation with the original authors** (**including but not limited to** Hack Club employees/contractors on this repository, Hack Club, HCB, HCB employees/contractors on this repository, or the Hack Foundation), and **assumes no responsibility for the function or deployment of this code.**
>
> * Trademark Notice: All third-party trademarks mentioned (including but not limited to Hack Club, HCB, and The Hack Foundation) are the property of their respective owners. **The archiver claims no ownership rights over any third-party trademarks mentioned herein.**

> [!NOTE]
>
> Any links below the divider line link to Hack Club owned/operated servers on the internet. **I do not control the links below**, so visit them at your own risk using appropriate cybersecurity procedures as necessary.

---

Original repo description (unchanged):
<h1>
    <img height="64" src="./apps/client/src/assets/icon.svg">&nbsp;&nbsp;
    Hack Club Lapse
</h1>

[**Lapse**](https://lapse.hackclub.com) is a place for Hack Clubbers to record and share timelapses.
Integrates with [Hackatime](https://hackatime.hackclub.com)!
Lapse is currently in beta.

Think of it like a fancy WakaTime plugin.
Just as you can install WakaTime for VS Code, Lapse serves to be the WakaTime plugin for timelapses.

All timelapses are encrypted before being published.
That means that you (and *only* you!) can access them.
That way, we can synchronize your progress with our servers, while still making it possible for you to censor or remove anything you wouldn't want other people to see.

## 🧑‍💻 Development

In order to get started with developing Lapse, run these commands:
```bash
# Install all packages
pnpm install

# Set up the development environment (opens the environment variables wizard)
pnpm dev:setup-env

# Start the web client and backend
pnpm dev
```

Lapse relies on ffmpeg for its serverside video processing.
You can install ffmpeg [from their website](https://ffmpeg.org/download.html) by downloading the appropriate ffmpeg binary for your operating system.
To start and stop the development environment, use `pnpm dev:start-env` and `pnpm dev:stop-env` respectively.

When developing, it's a good idea to re-compile all packages on the fly!
```bash
pnpm dev:watch-all
```

If you're interested, you're welcome to build your own custom client!
See [`./docs/custom-clients.md`](./docs/custom-clients.md) for more info.

## 🛠️ Deployment

Lapse is meant to be deployed via Docker, featuring three main services out-of-the-box: `client`, `server`, and `worker`. 
- `client`: the web client server. Interfaces with `server` - use `Dockerfile.client` to deploy this service.
- `server`: the backend server. Use `Dockerfile.server` to deploy this service.
- `worker`: background job worker. This service is CPU-bound - it's a good idea to put it on a beefy server! Handles tasks like encoding. Use `Dockerfile.worker` to deploy this service.

Both `server` and `worker` interface via Redis.
You can put them on separate machines, as long as both have access to the same Redis server.
`server` additionally needs a PostgreSQL database.
Schemas are automatically applied - no need for any special setup.

In order to deploy the main frontend/backend microservice, use `Dockerfile.web`, located in the root of this repo.

For example - when deploying with Coolify, set these settings:
- `Base Directory`: `/`
- `Dockerfile Location`: `/Dockerfile.web`
- `Ports Exposes`: `3000`

You'll need at least one root user in order to promote other users to admins.
The recommended way to do this is via the `console.ts` script of `server`!

```sh
cd apps/server

# You'll be taken to a REPL after launching this!
pnpm dev:console
```

```ts
connect("postgresql://postgres:postgres@localhost:5432/lapse?schema=public") // you'll probably want your production database URL here!
await promoteUser("ascpixi@hackclub.com")
```
