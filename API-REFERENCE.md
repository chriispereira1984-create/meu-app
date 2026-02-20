# 📡 API REFERENCE - AirControl Pro

## Visão Geral da API RESTful

Este documento descreve todos os endpoints disponíveis da API REST utilizada pelo sistema AirControl Pro.

---

## 🔗 Base URL

```
tables/
```

Todos os endpoints utilizam URLs relativas a partir desta base.

---

## 📊 Tabelas Disponíveis

### 1. **clientes**
Gerenciamento de clientes

### 2. **ordens_servico**
Gerenciamento de ordens de serviço

### 3. **ar_condicionado**
Catálogo de modelos de ar condicionado

### 4. **tecnicos**
Gerenciamento de técnicos

---

## 🛠️ Endpoints

### GET tables/{table}
**Descrição:** Lista todos os registros de uma tabela

**Parâmetros de Query:**
- `page` (opcional) - Número da página (padrão: 1)
- `limit` (opcional) - Registros por página (padrão: 100)
- `search` (opcional) - Busca em campos de texto
- `sort` (opcional) - Ordenação (ex: `-created_at` para decrescente)

**Exemplo:**
```javascript
const response = await fetch('tables/clientes?page=1&limit=10');
const data = await response.json();
```

**Resposta:**
```json
{
  "data": [...],
  "total": 100,
  "page": 1,
  "limit": 10,
  "table": "clientes",
  "schema": {...}
}
```

---

### GET tables/{table}/{id}
**Descrição:** Obtém um registro específico por ID

**Exemplo:**
```javascript
const response = await fetch('tables/clientes/cli001');
const cliente = await response.json();
```

**Resposta:**
```json
{
  "id": "cli001",
  "nome": "João Silva",
  "telefone": "(11) 98765-4321",
  ...
}
```

---

### POST tables/{table}
**Descrição:** Cria um novo registro

**Body:** JSON com os dados do registro

**Exemplo:**
```javascript
const response = await fetch('tables/clientes', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    nome: 'João Silva',
    telefone: '(11) 98765-4321',
    email: 'joao@email.com',
    cep: '01310-100',
    endereco: 'Av. Paulista, 1000',
    bairro: 'Bela Vista',
    cidade: 'São Paulo',
    estado: 'SP'
  })
});
const novoCliente = await response.json();
```

**Status:** 201 Created

**Resposta:** Registro criado com campos do sistema

---

### PUT tables/{table}/{id}
**Descrição:** Atualiza um registro completo (substitui todos os campos)

**Body:** JSON completo com todos os campos

**Exemplo:**
```javascript
const response = await fetch('tables/clientes/cli001', {
  method: 'PUT',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    nome: 'João Silva Santos',
    telefone: '(11) 98765-4321',
    email: 'joao.silva@email.com',
    ...
  })
});
const clienteAtualizado = await response.json();
```

**Resposta:** Registro atualizado completo

---

### PATCH tables/{table}/{id}
**Descrição:** Atualiza campos específicos (atualização parcial)

**Body:** JSON apenas com os campos a atualizar

**Exemplo:**
```javascript
const response = await fetch('tables/clientes/cli001', {
  method: 'PATCH',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    telefone: '(11) 99999-8888'
  })
});
const clienteAtualizado = await response.json();
```

**Resposta:** Registro atualizado completo

---

### DELETE tables/{table}/{id}
**Descrição:** Exclui um registro (soft delete)

**Exemplo:**
```javascript
await fetch('tables/clientes/cli001', {
  method: 'DELETE'
});
```

**Status:** 204 No Content

**Nota:** O registro não é removido fisicamente, apenas marcado como `deleted: true`

---

## 🔑 Campos do Sistema

Todos os registros possuem campos automáticos:

```javascript
{
  "id": "uuid-gerado-automaticamente",
  "gs_project_id": "id-do-projeto",
  "gs_table_name": "nome-da-tabela",
  "created_at": 1708300800000,  // timestamp em ms
  "updated_at": 1708300800000,  // timestamp em ms
  "deleted": false               // flag de exclusão
}
```

---

## 📝 Schema das Tabelas

### Tabela: clientes

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| id | text | ✅ | ID único |
| nome | text | ✅ | Nome completo |
| telefone | text | ✅ | Telefone |
| email | text | ❌ | Email |
| cpf_cnpj | text | ❌ | CPF ou CNPJ |
| cep | text | ✅ | CEP |
| endereco | text | ✅ | Endereço |
| bairro | text | ✅ | Bairro |
| cidade | text | ✅ | Cidade |
| estado | text | ✅ | UF (2 letras) |
| complemento | text | ❌ | Complemento |
| observacoes | text | ❌ | Observações |

---

