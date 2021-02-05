# SSR - side server rendering
(Renderização do lado do servidor) 

npx create-react-app app6-ssr

Copyright.js

~~~
export default props => <h1>Recode Pro {props.ano}!</h1>;
~~~

App.js

~~~
import Copyright from "./Copyright";

function App() {
  return <Copyright ano="2021" />;
}

export default App;
~~~

remover as linhas desnecessarias do index.js

substituir ReactDOM.render por ReactDOM.hydrate

npm i express

~~~
import path from 'path';

import fs from 'fs';

import React from 'react';

import express from 'express';

import ReactDOMServer from 'react-dom/server';

import App from '../src/App';

const PORT = process.env.PORT || 3006;

const app = express();

app.get('/', (req, res) => {

  const app = ReactDOMServer.renderToString(<App />);

  const indexFile = path.resolve('./build/index.html');

   fs.readFile(indexFile, 'utf8', (err, data) => {

    if (err) {

       console.error('Something went wrong:', err);

      return res.status(500).send('Oops, better luck next time!');

    }

    return res.send(

       data.replace('<div id="root"></div>', `<div id="root">${app}</div>`)

    );

  });

});

app.use(express.static('./build'));

app.listen(PORT, () => {

  console.log(`Server is listening on port ${PORT}`);

});
~~~

Pronto! Já temos nossa estrutura para fazer com que o aplicativo seja renderizado no servidor. Agora precisamos realizar as configurações necessárias.

## Configurando o aplicativo para renderizar no servidor

Para que o código do nosso servidor funcione, vamos precisar empacotar e transcompilá-lo  usando o webpack e o Babel. Para isso, vamos adicionar as dependências de desenvolvimento ao projeto inserindo o seguinte comando na  janela do terminal:

~~~
 npm install webpack-cli webpack-node-externals @babel/core babel-loader @babel/preset-env @babel/preset-react --save-dev
~~~

Vamos iniciar com o arquivo de configuração do Babel na raiz da pasta do projeto, criando um arquivo chamado “.babelrc.json” (iniciando com ponto assim mesmo) e inserindo o seguinte conteúdo:

~~~
{  
    "presets": [
    "@babel/preset-env",
    "@babel/preset-react" 
    ]
}
~~~

O próximo arquivo de configuração é do webpack. Para isso, crie o arquivo chamado “webpack.server.js” , também na raiz do projeto, com o seguinte conteúdo: 

~~~
const path = require("path");
const nodeExternals = require("webpack-node-externals");

module.exports = {
    
  entry: "./server/index.js",
  target: "node",
  externals: [nodeExternals()],

  output: {
    path: path.resolve("server-build"),
    filename: "index.js",
  },

  module: {
    rules: [
      {
        test: /\.js$/,
        use: "babel-loader",
      },
    ],
  },
};
~~~

Isso completa a instalação de dependência e webpack, assim como a configuração do Babel.

 Com as dependências e configurações realizadas, precisamos fazer uma alteração no arquivo “package.json”, incluindo essas essas linhas de comandos na estrutura de “scripts”:

 ~~~
"dev:build-server": "webpack --config webpack.server.js --mode=development -w",
"dev:start": "nodemon ./server-build/index.js",
"dev": "npm-run-all --parallel build dev:*"
 ~~~

 Nesse comando usamos o módulo “nodemon” para reiniciar nosso servidor.

 Outro módulo útil que precisamos instalar é o “npm-run-all" que, como o nome diz, apenas executa mais de um comando ao mesmo tempo, de forma paralela.

Sendo assim, vamos instalar o nodemon e o npm-run-all em nosso projeto, com o comando a seguir:


npm install nodemon npm-run-all --save-dev


 Pronto! Todas as instalações e configurações foram realizadas. Por fim, para executar o aplicativo no servidor, precisamos executar o seguinte comando:

npm run dev

Agora, todo o aplicativo é renderizado no servidor e transpilado para HTML simples, que será enviado para o navegador como um site estático.