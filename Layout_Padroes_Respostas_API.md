# Layout Padrões de Respostas da API

Todos os retornos devem seguir o padrão JSON e conter:

| Campo        | Tipo    | Descrição                                                                 |
|--------------|---------|---------------------------------------------------------------------------|
| status       | string  | `"success"` ou `"error"`                                                  |
| status_code  | integer | Código HTTP da resposta (ex: 200, 201, 400, 401, 404, 422, 500)            |
| message      | string  | Texto descritivo                                                         |
| data         | mixed   | Objeto ou array de dados (opcional, em caso de sucesso)                   |
| errors       | array   | Lista de erros (opcional, em caso de falha)                               |
