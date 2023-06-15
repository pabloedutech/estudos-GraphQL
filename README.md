# estudos-GraphQL
Estudos para consumo de uma API para buscar repositórios do GitHub no Brasil.


Passo 1: Configuração do ambiente

Certifique-se de ter o Node.js instalado em seu sistema. Em seguida, crie uma nova pasta para o projeto e, dentro dela, execute o seguinte comando para inicializar um novo projeto Node.js:

npm init -y

Instale as dependências necessárias:

npm install apollo-server axios

Passo 2: Criando o servidor GraphQL com busca na API brasileira de tecnologia

Crie um novo arquivo chamado server.js e adicione o seguinte código:


```
const { ApolloServer, gql } = require('apollo-server');
const axios = require('axios');

// Definindo o schema do GraphQL
const typeDefs = gql`
  type Repository {
    id: ID!
    name: String!
    description: String
    url: String!
  }

  type Query {
    repositories: [Repository]
  }
`;

// Definindo os resolvers
const resolvers = {
  Query: {
    repositories: async () => {
      try {
        // Faz uma requisição à API do GitHub para buscar repositórios no Brasil
        const response = await axios.get(
          'https://api.github.com/search/repositories',
          {
            params: {
              q: 'language:javascript location:brazil',
            },
          }
        );
        
        // Mapeia os dados da resposta para retornar apenas as informações necessárias
        const repositories = response.data.items.map((repo) => ({
          id: repo.id,
          name: repo.name,
          description: repo.description,
          url: repo.html_url,
        }));
        
        return repositories;
      } catch (error) {
        console.error('Erro ao buscar repositórios:', error);
        return [];
      }
    },
  },
};

// Criando uma instância do servidor Apollo
const server = new ApolloServer({ typeDefs, resolvers });

// Iniciando o servidor
server.listen().then(({ url }) => {
  console.log(`Servidor GraphQL iniciado em ${url}`);
});

```


### Execute a seguinte consulta GraphQL para buscar os repositórios na API do GitHub:

```
query {
  repositories {
    id
    name
    description
    url
  }
}

```
