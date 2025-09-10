# 🚀 Code Connect Backend

Uma API REST para uma plataforma de compartilhamento de conteúdo sobre programação, construída com **NestJS**, **Prisma** e **SQLite**.

## 📋 Sobre o Projeto

O Code Connect é uma plataforma onde desenvolvedores podem compartilhar posts sobre programação, interagir através de comentários e curtidas. Esta é a API backend que fornece todos os endpoints necessários para:

- **Autenticação de usuários** (registro, login, JWT)
- **Gerenciamento de posts** (criar, listar, editar, excluir)
- **Upload de imagens** (posts com capa e avatars de usuário)
- **Sistema de comentários**
- **Sistema de curtidas**
- **Busca e filtros** (por autor, busca textual)
- **Perfis de usuários**

## 🛠️ Tecnologias Utilizadas

- **[NestJS](https://nestjs.com/)** - Framework Node.js para APIs escaláveis
- **[Prisma](https://prisma.io/)** - ORM moderno para TypeScript/JavaScript
- **[SQLite](https://sqlite.org/)** - Banco de dados local (arquivo `dev.db`)
- **[JWT](https://jwt.io/)** - Autenticação via tokens
- **[bcrypt](https://www.npmjs.com/package/bcrypt)** - Hash de senhas
- **[Multer](https://github.com/expressjs/multer)** - Upload de arquivos
- **[UUID](https://www.npmjs.com/package/uuid)** - Geração de identificadores únicos
- **[TypeScript](https://www.typescriptlang.org/)** - Superset do JavaScript
- **[Swagger/OpenAPI](https://swagger.io/)** - Documentação interativa da API

## ⚡ Como Rodar o Projeto

### Pré-requisitos

- **Node.js** (versão 18 ou superior)
- **npm** ou **yarn**

### 🔧 Configuração

1. **Clone o repositório:**
```bash
git clone <url-do-repositorio>
cd code-connect-backend-node
```

2. **Instale as dependências:**
```bash
npm install
```

3. **Configure as variáveis de ambiente:**
O arquivo `.env` já está configurado com:
```
DATABASE_URL="file:./dev.db"
JWT_SECRET="your-secret-key-here"
APP_URL="http://localhost:3000"  # URL base para imagens (opcional)
```

> **Nota:** `APP_URL` é usado para gerar URLs completas das imagens. Em produção, defina como `https://seudominio.com`

4. **Execute as migrações e seeds:**
```bash
# Gera o banco de dados SQLite e aplica as migrações
npx prisma migrate dev

# Popula o banco com dados iniciais (usuários e posts de exemplo)
npx prisma db seed
```

### 🚀 Executando a Aplicação

```bash
# Modo desenvolvimento (com hot-reload)
npm run start:dev

# Modo produção
npm run build
npm run start:prod
```

A API estará disponível em: `http://localhost:3000`

### 📚 Documentação da API

A documentação interativa da API (Swagger) estará disponível em:
```
http://localhost:3000/api
```

Nesta interface você pode:
- ✅ **Testar todos os endpoints** diretamente no navegador
- ✅ **Ver exemplos** de requests e responses
- ✅ **Fazer upload de arquivos** (imagens para posts e avatars)
- ✅ **Autenticar com JWT** e testar endpoints protegidos

## 📊 Banco de Dados

O projeto usa **SQLite** como banco de dados, o que significa:

- ✅ **Sem dependências externas** (não precisa instalar PostgreSQL, Docker, etc.)
- ✅ **Portabilidade total** - o banco é um arquivo (`prisma/dev.db`)
- ✅ **Fácil backup** - basta copiar o arquivo do banco
- ✅ **Perfeito para desenvolvimento** e pequenas aplicações

### Visualizando os dados

```bash
# Abrir interface gráfica do Prisma Studio
npx prisma studio
```

## 📁 Estrutura do Projeto

```
src/
├── auth/           # Módulo de autenticação
├── users/          # Módulo de usuários
├── posts/          # Módulo de posts
│   ├── dto/        # DTOs para validação
│   ├── utils/      # Utilitários (geração de slug)
│   └── multer.config.ts # Configuração de upload
├── comments/       # Módulo de comentários  
├── prisma/         # Configurações do Prisma
└── main.ts         # Arquivo principal

prisma/
├── schema.prisma   # Schema do banco de dados
├── dev.db         # Banco SQLite (criado automaticamente)
├── migrations/    # Histórico de migrações
└── seed.ts        # Script de dados iniciais

uploads/            # Pasta para imagens (criada automaticamente)
├── *.jpg          # Imagens de posts
├── *.png          # Avatars de usuários
└── ...            # Outros arquivos de imagem
```

## 🔐 Endpoints da API

### 🔑 Autenticação
- `POST /auth/register` - Cadastro de usuário
- `POST /auth/login` - Login e obtenção do JWT

### 📝 Posts
- `GET /blog-posts` - Listar posts (com filtros opcionais)
- `GET /blog-posts/:id` - Obter post específico
- `GET /blog-posts/slug/:slug` - Obter post por slug
- `POST /blog-posts` - Criar novo post com imagem (autenticado)
- `PATCH /blog-posts/:id` - Editar post (autenticado, apenas autor)
- `DELETE /blog-posts/:id` - Excluir post (autenticado, apenas autor)
- `POST /blog-posts/:id/like` - Curtir post (autenticado)

#### 🔍 Filtros disponíveis em GET /blog-posts:
- `?authorId=xxx` - Filtrar por autor
- `?search=termo` - Buscar no título, corpo e markdown
- `?authorId=xxx&search=termo` - Combinar filtros

### 👤 Usuários
- `PUT /users/avatar` - Atualizar avatar do usuário logado (autenticado)

### 💬 Comentários
- `GET /comments` - Listar todos os comentários
- `GET /comments/:id` - Obter comentário específico
- `POST /comments` - Criar comentário (autenticado)
- `PATCH /comments/:id` - Editar comentário (autenticado, apenas autor)
- `DELETE /comments/:id` - Excluir comentário (autenticado, apenas autor)

## 🖼️ Upload de Imagens

### ✨ Funcionalidades de Upload:

#### 📝 **Posts com Imagem:**
- **Campo obrigatório:** Toda criação de post deve incluir uma imagem
- **Formatos aceitos:** JPG, JPEG, PNG, GIF
- **Tamanho máximo:** 5MB por arquivo
- **Slug automático:** Gerado automaticamente a partir do título
- **URL completa:** Retorna URL acessível da imagem

#### 👤 **Avatar de Usuário:**
- **Endpoint:** `PUT /users/avatar`
- **Upload opcional:** Pode ser atualizado a qualquer momento
- **Mesmas validações:** Formato e tamanho
- **Substituição:** Nova imagem substitui a anterior

### 🔧 **Como fazer upload:**

```bash
# Criar post com imagem
curl -X POST http://localhost:3000/blog-posts \
  -H "Authorization: Bearer seu-jwt-token" \
  -F "title=Meu Post Incrível" \
  -F "body=Conteúdo do post..." \
  -F "markdown=# Título do post" \
  -F "image=@caminho/para/imagem.jpg"

# Atualizar avatar
curl -X PUT http://localhost:3000/users/avatar \
  -H "Authorization: Bearer seu-jwt-token" \
  -F "avatar=@caminho/para/avatar.jpg"
```

### 📁 **Armazenamento:**
- **Pasta:** `uploads/` (criada automaticamente)
- **Nomes únicos:** UUID para evitar conflitos
- **URLs geradas:** `http://localhost:3000/uploads/nome-unico.jpg`
- **Servimento estático:** Imagens acessíveis via HTTP

## 🧪 Dados de Exemplo

Após executar o seed, você terá:

- **4 usuários** de exemplo (senha: `123456`)
  - ana@codeconnect.com
  - bruno@codeconnect.com
  - carla@codeconnect.com
  - diego@codeconnect.com

- **12 posts** sobre diversos temas de programação
- **Comentários** aleatórios nos posts

> **Nota:** Para testar uploads, use o Swagger em `http://localhost:3000/api` ou ferramentas como Postman/Insomnia.

## 🛠️ Comandos Úteis

```bash
# Instalar dependências
npm install

# Rodar em modo desenvolvimento
npm run start:dev

# Build para produção
npm run build

# Prisma - Gerar client
npx prisma generate

# Prisma - Aplicar migrações
npx prisma migrate dev

# Prisma - Resetar banco (cuidado!)
npx prisma migrate reset

# Prisma - Visualizar dados
npx prisma studio

# Rodar seeds novamente
npx prisma db seed

# Linting e formatação
npm run lint
npm run format

# Testes
npm run test

# Limpar uploads (cuidado!)
rm -rf uploads/*
```

## 🔄 Desenvolvimento

Para desenvolver novas funcionalidades:

1. **Modifique o schema** (`prisma/schema.prisma`)
2. **Crie uma migração:** `npx prisma migrate dev --name nome-da-migrao`
3. **Implemente os endpoints** nos controllers
4. **Teste** com Postman, Insomnia ou similar

## 💾 Backup e Migração

Para fazer backup dos dados:
```bash
# Copiar o banco
cp prisma/dev.db backup/dev-backup-$(date +%Y%m%d).db

# Ou exportar para SQL
sqlite3 prisma/dev.db .dump > backup/database-backup.sql
```

## 📝 Observações Importantes

### 💾 **Banco de Dados:**
- O banco SQLite é criado automaticamente na primeira execução
- Os dados persistem entre reinicializações da aplicação
- Para limpar todos os dados: `npx prisma migrate reset`
- Para ambiente de produção, considere PostgreSQL ou MySQL

### 🖼️ **Upload de Arquivos:**
- A pasta `uploads/` é criada automaticamente
- Imagens são servidas como arquivos estáticos em `/uploads/`
- Em produção, considere usar CDN (AWS S3, Cloudinary, etc.)
- Para limpar uploads: `rm -rf uploads/*`

### 🔒 **Segurança:**
- JWT tokens para autenticação
- Validação de tipos de arquivo (apenas imagens)
- Limite de tamanho de arquivo (5MB)
- Usuários só podem editar/excluir próprio conteúdo

### 🚀 **Deploy:**
- Configure `APP_URL` para seu domínio em produção
- Considere usar variáveis de ambiente para JWT_SECRET
- Uploads podem precisar ser movidos para storage externo
