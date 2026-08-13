# SKILL_LOG.md — Integración 4Geeks Academy + OpenClaw

## Estudiante: Diego Ignacio Reveco Palominos
## Cohorte: Ai Engineering General (latam-aie-pt-2)
## Fecha: 2026-08-13

---

## Resumen

Se construyeron **6 skills** para que OpenClaw interactúe con la API de BreatheCode (4Geeks Academy) usando el token de estudiante almacenado de forma segura. Cada skill se creó mediante conversación con el agente, sin escribir código directamente.

---

## Skill 1 — Autenticación (`4geeks-auth`)

**Prompt inicial:**  
> "Quiero darte la habilidad de conectarte a mi cuenta de 4Geeks usando mi token de estudiante"

**¿Qué hace?**  
Verifica que el token de estudiante de 4Geeks sea válido consultando `GET /v1/admissions/user/me`. Extrae nombre, email, cohortes activos y roles del usuario.

**Endpoint(s) utilizados:**
- `GET /v1/admissions/user/me` (autenticado)

**Resultado de prueba:**
```
Email: revecodiego22@gmail.com
Nombre: Diego Ignacio Reveco Palominos
Cohortes activos: 33 (incluyendo latam-aie-pt-2, ai-engineering-general, etc.)
Roles: student en 4Geeks Latam (id=7) y 4Geeks Madrid (id=6)
```

**Token almacenado en:** Config segura de OpenClaw (`env.vars.TOKEN_4GEEKS`).

---

## Skill 2 — Obtener proyectos (`4geeks-projects`)

**Prompt inicial:**  
> "Ahora quiero que puedas listar mis proyectos asignados con su estado"

**¿Qué hace?**  
Recupera la lista de proyectos/tareas asignadas al estudiante desde `GET /v1/assignment/user/me/task`, con filtros por tipo, estado y cohorte.

**Endpoint(s) utilizados:**
- `GET /v1/assignment/user/me/task`

**Resultado de prueba:**
```
Total: 20 proyectos
- Code an Excuse Generator in JavaScript with Prompts | PENDING
- Showcase your friend's artist talent with a website | DONE | APPROVED
- Setting Up Your Personal AI Agent with OpenClaw | PENDING
- Cinema Seat Manager in TypeScript | DONE | APPROVED
- A simple Dashboard with Tailwind CSS | DONE | REJECTED
- Milestone 1 — Your Company's Public Website | DONE | REJECTED
- (y 14 más...)
```

---

## Skill 3 — Trabajo pendiente (`4geeks-pending`)

**Prompt inicial:**  
> "Dime específicamente qué me falta completar"

**¿Qué hace?**  
Filtra las tareas por estado `PENDING` (pendiente de entregar) y `REJECTED` (requiere corrección), combinando resultados para mostrar exactamente qué falta. Requiere pasar el `cohort` ID para filtrar correctamente.

**Endpoint(s) utilizados:**
- `GET /v1/assignment/user/me/task?task_status=PENDING&cohort=ID`
- `GET /v1/assignment/user/me/task?task_status=REJECTED&cohort=ID`

**Resultado de prueba (cohorte latam-aie-pt-2):**
```
PENDIENTES: 30 tareas
  Proyectos pendientes:
    - A simple Dashboard with Tailwind CSS
    - Todo List CLI with Python
    - Showcase your friend's artist talent with a website
    - Milestone 1 — Your Company's Public Website
    - Command Line Challenge
    - My first collaborative professional project
    - Cinema Seat Manager in TypeScript
  
  Ejercicios pendientes: 23 (HTML, CSS, Tailwind, Git, TypeScript, JavaScript, etc.)

RECHAZADOS: 0 en este cohorte
```

---

## Skill 4 — Resumen de progreso (`4geeks-progress`)

**Prompt inicial:**  
> "Quiero una visión general de cuánto he avanzado en el curso"

**¿Qué hace?**  
Obtiene todas las tareas del estudiante, las clasifica por estado y tipo, y calcula métricas de progreso: total, completadas, pendientes, rechazadas, y distribución por tipo de contenido.

**Endpoint(s) utilizados:**
- `GET /v1/assignment/user/me/task?limit=200`
- `GET /v1/activity/me`

**Resultado de prueba:**
```
Total tareas (global): 174
  PENDING: 142 (82%)
  DONE: 32 (18%)
  REJECTED: 0
  APPROVED: 0

Completadas: 32 / 174
Pendientes: 142

Por tipo:
  PROJECT: 30
  EXERCISE: 119
  LESSON: 25

Top cohortes:
  latam-aie-pt-2: 54
  latam-ai-engineering-introduction: 28
  latam-frontend-development-with-coding-agents: 20
  coding-fundamentals-with-typescript: 20
  web-ui-fundamentals-with-tailwind-css: 17
  (y 4 más...)
```

---

## Skill 5 — Buscar contenido (`4geeks-search`)

**Prompt inicial:**  
> "Busca contenido de OpenClaw"

**¿Qué hace?**  
Busca en el registry de 4Geeks por palabra clave, tipo de contenido, tecnología o dificultad. Puede listar syllabi públicos y encontrar assets (lecciones, ejercicios, proyectos).

**Endpoint(s) utilizados:**
- `GET /v1/registry/asset?like=KEYWORD&asset_type=TYPE&technologies=TECH&difficulty=DIFF`
- `GET /v1/admissions/public/syllabus` (público)
- `GET /v1/registry/asset/{slug}`
- `GET /v1/registry/technology`

**Resultado de prueba:**
```
Búsqueda: "OpenClaw" en syllabi públicos:
  - Creating personal assistants with Openclaw
  - Basic personal assistants with Openclaw
  - Advanced personal assistants with Openclaw

Búsqueda: proyectos "agent":
  - Wazuh: Installation and Endpoint Configuration (INTERMEDIATE)

Total syllabi disponibles: 124
```

---

## Skill 6 — Eventos próximos (`4geeks-events`)

**Prompt inicial:**  
> "Crea una skill para ver eventos próximos"

**¿Qué hace?**  
Obtiene el `academy_id` del usuario desde su perfil y consulta los eventos próximos de esa academia.

**Endpoint(s) utilizados:**
- `GET /v1/events/all?academy=ID&upcoming=true`

**Resultado de prueba:**
```
Academy: 4Geeks Latam (id=7), 4Geeks Madrid (id=6)
Eventos próximos: No hay eventos agendados actualmente (API responde correctamente con lista vacía)
```

---

## Detalles técnicos

### Token
- Almacenado en: `env.vars.TOKEN_4GEEKS` en `~/.openclaw/openclaw.json`
- También presente en: `~/.openclaw/.env`
- **Nunca hardcodeado** en ningún archivo de skill.

### Base URL
`https://breathecode.herokuapp.com`

### Autenticación
Header: `Authorization: Token <token>`

### Skills instaladas
Todas las skills residen en `~/.openclaw/workspace/skills/`:
- `4geeks-auth/SKILL.md`
- `4geeks-projects/SKILL.md`
- `4geeks-pending/SKILL.md`
- `4geeks-progress/SKILL.md`
- `4geeks-search/SKILL.md`
- `4geeks-events/SKILL.md`

---

## Notas

- El token se verificó exitosamente contra la API antes de cada skill.
- El endpoint `GET /v1/events/all` devolvió 0 eventos — puede deberse a que no hay eventos agendados para las academias del estudiante en este momento.
- Algunos endpoints requieren el parámetro `cohort` para devolver resultados (como `task` y `activity`).
- El `profile_academy` del usuario estaba vacío; las academias se obtuvieron desde `roles`.