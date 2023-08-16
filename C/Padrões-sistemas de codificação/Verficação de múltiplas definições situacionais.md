É comúm em C  que se use várias definições para variar o comportamento do programa sem têr de quebrá-lo em várias instâncias. Em muitos casos, é requerido somente uma única delas, então olhar por cada definição contra outras para saber se múltiplas foram introduzidas pode ser um processo complicado, se o critério é simplesmente se estiver definida.

Exemplo: 

``` C
#define windows
#define linux
#define mac
```

``` C
#if windows
#if linux || mac
#error "Se a construcção é para Windows, Linux e Mac não podem ser definidos."
#endif
void janela_abrir() {}
#endif

#if linux
#if windows || mac
#error "Se a construcção é para Linux, Windows e Mac não podem ser definidos."
#endif
void janela_abrir() {}
#endif

#if mac
#if windows || linux
#error "Se a construcção é para Mac, Windows e Linux não podem ser definidos."
#endif
void janela_abrir() {}
#endif
```

Cada definição carrega consigo um 0 ou 1, fal ou vero. Pode-se somar seus valôres e verificar se somam mais de 1, assim, sabe-se que mais de uma foi definida. 

```C
#if defined(windows) + defined(linux) + defined(mac) != 1
#error "Defina somente um sistema operativo"
#endif

#if windows
void janela_abrir() {}
#endif

#if linux
void janela_abrir() {}
#endif

#if mac
void janela_abrir() {}
#endif
```

E, finalmente, podemos chamar a funcção por situação, de acordo com a definição.

```C
#include "janela.h"

void main(int** a, char** b) {
	janela_abrir();
}
```

#
***

#Docum #C #LSV/C