### Tabela: ordens_servico

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| id | text | ✅ | ID único |
| numero_os | text | ✅ | Número da OS |
| cliente_id | text | ✅ | ID do cliente |
| cliente_nome | text | ❌ | Nome (cache) |
| tecnico_id | text | ✅ | ID do técnico |
| tecnico_nome | text | ❌ | Nome (cache) |
| endereco_servico | text | ✅ | Endereço completo |
| cep | text | ✅ | CEP |
| tipo_servico | text | ✅ | Tipo (enum) |
| ar_condicionado_id | text | ❌ | ID do modelo |
| ar_modelo | text | ❌ | Modelo (cache) |
| ar_marca | text | ❌ | Marca (cache) |
| ar_btu | text | ❌ | BTU (cache) |
| status | text | ✅ | Status (enum) |
| prioridade | text | ✅ | Prioridade (enum) |
| descricao_problema | text | ✅ | Descrição |
| observacoes_tecnico | text | ❌ | Observações |
| valor_servico | number | ❌ | Valor em R$ |
| data_abertura | datetime | ✅ | Data de abertura |
| data_agendamento | datetime | ❌ | Data agendada |
| data_conclusao | datetime | ❌ | Data de conclusão |

**Enums:**
- **tipo_servico:** Instalação, Manutenção, Reparo, Limpeza, Desinstalação, Orçamento
- **status:** Pendente, Em Andamento, Concluída, Cancelada
- **prioridade:** Baixa, Normal, Alta, Urgente

---

### Tabela: ar_condicionado

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| id | text | ✅ | ID único |
| marca | text | ✅ | Marca |
| modelo | text | ✅ | Modelo |
| btu | text | ✅ | BTU |
| tipo | text | ✅ | Tipo (enum) |
| voltagem | text | ✅ | Voltagem (enum) |
| inverter | bool | ✅ | Possui Inverter |
| classificacao_energetica | text | ✅ | Classe A-E |
| preco_medio | number | ❌ | Preço médio |
| area_recomendada | text | ✅ | Área em m² |
| observacoes | text | ❌ | Observações |

**Enums:**
- **tipo:** Split Hi-Wall, Split Piso Teto, Cassete, Janela, Portátil, Multi-Split
- **voltagem:** 110V, 220V, Bivolt

---

### Tabela: tecnicos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| id | text | ✅ | ID único |
| nome | text | ✅ | Nome completo |
| telefone | text | ✅ | Telefone |
| email | text | ✅ | Email |
| especialidade | text | ✅ | Especialidade |
| status | text | ✅ | Status (enum) |
| cor_agenda | text | ✅ | Cor (hex) |

**Enums:**
- **status:** Disponível, Em Atendimento, Indisponível

---

## 🔄 Exemplos Práticos

### Listar todos os clientes
```javascript
async function loadClientes() {
  const response = await fetch('tables/clientes?limit=100');
  const data = await response.json();
  return data.data;
}
```

### Criar nova ordem de serviço
```javascript
async function createOS(osData) {
  const response = await fetch('tables/ordens_servico', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(osData)
  });
  return await response.json();
}
```

### Atualizar status da OS
```javascript
async function updateOSStatus(osId, newStatus) {
  const response = await fetch(`tables/ordens_servico/${osId}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ status: newStatus })
  });
  return await response.json();
}
```

### Buscar cliente por ID
```javascript
async function getCliente(clienteId) {
  const response = await fetch(`tables/clientes/${clienteId}`);
  return await response.json();
}
```

### Excluir técnico
```javascript
async function deleteTecnico(tecnicoId) {
  await fetch(`tables/tecnicos/${tecnicoId}`, {
    method: 'DELETE'
  });
}
```

---

## 🔐 Segurança

**Nota:** Esta API não possui autenticação nativa. Em produção, você deve:
1. Implementar autenticação (JWT, OAuth, etc.)
2. Adicionar validação de permissões
3. Sanitizar entradas
4. Implementar rate limiting
5. Usar HTTPS

---

## ⚡ Performance

**Dicas:**
- Use `limit` para paginação
- Cache respostas quando possível
- Use PATCH em vez de PUT quando atualizar poucos campos
- Utilize índices adequados no banco

---

## 🐛 Tratamento de Erros

**Códigos de Status HTTP:**
- `200` OK - Sucesso
- `201` Created - Registro criado
- `204` No Content - Exclusão bem-sucedida
- `400` Bad Request - Dados inválidos
- `404` Not Found - Registro não encontrado
- `500` Internal Server Error - Erro no servidor

**Exemplo de tratamento:**
```javascript
try {
  const response = await fetch('tables/clientes/cli001');
  
  if (!response.ok) {
    throw new Error(`Erro: ${response.status}`);
  }
  
  const cliente = await response.json();
  console.log(cliente);
} catch (error) {
  console.error('Erro ao buscar cliente:', error);
}
```

---

## 📞 Suporte

Para dúvidas ou problemas com a API, consulte o README.md ou entre em contato.

---

**Versão:** 1.0.0  
**Última atualização:** 18/02/2026
