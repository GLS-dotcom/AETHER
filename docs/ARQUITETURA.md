# ARQUITETURA - AETHER

## Princípios

1. **Modularidade**: Cada capacidade é um módulo independente
2. **Extensibilidade**: Novos provedores, ferramentas e agentes sem refatoração central
3. **Confiabilidade**: Falhas isoladas, tratamento gracioso, recuperação automática
4. **Observabilidade**: Logs estruturados, rastreamento de execução, auditoria
5. **Segurança**: Credenciais no backend, validação rigorosa, limites de execução
6. **Desempenho**: Streaming quando possível, cache inteligente, operações assíncronas

## Componentes Principais

### 1. API Central (Backend)

Responsável por coordenar toda a comunicação.

```
GET/POST /api/conversations      # CRUD de conversas
GET/POST /api/conversations/:id/messages  # Mensagens
POST /api/chat                   # Enviar mensagem (com streaming WebSocket)
GET/POST /api/models             # Listar/configurar modelos
GET/POST /api/tools              # Ferramentas disponíveis
POST /api/tasks                  # Criar tarefa (execução de agente)
GET/POST /api/memory             # Consulta e gerenciamento de memória
GET/POST /api/projects           # Projetos e workspaces
GET/POST /api/files              # Upload e manipulação de arquivos
```

### 2. Camada de Orquestração de IA

Abstração sobre provedores heterogêneos.

```typescript
interface AIProvider {
  name: string
  available: boolean
  models: string[]
  
  generateCompletion(
    messages: Message[],
    model: string,
    options: GenerationOptions
  ): Promise<string | AsyncIterable<string>>
  
  parseToolCall(content: string): ToolCall | null
  validateConfig(): Promise<boolean>
}

class AIOrchestrator {
  // Roteamento inteligente entre provedores
  selectProvider(task: Task): AIProvider
  
  // Fallback automático
  executeWithFallback(task: Task): Promise<Result>
  
  // Gestão de créditos e limites
  trackUsage(provider: string, model: string, tokens: number): void
}
```

### 3. Sistema de Ferramentas

Cada ferramenta é autônoma, testável e isolada.

```typescript
interface Tool {
  id: string
  name: string
  description: string
  schema: JSONSchema
  
  execute(params: Record<string, any>): Promise<Result>
  validate(params: Record<string, any>): boolean
  canExecute(user: User, params: Record<string, any>): boolean
}

class ToolRegistry {
  register(tool: Tool): void
  get(id: string): Tool
  list(): Tool[]
  execute(id: string, params: any, context: ExecutionContext): Promise<Result>
}
```

### 4. Sistema de Memória

Camadas de memória independentes.

```
┌─────────────────────────────┐
│   Memória de Conversação    │ (Histórico de chat)
├─────────────────────────────┤
│   Memória de Contexto       │ (Info. relevante recuperada)
├─────────────────────────────┤
│   Memória Factual           │ (Fatos extraídos com confiança)
├─────────────────────────────┤
│   Memória de Projetos       │ (Objetivos, decisões, progresso)
├─────────────────────────────┤
│   Memória de Procedimentos  │ (Estratégias que funcionaram)
├─────────────────────────────┤
│   Preferências de Usuário   │ (Configurações explícitas)
└─────────────────────────────┘
```

### 5. Sistema de Tarefas e Agentes

Execução assíncrona, controlada e verificável.

```typescript
interface Task {
  id: string
  status: 'pending' | 'running' | 'completed' | 'failed' | 'cancelled'
  objective: string
  plan: TaskPlan
  steps: TaskStep[]
  results: any
  startedAt: Date
  completedAt?: Date
}

class TaskExecutor {
  // Planejamento
  createPlan(objective: string, context: Context): Promise<TaskPlan>
  
  // Execução em etapas
  executeStep(task: Task, step: number): Promise<StepResult>
  
  // Verificação
  verifyResult(task: Task): Promise<Verification>
  
  // Cancelamento
  cancel(taskId: string): Promise<void>
}
```

### 6. Banco de Dados

Schema normalizado com suporte a queries avançadas.

