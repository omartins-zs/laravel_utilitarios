
# Resolver Erros do Laravel Herd

Este documento oferece soluções para erros comuns enfrentados ao usar Laravel com o Herd.

---

## **Erro: Porta 80 em Uso**

**Mensagem de Erro:**  
`2024/10/24 15:00:01 [emerg] 10744#10672: bind() to 127.0.0.1:80 failed (10013: Foi feita uma tentativa de acesso a um soquete de uma maneira que é proibida pelas permissões de acesso)`

### **Resolução**

1. **Verificar se outra aplicação está usando a porta 80:**  
   Execute o Prompt de Comando como administrador e insira o seguinte comando:  

   ```
   netstat -aon | findstr :80
   ```

   - Normalmente, o PID 4 está associado ao "Sistema", indicando que um processo do sistema operacional (como o Serviço de Publicação na World Wide Web) está usando a porta.

2. **Alterar a porta de escuta no NGINX:**  
   Caso não seja essencial usar a porta 80, configure o NGINX para escutar em uma porta diferente, como a 8080.  
   No arquivo de configuração do NGINX, localize a diretiva `listen` e altere para:  

   ```
   listen 127.0.0.1:8080;
   ```

---

## **Erro: Extensão PCNTL Faltando**

**Mensagem de Erro:**  
`Erro com o Pail: The [pcntl] extension is required to run Pail.`

### **Resolução**

1. **Remover ou ignorar o Pail:**  
   Caso o Pail não seja essencial para o seu projeto, remova-o com o seguinte comando:  

   ```
   composer remove laravel/pail
   ```

---

Para mais dúvidas ou problemas, sinta-se à vontade para procurar ajuda na [documentação oficial do Laravel](https://laravel.com/docs) ou em comunidades de desenvolvedores.

