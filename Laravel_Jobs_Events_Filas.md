# Guia Definitivo Laravel — Jobs, Dispatch, Events, Listeners, Filas, Notifications, Mail e Observers

## Objetivo

Este documento explica, de forma prática e objetiva, **quando usar**:

- `dispatch()` com **Job**
- **Event + Listener**
- **Queues / Filas**
- **Notifications**
- **Mail / Mailable**
- **Observers**
- **Services**

Além disso, traz exemplos reais, um padrão recomendado para projetos Laravel profissionais e uma explicação clara sobre a diferença entre **Job** e **Fila**.

---

# 1. Visão Geral

No Laravel, existem várias formas de reagir a ações do sistema.

Os cenários mais comuns são:

- executar uma tarefa pesada em segundo plano
- reagir a um acontecimento do sistema
- enviar e-mail
- enviar notificação
- observar mudanças em models
- desacoplar regras de negócio

A dúvida mais comum costuma ser:

> “Uso Job com `dispatch()` ou uso Event + Listener?”

A resposta correta é:

- use **Job** quando você quer executar **uma tarefa**
- use **Event + Listener** quando você quer representar **um acontecimento** e permitir que várias partes do sistema reajam a ele
- use **fila** quando a execução pode ser assíncrona e não precisa bloquear a resposta da aplicação

---

# 2. Regra Mental Mais Importante

Use esta lógica:

## Pergunta 1
**Isso é uma tarefa direta?**

Se sim, use **Job**.

Exemplos:
- gerar PDF
- importar planilha
- enviar relatório
- processar imagem
- sincronizar dados com API externa

## Pergunta 2
**Isso é um acontecimento do sistema?**

Se sim, use **Event**.

Exemplos:
- usuário foi cadastrado
- pedido foi pago
- partida foi confirmada
- pagamento foi aprovado

## Pergunta 3
**A reação pode acontecer em segundo plano?**

Se sim, coloque em **fila**.

---

# 3. O que é Job

Um **Job** representa uma **tarefa executável**.

Normalmente ele é usado quando você quer:

- fazer processamento pesado
- executar algo assíncrono
- organizar responsabilidades
- enviar algo para a fila

## Exemplos de cenários ideais para Job

- enviar e-mail em massa
- gerar ranking
- importar CSV
- processar upload
- recalcular estatísticas
- integrar com gateway de pagamento
- gerar thumbnails de imagens

---

# 4. O que é `dispatch()`

`dispatch()` é a forma mais comum de **disparar um Job**.

Exemplo:

```php
ProcessInvoiceJob::dispatch($invoice->id);
```

Isso significa:

- o Laravel vai despachar a tarefa
- se o Job estiver configurado para fila, ele irá para o worker
- se for síncrono, ele executa no fluxo atual

---

# 5. Quando usar Job com `dispatch()`

Use **Job direto** quando:

- existe uma única responsabilidade
- a ação já está clara
- não há necessidade de várias reações desacopladas
- você quer simplicidade
- o código representa uma tarefa, e não um evento de domínio

## Exemplos práticos

### Exemplo 1 — Gerar PDF

```php
GenerateContractPdfJob::dispatch($contract->id);
```

### Exemplo 2 — Importar planilha

```php
ImportUsersJob::dispatch($path);
```

### Exemplo 3 — Recalcular ranking

```php
RecalculateTeamRankingJob::dispatch($team->id);
```

### Exemplo 4 — Enviar e-mail pesado

```php
SendMonthlyReportJob::dispatch($user->id);
```

---

# 6. Exemplo real de Job

## Criando Job

```bash
php artisan make:job SendWelcomeEmailJob
```

## Exemplo de implementação

```php
<?php

namespace App\Jobs;

use App\Models\User;
use App\Mail\WelcomeMail;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Mail;

class SendWelcomeEmailJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(public int $userId)
    {
    }

    public function handle(): void
    {
        $user = User::find($this->userId);

        if (! $user) {
            return;
        }

        Mail::to($user->email)->send(new WelcomeMail($user));
    }
}
```

## Disparando o Job

```php
SendWelcomeEmailJob::dispatch($user->id);
```

---

# 7. O que é Event

Um **Event** representa **algo que aconteceu no sistema**.

Ele não representa a tarefa em si.

Ele representa o fato.

Exemplos:
- `UserRegistered`
- `OrderPaid`
- `MatchConfirmed`
- `SubscriptionRenewed`

