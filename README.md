# 42 BEEP BEEP
## BEEP!

### Sumário
Este projeto tem como objetivo criar um serviço que lê cartões NFC, comunicando-se por IPC com um broker.

---

### Introdução
Ter que ficar logando nos computadores do campus para registrar horas na casa é um pé no saco. Vamos tentar resolver esse BO.
Por favor leve em conta que a única coisa que estaremos adquirindo aqui vai ser conhecimento. E possivelmente um cartão NFC que usamos de teste para guardar de lembrança.

---

### Instruções e Regras Gerais
- É terminantemente **proibido** guardar informações dos cadetes em memória não volátil;[^1]
- Se o projeto vai mexer com hardware e periféricos, você provavelmente vai ter que estar no campus;
- A norma não se aplica aqui. Isso não significa que você possa usar goto. Não use goto;
- O código será aberto;
- Voces **PODEM** (e provavelmente vão) usar as funções de libs que não são suas;
- Voces podem usar globais. Isso não quer dizer que seja uma boa prática;
- Lembre-se que outras pessoas podem ter que trabalhar em cima do seu código;
- Temos muita pouca memória ram, evite mallocs;[^2]
- Ao contrário dos projetos habituais da 42, não teremos que lidar com valores absurdos;
- Atenha-se ao protocolo de mensagens - se ainda não houver, documente-o;
- TDD, logs e documentaçaõ, fazenofavô;
- Divirta-se, aprenda e ensine :)
- Tente não brickar o OrangePI;[^3]

---

### Overview
flags;

daemoninzação -> serviço;

documentação necessária para o projeto;

protocolo;

cartões diferentes;

leitura, status do cartão ;

pensar que a conexão é feita via wifi;

test dobules - fake broker;



`man 7 daemon`

---

### Especificações técnicas

> - Computador: [Orange PI 3 LTS, 256mb ram](http://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/details/orange-pi-3-LTS.html)
> - OS: Armbian 22.08 Jammy Kernel 5.15.y //REVISAR
> - Leitor: [\[ACS\]ACR 122U](http://www.acs.com.hk/en/products/3/acr122u-usb-nfc-reader/)
> - Cartões:  ISO 14443-A com chip EEPROM 1K de memória
> - [LibPCSC](https://github.com/LudovicRousseau/PCSC)
---

### Protocolo
```Aqui definiremos o protocolo para que evitar erros na mensageria.```

---

### Parte Mandatória


---

### Parte Bonus

---


[^1]: Por motivos de segurança.
[^2]: Para evitar a fragmentação da RAM em múltiplas páginas de memória e mater a performance boa.
[^3]: Não brinque com a flash nativa.