```sql
-- Usuários
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR UNIQUE NOT NULL,
  password_hash VARCHAR,
  created_at TIMESTAMP,
  preferences JSONB
);

-- Conversas
CREATE TABLE conversations (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  title VARCHAR,
  model_used VARCHAR,
  provider VARCHAR,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

-- Mensagens
CREATE TABLE messages (
  id UUID PRIMARY KEY,
  conversation_id UUID REFERENCES conversations,
  role ENUM('user', 'assistant', 'system'),
  content TEXT,
  tokens_used INT,
  tool_calls JSONB,
  created_at TIMESTAMP
);

-- Memória
CREATE TABLE memory_facts (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  content TEXT,
  source VARCHAR,
  confidence FLOAT,
  extracted_from_message_id UUID REFERENCES messages,
  created_at TIMESTAMP
);

-- Tarefas
CREATE TABLE tasks (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  status VARCHAR,
  objective TEXT,
  plan JSONB,
  steps JSONB,
  results JSONB,
  created_at TIMESTAMP,
  completed_at TIMESTAMP
);

-- Execução de ferramentas
CREATE TABLE tool_executions (
  id UUID PRIMARY KEY,
  task_id UUID REFERENCES tasks,
  tool_id VARCHAR,
  parameters JSONB,
  result JSONB,
  error VARCHAR,
  duration_ms INT,
  created_at TIMESTAMP
);

-- Projetos
CREATE TABLE projects (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  name VARCHAR,
  description TEXT,
  data JSONB,
  created_at TIMESTAMP
);

-- Arquivos
CREATE TABLE files (
  id UUID PRIMARY KEY,
  project_id UUID REFERENCES projects,
  name VARCHAR,
  path VARCHAR,
  mime_type VARCHAR,
  size INT,
  storage_path VARCHAR,
  created_at TIMESTAMP
);

-- Configuração de provedores
CREATE TABLE provider_configs (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users,
  provider VARCHAR,
  config JSONB,
  tested_at TIMESTAMP,
  is_valid BOOLEAN
);
```

### 7. WebSocket para Streaming

Comunicação bidirecional em tempo real.

```
Client                          Server
  |  CONNECT                        |
  |------------------------------>  |
  |                         ACK      |
  |  <------------------------------  |
  |  SEND_MESSAGE                    |
  |------------------------------>  |
  |                    RESPONSE_CHUNK |
  |  <------------------------------  |
  |                    RESPONSE_CHUNK |
  |  <------------------------------  |
  |                  RESPONSE_COMPLETE |
  |  <------------------------------  |
  |  CANCEL_TASK                     |
  |------------------------------>  |
  |                        CANCELLED  |
  |  <------------------------------  |
```

### 8. Frontend - Estrutura de Componentes

```
App
├── Layout
│   ├── Sidebar (Conversas, Projetos)
│   ├── Main
│   │   ├── ConversationArea
│   │   │   ├── MessageList
│   │   │   └── MessageInput
│   │   ├── TaskPanel (execução de agentes)
│   │   ├── MemoryPanel
│   │   └── ToolBar (arquivos, modelos, etc.)
│   └── RightPanel (Configurações, Histórico)
└── Modals
    ├── ModelSelector
    ├── ProviderConfig
    ├── TaskDetails
    └── FileUpload
```

## Fluxo de uma Mensagem

```
1. Usuário digita mensagem
2. Frontend serializa + envia via WebSocket
3. Backend valida entrada
4. Backend recupera contexto (conversa + memória)
5. Backend seleciona modelo/provedor
6. Backend constrói prompt com ferramentas disponíveis
7. Backend inicia geração (streaming)
8. Se modelo detecta tool_call:
   a. Backend extrai parâmetros
   b. Backend valida permissões
   c. Backend executa ferramenta
   d. Backend retorna resultado ao modelo
   e. Modelo continua geração
9. Resposta final é enviada chunk-by-chunk ao cliente
10. Backend persiste conversa no banco
11. Memória contextual é atualizada
12. Cliente renderiza resposta em tempo real
```

## Segurança em Camadas

```
┌─────────────────────────────────────────┐
│  Validação de Entrada (Schemas)         │
├─────────────────────────────────────────┤
│  Autenticação (JWT/Sessions)            │
├─────────────────────────────────────────┤
│  Autorização (RBAC)                     │
├─────────────────────────────────────────┤
│  Sanitização (XSS, Injection)           │
├─────────────────────────────────────────┤
│  Limites de Taxa (Rate Limiting)        │
├─────────────────────────────────────────┤
│  Isolamento de Recursos (Timeouts)      │
├─────────────────────────────────────────┤
│  Auditoria (Logs de ações sensíveis)    │
├─────────────────────────────────────────┤
│  Encriptação (Em trânsito e em repouso) │
└─────────────────────────────────────────┘
```

---

Próxima: Roadmap de Implementação
