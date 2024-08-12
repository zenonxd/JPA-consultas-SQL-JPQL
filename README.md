<p align="center">
  <img src="https://img.shields.io/static/v1?label=SpringProfessional - Dev Superior&message=JPA, Consultas SQL e JPQL&color=8257E5&labelColor=000000" alt="Testes automatizados na prática com Spring Boot" />
</p>


# Tópicos

- [Salvando entidade associada para um](#salvando-entidade-associada-para-um---pt-1-2-e-3)
    - [Exemplo 1 - Objeto aninhado](#1-objeto-aninhado)
    - [Exemplo 2 - Passando id direto na requisição](#2-passando-somente-o-id-do-department)
<hr>

- [Salvando entidade associada para muitos](#salvando-entidades-associadas-para-muitos)
<hr>

- [Evitando degradação de perfomance (Lentidão JPA)](#evitando-degradação-de-perfomance-lentidão-jpa)
  - [Carregamento Eager e Lazy](#carregando-eager-e-lazy)
  - [Analisando Lazy (Classe Employee)](#analisando-o-carregamento-lazy-dos-funcionários)
  - [Alterando atributo fetch dos relacionamentos](#alterando-o-atributo-fetch-dos-relacionamentos)
  - [Otimizando consultas com Join Fetch](#otimizando-consultas-com-cláusula-join-fetch)
  - [Transactional e open-in-view](#entendendo-transactional-e-open-in-view)
<hr>

- [Alterando atributo fetch dos relacionamentos (não recomendado)](#alterando-o-atributo-fetch-dos-relacionamentos)
  - [Eager (não recomendado)](#eager)
  - [Lazy (não recomendado)](#lazy)
<hr>

- [Otimizando consulta com Join Fetch](#otimizando-consultas-com-cláusula-join-fetch)
<hr>

- [Entendendo transactional](#entendendo-transactional)
- [open-in-view](#open-in-view)
<hr>

- [Consultas customizadas](#consultas-customizadas)
  - [Query methods](#query-methods)
    - [Buscando employee por name](#buscando-employee-por-name)
  - [Introdução JPQL](#introdução-sobre-jpql)
    - [Exemplo 1](#exemplo-1)
    - [Exemplo 2](#exemplo-2)
  - [Vale a pena se especializar em JPQL?](#polêmica-vale-a-pena-se-especializar-em-jpql)
<hr>

## Estudos de caso

👇 Para utilizar nos casos de uso.

- [Como fazer projeção de dados (limitar campo de pesquisa)](#projeção-de-dados-limitação-de-campo)
- [Convertendo projeção para DTO](#convertendo-projection-para-dto)


- [URI 2602 - Elaborando Consulta](#uri-2602---elaborando-consulta)
  - [SQL](#2602---sql-)
  - [JPQL](#2602---jpql)

  
- [URI 2611 - Elaborando consulta]()
  - [SQL]()
  - [JPQL]()


- [URI 2621 - Elaborando consulta]()
  - [SQL]()
  - [JPQL]()

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

## Evitando degradação de performance (Lentidão JPA)

Nós podemos melhorar o desempenho da JPA usando ela de forma apropriada.

Como evitar a lentidão da JPA? Com carregamento lazy, tratativas e transactional. 

Grande vilão da JPA: indas e vindas desnecessárias ao banco de dados.

Uma causa comum: entidades associadas lazy carregando sob demanda.

[Projeto exemplo](https://github.com/devsuperior/aula-lazy)

[Collection do Postman](https://www.postman.com/collections/d5efb333d1d308d52b7c)

![img_7.png](img_7.png)

## Carregando EAGER e LAZY

Temos um carregando padrão para entidades que estão associadas.

ToOne - **EAGER**: Um carregamento apressado. Se carregarmos uma entidade e ela tiver outra entidade associada (para um),
essa outra entidade virá junto, na mesma hora.

ToMany - **LAZY**: Um carregamento mais preguiçoso/tardio. Se carregarmos uma entidade e tiver outras associadas (para
muitos), essas outras não serão carregadas.

## Analisando o carregamento lazy dos funcionários

Em virtude do comoportamento lazy (carregar de forma tardia) os objetos, devemos citar o seguinte: enquanto a sessão
JPA estiver ATIVA, o acesso a um objeto associado pode provocar uma nova consulta ao banco.

Ou seja, se buscarmos um departamento pelo ID, ele não irá trazer os funcionários. Mas se a sessão tiver ativa e dermos
um ".getEmployes" nesse departamento a gente pode disparar uma nova consulta ao banco de dados.

Exemplo:

Postman 

Pegando o department pelo Id 1 e depois, pegando os funcionários desse departamento.

![img_8.png](img_8.png)

Console

![img_9.png](img_9.png)


## Alterando o atributo fetch dos relacionamentos

E se quiséssemos trocar o comportamento acima e fazer somente uma consulta?

Existem 3 formas:

1. Atributo fetch no relacionamento da entidade (não recomendado);
2. Cláusula JOIN FETCH;
3. Consulta customizada (ideal).

O item 1 não é recomendado, pois você muda o comportamento padrão daquele relacionamento. Então se fizermos essa 
alteração, ela passa a ser o padrão da aplicação.

Em resumo, ocorreria: sempre que buscássemos o departamento, viria também os funcionários.

### Eager

Na classe Department

![img_10.png](img_10.png)

Assim, ao buscar um departamento específico ele sempre fará o left join com a tabela employee.

![img_11.png](img_11.png)

<hr>

### Lazy

Se buscássemos um funcionário por seu ID, chamando os dados minimos, dessa forma:

![img_12.png](img_12.png)

Ele retornaria o que desejamos:

![img_13.png](img_13.png)

Mas no console, se observarmos, ele também consultará o departamento, pois como padrão estará EAGER.

![img_14.png](img_14.png)

Podemos fazer a troca do atributo para Lazy. LEMBRANDO: ESTA FORMA NÃO É O IDEAL.

![img_15.png](img_15.png)

Agora, a consulta será somente da tabela de funcionários:

![img_16.png](img_16.png)

Abaixo, faremos da forma correta! Veja 👇

## Otimizando consultas com cláusula JOIN FETCH

❗Importante dizer, essa cláusula não funciona para busca paginadas do Spring.

Na classe de EmployeeRepository, criaremos um método com uma consulta personalizada

Exemplo:

```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    @Query("SELECT obj FROM Employee obj")
    List<Employee> findEmployeesWithDepartments();
}
```

Importante ressaltar, essa consulta na query não é SQL e sim JPQL, uma linguagem de consulta da JPA. A semântica muda,
**precisamos dar um "apelido" pro objeto que vamos buscar. Como colocamos acima: "obj".** E no final, **não precisa
colocar o nome da tabela e sim da classe, conforme colocamos: Employee.**

❗Importante

A JPA mantém um "cache" das entidades gerenciadas na mesma sessão JPA.

Ou seja, se você trouxer essas entidades para a memória, A JPA não volta ao banco se você precisar novamente delas
(desde que seja a mesma seção JPA).

Com o código daquele jeito acima, ele buscaria alguns departamentos até achar o correto.

Para que isso não aconteça, utilizamos o **JOIN FETCH**, veja:

```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    @Query("SELECT obj FROM Employee obj JOIN FETCH obj.department")
    List<Employee> findEmployeesWithDepartments();
}
```

Consulta otimizada no console, buscando por funcionários já fazendo o Join no department:

![img_17.png](img_17.png)

## Entendendo Transactional

A annotation @Transactional assegura:

1. resolução da transação com o banco de dados;
2. resolução de todas as pendências "lazy" com o banco de dados.


## open-in-view

A propriedade spring.jpa.open-in-view=false faz com que a sessão JPA seja encerrada antes de voltar para a camada 
controller (camada web).

**A ideia é que assim que o controlador receba a requisição, a JPA não esteja mais aberta. Tudo será feito na camada de
serviço (pendências lazy, transações...).**

## Consultas Customizadas

Consultas personalizadas, com filtros e critérios específicos.

## Query Methods

No JpaRepository do Spring Data JPA, é possivel fazer uma consulta customizadas apenas pelo nome do método.

Exemplo:

```java
public interface UserRepository extends Repository<User, Long> {
    //escrevendo somente isso com os parâmetros, ele vai funcionar
  List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```

[Veja mais](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)

^ Embaixo nesse link tem uma tabela com "keywords" suportadas pela JPA para realizar os métodos.

[Veja aqui as keywords](https://docs.spring.io/spring-data/jpa/reference/repositories/query-keywords-reference.html)

**Vale a pena utilizar?**

Para consultas muito simples, sim.

Mais complexas? Melhor escrever a consulta, pois já dominamos a consulta SQL.

### Buscando Employee por name

![img_18.png](img_18.png)

No Spring, criaremos um método para essa consulta, veja:

No Controller:
```java
@GetMapping
public ResponseEntity<List><EmployeeMinDTO>> findByName(
        //default value é: se o nome não for informado, por padrão será
        //uma string vazia
        @RequestParam(name= "name", defaultValue = "") String name) {
        
    List<EmployeeMinDTO> result = service.findByName(name);
    return ResponseEntity.ok(result);
}
```
<hr>

No Service:
```java
@Transactional(readOnly = true)
public List<EmployeeMinDTO> findByName(String name) {
    
    //criaremos esse método abaixo no repository
    List<Employee> result = repository.findByName(name);
    
    //convertendo o result acima para um tipo DTO
    return result.stream().map(x -> new EmployeeMinDTO(x)).toList();
}
```
<hr>

No Repository:
```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    
    //caso quiséssemos procurar por "maria" ou "Maria", colocar
    // "IgnoreCase" no final dos métodos (aqui e no service também).
    List<Employee> findByName(String name);
}
```

## Introdução sobre JPQL

É uma linguagem de consulta específica da JPA.

Toda ferramente ORM geralmente possui uma linguagem ou ferramentas próprias para realização de consulta a banco de dado.

A JPQL é parecida com a SQL, porém é adaptado para modelo de acesso a dados JPA.

### Exemplo 1

SQL:
```sql
SELECT *
FROM tb_employee
WHERE UPPER(name) LIKE 'MARIA%'
```

JPQL
```sql
SELECT obj

//não escrevemos a tabela e sim Entidade + obj
FROM Employee obj
//obj.name pois estamos acessando o atributo
WHERE UPPER(obj.name) LIKE 'MARIA%'
```
<hr>

### Exemplo 2

SQL:
```sql
SELECT tb_employee.*
FROM tb_employee
INNER JOIN tb_department ON tb_department.id = tb_employee.department_idWHERE tb_department.name = 'Financeiro'
````

JPQL:
```sql
SELECT obj
           
//não escrevemos a tabela e sim Entidade + obj
FROM Employee obj

//obj.department (para acessar dep dentro de employee) + .name para acessar o nome de department
WHERE obj.department.name = 'Financeiro'
```

## Polêmica: vale a pena se especializar em JPQL?

**Vantagens**:

1. Algumas consultas podem ficar mais simples;
2. Usufrui melhor do Spring Data JPA (paginação, JpaRepository);
3. Os objetos resultantes são entidades gerenciadas pela JPA.
   4. Se precisarmos dar uma ".get" será automaticamente feita a consulta auxiliar.

**Desvantagens**:

1. Consultas complexas podem ficar difíceis de escrever e validar (mais fácil escrever e testar a consulta SQL)
diretamente na ferramenta de banco de dados;
2. Não tem união (JPA 2.x);
3. Curva de apredizada para uma tecnologia específica.

Em suma, se for consulta simples usa **JPQL**. Se for mais específica e complexa, **SQL**.

## URI 2602 - Elaborando consulta

```sql
SELECT name FROM customers WHERE state = 'RS';
````

### Projeção de Dados (limitação de campo)

Caso a gente queira fazer uma projeção (limitar campos de consulta, acessando somente os campos que precisamos), faremos
o seguinte:

Criaremos um pacote **projections**, com uma interface contendo somente os campos que a gente quer.

```java
public interface CustomerMinProjection {
    String getname();
}
```

E no Repository, podemos criar uma consulta para retornar uma lista de objetos dessa projection, veja:

```java
public interface CustomRepository extends JpaRepository<Customer, Long> {
  //nativeQuery = SQL raíz, não SJPQL
  @Query(nativeQuery = true, value = "SELECT name "
          + "FROM customers "
          + "WHERE UPPER(state) = UPPER(:state)")
  List<CustomerMinProjection> search1(String state);
}

```

❗Importante. Na prática, nós não utilizamos esse Projection e sim um DTO.

## Convertendo Projection para DTO

Criar um pacote DTO e uma classe CustomerMinDTO, contendo:

```java
public class CustomerMinDTO {

    private String name;

    public CustomerMinDTO() {

    }

    public CustomerMinDTO(String name) {
        this.name = name;
    }

    //outro construtor convertendo de Projection para DTO
    public CustomerMinDTO(CustomerMinProjection projection) {
        name = projection.getname();
    }


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
      return "CustomerMinDTO{" +
              "name='" + name + '\'' +
              '}';
    }
}
```

Na classe Application, podemos implementar o CommandLineRunner, e executar a consulta!

```java
@SpringBootApplication
public class Uri2602Application implements CommandLineRunner {

    //repository importado
  @Autowired
  private CustomRepository customRepository;

  public static void main(String[] args) {
    SpringApplication.run(Uri2602Application.class, args);
  }

  @Override
  public void run(String... args) throws Exception {
      
      //lista inicial da projection com a consulta customizada
    List<CustomerMinProjection> list = customRepository.search1("RS");

    List<CustomerMinDTO> result1 = list.stream().map(
            //transformando cada elemento da lista acima em um DTO
            x -> new CustomerMinDTO(x)
    ).collect(Collectors.toList());

    for (CustomerMinDTO obj : result1) {
      System.out.println(obj);
    }
  }
}
```

SOUT console:

![img_20.png](img_20.png)

### 2602 - SQL 

No SQL usamos no Repository a "consulta raiz", conforme pode ser observado no parâmetro do @Query.

### 2602 - JPQL

Já no JPQL, precisamos dar "apelidos" para os nossos objetos, veja:

```java
    /*
    leia-se: "SELECT new (objetos desse tipo 👇 + o nome do construtor
    customizado que criamos. "obj.name" pois o apelido que damos para
    cada objeto que vamos buscar, é obj.
    */
    @Query("SELECT new com.devsuperior.uri2602.dto.CustomerMinDTO(obj.name) "
    + "FROM Customer obj "
    + "WHERE upper(obj.state) = upper(:state) ")
    List<CustomerMinDTO> search2(String state);
```

Fica um pouco verboso, pois precisamos instanciar com new ali em cima, utilizando a classe do DTO.

Na classe application:

```java
		List<CustomerMinDTO> result2 = customRepository.search2("rs");
		for (CustomerMinDTO obj : result2) {
			System.out.println(obj);
		}
```

SOUT:

![img_21.png](img_21.png)

<hr>

## URI 2611 Elaborando a consulta

```sql
select movies.id, movies.name
FROM movies
INNER JOIN genres ON movies.id_genres = genres.id
WHERE genres.description = 'Action';
```

### 2611 - SQL

Criação do Repository com o método SQl, sendo o mesmo este:

```java
public interface MovieRepository extends JpaRepository<Movie, Long> {

    @Query(nativeQuery = true, value = "SELECT movies.id, movies.name "
    + "FROM movies "
    + "INNER JOIN genres ON movies.id_genres = genres.id "
    + "WHERE genres.description = :genreName")
    List<MoviesMinProjection> search1(String genreName);
}
```

Criação da Projection

```java
public interface MoviesMinProjection {
    Long getId();
    String getName();
}
```

Código Application:
```java
	@Override
	public void run(String... args) throws Exception {
		List<MoviesMinProjection> list = movieRepository.search1("Action");
		
		List<MovieMinDTO> result = list.stream().map(
				x -> new MovieMinDTO(x)
		).collect(Collectors.toList());

		for (MovieMinDTO obj : result) {
			System.out.println(obj);
		}
	}
```

SOUT:

![img_22.png](img_22.png)

### 2611 - JPQL

No Repository, usaremos a query diferente conforme já aprendemos, veja:

```java
    @Query("SELECT new com.devsuperior.uri2611.dto.MovieMinDTO(obj.id, obj.name) "
    + "FROM Movie obj "
    + "WHERE obj.genre.description = :genreName ")
    List<MovieMinDTO> search2(String genreName);
```

Fica bem menos verboso, pois podemos acessar o genre direto no obj! Lembre-se: o obj, na verdade, é a classe Movie, 
então podemos navegar nos seus atributos. Como ela possui o genre pela relacinamento entre eles, fica mais fácil.

Na Application:

```java
		List<MovieMinDTO> result2 = movieRepository.search2("Action");

		for (MovieMinDTO obj : result2) {
			System.out.println(obj);
		}
```

SOUT:

![img_23.png](img_23.png)

<hr>

## URI 2621 Elaborando a consulta

### 2621 - SQL


### 2621 - JPQL

<hr>

## Dica - pratique um pouco se você estiver precisando

<hr>


## URI 2609 Elaborando a consulta

### 2609 - SQL

### 2609 - JPQL

<hr>


## URI 2737 Elaborando a consulta

## URI 2737 Solução alternativa


## URI 2737 - SQL
<hr>

## URI 2990 Elaborando a consulta


## URI 2990 Solução alternativa com LEFT JOIN


## U2990 - SQL

## U2990 - JPQL
<hr>


## DSCommerce consulta de produtos por nome


## Evitando consultas lentas muitos-para-muitos


## Evitando consultas lentas muitos-para-um com countQuery