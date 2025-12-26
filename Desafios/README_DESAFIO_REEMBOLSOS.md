# Teste de Programação — Sistema de Reembolsos Corporativos

## Objetivo
Criar um sistema para solicitação e aprovação de reembolsos corporativos.

## Problema
Solicitações feitas por e-mail geram atrasos e falta de controle.

## Solução Proposta
Centralizar solicitações, permitir aprovação e manter histórico rastreável.

## Estrutura do Banco de Dados

```sql
CREATE TABLE Colaboradores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100),
    email VARCHAR(100),
    cargo VARCHAR(100)
);

CREATE TABLE Reembolsos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    colaborador_id INT,
    descricao VARCHAR(255),
    valor DECIMAL(10,2),
    status VARCHAR(50),
    data_solicitacao DATE,
    data_aprovacao DATE,
    FOREIGN KEY (colaborador_id) REFERENCES Colaboradores(id)
);
```
