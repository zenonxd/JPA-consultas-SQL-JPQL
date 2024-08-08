<p align="center">
  <img src="https://img.shields.io/static/v1?label=SpringProfessional - Dev Superior&message=JPA, Consultas SQL e JPQL&color=8257E5&labelColor=000000" alt="Testes automatizados na prática com Spring Boot" />
</p>


# Tópicos

- [Salvando entidade associada para um](#salvando-entidade-associada-para-um---pt-1-2-e-3)
    - [Exemplo 1 - Objeto aninhado](#1-objeto-aninhado)
    - [Exemplo 2 - Passando id direto na requisição](#2-passando-somente-o-id-do-department)

- [Salvando entidade associada para muitos](#salvando-entidades-associadas-para-muitos)


    

# Objetivo

Esse repositório possui muito conteúdo e referências sobre consulta a banco de dados e sobre ferramenta de ORM (JPA),
para quando formos realizar operações/casos de uso.

## Requisitos projeto

Todas as premissas e o sumário com o que deve ser feito está no "Documento de Requesitos DSCommerce.pdf".
Como é algo específico do curso, não colocarei o link, mas você pode adquirir no site [devsuperior]().

## UML

## Sessão JPA e estados das entidades

A JPA faz o gerenciamento das entidades do sistema durante a sessão JPA.

Uma sessão JPA seria o contexto que JPA está realizando operações com entidades durante uma conexão com o banco de
dados.

- EntityManager: é um objeto da JPA que encapsula uma conexão com o banco de dados e que gerencia as entidades durante
uma sessão JPA.

![img.png](img.png)

O JPA, além disso, gerencia as entidades atribuindo estados a ela. Ou seja, ela tem um ciclo de vida.

![img_1.png](img_1.png)


## Salvando entidade associada para um - PT 1, 2 e 3

Quando uma entidade está associada a outra e essa outra, é somente uma. Exemplo:

![img_2.png](img_2.png)

Como salvar uma pessoa associada a um departamento?

[Repositório para fazer o exercício](https://github.com/devsuperior/aula-salvar-para-um)

Temos duas formas de fazer isso: com objeto aninhado, ou seja, passando um nome, salário e um objeto de department 
com seu id, ou passando somente o id do department. Faremos das duas formas!

### 1. Objeto Aninhado

Requisição Post

```json 
POST http://localhost:8080/people
{
  "name": "Nova Pessoa",
  "salary": 8000.0,
  "department": {
  "id": 1
  }
}
```


### O primeiro passo é criar um DTO para recebermos os dados e instanciar um objeto Java.

Esse DTO criado, precisa ter os dados da pessoa + o seu departamento, exatamente igual na imagem acima.

**Como não podemos dentro de um DTO usar uma entidade**. Precisaremos também criar um DepartmentDTO.

<hr>

### O segundo passo é criar um service para realizar a operação

Criaremos o Service juntamente com o método Insert recebendo o DTO criado.

Importar o repository.

Criaremos uma entity Person vazia.

Copiaremos os dados do DTO para a nossa entidade Person.

Para colocar o ID do department dentro da Entity, precisa instanciar um departament para puxar os dados do dto.

Por fim, precisamos converter de Entity para DTO.

Para isso é só nos DTOS, criar um construtor apto a receber um Person. Já no DepartmentDto, ele precisa de um construtor
para receber um Department. (Qualquer coisa ver o video na pt2).

Salva e dá o return.
<hr>

### O terceiro passo é criar um controller

Injetaremos o service e colocaremos na classe o método insert, passando os parâmetros juntamente com a lógica correta.

A lógica é a de sempre... ResponseEntity + URI. (Qualquer coisa ver o video na pt2).

<hr>

### Teste no Postman

Aqui é só dar o Post no HTTP da imagem acima, juntamente com os pârametros JSON. Ao dar send, deve funcionar.


<hr>

### 2. Passando somente o id do department

Requisição Post

```json
POST http://localhost:8080/people
{
  "name": "Nova Pessoa",
  "salary": 8000.0,
  "departmentId": 1
}
```

### O primeiro passo é criar o DTO contendo exatamente a estrutura acima (name, salary e departmentID).

Diferente do exemplo acima, essa classe tera um atributo "Long departmentId".

Para pegar o id do department: o construtor de conversão de entidade para DTO, usaremos entity (parametro), para acessar
o departamento e pegar seu id. (Qualquer coisa, video pt3)
<hr>

### O segundo passo é no service já criado, criar um novo método de insert

Mas dessa vez, o retorno será PersonDTO e não PersonDepartmentDTO.

O raciocínio de lógica é o mesmo. 
<hr>

### O terceiro passo é no Controller criar um novo método de insert também

Fazer as alterações no método.

<hr>

### Teste no Postman

Só dar o HTTP com os dados da imagem acima e esperar a inserção.

## Salvando entidades associadas para muitos

Quando uma entidade é ManyToMany.

![img_5.png](img_5.png)

[Repositório para fazer o exercício](https://github.com/devsuperior/aula-salvar-para-muitos)

Requisição Post:

```json
POST http://localhost:8080/products
        
{
    "name": "Produto novo",
    "price": 1000.0,
    "categories": [
      {
        "id": 2
      },
      {
        "id": 3
      }
    ]
}
```

A intenção é inserir um novo produto, que terá uma lista de categorias aninhada.

### Primeiro Passo 

Criar os DTOS, tanto de Category quanto de Product, com os seus atributos.

Lembrar também, de criar os construtores para versão a conversão dos datos (de entity para DTO). Qualquer coisa, ver
a aula "**salvando entidades associadas para muitos**".

A única coisa diferente é: No ProductDTO para converter de entidade para DTO, na parte da lista de categorias, 
precisamos fazer um for dentro do construtor para inserir direitinho.

![img_6.png](img_6.png)
<hr>

### Segundo passo

Criar um controller e fazer a lógica de sempre.
<hr>

### Terceiro passo

Criar um service e fazer a lógica de sempre. 
A diferença é que para inserir a lista de categorias, precisa fazer um for. Qualquer coisa, ver a aula "**salvando 
entidades associadas para muitos**".
<hr>

### Postman

Só fazer um post com a requisição lá de cima e inserir.


## Evitando degradação de perfomance

## Carregando EAGER e LAZY

## Analisando o carregamento lazy dos funcionários

## Alterando o atributo fetch dos relacionamentos

## Otimizando consultas com cláusula JOIN FETCH

## Entendendo Transactional e open-in-view

## Consultas com Query Methods

## Introdução sobre JPQL

## Polêmica: vale a pena se especializar em JPQL

## Preparando para os estudos de caso de consulta

## URI 2602 - Elaborando a consulta

## Baixando os projetos iniciados dos estudos de caso


## URI 2602 Spring Boot SQL


## URI 2602 Spring Boot JPQL


## URI 2611 Elaborando a consulta


## URI 2611 Spring Boot SQL e JPQL


## URI 2621 Elaborando a consulta


## URI 2621 Spring Boot SQL e JPQL


## Dica - pratique um pouco se você estiver precisando


## URI 2609 Elaborando a consulta


## URI 2609 Spring Boot SQL e JPQL


## URI 2737 Elaborando a consulta


## URI 2737 Solução alternativa


## URI 2737 Spring Boot SQL


## URI 2990 Elaborando a consulta


## URI 2990 Solução alternativa com LEFT JOIN


## URI 2990 Spring Boot SQL e JPQL


## DSCommerce consulta de produtos por nome


## Evitando consultas lentas muitos-para-muitos


## Evitando consultas lentas muitos-para-um com countQuery