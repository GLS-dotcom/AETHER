# ROADMAP - AETHER

## Timeline e Prioridades

### 🔴 Fase 1 - Estabilização Fundamental (CRÍTICO)

**Objetivo**: Sistema inicializável, conectado, persistido.

- [ ] Projeto backend Node.js + TypeScript + Express
- [ ] Banco PostgreSQL com migrations
- [ ] Projeto frontend React + Vite
- [ ] Autenticação básica (usuário/senha)
- [ ] WebSocket para comunicação real-time
- [ ] Esquema de conversa/mensagem
- [ ] Adaptador OpenAI (mock inicialmente)
- [ ] Endpoint POST /api/chat com resposta
- [ ] Persistência de conversa
- [ ] Recuperação de histórico
- [ ] Tratamento de erros em cascata
- [ ] Testes unitários de persistência
- [ ] Docker-compose para desenvolvimento

**Critérios de Sucesso**:
- ✓ `npm run dev` inicia backend em :3000
- ✓ `npm run dev` inicia frontend em :5173
- ✓ Usuário envia mensagem via interface
- ✓ Backend retorna resposta (modelo ou mock)
- ✓ Histórico persiste e é recuperado
- ✓ Não há erros no console

**Tempo Estimado**: 6-8 horas (primeira sessão)

---

### 🟠 Fase 2 - Núcleo de IA Real (ALTA PRIORIDADE)

**Objetivo**: Integração real com modelos, fallback, seleção.

- [ ] Abstração AIProvider (interface genérica)
- [ ] Implementar OpenAI real (se chave disponível)
- [ ] Implementar Anthropic Claude (alternativa)
- [ ] Implementar Ollama local (fallback offline)
- [ ] Validação de configuração de provedores
- [ ] Seleção automática de provedor
- [ ] Fallback em cascata
- [ ] Streaming em tempo real
- [ ] Function calling (tool execution)
- [ ] Gestão de tokens e custos
- [ ] Cache de prompts
- [ ] Testes de cada adaptador
- [ ] Painel de seleção de modelo (UI)

**Critérios de Sucesso**:
- ✓ Resposta vem de um modelo real (não mock)
- ✓ Alternar modelo funciona
- ✓ Fallback automático funciona
- ✓ Streaming é observável
- ✓ Custos são registrados

**Tempo Estimado**: 4-6 horas

---

### 🟡 Fase 3 - Memória e Projetos (ALTA PRIORIDADE)

**Objetivo**: Sistema de memória contextual, projetos, persistência de contexto.

- [ ] Schema de memória (fatos, confiança, origem)
- [ ] Recuperação de contexto relevante
- [ ] Sistema de projetos/workspaces
- [ ] Associação de conversas a projetos
- [ ] Preferências persistentes
- [ ] Search em histórico
- [ ] Metadata de mensagens (ferramentas usadas, modelos, custos)
- [ ] Limpeza e retenção de memória
- [ ] Testes de recuperação contextual

**Critérios de Sucesso**:
- ✓ Modelo recupera fatos anteriores quando relevante
- ✓ Novo projeto isola dados
- ✓ Busca encontra mensagens antigas
- ✓ Memória não vaza entre projetos

**Tempo Estimado**: 3-4 horas

---

### 🟢 Fase 4 - Sistema de Ferramentas (MÉDIA PRIORIDADE)

**Objetivo**: Ferramentas reais, execução segura, rastreamento.

Ferramentas Iniciais:
1. **Web Search** — Pesquisa em fontes reais (Bing/Google)
2. **File Read** — Leitura de arquivos do projeto
3. **Code Search** — Busca em repositórios
4. **Calculator** — Cálculos e análise
5. **File Operations** — Criar/editar arquivos (controlado)

- [ ] Interface Tool abstrata
- [ ] Tool Registry
- [ ] Validação de parâmetros
- [ ] Limites de execução (timeout, rate limit)
- [ ] Logging estruturado
- [ ] Feedback em tempo real ao usuário
- [ ] Permissões por usuário
- [ ] Testes de execução segura

**Critérios de Sucesso**:
- ✓ Modelo pode solicitar ferramenta
- ✓ Backend executa com segurança
- ✓ Resultado retorna ao modelo
- ✓ Sem acesso fora de diretórios autorizados

**Tempo Estimado**: 4-5 horas

---

### 🔵 Fase 5 - Agentes e Tarefas (MÉDIA PRIORIDADE)

