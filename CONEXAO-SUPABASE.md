# Conectar o Planejamento ao Supabase (compartilhado em tempo real)

Siga uma vez. Depois disso, todo mundo que abrir o link vê e edita o mesmo planejamento.

## 1. Criar o projeto
1. Acesse **https://supabase.com** e crie uma conta (pode usar o Google).
2. Clique em **New project**. Dê um nome (ex.: `formosa-planejamento`), defina uma senha de banco e crie.
3. Aguarde ~1 min até o projeto ficar pronto.

## 2. Criar as tabelas
1. No menu lateral, abra **SQL Editor** → **New query**.
2. Cole o SQL abaixo e clique em **Run**:

```sql
-- Tabela de ações de marketing
create table if not exists planejamento (
  id        text primary key,
  tipo      text,           -- 'on' (digital) ou 'off' (offline)
  canal     text,
  titulo    text,
  segmento  text,
  data      date,
  status    text,
  resp      text,
  obs       text,
  link      text,
  updated_at timestamptz default now()
);

-- Tabela de configurações (ex.: mês de referência)
create table if not exists meta (
  chave text primary key,
  valor text
);

-- Acesso público (link aberto, sem senha)
alter table planejamento enable row level security;
alter table meta         enable row level security;

create policy "acesso publico planejamento" on planejamento
  for all to anon using (true) with check (true);
create policy "acesso publico meta" on meta
  for all to anon using (true) with check (true);

-- Atualização em tempo real
alter publication supabase_realtime add table planejamento;
alter publication supabase_realtime add table meta;
```

## 3. Pegar as 2 chaves
1. Menu lateral → **Project Settings** (engrenagem) → **API**.
2. Copie:
   - **Project URL** → algo como `https://xxxxxxxx.supabase.co`
   - **anon public** (em "Project API keys") → começa com `eyJ...`

## 4. Conectar
Tem dois jeitos:

**A) Rápido (só para você testar agora):**
- Abra o app, clique em **🔌 Conexão**, cole o URL e a chave, clique **Conectar**.
- Obs.: assim a conexão fica salva só **neste navegador**.

**B) Para valer para TODA a equipe automaticamente (recomendado):**
- Me envie aqui o **Project URL** e a **anon public key**.
- Eu coloco no arquivo (no topo, em `const HARDCODED`) e publico.
- A partir daí, qualquer pessoa que abrir o link já entra conectada, sem configurar nada.

> A chave **anon public** é feita para ficar exposta no navegador — é seguro embuti-la. Como escolhemos "link aberto", quem tiver o link consegue ver e editar. Se quiser, depois dá para adicionar uma senha simples.
