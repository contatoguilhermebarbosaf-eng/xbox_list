# Publicar o Top 300 BR no GitHub Pages

Este diretorio foi preparado para publicar somente a lista `top_300_br.html` e dependencias diretas dela.

## Nome recomendado

Repositorio:

```text
top-300-xbox-360-br
```

Arquivo de entrada do site:

```text
index.html
```

O `index.html` apenas abre `top_300_br.html`. Assim o site funciona no endereco principal do GitHub Pages e o arquivo da lista continua com o nome descritivo.

## O que entra no GitHub

Arquivos publicados:

```text
top_300_br.html
index.html
assets/styles.css
.nojekyll
.gitignore
.gitattributes
README.md
PUBLICAR_NO_GITHUB.md
```

Arquivos que ficam fora:

```text
organizador_de_jogos.html
progresso-backup.json
top_300_br.txt
```

## Primeira publicacao

Crie no GitHub um repositorio vazio chamado:

```text
top-300-xbox-360-br
```

Depois rode na pasta:

```powershell
cd "C:\Users\W10\Desktop\EASY XBOX\listadejogosbaixar"
git init
git add .
git status
git commit -m "Publica Top 300 BR"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/top-300-xbox-360-br.git
git push -u origin main
```

Troque `SEU_USUARIO` pelo seu usuario do GitHub.

## Ativar GitHub Pages

No repositorio do GitHub:

1. Abra `Settings`.
2. Abra `Pages`.
3. Em `Build and deployment`, selecione `Deploy from a branch`.
4. Em `Branch`, escolha `main`.
5. Em `Folder`, escolha `/ (root)`.
6. Clique em `Save`.

O site ficara neste formato:

```text
https://SEU_USUARIO.github.io/top-300-xbox-360-br/
```

Tambem sera possivel abrir diretamente:

```text
https://SEU_USUARIO.github.io/top-300-xbox-360-br/top_300_br.html
```

## Persistencia do progresso

O progresso fica salvo no navegador em `localStorage`, usando a chave:

```text
top300GameState
```

Isso significa:

- ao fechar e reabrir o navegador no mesmo aparelho, os checks continuam salvos;
- em outro celular, PC ou navegador, o progresso nao aparece automaticamente;
- para levar o progresso para outro lugar, use os botoes `Exportar` e `Importar` da propria pagina.

## Sincronizacao em tempo real

Para PC e celular enxergarem os mesmos checks e observacoes em tempo real, configure um projeto Supabase.

No Supabase, abra `SQL Editor` e rode:

```sql
create table if not exists public.top300_state (
  list_id text not null default 'default',
  game_id text not null,
  data jsonb not null default '{}'::jsonb,
  updated_at timestamptz not null default now(),
  primary key (list_id, game_id)
);

alter table public.top300_state enable row level security;

drop policy if exists "top300_select" on public.top300_state;
drop policy if exists "top300_insert" on public.top300_state;
drop policy if exists "top300_update" on public.top300_state;

create policy "top300_select"
on public.top300_state
for select
using (true);

create policy "top300_insert"
on public.top300_state
for insert
with check (list_id = 'default');

create policy "top300_update"
on public.top300_state
for update
using (list_id = 'default')
with check (list_id = 'default');

alter table public.top300_state replica identity full;

alter publication supabase_realtime add table public.top300_state;
```

Depois, no Supabase:

1. Abra `Project Settings`.
2. Abra `API`.
3. Copie `Project URL`.
4. Copie a chave `anon public`.
5. No arquivo `top_300_br.html`, preencha:

```js
const SYNC_CONFIG = {
    supabaseUrl: 'COLE_AQUI_PROJECT_URL',
    supabaseAnonKey: 'COLE_AQUI_ANON_PUBLIC',
    tableName: 'top300_state',
    listId: 'default'
};
```

Com isso, a pagina muda de `Local` para `Tempo real` e passa a sincronizar checks, idioma e observacoes entre dispositivos.

## Atualizar depois

Quando mudar a lista ou o visual:

```powershell
cd "C:\Users\W10\Desktop\EASY XBOX\listadejogosbaixar"
git status
git add .
git commit -m "Atualiza Top 300 BR"
git push
```
