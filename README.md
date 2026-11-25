# Repositório das Letras — Empréstimos

Microsserviço de **Empréstimos** do sistema [Repositório das Letras](https://github.com/kauanysartor/Repositorio-das-Letras). O documento de requisitos e o descritivo técnico completo estão no repositório principal.


Registra empréstimos e devoluções. Valida o leitor e o livro antes de emprestar.

## Endpoints

| Método | Rota                                                    | Descrição                                          |
|--------|---------------------------------------------------------|----------------------------------------------------|
| POST   | `/api/emprestimos`                                      | Registra empréstimo (aciona integrações 1 e 2)     |
| GET    | `/api/emprestimos`                                      | Lista os empréstimos                               |
| GET    | `/api/emprestimos/{id}`                                 | Consulta empréstimo com dados do leitor e do livro |
| PUT    | `/api/emprestimos/{id}/devolucao`                       | Registra devolução                                 |
| GET    | `/api/emprestimos/livros/{codigoLivro}/disponibilidade` | Consulta a disponibilidade de um livro             |

Documentação interativa (Scalar) em `/scalar`.

## Integrações

| # | Tipo                   | Serviço chamado                                                   | Rota                     |
|---|------------------------|-------------------------------------------------------------------|--------------------------|
| 1 | Busca de dados simples | [Leitores](https://github.com/alexsanderantunesbarbosa/rdl-leitores) | `GET /api/leitores/{id}` |
| 2 | Busca de dados simples | [Livros](https://github.com/alexsanderantunesbarbosa/rdl-livros)     | `GET /api/livros/{id}`   |

- **Integração 1:** busca o leitor e valida se está ativo e sem bloqueio. Os dados do leitor aparecem na consulta do empréstimo.
- **Integração 2:** busca o livro e compara a quantidade de exemplares com os empréstimos em aberto neste serviço para saber se há exemplar disponível.

As chamadas ficam nas classes `LeitorClient` e `LivroClient`, com as URLs lidas da configuração.

## Configuração

| Variável                               | Descrição                                   | Padrão local                   |
|----------------------------------------|---------------------------------------------|--------------------------------|
| `LivrosUrl`                            | URL base do serviço de Livros               | `http://localhost:5001`        |
| `LeitoresUrl`                          | URL base do serviço de Leitores             | `http://localhost:5002`        |
| `ConnectionStrings__DefaultConnection` | Conexão com o banco SQLite                  | `Data Source=Emprestimos.db`   |
| `PORT`                                 | Porta informada ao Render (usar `8080`)     | —                              |

No Render, `LivrosUrl` e `LeitoresUrl` devem apontar para as URLs públicas (`https://...onrender.com`) dos outros serviços.

## Como executar localmente

Subir Livros e Leitores antes. Depois:

```
cd AppTemplate
dotnet run
```

Acessar `http://localhost:5003/scalar`.

## Docker

```
docker build -t rdl-emprestimos .
docker run -p 8080:8080 -e LivrosUrl=<url-livros> -e LeitoresUrl=<url-leitores> rdl-emprestimos
```

## Outros microsserviços

- [Livros](https://github.com/alexsanderantunesbarbosa/rdl-livros)
- [Leitores](https://github.com/alexsanderantunesbarbosa/rdl-leitores)
- [Multas](https://github.com/alexsanderantunesbarbosa/rdl-multas)