Pense assim:

- **Job** = “faça isso”
- **Event** = “isso aconteceu”

---

# 8. O que é Listener

Um **Listener** é quem **escuta um Event** e reage a ele.

Exemplo:

Evento:
- `OrderPaid`

Listeners:
- `SendPaymentConfirmationEmail`
- `UpdateRevenueMetrics`
- `NotifyAdminAboutOrder`
- `GenerateInvoice`

Ou seja:

- o evento informa o que aconteceu
- os listeners cuidam das consequências

---

# 9. Quando usar Event + Listener

Use **Event + Listener** quando:

- você quer desacoplar o sistema
- um fato pode gerar várias reações
- você quer crescimento sem alterar o código principal
- a regra é mais de domínio do que técnica
- várias partes do sistema dependem daquele acontecimento

## Exemplos reais

### Exemplo 1 — Pedido pago

Evento:
- `OrderPaid`

Listeners:
- enviar e-mail de confirmação
- gerar nota fiscal
- atualizar financeiro
- notificar equipe interna

### Exemplo 2 — Usuário criado

Evento:
- `UserRegistered`

Listeners:
- enviar boas-vindas
- criar preferências iniciais
- registrar analytics
- disparar onboarding

### Exemplo 3 — Partida confirmada

Evento:
- `MatchConfirmed`

Listeners:
- enviar notificação aos times
- atualizar agenda
- registrar histórico
- gerar dados do ranking

---

# 10. Exemplo real de Event + Listener

## Criando Event

```bash
php artisan make:event UserRegistered
```

## Event

```php
<?php

namespace App\Events;

use App\Models\User;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class UserRegistered
{
    use Dispatchable, SerializesModels;

    public function __construct(public User $user)
    {
    }
}
```

## Criando Listener

```bash
php artisan make:listener SendWelcomeNotification --event=UserRegistered
```

## Listener

```php
<?php

namespace App\Listeners;

use App\Events\UserRegistered;
use App\Notifications\WelcomeNotification;
use Illuminate\Contracts\Queue\ShouldQueue;

class SendWelcomeNotification implements ShouldQueue
{
    public function handle(UserRegistered $event): void
    {
        $event->user->notify(new WelcomeNotification());
    }
}
```

## Disparando o evento

```php
event(new UserRegistered($user));
```

---

# 11. Melhor padrão entre os dois

## Quando Job é melhor

Use Job quando:

- a ação é direta
- existe uma única responsabilidade
- você quer código simples
- não precisa modelar um acontecimento do domínio

## Quando Event + Listener é melhor

Use Event + Listener quando:

- existe desacoplamento real
- várias coisas podem acontecer após o fato
- você quer um sistema mais extensível
- o centro da regra é o acontecimento

---

# 12. Melhor padrão profissional

O padrão mais equilibrado costuma ser este:

## Event representa o fato
## Listener reage
## Job executa o processamento pesado

Ou seja:

- o Event diz que algo aconteceu
- o Listener decide o que fazer
- o Job processa a parte pesada em fila

## Exemplo

### Evento

```php
event(new OrderPaid($order));
```

### Listener

```php
<?php

namespace App\Listeners;

use App\Events\OrderPaid;
use App\Jobs\GenerateInvoiceJob;
use Illuminate\Contracts\Queue\ShouldQueue;

class QueueInvoiceGeneration implements ShouldQueue
{
    public function handle(OrderPaid $event): void
    {
        GenerateInvoiceJob::dispatch($event->order->id);
    }
}
```

### Job

```php
<?php

namespace App\Jobs;

use App\Models\Order;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class GenerateInvoiceJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(public int $orderId)
    {
    }

    public function handle(): void
    {
        $order = Order::find($this->orderId);

        if (! $order) {
            return;
        }

        // Gerar nota fiscal
    }
}
```

---

# 13. Quando NÃO exagerar em Event + Listener

Um erro comum é usar Event para tudo.

Exemplos ruins:
- salvar configuração simples
- atualizar um campo interno
- disparar apenas uma ação extremamente direta

Se você tem só isso:

```php
SendWelcomeEmailJob::dispatch($user->id);
```

não precisa obrigatoriamente transformar isso em:

- `UserRegistered`
- `SendWelcomeEmailListener`
- `SendWelcomeEmailJob`

