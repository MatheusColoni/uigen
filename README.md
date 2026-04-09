UIGen
Gerador de componentes React com IA e pré-visualização ao vivo.
Pré-requisitos

Node.js 18+
npm

Configuração

Opcional: Edite o arquivo .env e adicione sua chave de API da Anthropic:

ANTHROPIC_API_KEY=sua-chave-aqui
O projeto funciona sem uma chave de API. Nesse caso, em vez de usar uma IA para gerar componentes, será retornado um código estático.

Instale as dependências e inicialize o banco de dados:

npm run setup
Este comando irá:

Instalar todas as dependências
Gerar o cliente Prisma
Executar as migrações do banco de dados

Rodando a aplicação
Desenvolvimento:
npm run dev
Acesse http://localhost:3000
Como usar

Cadastre-se ou continue como usuário anônimo
Descreva o componente React que deseja criar no chat
Veja os componentes gerados na pré-visualização em tempo real
Alterne para a visualização de Código para ver e editar os arquivos gerados
Continue iterando com a IA para refinar seus componentes

Funcionalidades

Geração de componentes com IA usando Claude
Pré-visualização ao vivo com hot reload
Sistema de arquivos virtual (nenhum arquivo é gravado no disco)
Realce de sintaxe e editor de código
Persistência de componentes para usuários cadastrados
Exportação do código gerado

Tecnologias utilizadas

Next.js 15 com App Router
React 19
TypeScript
Tailwind CSS v4
Prisma com SQLite
Anthropic Claude AI
Vercel AI SDK



Estudos do Curso Claude in Action da Anthropic
