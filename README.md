# TESTE
## 1. Crie um repositório no github
- Acesse o github e crie um novo repositório que já inclua o arquivo README.md.

<img src="imagens/img01.jpeg">

## 2. Crie um codespace
- Caso o repositório não tenha sido aberto automaticamente, abra-o
- Clique no botão verde `<>Code`
-Vá para a aba "Codespaces" e selecione a opção “Create Codespace on main” para criar um novo Codespace.
-O Codespace abrirá automaticamente

<img src="imagens/img02.jpeg">

-Irá aparecer a seguinte tela:

<img src="imagens/img03.jpeg">

## 3. Configurando o Projeto
- Caso o terminal não tenha sido aberto automaticamente, abra-o
- Use o atalho Ctrl+" ou clique em "Terminal"-> "Novo terminal"

<img src="imagens/img04.jpeg">

- Digite o seguinte comando no terminal e após escrever aperte "enter"
```bash  
npm init -y
```
- Digite o seguinte comando no terminal e após escrever aperte "enter"
```bash
npm install express cors sqlite3 sqlite
```
- Digite o seguinte comando no terminal e após escrever aperte "enter"
```bash
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
```
- Digite o seguinte comando no terminal e após escrever aperte "enter"
```bash
npx tsc --init
```
- Digite o seguinte comando no terminal e após escrever aperte "enter"
```bash
mkdir src public
```
- Digite o seguinte comando no terminal e após escrever aperte "enter"
```bash
touch src/app.ts src/database.ts public/index.html public/index.css teste.http
```
- Depois de todos esses comandos seu projeto precisa ter os seguintes arquivos:

<img src="imagens/img05.jpeg">

## 4. Configurando o arquivo tsconfig.json
- Abra o arquivo tsconfig.json para alterar a linha que estiver escrito "outDir: "./" (linha 58 do arquivo)

<img src="imagens/img06.jpeg">

- Tire os comentários // e mude para "outDir: "./dist", pule uma linha e adicione "rootDir": "./src"
- Deverá ficar assim:

<img src="imagens/img07.jpeg">

## 5. Configure o arquivo package.json
- Abra o arquivo package.json para alterar a linha que estiver escrito "Script": (linha 6 do arquivo)

<img src="imagens/img08.jpeg">

- Na linha baixo de `"Script":` estará escrito<br>
`"test": "echo \"Error: no test specified\" && exit 1"` 
- Você deverá adicionar uma `,` no final e pular uma linha para adicionar o seguinte código:
```bash
"dev": "npx nodemon src/app.ts"
```
Seu código deverá ficar assim:

<img src="imagens/img09.jpeg">

## 6. Código do servidor
- Selecione o arquivo app.ts

<img src="imagens/img10.jpeg">

- Adicione o seguinte código neste arquivo:
```bash
import express from 'express';
import cors from 'cors';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

## 7. Iniciando o servidor
- Para iniciar você precisará instalar a extenção REST Client
- No menu aperte em "Extenção" e na barra de pesquisa escreva REST Client 

<img src="imagens/img11.jpeg">

- Depois de instalar a extenção, no terminal digite o seguinte comando:
```bash
npm run dev
```
- Após esse comando irá aparecer o aviso Server running on port 3333 e aperte no botão verder Abrir no navegador

<img src="imagens/img12.jpeg">

- Caso esse botão não apareca você pode abrir o navegador pela seguinte forma:

<img src="imagens/img13.jpeg">

- Se todos os passos estiverem certos irá aparecer a seguinte página com a mensagem Hello world

<img src="imagens/img14.jpeg">

## 8. Configurando o BD
- No arquivo `database.ts` adicione o seguinte código:
```bash
import { Database, open } from 'sqlite';
import sqlite3 from 'sqlite3';

let instance: Database | null = null;

export async function connect() {
  if (instance) return instance;

  const db = await open({
     filename: './src/database.sqlite',
     driver: sqlite3.Database
   });
  
  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      email TEXT
    )
  `);

  instance = db;
  return db;
}
```

- Agora no arquivo `app.ts` apague **TODO** o código, copie o seguinte código e cole no arquivo:
```bash
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```
## 9. Teste de inserção de dados
- No arquivo `teste.http` coloque o seguinte código:
```bash
POST  users HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

- Ao lado de Terminal selecione a aba Portas

<img src="imagens/img15.jpeg">

- Irá aparecer o número da sua porta,endereço e a visibilidade 

<img src="imagens/img16.jpeg">

- Agora com o mouse em cima de Private (que está em baixo de visibilidade) aperte com o botão direito e selecione a opção `visibilidade de portas` e selecione `public` 

<img src="imagens/img17.jpeg">

- Agora copie o link do endereço 

<img src="imagens/img18.jpeg">

- Vá no arquivo teste.http e no código e antes de onde estiver escrito `Users` cole o link que foi copiado, deverá ficar assim

<img src="imagens/img19.jpeg">

- Agora, clique na opção `Send Request` que aparecera em cima de `Post`.

<img src="imagens/img20.jpeg">

- E se estiver tudo certo aparecerá a seguinte aba:

<img src="imagens/img21.jpeg">
<br><br><br>
<img src="imagens/img22.jpeg">

## 10. Update e Delete
- No arquivo `teste.http` você precisará adicionar o seguinte código:
```bash
####