**Objetivo**: Execução em múltiplas etapas, planejamento, verificação.

- [ ] Schema Task com plano de etapas
- [ ] TaskExecutor (orquestração)
- [ ] Prompt de planejamento
- [ ] Execução iterativa
- [ ] Verificação de resultados
- [ ] Cancelamento de tarefas
- [ ] Limites (iterações, tempo, custo)
- [ ] Relatório final com evidências
- [ ] Painel de execução (UI)
- [ ] Testes de loop de agente

**Critérios de Sucesso**:
- ✓ Tarefa multietapa executa com planejamento real
- ✓ Cada etapa é observável
- ✓ Cancelamento funciona
- ✓ Relatório final é entregue

**Tempo Estimado**: 5-6 horas

---

### 🟣 Fase 6 - UX Integrada (MÉDIA PRIORIDADE)

**Objetivo**: Interface moderna, completa, responsiva.

- [ ] Redesign do layout (Sidebar + Main + RightPanel)
- [ ] Gerenciamento de conversas
- [ ] Painel de projetos
- [ ] Painel de tarefas em andamento
- [ ] Seletor de modelo/provedor
- [ ] Configuração de provedores
- [ ] Gerenciador de arquivos
- [ ] Histórico de execuções
- [ ] Estados visuais (loading, erro, sucesso)
- [ ] Responsividade (mobile-first)
- [ ] Acessibilidade (WCAG 2.1)
- [ ] Temas (light/dark)

**Critérios de Sucesso**:
- ✓ Interface transmite capacidades reais
- ✓ Todos os fluxos são intuitivos
- ✓ Sem placeholders fake
- ✓ Funciona em mobile

**Tempo Estimado**: 6-8 horas

---

### 📊 Fase 7 - Evolução e Avaliação (MÉDIA PRIORIDADE)

**Objetivo**: Feedback, testes de regressão, otimização.

- [ ] Sistema de coleta de feedback
- [ ] Teste A/B de estratégias
- [ ] Comparação de modelos
- [ ] Regressão de qualidade
- [ ] Recomendações de melhoria
- [ ] Versionamento de configs
- [ ] Dashboard de métricas
- [ ] Análise de custos

**Critérios de Sucesso**:
- ✓ Feedback é coletado e analisado
- ✓ Decisões baseadas em evidências
- ✓ Regressões são detectadas

**Tempo Estimado**: 3-4 horas

---

### 🔬 Fase 8 - Laboratório de Ideias (BAIXA PRIORIDADE)

**Objetivo**: Recursos originais, inovação.

Candidatos:
- [ ] Detecção automática de informações desatualizadas
- [ ] Registro de decisões com consequências
- [ ] Adaptação de planos em tempo real
- [ ] Verificação cruzada de respostas
- [ ] Recuperação de contexto interrompido
- [ ] Visualização de raciocínio
- [ ] Simulações de cenários
- [ ] Laboratório de hipóteses

**Critérios de Sucesso**:
- ✓ Funcionalidades têm valor demonstrável
- ✓ Não são meros efeitos visuais

**Tempo Estimado**: 4-6 horas (se houver tempo)

---

### 📦 Fase 9 - Validação Final (CRÍTICO)

**Objetivo**: Testes, documentação, entrega.

- [ ] Suite completa de testes
- [ ] Teste de instalação limpa
- [ ] Auditoria de segurança
- [ ] Documentação completa
- [ ] README detalhado
- [ ] INSTALL.md com passo-a-passo
- [ ] Changelog completo
- [ ] Pacote ZIP de entrega
- [ ] Validação de integridade

**Critérios de Sucesso**:
- ✓ Tudo está documentado
- ✓ Novo usuário consegue instalar
- ✓ Sem chaves de API no git
- ✓ ZIP é inteligível

**Tempo Estimado**: 2-3 horas

---

## Prioridade por Impacto

```
Alto Impacto + Baixa Complexidade    HIGH PRIORITY
└─ Fase 1: Inicialização
└─ Fase 2: Núcleo de IA
└─ Fase 4: Ferramentas básicas

Alto Impacto + Média Complexidade    MEDIUM PRIORITY
└─ Fase 3: Memória
└─ Fase 5: Agentes
└─ Fase 6: UX

Baixo Impacto + Alta Complexidade    DEFER
└─ Fase 8: Laboratório

Essencial para Entrega              FINAL
└─ Fase 9: Validação
```

---

**Próxima Ação**: Iniciar Fase 1 imediatamente.
