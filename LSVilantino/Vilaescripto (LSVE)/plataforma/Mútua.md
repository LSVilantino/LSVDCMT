# v0-1.0.0-0 - Estabilização 

- [-] Estabilização completa do LSVE

``` 
Eliminar problemas de fechas repentinas no programa, causadas por uso de valôr des-inicializado, ou má gestão de memórias e código mal-escripto por in-experiência.

Em suma, re-escrever a applicação da maneira correcta.
```

#LSVE/Correcção

#
---

# v0-0.4.0-0

- [x] Automatização da gestão de memória

```
Deve-se encontrar alguma forma de extraviar a complexidade natural do uso de memória em C.
```

### Grade
#LSV/C

A grade é um jeito preguiçoso de se libertar memória com uma linha só.

C tem uma limitação problemática quanto à libertação de memória. Há de se liberar toda memória que é allocada, mas se tens uma estructura e todos os seus campos são allocados, tens de libertar cada um deles.

Quando há ponteiros de matrizes o problema é pior, além do campo, há de libertar cada um dos índices da matriz.

O sistema de Grade é recursivo e deve ser uma representação fiel de uma estructura qualquer. A Grade guarda um elemento e um filho de si mesmo, que contém outro elemento e outro filho de si mesmo, onde os elementos devem ser armazenados.

```C
struct Grade {
    void* elemento;
    Grade* filho;
};
```

``` diff
AVISO

-As imposições de estructuras devem respeitar o seu uso sem o Sistema-Grade, para applicações onde não se usa.
```

``` C
struct Exemplo {
	int empilhada;
	char* allocada;
}
```

Na estrutura exemplar, uma elemento é definido em pilha e outro em allocação, deve ser encapsulada em uma Grade, sendo a própria estructura um elemento.

```diff
OBSERVAÇÃO

+Usa-se ponteiro pela questão de que a funcção de des-allocação exige por ser recursiva. Pode ser que não hajam muitos pais, mas hão muitos filhos.
```

``` C
Grade* exemplo_grade = nil;
```

Este elemento é somente representativo, para que faça o papel de pai, fiélmente à estrutura original.

```diff
OBSERVAÇÃO

+A funcção usa re-allocação. Ao introduzir em nil, ele alloca normalmente.
```

``` C
grade_introduzir(&exemplo_grade, (Grade)
{
	.índice = 0,
	.constatação = var_nome(exemplo),
	.tipo = tipo_exemplo,
	.precisa_libertar = fal,
	.elemento = (Exemplo) { 0, nil },
});
```

Aqui é onde se introduz um elemento à grade que deve ser libertada.

```C
grade_introduzir(&exemplo_grade->filho, (Grade) 
{
	.índice = 0,
	.constatação = var_nome(linha),
	.tipo = tipo_linha,
	.precisa_libertar = vero,
	.elemento = memória_allocar(6 + 1),
});
```

Para accessar o elemento e definir seus valôres, deve-se usar os pré-processadores para que se evite repetições demasiadas e linhas muito extensas de código, mas se comportar como uma variável.

```diff
AVISO

-Às vezes são necessárias guardas de definições para funcções recursivas ou corridas a-sincronamente.

-Não use nomes das funcções como a guarda se não tiverem algum diferencial, em funcções recursivas a funcção é confundida com a definição. Se o diferencial for numérico, é preferível que se use o mesmo número em todas as definições. Ainda que não faça muito sentido, semânticamente, é melhor ao ler e teclar.

-Não use definições com valôres variáveis, podem ser problemáticas se a funcção for recursiva e, em cada recursão, usa-se differentes contextos.

-Não use definições no meio das funcções, sempre aos início e fim.
```

```diff
RECOMENDAÇÃO

+Deve-se usar direccionamento ('goto') se a funcção tiver um retorno ou mais de uma quebra em seu fluxo.
```

##### ___Uso real dentro do LSVE___

``` C
#if defined(DEFINIÇÃO)

#define linha_                  ((char*) (*linha).elemento)
#define intérprete_             (*((Intérprete*) (*intérprete)->elemento))
#define intérprete_grade_       (*intérprete)
#define rastilho_               (*(Rastilho*) &intérprete_.filho[1].elemento)
#define expressão_n_            (*tf.expressão_n)
#define expressões_grade_       (intérprete_grade_.filho)
#define expressão_              (**(Expressão**) &expressões_grade_[expressão_n_].elemento)
#define expressão_grade_        (expressões_grade_[expressão_n_])
#define operadores_grade_       (expressão_grade_.filho)
#define operadores_             (operadores_grade_[tf.operador_n])
#define operador_               (**(Operação**) &operadores_.elemento)
#define operador_grade_         (operadores_grade_[tf.operador_n])
#define operador_linha_         (*(Linha*) &linha_grade->elemento)
#define recúo_                  (tf.recúo - 1)

#endif // #if defined(DEFINIÇÃO)
```

