
# Comandos para Configuração e Cache no Laravel

Este documento lista comandos úteis relacionados à configuração e ao cache no Laravel, com instruções adicionais para acessos locais na rede.

---

## **Comandos de Configuração e Cache**

1. **`php artisan config:cache`**  
   - Combina todas as configurações em um único arquivo cache para melhorar o desempenho.  
   - Use em ambientes de produção.

2. **`php artisan config:clear`**  
   - Limpa o cache de configuração para garantir que as alterações no arquivo de configuração sejam aplicadas.

3. **`php artisan cache:clear`**  
   - Limpa todos os dados armazenados no cache do aplicativo.

4. **`php artisan view:clear`**  
   - Remove os arquivos de cache das views armazenadas.

5. **`php artisan view:cache`**  
   - Compila todas as views e as armazena em cache.  
   - Útil para otimizar aplicações em produção.

6. **`php artisan route:clear`**  
   - Remove o cache de rotas, se existir.

7. **`php artisan route:cache`**  
   - Compila todas as rotas registradas em um único arquivo para desempenho otimizado.  
   - Apenas rotas baseadas em controladores podem ser armazenadas em cache.

8. **`php artisan clear-compiled`**  
   - Remove os arquivos compilados e o cache do framework.

---

## **Comando para Acessar Localmente na Rede**

Para acessar o servidor local do Laravel a partir de dispositivos na mesma rede, substitua `IP` pelo endereço IP da máquina que está rodando o servidor:

```bash
php artisan serve --host=IP --port=8000
```

- **Exemplo:**  
  Se o endereço IP da máquina for `192.168.1.10`, o comando será:

  ```bash
  php artisan serve --host=192.168.1.10 --port=8000
  ```

  Após executar o comando, você poderá acessar o projeto pelo navegador de outro dispositivo na rede utilizando a URL:

  ```
  http://192.168.1.10:8000
  ```

---

Para dúvidas adicionais, consulte a [documentação oficial do Laravel](https://laravel.com/docs).

