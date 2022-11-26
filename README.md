# SOLID

Sempre que falamos de qualidade de um software, é impossível não falar sobre utilização de boas práticas e padrões
durante a sua construção e manutenção.

A partir dos vários princípios criados por Robert C. Martin para a programação orientada a objetos, Michael Feathers
propôs o acrônimo S.O.L.I.D. para 5 princípios básicos que devem reger o processo de desenvolvimento de um software de
qualidade.

A idéia é que estes princípios deveriam ser seguidos na programação durante todo o tempo de vida de um software. Com
isso, evitamos possíveis problemas e também melhoramos a produtividade nas entregas, além de tornar o nosso código muito
mais legível para outras pessoas.

Para exemplificar cada um dos princípios, vou utilizar a linguagem Python.

### 1 - SINGLE RESPONSABILITY PRINCIPLE (SRP):

<b>
Princípio da responsabilidade única: <br>
"Uma classe não pode ter mais de um motivo para ser alterada."
Sendo ainda mais específico, uma classe deve ter somente um motivo para ser alterada.
</b>

Muitas vezes precisamos de algum método ou de alguma propriedade e inserimos em uma determinada classe porque
simplesmente não existe nenhum outro lugar melhor para colocá-lo. Com isso, estamos alterando o comportamento de uma
classe que já havia sido definida para o que deveria fazer. Além de alterar o comportamento da classe, ainda estamos
tornando ela ainda mais frágil <b>(aumentando acoplamento e diminuindo a coesão)</b>.

```
class Quadrado:
    
    def __init__(self, comprimento_lado=0):
        self.comprimento_lado = comprimento_lado
    def calcula_area(self):
        return self.comprimento_lado ** 2
    def desenhar(self):
        # Desenha um quadrado
        pass
```

Dessa forma, podemos claramente ver que estamos atribuindo duas responsabilidades para a classe <b>Quadrado</b>, a de
calcular
a área e também desenhar o quadrado. Isso não está errado, porém se pensarmos no princípio de responsabilidade única,
podemos ter duas classes, uma para calcular e outra para desenhar:

```
class CalculaQuadrado:
    def __init__(self, comprimento_lado=0):
        self.comprimento_lado = comprimento_lado
    def calcula_area(self):
        return self.comprimento_lado * 2
class DesenhaQuadrado:
    
    def desenha(self):
        # Desenha um quadrado
        pass
```

<b> Assim, cada classe tem um único propósito: temos uma classe que podemos utilizar para cálculos geométricos e outra
classe responsável por desenhos geométricos. </b>

-------

### 2 - OPEN/CLOSED PRINCIPLE (OCP):

<b> Princípio do aberto/fechado: <br>
"O comportamento de uma classe deve estar aberta para extensão porém 
fechada para alterações." </b>


A principal idéia é que uma classe mãe deve ser genérica e abstrata para ser estendida e reaproveitada por classes
filhas. Esse princípio rege que a classe mãe não deve ser alterada.

No Python podemos fazer uma operação conhecida como <b> Monkey-Patching.</b> Resumidamente, uma classe em Python é
mutável e um método é apenas um atributo de uma classe. Dessa forma, é possível sobrescrever um método dinamicamente em
tempo de execução. Não entrarei em mais detalhes sobre esta prática por não estar no escopo desse artigo, porém entenda
que essa isso deve ser realizado com muito cuidado.

Exemplo:

````
area = CalculaQuadrado(10)
def forma_geometrica():
    return 'Sou um quadrado.'
area.forma_geometrica = forma_geometrica
print(area.forma_geometrica()) # Imprime: Sou um quadrado.
````

Dessa forma, acabamos de adicionar uma nova função para a instância area. Você deve ter percebido que, ainda assim,
forma_geometrica não está referenciando nossa instância <b>(self)</b>. Por esse motivo, trata-se de uma função e não de
um
método inerente a instância. No entanto, também podemos adicionar métodos para a classe dessa mesma forma, conforme
observamos abaixo:

```
def calcula_volume(self):
    return self.comprimento_lado ** 3
CalculaQuadrado.volume = calcula_volume
quadrado = CalculaQuadrado(10)
print(quadrado.volume()) # Imprime 1000
```

Acabamos de alterar a implementação da classe para todas instâncias de <b>CalculaQuadrado</b> depois desse <b>path</b>.
Essa
possibilidade é muito poderosa, mas também pode representar um risco gigantesco se usado sem cautela.

Você percebeu que podemos facilmente alterar o comportamento de uma classe em Python?

Vale ressaltar que esse princípio visa nos chamar a atenção justamente de que não devemos desrespeitar o encapsulamento
de uma classe

---

### 3 - LISKOV SUBSTITUTION PRINCIPLE (LSP):

<b>
Princípio da substituição de Liskov: "Uma classe filha deve poder ser substituída pela suas classe pai."
</b>

