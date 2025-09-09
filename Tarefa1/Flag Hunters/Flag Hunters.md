# Flag Hunters

###### Solved by @jaumg2004

### Descrição

As letras saltam dos versos para o refrão, como uma chamada de subrotina. Há um refrão oculto que este programa não imprime por padrão. Você consegue imprimi-lo? Pode haver algo nele para você.
O código-fonte do programa pode ser baixado [aqui](C:\Users\JOÃO GABRIEL\PycharmProjects\Escola de CS\Tarefa1\Flag Hunters\lyric-reader.py). ` nc verbal-sleep.picoctf.net 65185`

### Resolução

Ao iniciar o desafio, uma das instâncias diz o seguinte: "Existe alguma sintaxe que esteja madura para a subversão?"

Pra resolver esse desafio tive que ir além dos conhecimentos convencionais de Python e aprender sobre um novo tipo de invulnerabilidade chamado Injection Command.

Injeção de comando refere-se a um método de ataque no qual um invasor injeta maliciosamente comandos do sistema em campos de entrada do usuário de applications da web para manipular e comprometer o sistema operacional (SO) subjacente ([fonte](https://www.f5.com/pt_br/glossary/command-injection)).

Antes de prosseguir é necessário entender um pouco de Python pra pegar a flag. No Python não é preciso colocar ponto-virgúla pro código funcionar como é o caso de outras linguagens, tipo C, C++, ou Java, porém não quer dizer que o ponto-virgúla não tem a sua funcionalidade, pois ele serve pra fazer a quebra de linha tanto no código, quanto na entrada de dados do usuário. Sabendo disso partiremos pra resolução da flag.

```
secret_intro = \
'''Pico warriors rising, puzzles laid bare,
Solving each challenge with precision and flair.
With unity and skill, flags we deliver,
The ether’s ours to conquer, '''\
+ flag + '\n'


song_flag_hunters = secret_intro +\
'''

[REFRAIN]
We’re flag hunters in the ether, lighting up the grid,
No puzzle too dark, no challenge too hid.
With every exploit we trigger, every byte we decrypt,
We’re chasing that victory, and we’ll never quit.
CROWD (Singalong here!);
RETURN

[VERSE1]
Command line wizards, we’re starting it right,
Spawning shells in the terminal, hacking all night.
Scripts and searches, grep through the void,
Every keystroke, we're a cypher's envoy.
Brute force the lock or craft that regex,
Flag on the horizon, what challenge is next?

REFRAIN;
```

No código é feito uma concatenação do `secret_intro` com o restante da música, tornando o `secret_intro` o ponto 0 da música.
Em determinado momento do refrão da música ele pede pra entrar com um CROWD(grito da multidão, como em um show), e está parte do código é executada: 
```    
      if line == 'REFRAIN':
        song_lines[refrain_return] = 'RETURN ' + str(lip + 1)
        lip = refrain
      elif re.match(r"CROWD.*", line):
        crowd = input('Crowd: ')
        song_lines[lip] = 'Crowd: ' + crowd
        lip += 1
      elif re.match(r"RETURN [0-9]+", line):
        lip = int(line.split()[1])      
```

Pra capturar a flag, no momento que ele pedir pra entrar com o CROWD, pode digitar qualquer coisa seguido de `;RETURN 0 `, o que vai acontecer:
o ponto-virgúla vai quebrar a linha, separando a entrada falsa do comando injetado, e o comando injetado, que nesse caso é o `RETURN 0`, vai chamar o `secret_intro` com a flag.

### Flag

`picoCTF{70637h3r_f0r3v3r_c373964d}`