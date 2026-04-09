# uigen — Instruções do Projeto

Gerador de componentes React via chat com Claude AI. O usuário descreve uma UI em linguagem natural; o AI gera/edita arquivos num sistema de arquivos virtual que renderiza ao vivo num iframe.

## Stack
- Next.js 15 + React 19 + TypeScript
- Tailwind CSS v4 + shadcn/ui (Radix)
- Prisma + SQLite (`prisma/dev.db`)
- AI SDK (`ai`, `@ai-sdk/anthropic`) com streaming
- Monaco Editor
- Auth: bcrypt + jose (JWT)

## Fluxo principal
```
Chat (usuário) → POST /api/chat → streamText (Claude)
  → tools: str_replace_editor / file_manager
  → VirtualFileSystem (em memória, no contexto React)
  → PreviewFrame (iframe) / CodeEditor (Monaco)
  → onFinish: salva messages + data no Prisma (se autenticado)
```

## Arquivos críticos
| Arquivo | Responsabilidade |
|---|---|
| `src/app/api/chat/route.ts` | Endpoint de streaming; registra tools; salva no DB |
| `src/lib/file-system.ts` | Classe `VirtualFileSystem` (serialize/deserialize) |
| `src/lib/contexts/file-system-context.tsx` | Estado global do FS; handler de tool calls |
| `src/lib/contexts/chat-context.tsx` | Wrapper do `useChat` do AI SDK |
| `src/lib/tools/str-replace.ts` | Tool `str_replace_editor` |
| `src/lib/tools/file-manager.ts` | Tool `file_manager` |
| `src/lib/prompts/generation.ts` | System prompt enviado ao Claude |
| `src/lib/provider.ts` | Seleciona modelo (real ou mock) |
| `src/components/preview/PreviewFrame.tsx` | Iframe de preview ao vivo |
| `src/components/editor/` | FileTree + CodeEditor (Monaco) |
| `src/actions/` | Server actions: auth, CRUD de projetos |
| `prisma/schema.prisma` | Modelos: User, Project — **ler sempre que precisar entender a estrutura do banco** |

## Dev
```bash
npm run dev        # Next.js + Turbopack
npm run test       # Vitest
npm run db:reset   # Reset do banco SQLite
```

`ANTHROPIC_API_KEY` já está no `.env`. Sem a key, cai no mock provider (4 steps max).

## Schema do banco (Prisma/SQLite)

> Sempre leia `prisma/schema.prisma` antes de qualquer tarefa que envolva banco de dados.

```
User
├── id        String   @id @default(cuid())
├── email     String   @unique
├── password  String
├── createdAt DateTime
├── updatedAt DateTime
└── projects  Project[]

Project
├── id        String   @id @default(cuid())
├── name      String
├── userId    String?  (FK → User, opcional — projetos anônimos não têm dono)
├── messages  String   @default("[]")   ← JSON serializado
├── data      String   @default("{}")   ← JSON serializado (VirtualFileSystem)
├── createdAt DateTime
├── updatedAt DateTime
└── user      User?    @relation(onDelete: Cascade)
```

Relação: `User` 1 ──── 0..N `Project` (cascade delete)

## Regras
- Arquivos temporários vão em `.tmp/` (podem ser apagados)
- Deliverables ficam no banco (Prisma) ou na nuvem
- Usuários anônimos podem usar sem login — trabalho rastreado em `anon-work-tracker.ts`
- Ao modificar o schema Prisma, rodar `npx prisma migrate dev`
