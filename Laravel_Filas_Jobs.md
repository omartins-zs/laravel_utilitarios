# 📦 Filas e Jobs no Laravel

Este documento descreve **todas as principais propriedades e atributos** que podem ser utilizados em **Jobs do Laravel**, explicando **o que fazem**, **quando usar** e **exemplos práticos**.

---

## 📌 O que é um Job no Laravel?

Um **Job** representa uma tarefa que pode ser executada de forma **assíncrona** através do sistema de filas (Queues).

Exemplos de uso:
- Envio de e-mails
- Processamento de arquivos
- Integração com APIs externas
- Tarefas pesadas ou demoradas

Todo Job que usa filas implementa:

```php
implements ShouldQueue
```

---

## 🧵 Propriedades e Atributos dos Jobs

Abaixo estão as **principais propriedades que podem ser declaradas diretamente na classe do Job**.

---

## 🔹 $tries

Define **quantas vezes o Job será tentado** antes de ser marcado como falha definitiva.

### Quando usar
- Quando o Job depende de serviços externos (API, webhook, pagamento)
- Para evitar falhas definitivas por erros temporários

### Exemplo
```php
public int $tries = 3;
```

---

## 🔹 $timeout

Define o **tempo máximo (em segundos)** que o Job pode executar.

### Exemplo
```php
public int $timeout = 120;
```

---

## 🔹 $maxExceptions

Define o **número máximo de exceções** que o Job pode lançar antes de falhar.

### Exemplo
```php
public int $maxExceptions = 2;
```

---

## 🔹 $backoff

Define o **tempo de espera entre uma tentativa e outra**.

### Exemplo
```php
public int $backoff = 30;
```

---

## 🔹 $backoff progressivo

Permite definir **tempos diferentes para cada tentativa**.

### Exemplo
```php
public array $backoff = [10, 30, 60];
```

---

## 🔹 retryUntil()

Define **até quando o Job pode continuar tentando**.

### Exemplo
```php
public function retryUntil()
{
    return now()->addMinutes(15);
}
```

---

## 🔹 $failOnTimeout

Define se o Job deve ser **marcado como falha** quando estourar o timeout.

### Exemplo
```php
public bool $failOnTimeout = true;
```

---

## 🔹 $queue

Define **em qual fila** o Job será executado.

### Exemplo
```php
public string $queue = 'emails';
```

---

## 🔹 $connection

Define **qual conexão de fila** será usada.

### Exemplo
```php
public string $connection = 'redis';
```

---

## 🚨 Método failed()

Executado **quando o Job falha definitivamente**.

### Exemplo
```php
public function failed(Throwable $exception)
{
    Log::error('Job falhou', [
        'erro' => $exception->getMessage()
    ]);
}
```

---

## ✅ Boas Práticas

- Sempre definir tries, timeout e backoff
- Usar filas diferentes para cargas diferentes
- Monitorar falhas com Horizon