Isso pode virar burocracia desnecessária.

## Regra prática

Se o ganho de desacoplamento for pequeno, use **Job direto**.

---

# 14. Filas no Laravel

As **filas** servem para executar tarefas em segundo plano.

Elas são ideais para tudo que:

- demora
- depende de serviço externo
- não precisa bloquear a resposta do usuário

## Exemplos

- e-mails
- importações
- exportações
- processamento de imagem
- geração de relatórios
- integração com APIs
- notificações pesadas

---

# 15. Quando usar fila

Use fila quando a operação:

- pode demorar
- pode falhar e merecer retry
- não precisa terminar antes da resposta HTTP
- consome CPU, rede ou I/O
- é melhor ficar desacoplada do request

## Não precisa de fila quando:

- a ação é muito rápida
- o resultado precisa existir imediatamente
- a operação faz parte essencial da resposta atual

---

# 16. `ShouldQueue`

Quando um Job implementa `ShouldQueue`, ele é enviado para a fila.

Exemplo:

```php
class ImportUsersJob implements ShouldQueue
{
    //
}
```

Isso também vale para:

- **Listeners**
- **Notifications**
- **Mailables**, em cenários apropriados

---

# 17. Exemplo de Listener em fila

```php
<?php

namespace App\Listeners;

use App\Events\OrderPaid;
use Illuminate\Contracts\Queue\ShouldQueue;

class SendOrderPaidEmail implements ShouldQueue
{
    public function handle(OrderPaid $event): void
    {
        // envio de e-mail
    }
}
```

---

# 18. Exemplo de Job síncrono vs assíncrono

## Job assíncrono

```php
SendInvoiceJob::dispatch($invoice->id);
```

## Job síncrono

```php
SendInvoiceJob::dispatchSync($invoice->id);
```

## Quando usar `dispatchSync()`

Use quando:

- você quer a estrutura de Job
- mas precisa executar imediatamente
- sem passar pela fila

---

# 19. Padrão recomendado para filas

Para projeto profissional, prefira:

- controllers leves
- services para regra de negócio
- jobs para processamento pesado
- events para acontecimentos relevantes
- listeners para reações desacopladas

---

# 20. Exemplo ruim vs exemplo bom

## Exemplo ruim — Controller gordo

```php
public function store(Request $request)
{
    $order = Order::create($request->all());

    Mail::to($order->user->email)->send(new OrderCreatedMail($order));

    // gerar PDF
    // atualizar financeiro
    // notificar admin
    // integrar API externa

    return response()->json($order);
}
```

## Exemplo melhor — Event

```php
public function store(Request $request)
{
    $order = Order::create($request->all());

    event(new OrderCreated($order));

    return response()->json($order);
}
```

## Melhor ainda — listeners + jobs

- `SendOrderCreatedEmailListener`
- `GenerateOrderPdfListener`
- `SyncOrderToERPListener`

Cada um desacoplado.

---

# 21. Quando usar Notification

Use **Notification** quando o objetivo é **notificar um usuário** por um ou mais canais.

Exemplos:
- e-mail
- banco de dados
- Slack
- SMS
- push

## Use Notification quando:

- a intenção principal é comunicar algo ao usuário
- você pode ter múltiplos canais
- quer padronizar comunicação

## Exemplos ideais

- “Seu pedido foi aprovado”
- “Sua assinatura vence amanhã”
- “Sua partida foi confirmada”
- “Seu relatório está pronto”

---

# 22. Exemplo de Notification

```bash
php artisan make:notification OrderPaidNotification
```

```php
<?php

namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;

class OrderPaidNotification extends Notification implements ShouldQueue
{
    use Queueable;

    public function via(object $notifiable): array
    {
        return ['mail', 'database'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject('Pagamento confirmado')
            ->line('Seu pagamento foi confirmado com sucesso.');
    }

    public function toArray(object $notifiable): array
    {
        return [
            'message' => 'Seu pagamento foi confirmado com sucesso.',
        ];
    }
}
```

## Disparo

```php
$user->notify(new OrderPaidNotification());
```

---

# 23. Quando usar Mail / Mailable

Use **Mail / Mailable** quando você quer montar um e-mail de forma clara e estruturada.

## Use Mailable quando:

- o foco é e-mail
- você precisa de template de e-mail
- o conteúdo do e-mail é mais elaborado
- a mensagem não precisa necessariamente passar pelo sistema de Notification

