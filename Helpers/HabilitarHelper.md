# 🧩 Registro do Helper no Laravel

⚙️ **Passo 3 — Registrar o helper no autoload do Laravel**

Abra o arquivo `composer.json` e adicione o caminho do helper na seção `"autoload"`:

```json
"autoload": {
    "psr-4": {
        "App\\": "app/"
    },
    "files": [
        "app/Helpers/jhelpers.php"
    ]
}
```

---

⚡ **Passo 4 — Atualizar o autoloader**

Rode no terminal:

```bash
composer dump-autoload
```

Isso garante que o Laravel carregue automaticamente o helper em qualquer lugar do projeto.
