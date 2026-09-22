# 🏛️ Análise de Arquitetura de Software e Implementação de Padrões de Projeto — Guia de Estudo

> Guia feito **sob medida para a sua prova**, com base nas Aulas 02 a 07 do Prof. Guilherme Alves. Tudo em **Java + Orientação a Objetos**.

> 💡 **Dica:** metade da prova é ler um código e dizer **qual padrão é**. Deixe salvo o [🧭 Mapa de decisão](#-mapa-de-decisão--que-padrão-é-esse) e as seções **🔎 Como identificar em código** de cada padrão — é exatamente esse formato.

## 🗺️ Sumário

- [🎯 Como a prova funciona](#-como-a-prova-funciona-leia-primeiro)
- [**Parte 0** · Base de POO em Java](#parte-0--base-de-orientação-a-objetos-java)
- [**Parte 1** · Princípios de Bom Projeto](#parte-1--princípios-de-bom-projeto)
  - [SOLID — visão geral](#solid--visão-geral)
  - [S — Single Responsibility (SRP)](#s--single-responsibility-principle-srp)
  - [O — Open/Closed (OCP)](#o--openclosed-principle-ocp)
  - [I — Interface Segregation (ISP)](#i--interface-segregation-principle-isp)
  - [DRY](#-dry--dont-repeat-yourself) · [KISS](#-kiss--keep-it-simple-stupid) · [YAGNI](#-yagni--you-arent-gonna-need-it)
  - [Code Smells](#-code-smells)
  - [GoF — Gang of Four](#-gof--gang-of-four)
- [**Parte 2** · Padrões de Projeto](#parte-2--padrões-de-projeto)
  - [🏭 Factory Method — *Criacional*](#-factory-method--criacional)
  - [🧱 Builder — *Criacional*](#-builder--criacional)
  - [🔌 Adapter — *Estrutural*](#-adapter--estrutural)
  - [🌳 Composite — *Estrutural*](#-composite--estrutural)
  - [🎁 Decorator — *Estrutural*](#-decorator--estrutural)
- [**Parte 3** · Resumo de cada tópico](#parte-3--resumo-de-cada-tópico-revisão-relâmpago)
- [🧭 Mapa de decisão — "que padrão é esse?"](#-mapa-de-decisão--que-padrão-é-esse)
- [**Parte 4** · Exercícios resolvidos](#parte-4--exercícios-resolvidos)
  - [🏭 Factory Method — Notificações](#-factory-method--sistema-de-notificações)
  - [🧱 Builder — Pizza](#-builder--sistema-de-pizza)
  - [🔌 Adapter — Pagamentos](#-adapter--sistema-de-pagamentos)
  - [🌳 Composite — Arquivos e Pastas](#-composite--sistema-de-arquivos-e-pastas)
  - [🎁 Decorator — Bebidas](#-decorator--sistema-de-bebidas)

---

## 🎯 Como a prova funciona (leia primeiro)

A prova tem **duas metades**:

1. **Conteúdo** → explicar princípios (SOLID, KISS, YAGNI, Code Smells, GoF) e padrões.
2. **Análise de código** → você recebe um trecho de código e precisa:
   - **identificar qual padrão** está sendo usado,
   - às vezes **completar o código**,
   - e **explicar** o que ele faz.

Por isso este guia tem, em cada padrão, uma seção **🔎 Como identificar em código** — é literalmente o que a segunda metade da prova cobra. No fim há um **[mapa de decisão](#-mapa-de-decisão--que-padrão-é-esse)** para bater o olho no código e cravar o padrão.

### 🗺️ O que vai cair
- **Base de POO** (Java)
- **SOLID** → foco em **S** (SRP), **O** (OCP), **I** (ISP)
- **KISS**, **YAGNI**, **Code Smells**, **GoF**
- **Padrões**: Factory Method, Builder, Adapter, Composite, Decorator

---
---

# Parte 0 — Base de Orientação a Objetos (Java)

> Todo padrão deste guia é feito de 4 ferramentas: **interface**, **classe abstrata**, **herança** e **polimorfismo**. Se você dominar esta parte, o código dos padrões vira quase leitura.

## Classe e objeto
- **Classe** = a "fôrma", o molde. Define atributos (dados) e métodos (ações).
- **Objeto** = uma instância criada a partir da classe, com `new`.

```java
public class Produto {          // classe (molde)
    String nome;                // atributo
    double preco;               // atributo
    double calcularPreco() {    // método
        return preco;
    }
}

Produto p = new Produto();      // objeto (instância)
```

## Encapsulamento
Esconder os detalhes internos e expor só o necessário. Atributos ficam `private`; o acesso é feito por métodos (`getters`/`setters`). Protege os dados e reduz acoplamento.

```java
public class Conta {
    private double saldo;                       // ninguém mexe direto
    public double getSaldo() { return saldo; }  // acesso controlado
    public void depositar(double v) {
        if (v > 0) saldo += v;                  // regra protegida aqui dentro
    }
}
```

## Construtor e `this`
O **construtor** roda quando o objeto é criado; serve para inicializar os atributos. `this` diferencia o atributo do parâmetro de mesmo nome.

```java
public class Produto {
    private String nome;
    public Produto(String nome) {   // construtor
        this.nome = nome;           // this.nome = atributo | nome = parâmetro
    }
}
```

## Herança (`extends`) e `super`
Uma classe **filha** herda atributos e métodos de uma classe **mãe**. `super(...)` chama o construtor da mãe.

```java
public class Animal {
    protected String nome;
    public Animal(String nome) { this.nome = nome; }
}
public class Cachorro extends Animal {
    public Cachorro(String nome) {
        super(nome);                // chama o construtor de Animal
    }
}
```

## Polimorfismo (o conceito mais importante para padrões)
**Polimorfismo** = objetos diferentes respondem à **mesma operação** de maneiras **diferentes**. Você usa um mesmo tipo (interface/classe abstrata) para representar objetos diferentes, e cada um executa do seu jeito.

```java
Bebida b = new Cafe();     // tipo Bebida, objeto Cafe
b = new Cha();             // mesmo tipo Bebida, objeto Cha
b.calcularPreco();         // cada um responde à sua maneira
```

`@Override` marca que um método está **sobrescrevendo** (reimplementando) o método da classe/interface pai. Sobrescrever ≠ sobrecarregar:
- **Sobrescrita (override)**: mesma assinatura, comportamento diferente na subclasse.
- **Sobrecarga (overload)**: mesmo nome, parâmetros diferentes na mesma classe.

## Abstração: `interface` × `abstract class`
Ambas definem "o que" um objeto faz sem dizer "como". Escolher a certa aparece direto na análise de código.

| | **Interface** | **Classe abstrata** |
|---|---|---|
| Palavra-chave | `implements` | `extends` |
| Tem atributos de estado? | Não (só constantes) | Sim |
| Tem construtor? | Não | Sim |
| Corpo de método? | Só `default`/`static` | Sim (métodos concretos + abstratos) |
| Quantas por classe? | **Várias** (`implements A, B`) | **Uma só** (herança única) |
| Quando usar | "é um contrato / consegue fazer X" | "é um tipo base com código comum a compartilhar" |

```java
public interface Notificacao {          // contrato: só assinaturas
    void enviar(String mensagem);
}

public abstract class BebidaDecorator implements Bebida {
    protected Bebida bebida;            // estado compartilhado
    public BebidaDecorator(Bebida bebida) {  // construtor
        this.bebida = bebida;
    }
}
```

> 💡 **Regra de bolso:** precisa **guardar estado/código comum** → classe abstrata. Precisa só de um **contrato** que várias classes cumprem → interface.

## Composição × Herança
- **Herança** ("é um"): `Cachorro extends Animal`.
- **Composição** ("tem um"): a classe **guarda uma referência** para outro objeto e delega trabalho a ele.

```java
public class NotificacaoService {
    private final Notificacao notificacao;   // "tem um" canal (composição)
    public void notificar(String m) {
        notificacao.enviar(m);               // delega
    }
}
```

> ⚠️ **Guarde isto:** quase todos os padrões estruturais (Adapter, Composite, Decorator) preferem **composição a herança**. Ver `private X algo;` sendo usado por dentro é um sinal forte de padrão estrutural.

## ⚡ Resumo relâmpago (POO)
- Classe = molde; objeto = instância (`new`).
- Encapsulamento = `private` + getters/setters.
- Herança = `extends` + `super`; polimorfismo = mesmo tipo, comportamentos diferentes (`@Override`).
- Interface = contrato (`implements`, várias); classe abstrata = base com estado/código (`extends`, uma).
- Composição ("tem um") costuma vencer herança nos padrões estruturais.

---
---

# Parte 1 — Princípios de Bom Projeto

## 🧼 O que é código limpo?
Código limpo é aquele que: **é fácil de ler**, **é fácil de modificar**, tem **baixo acoplamento**, **alta coesão** e **comunica a intenção**.

O custo do código ruim: **bugs**, **retrabalho**, **dívida técnica**, **baixa produtividade** e **alto custo de manutenção**.

- **Acoplamento**: o quanto uma classe depende de outra. **Baixo acoplamento = bom** (mexer numa não quebra a outra).
- **Coesão**: o quanto uma classe é focada em uma só coisa. **Alta coesão = bom**.

---

## SOLID — visão geral

**SOLID** é um acrônimo (popularizado por Michael Feathers / Robert C. Martin) de **cinco princípios** de projeto orientado a objetos:

| Letra | Princípio | Ideia em uma frase |
|---|---|---|
| **S** | Single Responsibility | Uma classe, um só motivo para mudar. |
| **O** | Open/Closed | Aberta para extensão, fechada para modificação. |
| **I** | Interface Segregation | Muitas interfaces específicas > uma genérica. |
| L | Liskov Substitution | *(fora do foco)* a filha deve poder substituir a mãe sem quebrar. |
| D | Dependency Inversion | *(fora do foco)* dependa de abstrações, não de implementações. |

> Sua prova foca em **S, O e I** — são os três detalhados abaixo. L e D ficam na tabela só para o acrônimo fazer sentido.

**O SOLID ajuda a criar sistemas** fáceis de entender, modificar e testar, mais reutilizáveis, menos acoplados e mais preparados para mudanças.

---

## S — Single Responsibility Principle (SRP)

**Uma classe deve ter um, e somente um, motivo para mudar.** Ou seja: cada classe é especializada em **um único assunto** e tem **uma única responsabilidade**.

**❌ Violação** — a classe `Funcionario` faz tudo (calcula salário, salva no banco, gera relatório):

```java
public class Funcionario {
    public double calcularSalario() { /* regra de negócio */ return 0; }
    public void salvarNoBanco()     { /* acesso a banco   */ }
    public String gerarRelatorio()  { /* formatação       */ return ""; }
}
```
Três motivos diferentes para mudar → três responsabilidades misturadas.

**✅ Corrigido** — cada responsabilidade em sua classe:

```java
public class CalculadoraSalario { public double calcular(Funcionario f) { return 0; } }
public class FuncionarioRepository { public void salvar(Funcionario f) { } }
public class RelatorioFuncionario  { public String gerar(Funcionario f) { return ""; } }
```

> 🎯 **Na prova:** viu uma classe que persiste dados **e** aplica regra de negócio **e** formata saída? É violação de SRP.

---

## O — Open/Closed Principle (OCP)

**Entidades devem estar abertas para extensão, mas fechadas para modificação.** Para adicionar um comportamento novo, você **estende** (cria classe nova) em vez de **alterar** o código que já funciona.

**❌ Violação** — cada nova forma exige mexer no `switch`:

```java
public class CalculadoraArea {
    public double calcular(Object forma) {
        if (forma instanceof Quadrado q)   return q.lado * q.lado;
        else if (forma instanceof Circulo c) return 3.14 * c.raio * c.raio;
        // Se amanhã surgir Pentágono, Trapézio, Hexágono...
        // a classe continuará crescendo e sendo modificada. ❌
        return 0;
    }
}
```

**✅ Corrigido** — cada forma implementa a própria área; a calculadora nunca mais muda:

```java
public interface Forma { double area(); }

public class Quadrado implements Forma {
    double lado;
    public double area() { return lado * lado; }
}
public class Circulo implements Forma {
    double raio;
    public double area() { return 3.14 * raio * raio; }
}
// Pentágono novo? Só criar uma classe nova — nada existente é alterado. ✅
```

> 🎯 **Na prova:** `if/else` ou `switch` gigante checando "tipo" de objeto quase sempre é violação de OCP. A correção costuma ser **polimorfismo** (interface + implementações).

---

## I — Interface Segregation Principle (ISP)

**Muitas interfaces específicas são melhores do que uma única interface geral.** Uma classe **não deve ser forçada a implementar métodos que não vai usar**.

**❌ Violação** — interface "gorda" obriga a impressora simples a implementar fax e scanner:

```java
public interface Multifuncional {
    void imprimir(String doc);
    void escanear(String doc);
    void enviarFax(String doc);
}

public class ImpressoraSimples implements Multifuncional {
    public void imprimir(String doc) { /* ok */ }
    public void escanear(String doc) { throw new UnsupportedOperationException(); } // ❌ não faz sentido
    public void enviarFax(String doc) { throw new UnsupportedOperationException(); } // ❌
}
```

**✅ Corrigido** — interfaces pequenas e específicas; cada classe implementa só o que usa:

```java
public interface Impressora { void imprimir(String doc); }
public interface Scanner    { void escanear(String doc); }
public interface Fax        { void enviarFax(String doc); }

public class ImpressoraSimples implements Impressora { public void imprimir(String d) {} }
public class Multifuncional  implements Impressora, Scanner, Fax {
    public void imprimir(String d) {}
    public void escanear(String d) {}
    public void enviarFax(String d) {}
}
```

> ⚠️ **ISP × LSP (pega muito):** os exemplos parecem iguais porque os dois costumam levar à mesma refatoração (quebrar interfaces), mas o **motivo** é diferente:
> - **LSP** pergunta: *"consigo trocar a classe pai por qualquer filha sem quebrar o sistema?"*
> - **ISP** pergunta: *"estou obrigando uma classe a implementar métodos que ela nunca vai usar?"*
>
> Se o sinal é **método que lança exceção / fica vazio porque não faz sentido** → **ISP**.

---

## 💧 DRY — Don't Repeat Yourself

Cada pedaço de conhecimento deve ter **uma única representação** no sistema. Se o mesmo código aparece em vários lugares, extraia para **um único módulo**. Duplicação = manutenção multiplicada (corrige num lugar, esquece nos outros).

---

## 🪶 KISS — Keep It Simple, Stupid

**Quanto mais simples o código, mais simples será mantê-lo no futuro.** Sistemas simples de manter funcionam melhor do que soluções complexas. Evite usar recursos "especiais" da linguagem **só porque a linguagem permite** — use-os apenas quando há **benefício perceptível** para o problema.

> 🎯 **Na prova:** solução com abstrações/generics/reflection desnecessários para um problema trivial → viola KISS. A pergunta-chave: *"dá para resolver de forma mais simples e ainda ficar claro?"*

---

## 🔮 YAGNI — You Aren't Gonna Need It

**Implemente apenas o que é necessário hoje.** Não adicione recursos "para o futuro" que ninguém pediu ainda. Vem do **XP (Extreme Programming)**: quando bater aquela ansiedade de codar algo extra "que talvez seja útil depois", respire e volte para o que realmente precisa ser feito agora.

> 🧩 **KISS × YAGNI:** KISS é sobre **como** você escreve (simples). YAGNI é sobre **o que** você escreve (só o necessário). Andam juntos, mas não são a mesma coisa.

---

## 👃 Code Smells

Tradução literal: "código que cheira mal". Um **Code Smell é um indício** de um problema de projeto ou implementação — **não é um bug**, é um **sinal de alerta** de que talvez seja hora de **refatorar**.

**Principais Code Smells:**

| Code Smell | O que é | Cheiro que denuncia |
|---|---|---|
| **Long Method** | Método muito longo | Faz coisa demais; difícil de ler/testar |
| **Large Class** | Classe muito grande | Acumulou responsabilidades (fere SRP) |
| **Duplicate Code** | Código duplicado | Mesma lógica copiada (fere DRY) |
| **Long Parameter List** | Lista enorme de parâmetros | Construtor/método com muitos argumentos (pede **Builder**) |
| **Switch Statements** | `switch`/`if-else` excessivo | Checa tipo em vez de usar polimorfismo (pede OCP/Factory) |
| **Magic Numbers** | Números "mágicos" soltos | `total * 1.07` sem explicar o 1.07 |

> 🎯 **Na prova:** cada smell aponta para uma correção. **Long Parameter List → Builder**. **Switch por tipo → polimorfismo/Factory**. **Duplicate Code → DRY/extrair método**. **Large Class → SRP**.

---

## 📚 GoF — Gang of Four

### O que é um padrão de projeto
Uma **solução típica para um problema comum** de projeto orientado a objetos. É um **modelo conceitual** que você adapta — **não é código pronto para copiar**. O mesmo padrão em dois programas pode gerar código diferente.

**Padrão × algoritmo:** um **algoritmo** é uma receita (passos claros e fixos para um objetivo). Um **padrão** é como uma **planta de arquitetura**: você vê o resultado e as características, mas a ordem exata de implementação depende de você.

### Origem (história)
- O conceito de "padrões" nasceu com **Christopher Alexander** (arquitetura, no livro *A Pattern Language*).
- Foi adotado por **quatro autores**: **Erich Gamma, John Vlissides, Ralph Johnson e Richard Helm**.
- Em **1994** publicaram *Design Patterns: Elements of Reusable Object-Oriented Software*, com **23 padrões**. Virou best-seller e o apelido "**Gang of Four (GoF)**" — a "gangue dos quatro".

### Por que aprender?
Você pode programar anos sem conhecer nenhum padrão (e até usar alguns sem saber). O valor real: padrões dão uma **linguagem comum** para o time se comunicar melhor ("aqui usei um Adapter" já diz tudo).

> ⚠️ **Cuidado:** *"se você só tem um martelo, tudo parece prego"* — não force padrão onde não precisa (isso fere KISS/YAGNI).

### As 3 categorias do GoF

| Categoria | Trata de... | Exemplos deste guia |
|---|---|---|
| **Criacionais** | **Como criar/instanciar** objetos, reduzindo dependência de classes concretas | **Factory Method**, **Builder** (+ Abstract Factory, Prototype, Singleton) |
| **Estruturais** | Como **organizar/compor** classes e objetos em estruturas maiores | **Adapter**, **Composite**, **Decorator** (+ Bridge, Facade, Flyweight, Proxy) |
| **Comportamentais** | Como os objetos **se comunicam** e dividem responsabilidades | *(não caem na sua prova)* |

> 🎯 **Na prova**, saber **classificar** o padrão (Criacional × Estrutural) vale ponto. Regra rápida: mexeu em **`new`/criação** → Criacional. **Embrulhou/agrupou/adaptou** objetos → Estrutural.

---
---

# Parte 2 — Padrões de Projeto

> Ordem por categoria (ajuda a responder "qual categoria?"): **Criacionais** (Factory Method, Builder) e depois **Estruturais** (Adapter, Composite, Decorator).

---

## 🏭 Factory Method — *Criacional*

### Definição
Define uma forma de **criar objetos sem que o código que os usa dependa das classes concretas** instanciadas. Quem decide **qual** classe concreta criar são as **subclasses**, através de um **método de criação** (o "factory method").

### Problema (exemplo das aulas: logística)
Um app de logística nasce só com transporte por **caminhão** (`Truck`). O código todo fica **acoplado a `Truck`**. Quando chega a demanda por transporte marítimo (`Ship`), adicionar isso **exigiria mexer na base inteira** — e a cada novo tipo de transporte, tudo de novo.

**Passo 1 — desacoplar com abstração + polimorfismo** (ainda **não** é o padrão):

```java
public interface Transport {
    void deliver();
}
public class Truck implements Transport {
    public void deliver() { System.out.println("Entrega por terra (caixa)"); }
}
public class Ship implements Transport {
    public void deliver() { System.out.println("Entrega por mar (contêiner)"); }
}
```
Agora o sistema pensa *"preciso de **algum Transporte**"* em vez de *"preciso de um Caminhão"*. Mas **a classe `Logistica` ainda decide qual criar** — isso ainda é só abstração + polimorfismo.

### Aplicando o Factory Method
A criação vira um **método abstrato** e a decisão passa para as **subclasses**:

```java
public abstract class Logistica {
    // Factory Method: a subclasse decide o objeto concreto
    public abstract Transport criarTransporte();

    // Lógica de negócio usa a abstração, sem conhecer Truck/Ship
    public void planejarEntrega() {
        Transport t = criarTransporte();
        t.deliver();
    }
}
public class LogisticaTerrestre extends Logistica {
    @Override
    public Transport criarTransporte() { return new Truck(); }
}
public class LogisticaMaritima extends Logistica {
    @Override
    public Transport criarTransporte() { return new Ship(); }
}
```
```java
Logistica logistica = new LogisticaMaritima();
logistica.planejarEntrega();   // usa Ship sem saber que é Ship
```

A lógica principal **não conhece** `Truck` nem `Ship`, e favorece o **OCP**: novo transporte = nova subclasse, sem alterar o que já existe. Sem o padrão, a mesma classe faria **duas coisas** (decidir o transporte **e** executar a entrega).

### 🔎 Como identificar em código
- Uma **classe/método abstrato** cujo nome sugere criação (`criar...()`, `create...()`) e **retorna uma abstração** (interface/classe base).
- **Subclasses** que só sobrescrevem esse método para dar o `new` de uma classe concreta.
- O resto do código trabalha com a **abstração**, nunca com `new ClasseConcreta()` espalhado.

### Aplicabilidade
- Não saber de antemão os tipos exatos com que o código vai trabalhar.
- Dar aos usuários de uma biblioteca/framework um jeito de estender componentes internos.
- Reutilizar objetos em vez de recriá-los sempre.

### Contras
- Pode ficar mais complexo (**muitas subclasses**). Melhor quando você já **tem uma hierarquia** de classes criadoras.

> 🧩 **Factory Method × Abstract Factory** (o primo que também está nos seus slides): Factory Method cria **um produto** por método (via subclasse). **Abstract Factory** cria **famílias inteiras** de produtos relacionados (ex.: `criarBotao()` **e** `criarCaixaTexto()` para Windows/Linux) por uma interface de fábrica. Se o código tem **uma interface com vários `criar...()`** que precisam ser da mesma "família", é Abstract Factory.

---

## 🧱 Builder — *Criacional*

### Definição
Permite **construir objetos complexos passo a passo**. Com o mesmo código de construção, você produz diferentes representações do objeto.

### Problema (exemplo das aulas: `Computador`)
Objeto com muitos campos (vários opcionais) leva a um **construtor monstruoso**:

```java
Computador gamer = new Computador(
    "Intel i9", 32, 2000, "RTX 5090", "Windows 11", true, true, true
);
```
Problemas: **muitos parâmetros**, **difícil lembrar a ordem**, **pouca legibilidade**, muitos **opcionais** e **fácil errar** (trocar dois `true` de lugar e ninguém percebe). Pior ainda: para cada combinação de opções, aparece **um construtor novo** (`Computador()`, `Computador(processador)`, `Computador(processador, memoria)`...) — o *Code Smell* **Long Parameter List** / **telescoping constructor**.

### Aplicando o Builder
Constrói o objeto **passo a passo** com métodos encadeados:

```java
Computador computador = new ComputadorBuilder()
        .processador("Intel i9")
        .memoria(32)
        .ssd(2000)
        .build();
```

Implementação típica:

```java
public class Computador {
    private String processador;
    private int memoria;
    private int ssd;
    // getters...
}

public class ComputadorBuilder {
    private Computador computador = new Computador();

    public ComputadorBuilder processador(String p) {
        computador.setProcessador(p);
        return this;                 // retorna o próprio builder → encadeia
    }
    public ComputadorBuilder memoria(int m) {
        computador.setMemoria(m);
        return this;
    }
    public ComputadorBuilder ssd(int s) {
        computador.setSsd(s);
        return this;
    }
    public Computador build() {      // entrega o objeto pronto
        return computador;
    }
}
```

### 🔎 Como identificar em código
- **Chamadas encadeadas** (`.x().y().z().build()`) — o *fluent interface*.
- Cada método de configuração **retorna `this`** (o próprio builder).
- Um método final `build()` (ou `construir()`) que **devolve o objeto pronto**.
- Serve para **fugir de construtor com muitos parâmetros**.

### Vantagens
- Legibilidade (você **lê o nome** de cada parâmetro).
- Ordem livre e parâmetros opcionais sem explosão de construtores.
- Difícil errar valores trocados.

### Quando usar
- Objeto com **muitos campos**, vários **opcionais**, ou construção em **etapas**.

> 🧩 **Builder × Factory:** **Factory** decide **QUAL** classe criar (esconde a classe concreta). **Builder** monta **UM** objeto complexo **passo a passo** (esconde a complexidade da construção). Viu `.build()`/encadeamento → Builder. Viu subclasse decidindo o `new` → Factory.

---

## 🔌 Adapter — *Estrutural*

### Definição
Permite que classes com **interfaces incompatíveis** trabalhem juntas. Funciona como uma **camada intermediária** que **converte** a forma como um componente é usado para a forma esperada por outro. (Analogia: adaptador de tomada.)

### Problema (exemplo das aulas: notificações)
O sistema espera enviar notificação com o método `enviar(...)`. Já existe `EmailService` com `enviar(...)`. Mas contratamos um serviço externo, `SmsExterno`, cujo método se chama `enviarSms(...)` — **nome diferente**, e **não podemos alterar essa classe externa**.

```java
// A classe externa que NÃO podemos mudar:
public class SmsExterno {
    public void enviarSms(String texto) {
        System.out.println("Enviando SMS: " + texto);
    }
}
```

**Solução ruim** (a evitar): encher o `NotificacaoService` de `if (email != null) ... if (sms != null) ...` e depender das duas classes concretas ao mesmo tempo.

### Aplicando o Adapter
**Passo 1 — definir a interface que o sistema espera:**

```java
public interface Notificacao {
    void enviar(String mensagem);
}
```
**Passo 2 — o e-mail já encaixa direto:**

```java
public class EmailService implements Notificacao {
    @Override
    public void enviar(String mensagem) {
        System.out.println("Enviando e-mail: " + mensagem);
    }
}
```
**Passo 3 — o Adapter "traduz" o SMS externo para a interface esperada:**

```java
public class SmsAdapter implements Notificacao {
    private final SmsExterno sms;           // guarda o objeto externo (composição)
    public SmsAdapter(SmsExterno sms) {
        this.sms = sms;
    }
    @Override
    public void enviar(String mensagem) {   // método esperado...
        sms.enviarSms(mensagem);            // ...traduzido para o método real
    }
}
```
**Passo 4 — o serviço depende só da abstração:**

```java
public class NotificacaoService {
    private final Notificacao notificacao;
    public NotificacaoService(Notificacao notificacao) {
        this.notificacao = notificacao;
    }
    public void notificar(String mensagem) {
        notificacao.enviar(mensagem);
    }
}
```
```java
Notificacao email = new EmailService();
new NotificacaoService(email).notificar("Pedido aprovado!");

Notificacao sms = new SmsAdapter(new SmsExterno());
new NotificacaoService(sms).notificar("Pedido aprovado!");
```

### 🔎 Como identificar em código
- Uma classe que **`implements` a interface esperada** pelo sistema...
- ...guarda **por dentro** um objeto de **outra classe** (composição, `private X algo;`)...
- ...e cujo método chama **um método de nome diferente** desse objeto interno (**tradução**).
- Sinal clássico: nome termina em **`Adapter`**; existe uma classe **externa/legada** que não pode ser alterada.

### Quando usar
- Integrar uma **biblioteca/serviço externo** cuja interface não bate com a do seu sistema, **sem alterar** o código de nenhum dos dois lados.

---

## 🌳 Composite — *Estrutural*

### Definição
Permite **tratar objetos individuais e grupos de objetos da mesma maneira**. Ideal para estruturas em **árvore**: um objeto pode conter outros objetos, que por sua vez contêm outros.

### Problema (exemplo das aulas: e-commerce)
A loja vende **produtos individuais** e **caixas (kits)** que contêm vários produtos — e **uma caixa pode conter outras caixas**. O sistema precisa **calcular o preço total** sem se importar se está lidando com um produto ou com uma caixa.

```java
public class Produto {
    private String nome;
    private double preco;
    public Produto(String nome, double preco) { this.nome = nome; this.preco = preco; }
    public double calcularPreco() { return preco; }
}
```
Com `Caixa` separada de `Produto`, o cliente precisaria de código diferente para cada uma — e o caso "caixa dentro de caixa" fica confuso.

### Aplicando o Composite
**Passo 1 — abstração comum** (folha e grupo compartilham a mesma interface):

```java
public interface Item {
    double calcularPreco();
}
```
**Passo 2 — `Produto` é a "folha":**

```java
public class Produto implements Item {
    private String nome;
    private double preco;
    public Produto(String nome, double preco) { this.nome = nome; this.preco = preco; }
    @Override
    public double calcularPreco() { return preco; }
}
```
**Passo 3 — `Caixa` é o "composto": também é `Item` e guarda uma lista de `Item`:**

```java
public class Caixa implements Item {
    private String nome;
    private List<Item> itens = new ArrayList<>();   // pode ter Produto OU Caixa
    public Caixa(String nome) { this.nome = nome; }

    public void adicionar(Item item) { itens.add(item); }

    @Override
    public double calcularPreco() {
        double total = 0;
        for (Item item : itens) {
            total += item.calcularPreco();   // recursão: soma folhas e caixas
        }
        return total;
    }
}
```
**Passo 4 — caixa dentro de caixa funciona naturalmente:**

```java
Caixa acessorios = new Caixa("Acessórios");
acessorios.adicionar(new Produto("Cabo HDMI", 50));
acessorios.adicionar(new Produto("Adaptador USB", 100));

Caixa kit = new Caixa("Kit Completo");
kit.adicionar(new Produto("Notebook", 4000));
kit.adicionar(new Produto("Mouse", 150));
kit.adicionar(acessorios);          // caixa dentro de caixa

kit.calcularPreco();                // 4300, sem saber o que tem dentro
```

### 🔎 Como identificar em código
- Uma **interface comum** (`Item`, `Componente`) implementada por **duas** classes:
  - a **folha** (objeto simples, ex.: `Produto`),
  - o **composto** que **guarda `List<Interface>`** (ex.: `Caixa` com `List<Item>`).
- O composto **percorre os filhos** (`for`) chamando **o mesmo método** neles → **recursão em árvore**.
- Pergunta-chave: *"o container é do mesmo tipo dos itens que ele contém?"* → sim = Composite.

### Quando usar
- Estrutura hierárquica/árvore onde **um todo e suas partes** devem ser tratados igual (menus, pastas/arquivos, kits de produtos).

---

## 🎁 Decorator — *Estrutural*

### Definição
Adiciona **comportamentos/responsabilidades a um objeto sem modificar sua classe original**. Em vez de criar uma subclasse para cada combinação possível de comportamentos, você **"embrulha" (wrap)** o objeto com outros objetos que **acrescentam funcionalidades**.

### Problema (exemplo das aulas: cafeteria)
Um café tem preço base, e o cliente adiciona complementos (leite, chocolate, chantilly...). A abordagem ingênua usa **um booleano por complemento** e um monte de `if`:

```java
public class Cafe {
    private double precoBase = 5.0;
    private boolean comLeite, comChocolate, comChantilly;
    // + construtor gigante com vários boolean...
    public double calcularPreco() {
        double total = precoBase;
        if (comLeite)     total += 2.0;
        if (comChocolate) total += 3.0;
        if (comChantilly) total += 1.5;
        return total;
    }
}
```
Cada complemento novo (canela, caramelo, baunilha...) = **mais um `boolean`** + **mais um `if`**. A classe cresce sem parar (Code Smells: **Long Parameter List** e **Switch/if excessivo**).

### Aplicando o Decorator
**Passo 1 — abstração comum:**

```java
public interface Bebida {
    double calcularPreco();
}
```
**Passo 2 — a bebida base:**

```java
public class Cafe implements Bebida {
    @Override
    public double calcularPreco() { return 5.0; }
}
```
**Passo 3 — o Decorator abstrato "embrulha" uma `Bebida`:**

```java
public abstract class BebidaDecorator implements Bebida {
    protected Bebida bebida;                 // a bebida que está sendo embrulhada
    public BebidaDecorator(Bebida bebida) {
        this.bebida = bebida;
    }
}
```
**Passo 4 — cada complemento é um decorator que soma seu preço:**

```java
public class Leite extends BebidaDecorator {
    public Leite(Bebida bebida) { super(bebida); }
    @Override
    public double calcularPreco() {
        return bebida.calcularPreco() + 2.0;    // preço de quem embrulha + o seu
    }
}
public class Chocolate extends BebidaDecorator {
    public Chocolate(Bebida bebida) { super(bebida); }
    @Override
    public double calcularPreco() {
        return bebida.calcularPreco() + 3.0;
    }
}
```
**Passo 5 — empilhamos os complementos livremente:**

```java
Bebida cafe = new Cafe();          // 5.0
cafe = new Leite(cafe);            // 5.0 + 2.0 = 7.0
cafe = new Chocolate(cafe);        // 7.0 + 3.0 = 10.0
cafe.calcularPreco();              // 10.0
```

### 🔎 Como identificar em código
- Uma classe (ou classe abstrata `...Decorator`) que **implementa a mesma interface** do objeto que ela embrulha...
- ...guarda **uma referência** a **um** objeto **do mesmo tipo** (`protected Bebida bebida;`)...
- ...e no método **chama o método do objeto embrulhado e soma/adiciona algo** (`bebida.calcularPreco() + 2.0`).
- Uso típico: **empilhamento recursivo** — `new Chocolate(new Leite(new Cafe()))`.

### Vantagens
- Adiciona comportamento **em tempo de execução**, sem alterar a classe base e sem explosão de subclasses para cada combinação.

---
---

# Parte 3 — Resumo de cada tópico (revisão relâmpago)

### POO
Classe (molde) vs objeto (`new`). Encapsulamento = `private` + getters/setters. Herança = `extends`/`super`. Polimorfismo = mesmo tipo, comportamentos diferentes (`@Override`). Interface = contrato (`implements`, várias); classe abstrata = base com estado/código (`extends`, uma). Composição ("tem um") > herança nos padrões estruturais.

### S — SRP
Uma classe, **um só motivo para mudar**. Sinal de violação: classe que faz regra de negócio + persistência + formatação juntas.

### O — OCP
Aberta para **extensão**, fechada para **modificação**. Sinal de violação: `if/switch` por tipo que cresce a cada novidade. Correção: polimorfismo.

### I — ISP
Muitas interfaces **específicas** > uma **genérica**. Sinal de violação: método vazio ou `UnsupportedOperationException` porque não faz sentido para a classe. Diferença do LSP: ISP = *"obrigado a implementar o que não uso?"*; LSP = *"a filha substitui a mãe sem quebrar?"*.

### KISS
Mantenha simples. Não use recurso avançado da linguagem só porque dá.

### YAGNI
Implemente só o necessário **hoje**. Nada de recurso "para o futuro" sem demanda.

### Code Smells
Sinal de alerta (não é bug). **Long Method, Large Class, Duplicate Code, Long Parameter List, Switch Statements, Magic Numbers.** Cada um aponta uma refatoração (ex.: Long Parameter List → Builder; Switch por tipo → polimorfismo/Factory).

### GoF
"Gang of Four" (Gamma, Vlissides, Johnson, Helm), 1994, **23 padrões**, **3 categorias**: **Criacionais** (criação de objetos), **Estruturais** (composição de objetos), **Comportamentais** (comunicação entre objetos). Padrão = planta conceitual, não código pronto.

### Factory Method *(Criacional)*
Cria objetos delegando **qual classe concreta** para as **subclasses** (`criarX()` abstrato). Cliente usa a abstração. Favorece OCP.

### Builder *(Criacional)*
Constrói objeto complexo **passo a passo** com métodos encadeados que retornam `this` e um `build()` final. Resolve construtor com muitos parâmetros.

### Adapter *(Estrutural)*
**Traduz** a interface de uma classe (externa/legada) para a interface esperada. Implementa a interface esperada e, por dentro, chama o método de nome diferente.

### Composite *(Estrutural)*
Trata **item e grupo do mesmo jeito**. Folha e composto implementam a mesma interface; o composto guarda `List<Interface>` e percorre recursivamente. Estrutura em árvore.

### Decorator *(Estrutural)*
**Embrulha** um objeto para **adicionar comportamento** sem alterar a classe original. Implementa a mesma interface do que embrulha, guarda uma referência dela e soma algo ao chamar o método interno. Empilhável.

---
---

# 🧭 Mapa de decisão — "que padrão é esse?"

Use na **segunda metade da prova** (análise de código). Bata o olho e procure o sinal:

| Sinal no código | Padrão | Categoria |
|---|---|---|
| Método/classe **abstrato** que dá o `new` de uma abstração, e **subclasses** decidem a classe concreta | **Factory Method** | Criacional |
| Métodos **encadeados** que retornam `this` + um **`build()`** no final | **Builder** | Criacional |
| Classe implementa a interface esperada e, por dentro, chama um **método de nome diferente** de outra classe (externa) | **Adapter** | Estrutural |
| Interface comum onde a **folha** e um **composto com `List<Interface>`** implementam a mesma coisa; o composto **percorre os filhos** | **Composite** | Estrutural |
| Classe **implementa a mesma interface** do objeto que guarda (**1 referência do mesmo tipo**) e **soma/adiciona** algo ao chamar o método dele | **Decorator** | Estrutural |

### Os três que "embrulham" (não confunda!)
Adapter, Composite e Decorator todos **guardam outro objeto por dentro**. O que diferencia:

| | Muda a interface? | Quantos objetos guarda? | Objetivo |
|---|---|---|---|
| **Adapter** | **Sim** (traduz) | **1** (de outro tipo) | Fazer interfaces incompatíveis se conversarem |
| **Decorator** | Não (mesma interface) | **1** (do mesmo tipo, empilhável) | **Adicionar** comportamento |
| **Composite** | Não (mesma interface) | **Vários** (`List` do mesmo tipo) | Tratar **todo e partes** igual (árvore) |

> 💡 Truque: **guarda `List`** do próprio tipo → **Composite**. Guarda **1** do próprio tipo e **soma** algo → **Decorator**. Guarda **1 de outro tipo** e **traduz** → **Adapter**.

---
---

# Parte 4 — Exercícios resolvidos

> Os **5 exercícios resolvidos**, um por padrão, na mesma ordem da [Parte 2](#parte-2--padrões-de-projeto). Cada um traz a **solução refatorada completa em Java**, o **desafio adicional** e as **respostas das questões de reflexão**.

## 🏭 Factory Method — Sistema de Notificações

O `NotificationService` original dava `new EmailNotification()` **direto** — acoplado à classe concreta. Cada novo tipo exigiria um `if/else`. Com o **Factory Method**, a criação vira um método abstrato e **cada subclasse decide** qual notificação instanciar.

### Solução

```java
// 1) Abstração do produto — toda notificação sabe enviar
public interface Notification {
    void send(String message);
}
```
```java
// 2) Produtos concretos
public class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending email: " + message);
    }
}
public class SmsNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}
public class PushNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending push: " + message);
    }
}
```
```java
// 3) Criador com o FACTORY METHOD (createNotification é abstrato)
public abstract class NotificationService {

    // ← Factory Method: a subclasse decide o produto concreto
    protected abstract Notification createNotification();

    // Lógica que USA o produto sem conhecer a classe concreta
    public void sendNotification(String message) {
        Notification notification = createNotification();
        notification.send(message);
    }
}
```
```java
// 4) Criadores concretos — cada um decide o que criar
public class EmailNotificationService extends NotificationService {
    @Override
    protected Notification createNotification() { return new EmailNotification(); }
}
public class SmsNotificationService extends NotificationService {
    @Override
    protected Notification createNotification() { return new SmsNotification(); }
}
public class PushNotificationService extends NotificationService {
    @Override
    protected Notification createNotification() { return new PushNotification(); }
}
```
```java
public class Main {
    public static void main(String[] args) {
        NotificationService email = new EmailNotificationService();
        NotificationService sms   = new SmsNotificationService();
        NotificationService push  = new PushNotificationService();

        email.sendNotification("Your order has been shipped!");
        sms.sendNotification("Your order has been shipped!");
        push.sendNotification("Your order has been shipped!");
    }
}
```

### Desafio adicional — WhatsApp
Só **adicionamos** duas classes; nada existente é alterado (Open/Closed):

```java
public class WhatsAppNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending WhatsApp: " + message);
    }
}
public class WhatsAppNotificationService extends NotificationService {
    @Override
    protected Notification createNotification() { return new WhatsAppNotification(); }
}
// no Main:  new WhatsAppNotificationService().sendNotification("...");
```

### Respostas
- **a)** O `NotificationService` instanciava `EmailNotification` **diretamente** (acoplado à classe concreta) e tenderia a virar um `if/else` gigante por tipo. O Factory Method remove o `new` direto e os condicionais: a decisão de qual classe criar sai da lógica de negócio.
- **b)** Adicionar um tipo novo = **criar classes novas** (um produto + um criador), **sem tocar** no código existente. Respeita o **OCP**.
- **c)** O Factory Method é o método **`createNotification()`** (abstrato em `NotificationService`), sobrescrito por cada criador concreto.
- **d)** O polimorfismo age em dois pontos: `sendNotification()` chama `send()` na abstração `Notification` (cada notificação envia do seu jeito); e a hierarquia de criadores também é polimórfica (a mesma chamada gera produtos diferentes conforme o criador).
- **e)** **Não.** Só ter a interface `Notification` + implementações é **abstração + polimorfismo**, não o padrão. O Factory Method exige **delegar a criação** a um método que as **subclasses** sobrescrevem para escolher a classe concreta. Sem essa delegação da criação, não há Factory Method (é o mesmo alerta do slide: *"isso ainda não é Factory Method"*).

---

## 🧱 Builder — Sistema de Pizza

O construtor com 9 parâmetros (`new Pizza("Grande","Tradicional",true,true,...)`) é ilegível e fácil de errar (trocar dois `true` de lugar). O **Builder** monta a pizza **passo a passo**, configurando só os ingredientes desejados, e `build()` entrega o objeto pronto.

### Solução

```java
public class Pizza {
    private final String size;
    private final String dough;
    private final boolean sauce;
    private final boolean cheese;
    private final boolean pepperoni;
    private final boolean bacon;
    private final boolean chicken;
    private final boolean stuffedCrust;
    // adicionais doces (desafio adicional)
    private final boolean chocolate;
    private final boolean strawberry;
    private final boolean condensedMilk;

    // Só o Builder constrói a Pizza (mesmo pacote)
    Pizza(PizzaBuilder b) {
        this.size          = b.size;
        this.dough         = b.dough;
        this.sauce         = b.sauce;
        this.cheese        = b.cheese;
        this.pepperoni     = b.pepperoni;
        this.bacon         = b.bacon;
        this.chicken       = b.chicken;
        this.stuffedCrust  = b.stuffedCrust;
        this.chocolate     = b.chocolate;
        this.strawberry    = b.strawberry;
        this.condensedMilk = b.condensedMilk;
    }

    public String describe() {
        StringBuilder sb = new StringBuilder("Pizza " + size + " (massa " + dough + ")");
        if (sauce)         sb.append(" + molho");
        if (cheese)        sb.append(" + queijo");
        if (pepperoni)     sb.append(" + pepperoni");
        if (bacon)         sb.append(" + bacon");
        if (chicken)       sb.append(" + frango");
        if (stuffedCrust)  sb.append(" + borda recheada");
        if (chocolate)     sb.append(" + chocolate");
        if (strawberry)    sb.append(" + morango");
        if (condensedMilk) sb.append(" + leite condensado");
        return sb.toString();
    }
}
```
```java
public class PizzaBuilder {
    // pacote-visível para a Pizza ler; com valores padrão
    String size = "Média";
    String dough = "Tradicional";
    boolean sauce, cheese, pepperoni, bacon, chicken, stuffedCrust;
    boolean chocolate, strawberry, condensedMilk;

    public PizzaBuilder size(String size)   { this.size = size; return this; }
    public PizzaBuilder dough(String dough) { this.dough = dough; return this; }
    public PizzaBuilder sauce()             { this.sauce = true; return this; }
    public PizzaBuilder cheese()            { this.cheese = true; return this; }
    public PizzaBuilder pepperoni()         { this.pepperoni = true; return this; }
    public PizzaBuilder bacon()             { this.bacon = true; return this; }
    public PizzaBuilder chicken()           { this.chicken = true; return this; }
    public PizzaBuilder stuffedCrust()      { this.stuffedCrust = true; return this; }
    // adicionais doces (desafio adicional — só métodos novos)
    public PizzaBuilder chocolate()         { this.chocolate = true; return this; }
    public PizzaBuilder strawberry()        { this.strawberry = true; return this; }
    public PizzaBuilder condensedMilk()     { this.condensedMilk = true; return this; }

    public Pizza build() {          // ← entrega o objeto pronto
        return new Pizza(this);
    }
}
```
```java
public class Main {
    public static void main(String[] args) {
        Pizza calabresa = new PizzaBuilder()
                .size("Grande").dough("Tradicional")
                .sauce().cheese().pepperoni()
                .build();

        Pizza portuguesa = new PizzaBuilder()
                .size("Grande").dough("Tradicional")
                .sauce().cheese().bacon().chicken()
                .build();

        Pizza personalizada = new PizzaBuilder()
                .size("Média").dough("Integral")
                .sauce().cheese().bacon().stuffedCrust()
                .build();

        System.out.println(calabresa.describe());
        System.out.println(portuguesa.describe());
        System.out.println(personalizada.describe());
    }
}
```

### Desafio adicional — pizzas doces
Os métodos `chocolate()`, `strawberry()`, `condensedMilk()` (e os atributos) já estão incluídos acima — foram **acrescentados** sem mexer nos métodos existentes:

```java
Pizza doce = new PizzaBuilder()
        .size("Grande").dough("Tradicional")
        .chocolate().strawberry().condensedMilk()
        .build();
System.out.println(doce.describe());
// Pizza Grande (massa Tradicional) + chocolate + morango + leite condensado
```

### Respostas
- **a)** O construtor com **muitos parâmetros** (*Long Parameter List*): ordem difícil de lembrar, ilegível e propenso a erro (trocar `boolean`s). O Builder resolve construindo passo a passo com nomes claros.
- **b)** Legibilidade (você **lê o nome** de cada ingrediente), configura só o que quer (opcionais), ordem livre e **sem explosão de construtores** para cada combinação.
- **c)** O Builder é a classe **`PizzaBuilder`** (métodos de configuração que retornam `this` + o `build()`).
- **d)** O `build()` **finaliza** a construção e **devolve a `Pizza` pronta** a partir da configuração acumulada no builder.
- **e)** Quando o objeto tem **muitos atributos** (vários opcionais), quando a **legibilidade** importa, ou quando a construção tem **etapas/validação**. Para poucos parâmetros obrigatórios, um construtor simples basta (**KISS/YAGNI** — não force Builder onde não precisa).

---

## 🔌 Adapter — Sistema de Pagamentos

O sistema espera `pay(double amount)`, mas o serviço externo `ExternalPaymentGateway` (que **não pode ser alterado**) só oferece `makePayment(String currency, double value)`. O **Adapter** implementa a interface esperada e, por dentro, **traduz** a chamada para o método do serviço externo.

### Solução

```java
// 1) Abstração esperada pelo sistema
public interface PaymentProcessor {
    void pay(double amount);
}
```
```java
// 2) Implementação própria já existente — agora cumpre a abstração
public class CreditCardPayment implements PaymentProcessor {
    @Override
    public void pay(double amount) {
        System.out.println("Payment approved: $" + amount);
    }
}
```
```java
// 3) Classe externa — NÃO é alterada (interface incompatível)
public class ExternalPaymentGateway {
    public void makePayment(String currency, double value) {
        System.out.println("External payment approved: " + currency + " " + value);
    }
}
```
```java
// 4) O ADAPTER: implementa PaymentProcessor e traduz para o método externo
public class ExternalPaymentAdapter implements PaymentProcessor {
    private final ExternalPaymentGateway gateway;
    private final String currency;                 // moeda vive AQUI, não no OrderService

    public ExternalPaymentAdapter(ExternalPaymentGateway gateway, String currency) {
        this.gateway = gateway;
        this.currency = currency;
    }
    public ExternalPaymentAdapter(ExternalPaymentGateway gateway) {
        this(gateway, "BRL");                      // moeda padrão
    }
    @Override
    public void pay(double amount) {               // método esperado...
        gateway.makePayment(currency, amount);     // ...traduzido para o externo
    }
}
```
```java
// 5) OrderService depende da ABSTRAÇÃO, não da classe concreta
public class OrderService {
    private final PaymentProcessor payment;
    public OrderService(PaymentProcessor payment) {
        this.payment = payment;
    }
    public void checkout(double amount) {
        payment.pay(amount);
    }
}
```
```java
public class Main {
    public static void main(String[] args) {
        // pagamento com cartão (implementação própria)
        OrderService cartao = new OrderService(new CreditCardPayment());
        cartao.checkout(150.00);

        // pagamento pelo gateway externo, via Adapter
        ExternalPaymentGateway gateway = new ExternalPaymentGateway();
        OrderService externo = new OrderService(new ExternalPaymentAdapter(gateway));
        externo.checkout(150.00);
    }
}
```

### Desafio adicional — múltiplas moedas (BRL, USD, EUR)
A moeda fica **encapsulada no Adapter**; o `OrderService` nunca sabe dela:

```java
ExternalPaymentGateway gateway = new ExternalPaymentGateway();

OrderService brl = new OrderService(new ExternalPaymentAdapter(gateway, "BRL"));
OrderService usd = new OrderService(new ExternalPaymentAdapter(gateway, "USD"));
OrderService eur = new OrderService(new ExternalPaymentAdapter(gateway, "EUR"));

brl.checkout(150.00);   // External payment approved: BRL 150.0
usd.checkout(150.00);   // External payment approved: USD 150.0
eur.checkout(150.00);   // External payment approved: EUR 150.0
```

### Respostas
- **a)** As interfaces eram **incompatíveis**: o sistema chama `pay(double)`, o externo exige `makePayment(String, double)`. Sem alterar o serviço externo nem espalhar seus detalhes pelo código, o Adapter faz a ponte.
- **b)** A classe **`ExternalPaymentAdapter`** é o Adapter.
- **c)** A esperada é `pay(double amount)` (simples); a fornecida é `makePayment(String currency, double value)` (**nome diferente** + parâmetro extra de **moeda**).
- **d)** Porque a classe pertence a **outra equipe / é externa** e não deve/pode ser modificada; alterá-la aumentaria o acoplamento e você não controla esse código. O Adapter **isola** a integração num único ponto.
- **e)** Quando a classe incompatível é **externa/legada/imutável**, quando **muitos clientes** já usam a interface esperada (mudar todos = caro e arriscado) e quando você quer **concentrar** os detalhes da integração em um só lugar.

---

## 🌳 Composite — Sistema de Arquivos e Pastas

O código inicial tratava arquivo e pasta de formas **diferentes** (`getSize()` vs `getTotalSize()`) e a `Folder` só aceitava arquivos. O **Composite** cria uma abstração comum (`FileSystemComponent`) para que arquivo e pasta sejam tratados igual, e a pasta possa conter **arquivos e outras pastas** recursivamente.

### Solução

```java
// Componente (abstração comum) — inclui display() do desafio adicional
public interface FileSystemComponent {
    String getName();
    long getSize();
    void display(String indent);
}
```
```java
// Folha — arquivo individual, sem filhos
public class DocumentFile implements FileSystemComponent {
    private final String name;
    private final long size;   // em KB

    public DocumentFile(String name, long size) {
        this.name = name;
        this.size = size;
    }
    @Override public String getName() { return name; }
    @Override public long getSize()   { return size; }

    @Override
    public void display(String indent) {
        System.out.println(indent + "- " + name + " (" + size + " KB)");
    }
}
```
```java
import java.util.ArrayList;
import java.util.List;

// Composto — pasta que guarda uma coleção de FileSystemComponent
public class Folder implements FileSystemComponent {
    private final String name;
    private final List<FileSystemComponent> children = new ArrayList<>();

    public Folder(String name) { this.name = name; }

    // add/remove SÓ na pasta (arquivo não tem filhos)
    public void add(FileSystemComponent component)    { children.add(component); }
    public void remove(FileSystemComponent component) { children.remove(component); }

    @Override public String getName() { return name; }

    @Override
    public long getSize() {
        long total = 0;
        for (FileSystemComponent child : children) {
            total += child.getSize();   // recursão via abstração — sem instanceof
        }
        return total;                   // pasta vazia → 0
    }

    @Override
    public void display(String indent) {
        System.out.println(indent + "[" + name + "] (" + getSize() + " KB)");
        for (FileSystemComponent child : children) {
            child.display(indent + "   ");
        }
    }
}
```
```java
public class Main {
    public static void main(String[] args) {
        // arquivo isolado
        FileSystemComponent report = new DocumentFile("report.pdf", 500);

        // pasta vazia
        Folder vazia = new Folder("Vazia");

        // hierarquia com 2 níveis de subpastas
        DocumentFile photo  = new DocumentFile("photo.png", 1_500);
        DocumentFile resume = new DocumentFile("resume.docx", 300);
        DocumentFile song   = new DocumentFile("song.mp3", 4_000);

        Folder music = new Folder("Music");
        music.add(song);

        Folder documents = new Folder("Documents");
        documents.add(resume);
        documents.add(music);      // subpasta dentro de subpasta

        Folder root = new Folder("Root");
        root.add(report);
        root.add(photo);
        root.add(documents);
        root.add(vazia);

        // consulta pela MESMA abstração, arquivo ou pasta
        System.out.println(report.getName() + ": " + report.getSize() + " KB");   // 500
        System.out.println(vazia.getName()  + ": " + vazia.getSize()  + " KB");   // 0
        System.out.println(root.getName()   + ": " + root.getSize()   + " KB");   // 6300

        // desafio adicional: exibir a hierarquia
        System.out.println();
        root.display("");
    }
}
```

### Desafio adicional — `display(String indent)`
Já incluído acima (na interface, no arquivo e na pasta). Saída do `root.display("")`:

```text
[Root] (6300 KB)
   - report.pdf (500 KB)
   - photo.png (1500 KB)
   [Documents] (4300 KB)
      - resume.docx (300 KB)
      [Music] (4000 KB)
         - song.mp3 (4000 KB)
   [Vazia] (0 KB)
```

### Respostas
- **a)** O sistema tratava arquivo e pasta de formas diferentes e a `Folder` só aceitava arquivos (sem subpastas). O Composite unifica tudo sob `FileSystemComponent` e permite **pasta dentro de pasta** com tratamento uniforme.
- **b)** **Componente** = `FileSystemComponent`; **folha** = `DocumentFile`; **composto** = `Folder`.
- **c)** Arquivo e pasta implementam `getSize()`. O código chama `getSize()` na **abstração**, sem saber o tipo concreto; cada um responde do seu jeito (arquivo devolve o próprio tamanho; pasta soma os filhos). Zero `instanceof`.
- **d)** A recursão está em `Folder.getSize()`: ela chama `getSize()` de cada filho; se o filho é outra `Folder`, ele mesmo soma seus filhos, **descendo a árvore** até as folhas. É isso que permite somar níveis arbitrários.
- **e)** Deixar `add`/`remove` **só na `Folder`** é a variação **"segura"** do Composite:
  - **Vantagem:** modela a realidade (arquivo não tem filhos) e não força a folha a implementar métodos sem sentido (não fere o **ISP**); é impossível adicionar filho a um arquivo.
  - **Limitação:** o cliente precisa conhecer o tipo `Folder` para montar a árvore — o tratamento 100% uniforme vale para **operar** sobre a estrutura (`getSize`, `display`), não para **montá-la**. É o trade-off clássico: **transparência** (métodos na abstração, uniforme porém inseguro) × **segurança** (métodos só no composto).

---

## 🎁 Decorator — Sistema de Bebidas

O código inicial criava **uma classe por combinação** (`CoffeeWithMilk`, `CoffeeWithMilkAndChocolate`...), duplicando preço e descrição. O **Decorator** "embrulha" a bebida com objetos que **acrescentam** descrição e custo, permitindo combinar adicionais em tempo de execução.

### Solução

```java
// Componente (abstração)
public interface Beverage {
    String getDescription();
    double getCost();
}
```
```java
// Componentes concretos (bebidas básicas)
public class Coffee implements Beverage {
    @Override public String getDescription() { return "Coffee"; }
    @Override public double getCost()        { return 5.00; }
}
public class Tea implements Beverage {
    @Override public String getDescription() { return "Tea"; }
    @Override public double getCost()        { return 4.00; }
}
```
```java
// Decorador base: É uma Beverage e TEM uma Beverage (composição)
public abstract class BeverageDecorator implements Beverage {
    protected final Beverage beverage;          // a bebida envolvida
    public BeverageDecorator(Beverage beverage) {
        this.beverage = beverage;
    }
}
```
```java
// Decoradores concretos — delegam e complementam
public class MilkDecorator extends BeverageDecorator {
    public MilkDecorator(Beverage beverage) { super(beverage); }
    @Override public String getDescription() { return beverage.getDescription() + ", milk"; }
    @Override public double getCost()        { return beverage.getCost() + 1.50; }
}
public class ChocolateDecorator extends BeverageDecorator {
    public ChocolateDecorator(Beverage beverage) { super(beverage); }
    @Override public String getDescription() { return beverage.getDescription() + ", chocolate"; }
    @Override public double getCost()        { return beverage.getCost() + 2.00; }
}
public class WhippedCreamDecorator extends BeverageDecorator {
    public WhippedCreamDecorator(Beverage beverage) { super(beverage); }
    @Override public String getDescription() { return beverage.getDescription() + ", whipped cream"; }
    @Override public double getCost()        { return beverage.getCost() + 2.50; }
}
```
```java
public class Main {
    public static void main(String[] args) {
        // sem adicionais
        Beverage coffee = new Coffee();
        print(coffee);                                    // Coffee -> R$ 5.0

        // café com leite e chocolate  ->  8.50
        Beverage coffeeMilkChoc = new ChocolateDecorator(new MilkDecorator(new Coffee()));
        print(coffeeMilkChoc);                            // Coffee, milk, chocolate -> R$ 8.5

        // chá com leite  ->  5.50
        Beverage teaMilk = new MilkDecorator(new Tea());
        print(teaMilk);                                   // Tea, milk -> R$ 5.5

        // duas porções do MESMO adicional (leite duplo)
        Beverage doubleMilk = new MilkDecorator(new MilkDecorator(new Coffee()));
        print(doubleMilk);                                // Coffee, milk, milk -> R$ 8.0
    }
    static void print(Beverage b) {
        System.out.println(b.getDescription() + " -> R$ " + b.getCost());
    }
}
```

### Desafio adicional — `CaramelDecorator` (R$ 1,00)
Só **criamos uma classe nova**; nada existente muda. Envolvemos uma bebida que já tem adicionais:

```java
public class CaramelDecorator extends BeverageDecorator {
    public CaramelDecorator(Beverage beverage) { super(beverage); }
    @Override public String getDescription() { return beverage.getDescription() + ", caramel"; }
    @Override public double getCost()        { return beverage.getCost() + 1.00; }
}
```
```java
Beverage base = new ChocolateDecorator(new MilkDecorator(new Coffee())); // 8.50
Beverage withCaramel = new CaramelDecorator(base);                       // + 1.00
System.out.println(withCaramel.getDescription() + " -> R$ " + withCaramel.getCost());
// Coffee, milk, chocolate, caramel -> R$ 9.5
```

### Respostas
- **a)** A **explosão de subclasses** (uma classe por combinação de café + adicionais), com preço e descrição **duplicados**. O Decorator combina adicionais em tempo de execução, sem uma classe por combinação.
- **b)** **Componente** = `Beverage`; **componentes concretos** = `Coffee`, `Tea`; **decorador base** = `BeverageDecorator`; **decoradores concretos** = `MilkDecorator`, `ChocolateDecorator`, `WhippedCreamDecorator` (e `CaramelDecorator`).
- **c)** Para que o objeto decorado seja tratado **exatamente igual** ao original pelo código cliente (mesma `Beverage`) e para que um decorador possa **envolver outro** (empilhar). O decorador **"é uma"** `Beverage` e **"tem uma"** `Beverage`.
- **d)** O decorador **guarda uma referência** a outra `Beverage` (composição) e **delega** `getCost()`/`getDescription()` a ela, **somando** o seu próprio valor. Assim qualquer combinação vira **empilhamento de objetos**, dispensando subclasse por combinação.
- **e)** Adicionar um adicional novo (Caramel) = **criar uma classe nova**, **sem modificar** `Coffee`, `Tea` nem os decoradores existentes. É o **OCP** na prática: aberto para extensão, fechado para modificação.

---

## ✅ Checklist final antes da prova

- [ ] Sei explicar POO: classe/objeto, encapsulamento, herança, **polimorfismo**, interface × classe abstrata.
- [ ] Sei o que cada letra de **SOLID** significa e detalho **S, O, I** com exemplo.
- [ ] Diferencio **ISP × LSP** (método inútil vs substituição da filha).
- [ ] Explico **KISS** e **YAGNI** e a diferença entre eles.
- [ ] Reconheço os **6 Code Smells** e a refatoração de cada um.
- [ ] Sei a história do **GoF** e as **3 categorias** (Criacional/Estrutural/Comportamental).
- [ ] Reconheço e implemento: **Factory Method, Builder, Adapter, Composite, Decorator**.
- [ ] Consigo **identificar o padrão** olhando o código (mapa de decisão).
- [ ] Não confundo os três que embrulham: **Adapter (traduz) × Decorator (soma) × Composite (agrupa)**.

> 💪 Bons estudos! Me manda os exercícios de cada padrão que eu preencho a Parte 4.
