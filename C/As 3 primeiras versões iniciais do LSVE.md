O LSVE teve 3 versões, a experimental, a ruim e a robusta. A primeira feita com as linhas de commando do Windows (CL), outra feita com o Visual Studio e mais outra, com as linhas do Linux com GCC.

A experimental foi a pior de todas, mas o resultado com o Visual Studio não foi muito diferente. Usando CL, é muito difícil detectar qualquer tipo de problema na corrida do programa, por limitação do compilador e filosofia da linguagem. C por si só tem a filosofia de que não se deve haver validações de nulificações por padrão, permite uma velocidade maior. A construcção dessa versão foi feita às cegas, sem muita verificação e o resultado foi um programa com muitos comportamentos desconhecidos, corra-o e o podes ter teu Disco formatado.

Com Visual Studio, aprimorou-se **alguns** problemas com vazamentos e leituras de variáveis indevidamente, mas acabei por ter um fim semelhante pelos modos de corrida dele, Debug e Release. Em Debug, parece que ele pré-enche algumas variáveis que não estão inicializadas correctamente, mas em modo Release não, corre o programa a crú. Pois quando corri o programa em outro modo, tive o mesmo problema anterior, o programa se encerrava sem motivos aparentes. 

Com **GCC** e **Valgrind**, nitidamente vê-se a diferença entre Windows e Nix para desenvolvimento C. As ferramentas Nix são bem mais simples de serem instaladas, corridas e testadas. Ainda mais do que com Visual Studio, agora consigo detectar ainda mais problemas e solucioná-los mais facilmente. A interface é complicada por ser meramente texto, mas funciona bem. 

Depois de terminar de reconstruir este pela 3ª vez, volto para terminar isto, mas por hora, tá a correr bem, sem CIs.

#LSV #LSVE #Docum 
#C