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

> Esta seção está reservada para os **exercícios de cada padrão** que você vai me enviar. Quando mandar, eu resolvo aqui — um bloco por padrão (**Decorator, Adapter, Builder, Factory, Composite**) — com o código completo, a resolução passo a passo e a explicação de qual padrão é e por quê.
>
> Se algum exercício pedir para **completar** ou **identificar** o padrão de um código, eu resolvo já explicando **o sinal** que denuncia o padrão (usando o mapa de decisão acima), que é exatamente o formato da sua prova.

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
