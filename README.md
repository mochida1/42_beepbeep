# 42 BEEP BEEP
## BEEP!

### Sumário
Este projeto tem como objetivo criar um serviço que lê cartões NFC, comunicando-se por IPC com um broker.

---

### Introdução
Ter que ficar logando nos computadores do campus para registrar horas na casa é um pé no saco. Vamos tentar resolver esse BO.
Por favor leve em conta que a única coisa que estaremos adquirindo aqui vai ser conhecimento. E possivelmente um cartão NFC que usamos de teste para guardar de lembrança.

**Note que este é nosso primeiro projeto colaborativo, provavelmente teremos falhas no processo em alguns pontos, mas como cadetes maneiros, daremos jeitos nessas lombadas**

E isso provavelmente vai tornar a coisa mais legal do que funcionando tudo perfeitamente :)

---

### Instruções e Regras Gerais
- É terminantemente **proibido** guardar informações dos cadetes em memória não volátil;[^1]
- Se o projeto vai mexer com hardware e periféricos, você provavelmente vai ter que estar no campus;
- A norma não se aplica aqui. Isso não significa que você possa usar goto. Não use goto;
- O código será aberto, pense no que isso implica;
- Voces **PODEM** (e provavelmente vão) usar as funções de libs que não são suas;
- Voces podem usar globais. Isso não quer dizer que seja uma boa prática;
- Lembre-se que outras pessoas podem ter que trabalhar em cima do seu código;
- Temos muita pouca memória ram, evite mallocs;[^2]
- Ao contrário dos projetos habituais da 42, não teremos que lidar com valores absurdos;
- Atenha-se ao protocolo de mensagens - se ainda não houver, documente-o;
- Haverão leaks de alguns bytes na libpcsclite;
- TDD, logs e documentaçaõ, fazenofavô;
- Divirta-se, aprenda e ensine :)
- Tente não brickar o OrangePI;[^3]

---

### Overview

Antes de tudo: este é um projeto bem simples para padrões 42. A parte difícil é ter que lidar com uma lib cuja documentação é praticamente inexistente (pior que a MLX) e setar o ambiente de desenvolvimento (que já foi feito - pelo menos dentro do orangepi). ;)

Vocês estarão num time de quatro cadetes, cada um com níveis diferentes de experiência. Pensem como vão se organizar e tudo mais. As pessoas mais experientes provavelmente passarão menos tempo codando e mais tempo ajudando os outros e definindo estratégias e arquitetura.

