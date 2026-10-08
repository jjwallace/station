# station

Monorepo for Jesse's small hosted platforms. Each one lives in its own folder
under `platforms/` and deploys as its own Railway service.

| Platform | Folder | What it is | Live at |
|---|---|---|---|
| Bird Trivia | [`platforms/bird-trivia`](platforms/bird-trivia) | Multiplayer bird trivia (Phaser 3 + Socket.IO) | https://triviabird.com |
| Rallly | [`platforms/rallly`](platforms/rallly) | Group scheduling polls ([upstream](https://github.com/lukevella/rallly)). Config only; runs the official Docker image | not deployed yet |

## How it deploys

The Railway project has one service per platform. Every service points at
this repo with its own **Root Directory** and **Watch Paths**, so a push that
only touches `platforms/bird-trivia/` redeploys only Bird Trivia.

Moving a service onto this repo: [docs/railway-reconfig.md](docs/railway-reconfig.md).

## History

`platforms/bird-trivia` came in with `git subtree add` from
[jjwallace/bird-trivia](https://github.com/jjwallace/bird-trivia) and keeps its
full commit history. Make new Bird Trivia changes here. The old repo stays
up until Railway is switched over (see the reconfig doc), then it gets archived.

## Adding a platform

```bash
# existing repo, keeping its history
git subtree add --prefix=platforms/<name> <repo-url> main
```

Then add a Railway service with Root Directory `/platforms/<name>` and Watch
Paths `/platforms/<name>/**`, and add a row to the table above.
