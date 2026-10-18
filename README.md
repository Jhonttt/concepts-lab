# concepts-lab

Laboratorio personal para aprender **conceptos de ingeniería de software**, no un lenguaje concreto.

Cada concepto se estudia, se entiende y se implementa **a mano, sin ayuda de IA**. El objetivo es entender el *porqué* y el *cómo* de cada técnica, de forma que pueda aplicarla en cualquier lenguaje o stack.

## Reglas

1. **Sin IA para implementar.** La IA no escribe código de este repo. Documentación oficial, libros, artículos y código fuente de librerías sí.
2. **Entender antes de escribir.** Primero se explica el concepto con mis propias palabras en su `NOTES.md`.
3. **Implementación mínima propia.** Construir la versión más simple que funcione antes de usar una librería que lo resuelva.
4. **Demostrarlo.** Cada concepto lleva una prueba o demo que muestre que funciona (y cuándo falla).
5. **Anotar los trade-offs.** Qué gano, qué pierdo y cuándo *no* usarlo.

## Estructura

Un único proyecto Bun en la raíz y una carpeta por concepto:

```
concepts-lab/
├── package.json          # dependencias y scripts de todo el repo
├── bun.lock              # versiones exactas instaladas
├── tsconfig.json         # configuración de TypeScript
├── .gitignore
├── README.md
├── node_modules/         # dependencias instaladas (no se sube)
└── concepts/
    ├── _TEMPLATE.md
    ├── 01-debounce/
    │   ├── NOTES.md              # copia de _TEMPLATE.md rellenada
    │   ├── src/
    │   │   └── debounce.ts       # implementación
    │   └── tests/
    │       └── debounce.test.ts  # pruebas
    ├── 02-throttle/
    └── 03-event-loop/
```

- **Un solo `package.json`:** una instalación y una configuración para todo el repo.
- **Prefijo numérico:** mantiene el orden de aprendizaje.
- **`src/` y `tests/` separados:** la implementación por un lado y la prueba de que funciona por otro.
- **Librerías por concepto** (por ejemplo `ioredis` para Redis): se instalan en la raíz y solo se usan en su carpeta.

## Setup

### Requisitos

