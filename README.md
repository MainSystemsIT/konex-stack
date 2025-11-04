# 🌐 GUIA DE INSTALAÇÃO — KONEX CRM NO PORTAINER
---

## 🧩 VISÃO GERAL

O **Konex CRM** roda com:
- 🔐 Traefik (proxy + SSL), NGINX ou APACHE
- 🧠 PgVector (PostgreSQL com suporte a IA)
- ⚙️ Redis (cache e filas)
- 💼 Konex CRM (aplicação principal)
- 🧵 Sidekiq (processador de tarefas)

---

## ⚙️ ETAPA 1 — PREPARAR O AMBIENTE

1. Acesse o **Portainer** (interface web de containers).  
2. Confirme:
   - 🌍 Domínio ativo (ex: `konex.suaempresa.com.br`)
   - ⚡ Traefik rodando (pode estar em outro stack)
   - 🔗 Permissão para criar redes

---

## 🌐 ETAPA 2 — CRIAR UMA REDE COMPARTILHADA

Todos os serviços se comunicam pela **mesma rede**.

**No Portainer:**
1. Vá em `Networks → Add Network`
2. Crie com:
   - **Name:** escolha livre (ex: `konex_network`)
   - **Driver:** `overlay`
   - **Attachable:** ✅ marcado
3. Clique em **Create the network**

> 📝 Esse nome (ex: `konex_network`) substituirá `nome_rede_interna` nos arquivos YAML.

---

## 🗄️ ETAPA 3 — CONFIGURAR O BANCO

### 🅰️ Se quiser um banco novo (PgVector)
1. Vá em `Stacks → Add Stack`
2. Nome: `pgvector`
3. Cole o YAML (modelo abaixo)
4. Substitua `nome_rede_interna` pelo nome da sua rede (ex: `konex_network`)
5. Deploy

📌 URL do banco resultante:
``postgres://postgres:senha_pgvector@pgvector:5432/konex_crm``

---

### 🅱️ Se já tiver um banco existente
Apenas use:
``postgres://usuario:senha@IP_DO_BANCO:5432/konex_crm``

> ⚠️ Confirme se o banco permite conexões externas e possui a extensão `pgvector`.

---

## 💾 ETAPA 4 — SUBIR O REDIS

1. `Stacks → Add Stack`
2. Nome: `redis`
3. Cole o YAML do Redis
4. Substitua `nome_rede_interna` pela rede criada (`konex_network`)
5. Deploy

📌 URL do Redis:
``redis://redis:6379/8`` 

---

## 💼 ETAPA 5 — IMPLANTAR O KONEX CRM

1. `Stacks → Add Stack`
2. Nome: `konex`
3. Cole o YAML do Konex CRM
4. Atualize:
   - `DATABASE_URL` → do seu banco
   - `REDIS_URL` → do seu Redis
   - Rede → troque `nome_rede_interna` pelo nome real
5. Deploy

🚀 Acompanhe:
- Portainer → Stacks → konex → Services → app → **Logs**
- Verifique: `Listening on tcp://0.0.0.0:3000`

🌍 Acesse:
``https://seu-dominio.com.br``

---

## 🧱 ETAPA 6 — EXECUTAR MIGRAÇÕES (se necessário)

1. Portainer → Stacks → konex → app → **Console**
2. Entre com:
```
bash
rails db:migrate
```

---
## 💡 DICAS FINAIS

| Situação | Solução |
|----------|----------|
| Sistema não inicia | Veja logs no Portainer (`Stacks → konex → Logs`) |
| Erro de banco | Revise `DATABASE_URL` e permissões |
| SSL não sobe | Verifique se o domínio aponta para o IP do Traefik |
| Atualização falhou | Use a tag anterior do Docker (`mainsystemsit/konex-crm:1.0.1`) |

---

## 🎯 CONCLUSÃO

✅ Agora o Konex CRM está pronto em produção  
🔗 Tudo gerenciado visualmente pelo **Portainer**  
🔐 SSL automático via Traefik  
🧩 Banco persistente e seguro  

> 💬 Em caso de dúvidas, contate o suporte MainSystems IT.