PUT  users/1 HTTP/1.1
Content-Type: application/json

{
  "name": "John Doe update",
  "email": "john@example.com"
}

####

DELETE  users/1 HTTP/1.1
```

- O mesmo link que você colocou em POST, você precisará colocar em PUT e no DELETE

<img src="imagens/img23.png">

- Agora vá no arquivo `app.ts` apague **TODO** o código, copie o seguinte código e cole no arquivo:
```bash
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});

app.put('/users/:id', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;
  const { id } = req.params;

  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

  res.json(user);
});

app.delete('/users/:id', async (req, res) => {
  const db = await connect();
  const { id } = req.params;

  await db.run('DELETE FROM users WHERE id = ?', [id]);

  res.json({ message: 'User deleted' });
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

## 11. Página
- No arquivo `index.html` que está na pasta `public` coloque o seguinte código:
```bash
<!DOCTYPE html>
<html lang="pt-BR">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="index.css">
  <title>Projeto</title>
</head>

<body>
  <div class="container">
    <h1>Projeto-Node</h1>
    <h3>-testando a página</h3>

    <form>
      <input type="text" name="name" placeholder="Nome" required>
      <input type="email" name="email" placeholder="Email" required>
      <button type="submit">Cadastrar</button>
    </form>

    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Nome</th>
          <th>Email</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <!--  -->
      </tbody>
    </table>
  </div>

  <script>
    const form = document.querySelector('form')

    form.addEventListener('submit', async (event) => {
      event.preventDefault()

      const name = form.name.value
      const email = form.email.value

      await fetch('/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email })
      })

      form.reset()
      fetchData()
    })

    const tbody = document.querySelector('tbody')

    async function fetchData() {
      const resp = await fetch('/users')
      const data = await resp.json()

      tbody.innerHTML = ''

      data.forEach(user => {
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${user.id}</td>
          <td>${user.name}</td>
          <td>${user.email}</td>
          <td>
            <button class="excluir">Excluir</button>
            <button class="editar">Editar</button>
          </td>
        `

        const btExcluir = tr.querySelector('button.excluir')
        const btEditar = tr.querySelector('button.editar')

        btExcluir.addEventListener('click', async () => {
          await fetch(`/users/${user.id}`, { method: 'DELETE' })
          tr.remove()
        })

        btEditar.addEventListener('click', async () => {
          const name = prompt('Novo nome:', user.name)
          const email = prompt('Novo email:', user.email)

          await fetch(`/users/${user.id}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
          })

          fetchData()
        })

        tbody.appendChild(tr)
      })
    }

    fetchData()
  </script>
</body>

</html>
```

## 12. Estilizando a página
- No arquivo `index.css` coloque o seguinte código:
```bash
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  
  body {
    background-image: url(https://i.pinimg.com/564x/d8/53/49/d85349f7e91f366aee9f3e21f468f4bf.jpg);
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    color: #fff;
    font-family: Arial, sans-serif;
    text-align: center;
    line-height: 1.5;
  }
  
  .container {
    background: rgba(0, 0, 0, 0.658); 
    border-radius: 10px;
    padding: 20px;
    max-width: 800px;
    margin: 50px auto;
  }
  
  h1 {
    font-size: 2.5em;
    margin-bottom: 10px;
  }
  
  h3 {
    font-size: 1.5em;
    margin-bottom: 20px;
  }
  
  form {
    margin-bottom: 20px;
  }
  
  form input[type="text"],
  form input[type="email"] {
    padding: 10px;
    border: none;
    border-radius: 5px;
    margin: 5px;
    width: calc(50% - 22px);
  }
  
  form button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    background-color: #0b3868;
    color: #fff;
    cursor: pointer;
    font-size: 1em;
  }
  
  form button:hover {
    background-color: #023469;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  table th, table td {
    padding: 10px;
    border: 1px solid #888888;
  }
  
  table th {
    background-color: rgba(19, 47, 80, 0.6);
  }
  
  table td {
    background-color: rgba(19, 68, 80, 0.6);
  }
  
  table button {
    padding: 5px 10px;
    border: none;
    border-radius: 5px;
    color: #fff;
    cursor: pointer;
    font-size: 0.9em;
    margin: 2px;
  }
  
  table button.excluir {
    background-color: #e91111;
  }
  
  table button.excluir:hover {
    background-color: #c82333;
  }
  
  table button.editar {
    background-color: #575757;
  }
  
  table button.editar:hover {
    background-color: #218838;
  }
  
  
```

- Agora volte no arquivo `app.ts`, procure o código em que esteja escrito:<br>
`app.use(express.json());` (possivelmente seja a linha 9)

<img src="imagens/img24.png">

- Em baixo desse código coloque o seguinte código
```bash
app.use(express.static(__dirname + '/../public'))
```

<img src="imagens/img25.png">