Vocês deverão criar um programa em C que se comunica com um broker, enviando informações, recebendo instruções e as realizando. Vocês vão ter que criar um [stub->mock->fake](https://blog.onedaytesting.com.br/test-doubles/) para isso (já podem aproveitar para fazer testes com ele).
O orangepi vai estar conectado à rede via wifi, e isso pode trazer problemas por parte do broker. Enquanto é fácil fazer com que o *[test double](https://blog.onedaytesting.com.br/test-doubles/)* funcione perfeitamente, é bem válido fazer testes de estresse caso hajam *delays* ou perda de informações no meio do caminho. "Tudo que é sólido se desmancha no ar". Por outro lado, isso nos dá a chance de usá-lo via ssh para compilação sem ter que necessariamente estar nele para codar - infelizmente pela nossa configuração de rede, daria muito mais trabalho usar nfs, então nos viramos com o que temos.

Vale a pena lembrar que existem vários tipos de cartões no mercado, com protocolos de acesso diferentes entre si. Neste projetos utilizaremos apenas um tipo de cartão, porém o time deve se preocupar em deixar viável para possíveis expansões com tipos diferentes de tags NFC (a grande maioria utiliza o ISO 14443 como padrão). Pra segurança de todos e evitar fraudes, não sabemos o que ou como o bocal irá botar dentro dos cartões.

Toda a documentação necessária está dentro do diretorio DOCS. A documentação da libPCSC não existe, então vão ter que procurar dentro dos headers :S

---

### Especificações técnicas

> - Computador: [Orange PI 3 LTS, 256mb ram](http://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/details/orange-pi-3-LTS.html)
> - OS: Armbian 22.08 Jammy Kernel 5.15.y //REVISAR
> - Leitor: [\[ACS\]ACR 122U](http://www.acs.com.hk/en/products/3/acr122u-usb-nfc-reader/)
> - Cartões:  ISO 14443-A com chip EEPROM 1K de memória
> - PSCSLite: [Site](https://pcsclite.apdu.fr/) - [Repo](https://github.com/LudovicRousseau/PCSC)
---

### Protocolo
```Aqui definiremos o protocolo para que evitar erros na mensageria.```

---

### Parte Mandatória

> Funções externas permitidas: As que vocês forem usar :)

> Arquivos a serem entregues: Makefile (ou CMake equivalente), *.c, *.h, *.md e qualquer outra coisa que não seja desnecessária.

#### O programa que vocês criarem deve:
- Poder ser rodado pela linha de comando com as flags denotadas na proxima sessão;
- Ser um serviço inicializado automaticamente no boot; `man 1 sytemd`
- Quando um cartão é aproximado, energizá-lo;
- Mandar uma mensagem com o tipo de cartão para o broker; `man 2 ipc`
- Enquanto o cartão estiver energizado, receber instruções e retornar o resultado para o broker;
- Caso o cartão seja removido, fazer com que o broker saiba disso com prioridade;
- A cada operação, deverá ser mandada uma mensagem de log para o broker;
- Fazer BEEP BEEP nas horas certas, com BEEPS e cores intuitivas (beeps são isentos de log);
- Ser daemonizável; `man 7 daemon`
- BEEPBEEP é um nome para placeholder. Fica à critério do grupo batizar o filho como quiserem ;)

#### Flags
O programa deve aceitar as seguintes flags quando rodado pela linha de comando:
- `-h` ou `--help`: Demonstra como usar o programa;
- `-d` ou `--daemon`: Roda o programa em forma de daemon ou não, dependendo de como for transformado em serviço;
- `-v` ou `--verbose`: Tasca printf pra tudo quanto é lado para facilitar debugging;
- `-l` ou `--log`: Mesma coisa que o -v, mas passa o output para um arquivo; `man 3 fprintf`
- `-u` ou `--unittest`: Liga testes unitários (vocês podem fazer isso por uma diretriz de compilação se quiserem e botarem na documentação);

A única flag que deve ser mantida como padrão é o `-h`, se explicitadas as outras dentro do help.

Não é necessário fazer com que o programa entenda flags concatenadas, ex: `-dvl`, porém ele deve aceitar multiplas flags;

#### Test Doubles & TDD
Pensaram que era só um programa? HÁ! Pegadinha.

Vocês vão ter que fazer outro programa pra testar o protocolo. Simples assim.

E aproveitando que vão estar com a mão na massa, já botem bateladas de testes nesse rolê.

---

### Parte Bonus

Fazer um programa que lê de um arquivo, grava e valida todas as informações contidas nele num cartão.

Bonus extra: Deixar no jeito para no futuro poder ter compatibilidade com cartões diferentes.

Bonus extra extra: Fazer o programa mudar os dados de dentro do cartão, por um template, para outro template.

---

### Régua (aberta sem -42) // Se sim, bom.

#### Testes Ridículos

1. O programa compila?
2. Roda?
3. Apresenta leaks?
4. Não apresenta crashes ou fechamentos inesperados?
5. Se rebootado o sistema, o processo continua ativo?
6. Cheque a documentação, ela existe?

#### Validação de argumentos

1. Rode o programa sem argumentos, funciona?
2. Funciona com -h?
3. Funciona com --help?
4. Funciona com as flags descritas no -h?
5. Funciona com todas as flags no -h juntas?
6. Sai com erro caso seja um argumento não descrito no -h?
7. Dá bom quando passa uma salada de argumentos (ie: argumentos duplicados)?

#### Validação de cartão
Com o programa desligado,
1. passe um cartão perto, acende?
2. tire o cartão, apaga?
3. passe o bilhete unico ou qualquer outro dispositivo NFC diferente perto, apita erro?
Repita com o programa ligado.

#### testes de rotina
1. Apresente um cartão, faça um pedido para o broker ler e validar todos os campos;
2. Peça para o fake broker gravar e validar alguns campos;
3. Grave senhas (fáceis e diferentes de 0x000..) no bloco 4. Remova o cartão, valide as senhas e tente acessar os dados dos blocos 1-3;
4. Crie um bloco de valor, mexa nele, teste coisas normais, incrementos, decrementos, underflows e overflows podem acontecer, veja se o grupo foi realmente legal de mandar uma mensagem de aviso para o log indicando-os;
5. Peça para o fake broker fazer vários comandos e retire o cartão antes do término das operações (você provavelmente vai ter que mandar um BEEP de alguns segundos). O que acontece está de acordo com o esperado?

#### Testes escabrosos
1. Tente rodar 2 instâncias do programa. Ele reclama?
2. Desligue o broker e tente rodar o programa, ele reclama?
3. Com o programa funcionando, aproxime um cartão válido e desligue o broker, o comportamento é consistente com o esperado?
4. Crie duas instâncias do fake do broker. O programa lida bem com esse comportamento totalmente desnecessário e errado?
5. O programa eventualmente limpa a fila de mensagens caso o broker esteja desligado? Avisa?

#### Testes extras
1. O que você achar que valha a pena testar.

---


[^1]: Por motivos de segurança.
[^2]: Para evitar a fragmentação da RAM em múltiplas páginas de memória e mater a performance boa.
[^3]: Não brinque com a flash nativa.