Esse princípio define que uma subclasse deve poder ser substituída pela respectiva superclasse. E estas podem ser
substituídas por qualquer uma das suas subclasses. Uma subclasse deve sobrescrever os métodos da superclasse de forma
que a interface continue sempre a mesma.

Por exemplo:

```
class Retangulo:
    def __init__(self, largura=0, altura=0):
        self.largura = largura
        self.altura = altura
    def calcula_area(self):
        return (self.largura * self.altura)
class Quadrado(Retangulo):
    def __init__(self, largura=0, altura=0):
        super(Quadrado, self).__init__(largura, altura)
        self.comprimento_largura = largura
        self.comprimento_altura = altura
    def calcula_area(self):
        return (self.comprimento_largura * self.comprimento_altura)
```

Esse princípio nos mostra que devemos verificar se o <b>polimorfismo</b> faz mesmo sentido no uso da herança. Ou seja,
devemos
ver se realmente qualquer subclasse pode ser usada no lugar da superclasse, para evitarmos utilizar herança de forma
inadequada.

Neste caso não faz sentido algum estender a classe <b>Retangulo</b> para a <b>classe Quadrado</b>. Pois já que todos os
lados
de um
quadrado são iguais, não há a necessidade de termos os atributos largura e altura. Ao criarmos <b>
comprimento_largura</b> e
<b>comprimento_altura</b>, estamos utilizando a famosa gambiarra(contorno técnico) para resolvemos o problema. Não faça
isso!

---

### 4 - INTERFACE SEGREGATION PRINCIPLE (ISP):

### Princípio da segregação de interfaces:

<b>
"Várias interfaces específicas são melhores do que uma interface genérica."
</b>

Este princípio define que uma classe não deve conhecer nem depender de métodos que não necessitam.

Já sabemos que para que uma classe seja coesa e reutilizável, ela não deve possuir mais de uma responsabilidade. Mas
ainda assim, essa única responsabilidade pode ser quebrada em responsabilidades ainda menores, tornando a interface
muito mais amigável.

No Python podemos fazer herança múltipla, então resolvi abstrair uma classe para calcular o volume de um polígono
baseado em seu número de lados, uma para calcular a área também baseado em seu número de lados. Assim, eu conseguiria
reutilizá-las para outros polígonos com quantidades de lados diferentes:

```
class CalculaPerimetro:
    def __init__(self, numero_lados=0):
        self.numero_lados = numero_lados
    def calcula_perimetro(self):
        return 'Calcula o perímetro para um polígono de N lados'
class CalculaArea:
    def __init__(self, numero_lados=0):
        self.numero_lados = numero_lados
    def calcula_area(self):
        return 'Calcula área para determinado polígono de N lados'
class CalculaVolume:
    def __init__(self, numero_lados=0):
        self.numero_lados = numero_lados
    def calcula_volume(self):
        return 'Calcula volume para determinado polígono de N lados'
class Quadrado(CalculaArea, CalculaVolume):
    def __init__(self, numero_lados=None):
        super(Quadrado, self).__init__(numero_lados)
        self.numero_lados=numero_lados
    @property
    def area(self):
        return self.calcula_area()
    @property
    def volume(self):
        return self.calcula_volume()
```

Perceba que na <b>classe Quadrado</b>, herdei e criei propriedades somente para <b>área e volume</b>. Não utilizei a
classe
<b>CalculaPerimetro</b> pois eu decidi que agora eu não iria usá-la, não tinha porque implementar uma nova propriedade
<b>(interface)</b> para a minha <b>classe Quadrado</b>.

Observação: Com um exemplo tão simples, talvez essa estrutura não faça muito sentido. Porém tentei aplicar o princípio
do ISP de forma que o conceito fique entendível. Agora nossa <b>classe Quadrado</b> tem as propriedades área e volume
que vou
precisar acessar, que são abstraídos das superclasses herdadas.

-----

### 5 - DEPENDENCY INVERSION PRINCIPLE (DIP):

### Princípio da inversão de dependências:

#### "Devemos depender de classes abstratas e não de classes concretas."

Esse princípio define que módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de
abstrações. Ao mesmo tempo, estas abstrações não devem depender detalhes. Os detalhes é que devem depender de
abstrações.

Legal, mas o que isso quer dizer?

Uma classe não deve conhecer outra classe para realizar alguma operação. Muito pelo contrário, deve existir uma
interface genérica para intermediar esse acesso da forma mais abstrata possível.

Mas vamos concordar que em Python, geralmente nós não utilizamos interfaces para resolvermos problemas. No entanto,
podem aparecer situações em que precisaremos definir uma interface, por exemplo em um contrato de uma API, na qual
gostaríamos de estender alguma biblioteca ou framework. Para este caso, podemos contar com o módulo de Abstract Base
Classes(ABC). E como o Python permite herança múltipla, as ABC's se comportam essencialmente como uma interface

