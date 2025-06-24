# Layout Padrões de Respostas da API

Todos os retornos devem seguir o padrão JSON e conter:

| Campo   | Tipo    | Descrição                                                           |
|---------|---------|---------------------------------------------------------------------|
| status  | string  | `"success"` ou `"error"`                                            |
| message | string  | Texto descritivo                                                   |
| data    | mixed   | Objeto ou array de dados (opcional)                                 |
| errors  | array   | Lista de erros (opcional, em caso de falha)                         |