## Exemplos

- proposta comercial
- boleto
- comprovante
- e-mail transacional específico
- e-mail com layout próprio

---

# 24. Exemplo de Mailable

```bash
php artisan make:mail WelcomeMail
```

```php
<?php

namespace App\Mail;

use App\Models\User;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\SerializesModels;

class WelcomeMail extends Mailable implements ShouldQueue
{
    use Queueable, SerializesModels;

    public function __construct(public User $user)
    {
    }

    public function build(): self
    {
        return $this
            ->subject('Bem-vindo ao sistema')
            ->view('emails.welcome');
    }
}
```

## Disparo

```php
Mail::to($user->email)->send(new WelcomeMail($user));
```

---

# 25. Notification vs Mail

## Use Notification quando:

- você quer notificar por vários canais
- o foco é comunicação com usuário
- quer integração com database, mail, Slack, SMS etc.

## Use Mail quando:

- o foco é um e-mail específico
- a composição do e-mail é mais importante
- não há necessidade de múltiplos canais

## Resumo simples

- **Notification** = sistema de notificação
- **Mail** = e-mail propriamente dito

---

# 26. Quando usar Observer

Use **Observer** quando a lógica precisa reagir a eventos do **Eloquent Model**.

Exemplos:
- `created`
- `updated`
- `deleted`
- `saving`
- `saved`

## Use Observer quando:

- a reação depende diretamente do ciclo de vida do model
- você quer centralizar comportamento de um model
- a lógica está fortemente ligada ao model

## Exemplos bons

- ao criar usuário, gerar código interno
- ao salvar produto, gerar slug
- ao excluir registro, limpar arquivos associados

---

# 27. Exemplo de Observer

```bash
php artisan make:observer UserObserver --model=User
```

```php
<?php

namespace App\Observers;

use App\Models\User;
use Illuminate\Support\Str;

class UserObserver
{
    public function creating(User $user): void
    {
        if (empty($user->uuid)) {
            $user->uuid = (string) Str::uuid();
        }
    }
}
```

## Registro do observer

```php
use App\Models\User;
use App\Observers\UserObserver;

public function boot(): void
{
    User::observe(UserObserver::class);
}
```

---

# 28. Quando NÃO usar Observer

Evite Observer quando:

- a regra não pertence realmente ao model
- a ação depende de contexto externo
- você precisa de fluxo explícito e fácil de rastrear
- a lógica pode surpreender quem lê o código

Observer demais pode esconder comportamento importante.

---

# 29. Service também entra nessa história?

Sim.

Embora a dúvida principal normalmente seja sobre Job, Event e Listener, em projeto profissional normalmente também existe **Service**.

## Use Service quando:

- existe regra de negócio reutilizável
- a lógica não pertence ao controller
- a lógica não é apenas reação a evento
- você precisa organizar casos de uso

## Exemplos

- `CreateOrderService`
- `ConfirmMatchService`
- `GenerateBillingService`

---

# 30. Arquitetura recomendada na prática

Um fluxo muito bom é:

## Controller
Recebe request e delega.

## Service
Executa regra principal.

## Event
Representa o acontecimento relevante.

## Listener
Reage ao acontecimento.

## Job
Executa o trabalho pesado.

## Notification / Mail
Comunica usuário.

## Observer
Cuida do ciclo de vida do model quando fizer sentido.

---

# 31. Exemplo completo real

## Cenário

Usuário se cadastra.

## Fluxo recomendado

### Controller

```php
public function store(RegisterRequest $request)
{
    $user = app(RegisterUserService::class)->handle($request->validated());

    return response()->json($user, 201);
}
```

### Service

```php
<?php

namespace App\Services;

use App\Events\UserRegistered;
use App\Models\User;
use Illuminate\Support\Facades\Hash;

class RegisterUserService
{
    public function handle(array $data): User
    {
        $user = User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
        ]);

        event(new UserRegistered($user));

        return $user;
    }
}
```

### Event

```php
<?php

namespace App\Events;

use App\Models\User;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class UserRegistered
{
    use Dispatchable, SerializesModels;

    public function __construct(public User $user)
    {
    }
}
```

### Listener 1

```php
<?php

namespace App\Listeners;

use App\Events\UserRegistered;
use App\Notifications\WelcomeNotification;
use Illuminate\Contracts\Queue\ShouldQueue;

class SendWelcomeNotification implements ShouldQueue
{
    public function handle(UserRegistered $event): void
    {
        $event->user->notify(new WelcomeNotification());
    }
}
```