#####

``` C
#define linha ((char*) exemplo_grade.filho[0].elemento)

linha[0] = 'e';
linha[1] = 'x';
linha[2] = 'e';
linha[3] = 'm';
linha[4] = 'p';
linha[5] = 'l';
linha[6] = 'o';
linha[7] = '\0';
```

Des-alloca-se o pai, seus filhos e seus elementos. Neste caso, o pai não se des-alloca, foi indicado para que não fosse com o campo 'precisa_liberar'. Mas, seu filho, será liberado.

```diff
AVISO

-Não se esqueça de eliminar as definições, compiladores podem não reclamar de re-definições por serem funcções recursivas.
```

``` C
grade_des_allocar(&exemplo_grade);

#undef linha
```

Pode ser que seja mais complicado e confuso de se codificar dessa forma, sendo um dos problemas trabalhar com alvos genéricos, sem saber o que deve ser enviado às funcções, ou receber delas, mas tendo o benefício em estruturas mais complexas.

Ao fim, uma estrutura só passa a ser representada de outra maneira. 

```
Em Estructura
```

``` mermaid
graph TD

A[Exemplo]
B[Empilhada]
C[Allocada]

F[free]

A --> B
A --> C --> F
```

```
Em Estructura com matriz (principal situação de uso, evitando o uso de ciclos e remoção manual para a liberação de memória)
```

``` mermaid
graph TD

A[Exemplo]
B[Empilhada]
C[Allocada]

0[0]
1[1]
2[2]

F1[free]
F2[free]
F3[free]

A --> B
A --> C 
C --> 0 
C --> 1 
C --> 2 

0 --> F1
1 --> F2
2 --> F3
```

```
Em Grade
```

``` mermaid
graph TD

A[Grade]
AF1[0]
AF1B[Empilhada]

AF2[1]
AF2C[Allocada]

AF2F0[0]
AF2F1[1]
AF2F2[2]

D[grade_des_allocar]

A --> AF1 --> AF1B --> D
A --> AF2 --> AF2C --> D
AF2 --> AF2F0 --> D
AF2 --> AF2F1 --> D
AF2 --> AF2F2 --> D
```

#LSVE/Traço

#
---

# v0-0.3.0-0

- [x] Detectar tipos diferentes de operadores

```
Com differentes claves, separa-se o que é ficha e o que é seu valor, além de como é attribuido.
```

### Claves

Quando se encontra o charactére de encerro comúm (fim do ficheiro), cerra-se a leitura e armazena seus dados.
```
clave_ficheiro_encerro | FDF

ficha > exemplo
-1 (implícito)
```
---
Cerra a leitura, obrigatoriamente posicionado no fim da linha.
```
clave_ficheiro_forçar_encerro | %

ficha > exemplo%
```
---
Ignora a linha lida, obrigatoriamente posicionado no início da linha. No meio dela, é mais um charactére.
```
clave_ficheiro_comentário | #

#ficha > exemplo
ficha > real
```
---
Avança sob outro ficheiro a procura de uma ficha entregue.
```
clave_lêr_avançar_e_procurar | >$()>

ficha >$(outra_ficha)> minhas_fichas.txt (ou qualquer outro caminho a algum ficheiro)
```
---
Obtém o valôr de uma ficha.
```
clave_ficha | $()

ficha > exemplo
outra_ficha > isto é um $(ficha)
```
---
Abertura da identificação de uma ficha.
```
clave_ficha_abre | (
```
---
Fechamento da identificação de uma ficha.
```
clave_ficha_fecha | )
```
---
Avança sob as fichas de outro ficheiro e permite a selecção de uma delas por interface textual.
``` 
clave_lêr_e_escolher | >>>

opções >>> minhas_opções.txt
```
---
Corre o commando entregue.
```
clave_corrêr | >*

commando >* cd ..
```
---
"Importa" as fichas de outro ficheiro para a corrida actual.
```
clave_lêr_e_avançar | >>

fichas de algum ficheiro >> ficheiro.txt
```
---
Lê brutamente o que há a seguir da ficha.
```
clave_lêr | >

ficha > texto bruto
```
---

###

#LSVE/Traço

#
---

# v0-0.2.0-0

- [ ] Sistema básico de ITU

#LSVE/Traço

#
---
# v0-0.1.0-0

- [x] Ler ficheiros

```
Os ficheiros devem ser lidos a partir das linhas de commandos, 2 ficheiros devem ser lidos. O primeiro é o ficheiro de desbraga, do segundo a frente, os de corrida.
```

#LSVE/Traço

#
---

#LSVE #Docum 
#C