# Bibliotecas de Alerts para Laravel

Existem várias bibliotecas populares para gerenciar e exibir alertas (notificações) em aplicações Laravel. Abaixo, algumas opções recomendadas e como integrá-las:

---

## 1. SweetAlert2

**Descrição:** Biblioteca de alertas bonitos e interativos, com modais, botões e animações.

**Instalação:**
```bash
npm install sweetalert2
```

**Incluir no Blade (resources/views/layouts/app.blade.php):**
```html
<script src="https://cdn.jsdelivr.net/npm/sweetalert2@10"></script>
```

**Uso no Controller:**
```php
session()->flash('success', 'Espaço atualizado com sucesso!');
```

**Exibição no Blade:**
```blade
@if (session('success'))
    <script>
        Swal.fire({
            title: 'Sucesso!',
            text: "{{ session('success') }}",
            icon: 'success',
            confirmButtonText: 'Ok'
        });
    </script>
@endif
```

---

## 2. Toastr

**Descrição:** Notificações no estilo "toast", discretas e visíveis no canto da tela.

**Instalação:**
```bash
npm install toastr
```

**Incluir no Blade:**
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/toastr.js/latest/toastr.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/toastr.js/latest/toastr.min.js"></script>
```

**Uso no Controller:**
```php
session()->flash('toastr_message', 'Operação realizada com sucesso!');
```

**Exibição no Blade:**
```blade
@if(session('toastr_message'))
    <script>
        toastr.success("{{ session('toastr_message') }}");
    </script>
@endif
```

---

## 3. Laravel Noty

**Descrição:** Integração do Laravel com a biblioteca Noty, leve e configurável.

**Instalação via Composer:**
```bash
composer require laravel-noty/laravel-noty
php artisan vendor:publish --provider="LaravelNoty\LaravelNotyServiceProvider"
```

**Uso no Controller:**
```php
Noty::success('Mensagem de sucesso!');
```

**Incluir no Blade:**
```html
<script src="{{ asset('vendor/noty/noty.min.js') }}"></script>
<link rel="stylesheet" href="{{ asset('vendor/noty/noty.css') }}">
```

---

## 4. Notify.js

**Descrição:** Biblioteca simples para notificações em estilo toast, fácil de configurar.

**Instalação:**
```bash
npm install notifyjs
```

**Incluir no Blade:**
```html
<script src="https://cdn.jsdelivr.net/npm/notifyjs"></script>
```

**Uso no Controller:**
```php
session()->flash('notify_message', 'Operação realizada com sucesso!');
```

**Exibição no Blade:**
```blade
@if(session('notify_message'))
    <script>
        $.notify("{{ session('notify_message') }}", { position: "top center", className: "success" });
    </script>
@endif
```

---

## Dicas de escolha

- **SweetAlert2:** ideal para alertas bonitos e interativos.  
- **Toastr:** excelente para notificações discretas em estilo toast.  
- **Noty:** alternativa leve e altamente configurável.  
- **Notify.js:** simples e eficaz para notificações rápidas.