### Listener 2

```php
<?php

namespace App\Listeners;

use App\Events\UserRegistered;
use App\Jobs\SyncUserToCrmJob;
use Illuminate\Contracts\Queue\ShouldQueue;

class QueueCRMIntegration implements ShouldQueue
{
    public function handle(UserRegistered $event): void
    {
        SyncUserToCrmJob::dispatch($event->user->id);
    }
}
```

### Job

```php
<?php

namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class SyncUserToCrmJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(public int $userId)
    {
    }

    public function handle(): void
    {
        // integração externa
    }
}
```

Esse fluxo é limpo, escalável e profissional.

---

# 32. Tabela de decisão rápida

| Situação | Melhor opção |
|---|---|
| Executar uma tarefa pesada | Job |
| Rodar tarefa em segundo plano | Job com fila |
| Representar algo que aconteceu | Event |
| Reagir a um evento | Listener |
| Reagir a vários efeitos colaterais | Event + múltiplos listeners |
| Notificar usuário por vários canais | Notification |
| Enviar e-mail específico | Mail / Mailable |
| Reagir ao ciclo de vida de model | Observer |
| Organizar regra de negócio | Service |

---

# 33. Padrão prático recomendado para Laravel profissional

## Use Job quando

- a ação é direta
- é processamento
- é tarefa técnica
- pode ir para fila

## Use Event quando

- quer representar fato de domínio
- existe mais de uma consequência
- quer desacoplar o sistema

## Use Listener quando

- precisa reagir a evento
- quer separar responsabilidades

## Use Notification quando

- precisa comunicar usuário

## Use Mail quando

- quer construir e-mail específico

## Use Observer quando

- a lógica está ligada ao ciclo de vida do model

## Use Service quando

- existe caso de uso ou regra de negócio principal

---

# 34. Erros comuns

## Erro 1 — Controller fazer tudo

Controller não deve concentrar regras, e-mails, integrações e processamento.

## Erro 2 — Event para tudo

Nem toda ação precisa virar evento.

## Erro 3 — Observer para lógica complexa

Observer demais pode esconder o fluxo real do sistema.

## Erro 4 — Não usar fila para tarefas pesadas

Isso deixa o sistema lento e piora a experiência do usuário.

## Erro 5 — Misturar responsabilidade

Uma classe deve ter um papel claro.

---

# 35. Regra definitiva

## Use Job com `dispatch()` quando:

- você quer mandar executar uma tarefa

## Use Event + Listener quando:

- você quer dizer que algo aconteceu e permitir reações desacopladas

## Use fila quando:

- a tarefa pode acontecer em segundo plano

## Use Notification quando:

- quer notificar usuário

## Use Mail quando:

- quer enviar e-mail específico

## Use Observer quando:

- quer escutar o ciclo de vida do model

## Use Service quando:

- quer organizar regra de negócio

---

# 36. Resumo final em uma frase

## Regra de ouro

- **Tarefa** = Job
- **Acontecimento** = Event
- **Reação** = Listener
- **Segundo plano** = Queue
- **Comunicação** = Notification / Mail
- **Ciclo do Model** = Observer
- **Regra principal** = Service

---

# 37. Exemplo final ultra resumido

## Job direto

```php
GenerateInvoiceJob::dispatch($order->id);
```

## Evento

```php
event(new OrderPaid($order));
```

## Listener

```php
class SendPaymentConfirmation implements ShouldQueue
{
    public function handle(OrderPaid $event): void
    {
        $event->order->user->notify(new OrderPaidNotification());
    }
}
```

## Observer

```php
class UserObserver
{
    public function creating(User $user): void
    {
        $user->uuid = (string) Str::uuid();
    }
}
```

---

# 38. Job e Fila são coisas diferentes?

Sim. **Job** e **Fila** são coisas diferentes, mas trabalham juntos no Laravel.

## O que é Job

**Job = uma tarefa a ser executada.**

É uma classe que contém uma ação que você quer rodar.

Exemplos de Jobs:

- enviar e-mail
- gerar PDF
- importar CSV
- processar imagem
- sincronizar API
- recalcular ranking

### Exemplo de Job

```php
class SendReportJob
{
    public function handle()
    {
        // lógica da tarefa
    }
}
```

