
# Comandos Úteis no Laravel

Este documento contém uma lista de comandos úteis no desenvolvimento de aplicações Laravel, organizados por categorias para facilitar a consulta.

---

## **Comandos Gerais**

1. **`php artisan serve`**  
   - Inicia o servidor de desenvolvimento local do Laravel.  
   - URL padrão: `http://localhost:8000`.

2. **`php artisan config:cache`**  
   - Combina todas as configurações em um único cache otimizado.  
   - Útil para produção.

3. **`php artisan cache:clear`**  
   - Limpa o cache de configuração, rotas e views.

4. **`php artisan optimize`**  
   - Otimiza o carregamento automático de arquivos e o cache de configuração.  
   - Útil em ambientes de produção.

5. **`php artisan tinker`**  
   - Inicia um REPL (ambiente interativo) para executar comandos PHP no contexto do Laravel.

6. **`php artisan storage:link`**  
   - Cria um link simbólico entre `storage` e `public/storage`, permitindo acesso público a arquivos.

7. **`php artisan route:list`**  
   - Lista todas as rotas registradas no aplicativo, exibindo métodos, URIs e controladores associados.

---

## **Comandos para Seeders, Migrations e Factories**

1. **`php artisan migrate`**  
   - Aplica as migrações para criar ou atualizar o banco de dados.

2. **`php artisan migrate:rollback`**  
   - Reverte a última migração executada.

3. **`php artisan migrate:refresh`**  
   - Reverte e reaplica todas as migrações do banco de dados.  
   - Útil para reiniciar o banco durante o desenvolvimento.

4. **`php artisan db:seed`**  
   - Executa as Seeders, preenchendo o banco de dados com dados de teste.  
   - Combine com `php artisan migrate --seed` para migrar e popular.

5. **`php artisan make:model NomeDoModel --migration`**  
   - Cria um modelo junto com uma migração associada.

6. **`php artisan make:controller NomeDoController --resource`**  
   - Gera um controller com os métodos básicos para CRUD.

7. **`php artisan make:migration nome_da_migration`**  
   - Cria um arquivo de migração na pasta `database/migrations`.

8. **`php artisan make:seeder NomeDoSeeder`**  
   - Cria uma nova classe de Seeder em `database/seeders`.

---

## **Comandos Relacionados ao Vite**

1. **`npm run dev`**  
   - Inicia o Vite em modo de desenvolvimento para recarregar mudanças automaticamente.

2. **`npm run build`**  
   - Compila e otimiza os assets para produção.

3. **`php artisan vite:publish`**  
   - Publica os assets do Vite para produção (se necessário).

4. **`npm install vue@next vue-loader@next`**  
   - Instala o Vue.js e o loader para processamento de arquivos `.vue`.

---


## **Como Utilizar?**

Você pode executar os comandos no terminal a partir do diretório do seu projeto Laravel. Certifique-se de que o ambiente esteja configurado corretamente, com PHP, Composer e Node.js instalados.

Se precisar de explicações detalhadas ou ajuda com algum comando específico, fique à vontade para pedir!