- **[Bun](https://bun.sh) ≥ 1.3** (runtime, gestor de paquetes y runner de pruebas en uno).

Instalación de Bun en Windows (PowerShell):

```powershell
powershell -c "irm bun.sh/install.ps1 | iex"
```

En Linux/macOS: `curl -fsSL https://bun.sh/install | bash`.

### Instalación

```bash
git clone git@github.com:Jhonttt/concepts-lab.git
cd concepts-lab
bun install
```

`bun install` lee `package.json` y `bun.lock` e instala las dependencias de desarrollo en `node_modules/`:

- `typescript`: compilador `tsc`, revisa los tipos.
- `@types/bun`: tipos de las APIs de Bun y de Node (`Bun.serve`, `setTimeout`, `process`, `fs`…).

Bun ejecuta `.ts` directamente, así que no hace falta `tsx` ni compilar. **Bun no revisa tipos**, solo los elimina y ejecuta: para eso está `tsc`.

### Comandos

| Comando | Qué hace |
|---------|----------|
| `bun concepts/01-debounce/src/debounce.ts` | Ejecuta el archivo una vez |
| `bun run dev concepts/01-debounce/src/debounce.ts` | Ejecuta el archivo y lo relanza al guardar (`bun --watch`) |
| `bun test` | Busca y ejecuta todos los `*.test.ts` del repo |
| `bun test concepts/01-debounce` | Ejecuta solo las pruebas de un concepto |
| `bun test --watch` | Relanza las pruebas al guardar |
| `bun run typecheck` | Revisa los tipos de todo el repo (`tsc --noEmit`) |

> `bun test` es el runner integrado de Bun, no un script de `package.json`. No confundir con `bun run test`.

### Añadir un concepto nuevo

1. Crear la carpeta con prefijo numérico: `concepts/01-debounce/`.
2. Copiar `concepts/_TEMPLATE.md` como `NOTES.md` y rellenarlo.
3. Implementar en `src/` y probar en `tests/`.

Ejemplo de prueba con `bun:test` (API estilo Jest; los imports locales llevan extensión `.ts`):

```ts
import { test, expect } from "bun:test";
import { debounce } from "../src/debounce.ts";

test("ejemplo", () => {
  expect(typeof debounce).toBe("function");
});
```

### Cómo se creó este setup

Pasos seguidos para montar el proyecto desde cero (no hace falta repetirlos, ya están en el repo):

1. Crear `package.json` a mano con `"type": "module"` (usar `import`/`export`) y los scripts:

   ```json
   "scripts": {
     "dev": "bun --watch",
     "typecheck": "tsc --noEmit"
   }
   ```

   Alternativa: `bun init -y`, que además genera `index.ts`, `tsconfig.json` y su propio README.

2. Instalar las dependencias de desarrollo:

   ```bash
   bun add -d typescript @types/bun
   ```

   `-d` (`--dev`) las guarda como `devDependencies`. Genera `node_modules/` y `bun.lock` (lockfile en texto, se sube al repo).

3. Crear `tsconfig.json` con la configuración recomendada para Bun:

   ```json
   {
     "compilerOptions": {
       "target": "ESNext",
       "lib": ["ESNext"],
       "module": "Preserve",
       "moduleResolution": "bundler",
       "types": ["bun"],
       "strict": true,
       "noEmit": true,
       "allowImportingTsExtensions": true,
       "verbatimModuleSyntax": true,
       "skipLibCheck": true
     },
     "include": ["concepts"]
   }
   ```

   - `strict`: todas las comprobaciones estrictas de tipos.
   - `noEmit`: `tsc` solo revisa tipos, no genera `.js` (de ejecutar se encarga Bun).
   - `module: "Preserve"` + `moduleResolution: "bundler"`: deja los imports tal cual y los resuelve como Bun.
   - `types: ["bun"]`: carga los tipos de `@types/bun` (TypeScript 6+ ya no los carga automáticamente).
   - `allowImportingTsExtensions`: permite `import ... from "./x.ts"`.
   - `verbatimModuleSyntax`: obliga a usar `import type` para imports que solo son tipos.

4. Comprobar que todo funciona: `bun test` y `bun run typecheck`.

## Roadmap

### Asincronía y concurrencia
- [ ] Event loop
- [ ] Promises / async-await internamente
- [ ] Concurrencia vs paralelismo
- [ ] Race conditions y locks / mutex
- [ ] Worker threads / pools
- [ ] Backpressure y streams

### Control de flujo
- [ ] Debounce
- [ ] Throttle
- [ ] Rate limiting: fixed window, sliding window, token bucket, leaky bucket
- [ ] Retries con exponential backoff y jitter
- [ ] Timeouts y cancelación
- [ ] Circuit breaker

### Caché y datos
- [ ] Redis: estructuras de datos básicas
- [ ] Patrones de caché: cache-aside, write-through, write-behind
- [ ] TTL e invalidación de caché
- [ ] LRU cache desde cero
- [ ] Locks distribuidos
- [ ] Pub/Sub
- [ ] Connection pooling

### Fiabilidad
- [ ] Idempotencia e idempotency keys
- [ ] Semánticas de entrega: at-most-once, at-least-once, exactly-once
- [ ] Colas de mensajes y dead letter queues
- [ ] Outbox pattern
- [ ] Graceful shutdown

### Sistemas distribuidos
- [ ] Consistent hashing
- [ ] Sharding y replicación
- [ ] Teorema CAP y consistencia eventual
- [ ] Leader election

### APIs y otros
- [ ] Paginación offset vs cursor
- [ ] WebSockets y Server-Sent Events
- [ ] Observabilidad: logs, métricas y trazas

## Lenguaje

**TypeScript con Bun.**

- Bun es compatible con la API de Node y tiene su propio event loop, así que se puede observar y romper de primera mano.
- Debounce y throttle son nativos del ecosistema JavaScript.
- Rate limiting, idempotencia o circuit breaker encajan de forma natural como middleware HTTP.
- Buen soporte para Redis y fácil de levantar con Docker.
- El tipado obliga a modelar bien los estados de cada concepto.

Los conceptos siguen siendo independientes del lenguaje: lo importante es entenderlos, no la sintaxis.