Um **Job define o que deve ser feito**.

## O que é Fila (Queue)

A **fila** é o sistema que executa os Jobs em segundo plano.

Ou seja:

- o Job é a tarefa
- a fila é a infraestrutura que executa as tarefas

## Analogia simples

| Conceito | Analogia |
|---|---|
| Job | Pedido no restaurante |
| Fila | Cozinha processando pedidos |
| Worker | Cozinheiro executando |

## Fluxo

```text
Controller
   ↓
dispatch(Job)
   ↓
Queue
   ↓
Worker executa Job
```

---

# 39. Exemplo prático de Job com fila

## Criando um Job

```bash
php artisan make:job SendInvoiceJob
```

## Job

```php
class SendInvoiceJob implements ShouldQueue
{
    public function handle()
    {
        // enviar email
    }
}
```

## Disparando o Job

```php
SendInvoiceJob::dispatch($invoice->id);
```

Agora acontece:

1. o Job vai para a fila
2. um worker pega o Job
3. o worker executa o `handle()`

---

# 40. Job sem fila

Um Job **não precisa necessariamente usar fila**.

Você pode executar direto:

```php
SendInvoiceJob::dispatchSync($invoice->id);
```

ou:

```php
app(SendInvoiceJob::class)->handle();
```

Nesse caso:

```text
Controller → Job → executa imediatamente
```

Sem fila.

---

# 41. Job com fila

Quando o Job implementa:

```php
ShouldQueue
```

ele vai para a fila.

```php
class SendInvoiceJob implements ShouldQueue
```

## Fluxo

```text
Controller
   ↓
dispatch
   ↓
Queue
   ↓
Worker
   ↓
Job executa
```

---

# 42. O que é Worker

O **Worker** é o processo que fica rodando e executa os Jobs da fila.

## Comando

```bash
php artisan queue:work
```

Ele fica:

- escutando fila
- pegando jobs
- executando `handle()`

---

# 43. Tipos de filas no Laravel

Laravel suporta várias filas:

| Driver | Uso |
|---|---|
| `sync` | executa imediatamente |
| `database` | salva jobs no banco |
| `redis` | fila rápida |
| `sqs` | AWS |
| `beanstalkd` | fila tradicional |

## Exemplo de `.env`

```env
QUEUE_CONNECTION=redis
```

---

# 44. Exemplo real completo

## Controller

```php
public function store()
{
    $order = Order::create();

    SendInvoiceJob::dispatch($order->id);

    return response()->json(['ok']);
}
```

## Job

```php
class SendInvoiceJob implements ShouldQueue
{
    public function handle()
    {
        // enviar email
    }
}
```

## Worker rodando

```bash
php artisan queue:work
```

## Fluxo real

```text
User request
   ↓
Controller
   ↓
dispatch Job
   ↓
Queue
   ↓
Worker
   ↓
Job handle()
```

---

# 45. Regra simples para lembrar

| Conceito | O que é |
|---|---|
| Job | a tarefa |
| Queue | a fila que armazena e organiza tarefas |
| Worker | o processo que executa tarefas |

---

# 46. Frase definitiva

**Job é a tarefa.**  
**Queue é a fila que executa a tarefa.**  
**Worker é quem processa essa fila.**

---

# 47. Conclusão

O melhor padrão não é escolher apenas um entre **Job** ou **Event + Listener**.

O melhor padrão é entender o papel de cada peça:

- Job resolve tarefa
- Event modela acontecimento
- Listener trata reação
- Queue melhora performance e desacoplamento
- Notification comunica por canais
- Mail cuida do e-mail
- Observer escuta models
- Service organiza regra de negócio

Quando usados do jeito certo, esses recursos deixam o projeto:

- mais limpo
- mais escalável
- mais testável
- mais profissional

---

# 48. Referências oficiais

- Laravel Events: https://laravel.com/docs/12.x/events
- Laravel Queues: https://laravel.com/docs/12.x/queues
- Laravel Notifications: https://laravel.com/docs/12.x/notifications
- Laravel Mail: https://laravel.com/docs/12.x/mail
- Laravel Eloquent / Observers: https://laravel.com/docs/12.x/eloquent
- Laravel Container: https://laravel.com/docs/12.x/container
- Laravel Mocking / Bus Fake: https://laravel.com/docs/12.x/mocking
